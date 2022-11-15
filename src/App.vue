<script setup>
import { ref, onMounted } from 'vue';
import ModalDelete from '@/components/partials/ModalDelete.vue'
import ModalDetails from '@/components/partials/ModalDetails.vue'
import { collection, onSnapshot, addDoc, doc, deleteDoc } from "firebase/firestore";
import { db } from '@/firebase'

const searchVideo = ref('')
const video = ref('')
const uid = ref('')
const id = ref('')
const title = ref('')
const description = ref('')
const duration = ref('')
const listVideos = ref([])


onMounted(async () => {
    onSnapshot(collection(db, "videos"), (querySnapshot) => {
        const fbvideos = [];
        querySnapshot.forEach((doc) => {
            const fbvideo = {
                id: doc.id,
                uid: doc.data().uid,
                title: doc.data().title,
                description: doc.data().description,
                url: doc.data().url,
                img: doc.data().img,
                duration: doc.data().duration,
            }
            fbvideos.push(fbvideo)
        })
        listVideos.value = fbvideos
    })
})

const extractId = ( url ) => {
    const match = url.match(/^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/)
    const videoId = (match && match[7].length === 11) ? match[7] : false
    return videoId
}

const add = (url) => {
    if (isUrl(url)) {
        fetch(`https://www.googleapis.com/youtube/v3/videos?id=${extractId(url)}&key=AIzaSyA5KkFSnkBYaS1ES3ObFy4nQUyIDMPhZSc&part=snippet,contentDetails,statistics,status`)
            .then(resp => resp.json())
            .then(response => {
                // listVideos.value
                if (listVideos.value.find(obj => obj.uid === response.items[0].id)) {
                    alert('Este video ya esta en la lista.')
                } else {
                    addDoc(collection(db, "videos"), {
                        uid: response.items[0].id,
                        title: response.items[0].snippet.localized.title,
                        description: response.items[0].snippet.localized.description,
                        url: url,
                        img: response.items[0].snippet.thumbnails.standard.url,
                        duration: response.items[0].contentDetails.duration.replace("PT", "").replace("H", ":").replace("M", ":").replace("S", ""),
                    });
                    video.value = ''
                }
            })
            .catch(err => console.error(err));
    } else {
        alert('Es necesario una URL')
    }
}

const deleted = () => deleteDoc(doc(collection(db, "videos"), id.value))

const details = () => detailsVideo = listVideos.value.filter(item => item.uid === uid.value)

const isUrl = url => url.includes('http') || url.includes('https')

</script>

<template>
    <div class="container-sm">
        <div class="mx-1">
            <h1>Añadir nuevo video</h1>
            <div class="input-group mb-3">
                <input v-model="video" @keyup.enter="add(video)" class="form-control" placeholder="Añadir..." autofocus
                    aria-label="URL de un video" aria-describedby="button-addon2">
                <button class="btn btn-primary gap-2 col-4 col-sm-2 mx-auto" type="button" id="button-addon"
                    :disabled="!isUrl(video)" @click="add(video)">Añadir</button>
            </div>
            <div>
                <div class="row">
                    <div class="col-12 col-sm-12 col-md-3 p-3" v-for="(video, index) in listVideos" :key="index"
                        data-bs-toggle="modal" data-bs-target="#details" style="cursor:pointer;"
                        @click="uid = video.uid, title = video.title, description = video.description, duration = video.duration">
                        <div class="card">
                            <img class="card-img " :src="video.img" alt="Card image cap">
                            <div class="card-img-overlay">
                                <button type="button"
                                    class="active btn btn-outline-light btn-close btn-close-white float-end"
                                    aria-label="Close" data-bs-toggle="modal" data-bs-target="#delete"
                                    @click="uid = video.uid, id = video.id">
                                </button>
                                <span class="badge text-bg-dark position-absolute bottom-0 end-0">
                                    {{ video.duration }}
                                </span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <ModalDelete @deleted="deleted(id)"></ModalDelete>
        <ModalDetails :title="title" :description="description" :id="uid" :duration="duration"></ModalDetails>
    </div>
</template>


