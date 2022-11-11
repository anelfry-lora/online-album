<script setup>

defineProps(['title', 'description', 'id'])
const url = "https://www.youtube.com/embed/"

</script>

<template>
    <!-- Modal -->
    <div class="modal fade" id="details" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1"
        aria-labelledby="staticBackdropLabel" aria-hidden="true">
        <div class="modal-dialog modal-lg modal-dialog-centered p-4">
            <div class="modal-content">
                <div class="modal-body">
                    <button type="button" class="btn-close float-end" data-bs-dismiss="modal" aria-label="Close">
                    </button>
                    <div class="row my-2 justify-content-center align-items-center">
                        <div class="col-6">
                            <iframe width="96%" height="200px" :src="url + id">
                            </iframe>
                        </div>
                        <div class="col-5 m-1 pt-auto">
                            <h5>{{ title }}</h5>
                            <p class="description">{{ description }}</p>
                            <!-- <p class="text-truncate">{{description}}</p> -->
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
.description {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    /* number of lines to show */
    line-clamp: 3;
    -webkit-box-orient: vertical;
}

iframe {
    max-width: 100%;
    /* height: auto; */
}
</style>