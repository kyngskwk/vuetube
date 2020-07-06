<template>
    <ul class="list-group col-lg">
        <VideoListItem 
            v-for="video in videos" 
            :key="video.etag" 
            :video="video"
            @video-select="onVideoSelect"
        />
    </ul>
</template>

<script>
import VideoListItem from '@/components/VideoListItem.vue'

export default {
    name: 'VideoList',
    components: {
        VideoListItem
    },
    // 상위 컴포넌트에서 'videos'라는 이름으로 props를 통해 데이터가 온다.
    props: {
        videos: {
            type: Array
        }
    },
    methods: {
        onVideoSelect(video) {
            this.$emit('video-select', video) // this 안쓴 이유는 받은 인자에 데이터가 있기에
        }
    }
}
</script>

<style>

</style>