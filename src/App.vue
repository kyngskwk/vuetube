<template>
  <div class="container">
    <SearchBar @input-change="onInputChange"/>
    <div class="row">
      <VideoDetail :video="selectedVideo"/>
      <VideoList :videos="videos" @video-select="onVideoSelect"/>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
// import HelloWorld from './components/HelloWorld.vue'
import SearchBar from '@/components/SearchBar.vue'
import VideoList from '@/components/VideoList.vue'
import VideoDetail from '@/components/VideoDetail.vue'


const API_KEY = process.env.VUE_APP_YOUTUBE_API_KEY
const API_URL = 'https://www.googleapis.com/youtube/v3/search'


export default {
  name: 'App',
  data() {
    return {
      videos: [],
      selectedVideo: null
    }
  },
  components: {
    SearchBar,
    VideoList,
    VideoDetail
  },
  methods: {
    onInputChange(inputValue) {
      // console.log(inputValue)
      axios.get(API_URL, {
        params: { // 추가적으로 필요한 데이터
          key: API_KEY,
          part: 'snippet', // 필수 매개변수들 넣어준다.
          type: 'snippet',
          q: inputValue // emit으로 받은 검색어 
        }
      })
      .then(response => {
        // console.log(response) => data.items
        this.videos = response.data.items
      })
      .catch(error => { 
        console.log(error);
      });
      this.selectedVideo = null
    },
    onVideoSelect(video) {
      console.log(video)
      this.selectedVideo = video
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
