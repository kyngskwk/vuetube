<template>
    <li @click="onVideoSelect" class="list-group-item">
      <img :src="thumbnailUrl" alt="youtubeimg">
      <div class="media-body" v-html="video.snippet.title">
      </div>
    </li>
</template>

<script>
export default {
    name: 'VideoListItem',
    props: {
        video: {
            type: Object
        }
    },
    computed: {
        thumbnailUrl() {
            return this.video.snippet.thumbnails.default.url
        }
    },
    methods: {
        onVideoSelect() {
            this.$emit('video-select', this.video)
        }
    }
}
</script>

<style scoped>
li {
    display: flex;
    cursor: pointer;
}

/* 마우스 오버시 백그라운드 흐리게 */
li:hover {
    background-color: #eee;
}

.media-body {
    margin: auto 0;
}
</style>