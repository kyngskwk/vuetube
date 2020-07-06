<template>
    <div v-if="video" class="col-lg-8">
        <div class="embed-responsive embed-responsive-16by9">
            <iframe  type="text/html" :src="videoUrl" frameborder="0" class="embed-responsive-item"></iframe>
        </div>
        <div class="details">
            <h4 v-html="video.snippet.title"></h4>
            <p v-html="video.snippet.description"></p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'VideoDetail',
    props: {
        video: {
            type: Object
        }
    },
    computed: {
        videoUrl() { // 이미 만들어진 비디오 url로 만들어 놓자!
            const videoId = this.video.id.videoId
            return `https://www.youtube.com/embed/${videoId}`
        }
    }
}
</script>

<style scoped>
.details {
    margin-top: 10px;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}
</style>