# :joystick: Vue.js를 이용한 Youtube 클론 코딩 :red_car:

- vue 구조

<img width="1068" alt="app 구조" src="https://user-images.githubusercontent.com/60081217/86607124-d6c5f380-bfe3-11ea-8c68-750170b87826.png">

- 폴더 구조

<img width="174" alt="폴더 구조" src="https://user-images.githubusercontent.com/60081217/86607118-d4fc3000-bfe3-11ea-8707-fdd781e1581a.png">

- bootstrap으로 꾸밀 예정이니 CDN 을 미리 옮겨 놓자.

- public/index.html 에 CDN을 넣어주고, vue.app에 container 클래스를 달아놓자!

- style을 할 경우 어느 컴포넌트 위치던 간에 div에 스타일을 준다면 모든 div에 적용된다.

  - 해결방법

    `<style:scoped>` 를 통해 현재 컴포넌트의 엘리먼트에만 적용할 수 있다. 하지만 이는 느려지는 이슈가 발생하기 때문에 클래스 지정을 해야한다.

    scoped는 vue.js에서만 제공하는 속성값이다.



## Search Bar

- input 역할을 할 searchBar 컴포넌트를 만든다. 

- 기본 설정을 완료한 후 (불러오기, 등록, 사용) input값을 작성했을 때 어디에 값이 기록되는지 console을 통해 확인한다. 

  <img width="393" alt="searchBar" src="https://user-images.githubusercontent.com/60081217/86607273-05dc6500-bfe4-11ea-988e-1de367ca2400.png">

- target → value 에서 확인 할 수 있다.

```vue
<template>
  <div>
      <input type="text" @keypress.enter="onInput">
  </div>
</template>

<script>
export default {
    name: 'SearchBar',
    methods: {
        onInput(event) {
            console.log(event) //입력된 데이터를 찾자! => event.target.value
        }
    }
}
</script>
```

- 입력값을 `$emit` 으로 app에 보내야지

- 첫번째 인자는 이벤트 이름,그 이후부터는 상위 컴포넌트로 보내줄 데이터들로 취급된다.

  ⇒ 첫번째는 이벤트 이름, 두번째에 event.target.value를 넣어주면 된다.

```vue
<script>
export default {
    name: 'SearchBar',
    methods: {
        onInput(event) {
            // console.log(event) 
            this.$emit('input-change', event.target.value)
        }
    }
}
</script>
```

- 상위 컴포넌트에서 이벤트를 확인

```vue
<template>
  <div class="container">
		<!-- 이벤트가 발생하면 메소드를 실행 -->
    <SearchBar @input-change="onInputChange"/>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import SearchBar from '@/components/SearchBar.vue'

export default {
  name: 'App',
  components: {
    SearchBar
  },
  methods: {
		// 데이터가 제대로 왔는지 콘솔로 확인!
    onInputChange(inputValue) {
      console.log(inputValue)
    }
  }
}
</script>
```

<img width="525" alt="search확인" src="https://user-images.githubusercontent.com/60081217/86607310-112f9080-bfe4-11ea-8a9f-0c88a5309d29.png">



## YouTube API 사용하여 검색

- 검색어를 받아왔으면 그걸로 검색을 해야지 → Youtube API 를 사용한다.

- **google developer console**에서 새 프로젝트를 생성한다.

  https://console.developers.google.com/projectselector2/apis/dashboard?hl=ko&supportedpurview=project

- **youtube API 키** 값 생성하는 절차!

  새프로젝트 만들기 > 생성한 프로젝트로 들어오기 > 햄버거바 - `API 및 서비스`안에 대시보드 클릭 > `+ API 및 서비스 사용 설정` 클릭 > `youtube Data API v3` 검색하여 사용 설정 클릭 > 사용자 인증 정보 클릭 > 사용자 인증정보만들기 > API 키 생성 > 받은 KEY 복사하여 vsc에서 활용하기

- HTTP 요청 URL 받기

  가이드 문서 > Learn More > Add Youtube data > reference > search > list > HTTP 요청 코드

  https://developers.google.com/youtube/v3/docs/search/list\\

  ```
  # HTTP 요청
  <https://www.googleapis.com/youtube/v3/search>
  ```

- **axios** 를 이용하기

  ```
  # axios 설치 코드
  $ npm i axios
  ```

  - 설치 후 App에 import 한 뒤에 axios.get을 사용한다.

- axios를 통해 찾은 필요한 데이터를 VideoList 컴포넌트를 만들어 보이도록 하기 => `props`



## .env.local 이용

- API_KEY는 노출이 되면 안된다! → .gitignore로 env파일로 관리 → 완벽한 보안이 안되니 백엔드에서 관리

- .gitignore → .env.local 포함

  <img width="247" alt="env 사진" src="https://user-images.githubusercontent.com/60081217/86607315-1260bd80-bfe4-11ea-9e4c-c7e288928967.png">

- 상위 위치에서 `.env.local` 파일 만들고 아래 형식으로 만든다.

  `VUE_APP_YOUTUBE_API_KEY=(API키 값 그대로, 공백 x)`

- `.env.local` 파일에 작성한 변수명이 서버 최소 실행시에 `process.env.변수명` 으로 자동 설정된다.



## App.vue 와 VideoList.vue

- VideoList 에 data 를 바인딩 시켜준다.

```vue
<template>
  <div class="container">
    <SearchBar @input-change="onInputChange"/>
    <VideoList :videos="videos"/>
  </div>
</template>

<script>
...
export default {
  name: 'App',
  data() {
    return {
      videos: []
    }
  },
...
</scripts>
```

- youtube api를 통해 얻는 값을 → videos로 할당할 것이다 → axios를 이용한다.



### axios

<img width="798" alt="axios예시 1" src="https://user-images.githubusercontent.com/60081217/86607322-1391ea80-bfe4-11ea-94dd-30e224f106eb.png">

<img width="820" alt="axios예시2" src="https://user-images.githubusercontent.com/60081217/86607419-302e2280-bfe4-11ea-9f2d-a35b5d36643e.png">

- 추가 데이터가 필요한 경우 [공식문서](https://github.com/axios/axios#example) 에 처럼 params를 통해 넣어준다.

- [youtube Data API > Search > list](https://developers.google.com/youtube/v3/docs/search/list?hl=ko) 에 나와있는 것 처럼 필요한 데이터 들을 넣어준다.

  <img width="886" alt="http 요청" src="https://user-images.githubusercontent.com/60081217/86607425-31f7e600-bfe4-11ea-8ec8-63164e8a39a8.png">
  <img width="886" alt="매개변수" src="https://user-images.githubusercontent.com/60081217/86607430-33291300-bfe4-11ea-8c37-81caf5b96f57.png">

  - emit으로 받은 검색어를 q로 넘겨주면 되겠다

  <img width="890" alt="q설명" src="https://user-images.githubusercontent.com/60081217/86607435-345a4000-bfe4-11ea-95c4-6bef39dd9251.png">
  <img width="874" alt="type 설명" src="https://user-images.githubusercontent.com/60081217/86607440-36240380-bfe4-11ea-93e6-8a2703556afb.png">

  

- App.vue

```vue
<script>
import axios from 'axios'
// import HelloWorld from './components/HelloWorld.vue'
import SearchBar from '@/components/SearchBar.vue'
import VideoList from '@/components/VideoList.vue'

const API_KEY = process.env.VUE_APP_YOUTUBE_API_KEY
const API_URL = '<https://www.googleapis.com/youtube/v3/search>'

export default {
  name: 'App',
  components: {
    SearchBar,
    VideoList
  },
	data() {
	    return {
	      videos: [] // 비디오의 배열을 VideoList.vue로 보낼거다
	    },
	  }
  methods: {
    onInputChange(inputValue) {
      // console.log(inputValue)
      axios.get(API_URL, {
        params: { // 추가적으로 필요한 데이터
          key: API_KEY,
          part: 'snippet', // 필수 매개변수들 넣어준다.
          type: 'video',
          q: inputValue // emit으로 받은 검색어 
        }
      })
      .then(response => {
        console.log(response) // 콘솔로 어디에 값이 있는지 확인해야 함.
      })
				.catch((error) => {
          console.log(error);
        });
        this.selectedVideo = null // 다른거 검색하면 selectedVideo 가 null 값이 되어야한다.
    },
  }
}
</script>
```

- console 찍어본 결과 : data → items → 배열로 존재한다.

<img width="1469" alt="콘솔 결과" src="https://user-images.githubusercontent.com/60081217/86607447-38865d80-bfe4-11ea-951c-5ecf3782f835.png">

- 콘솔의 경로로 data 에 할당해주자.

```vue
<script>
...
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
      .catch(error => { console.log(error) })
    }
  }
...
</script>
```

- VideoList.vue

```vue
<template>
  <div>
      <h1>VideoList</h1>
      {{ videos }}
  </div>
</template>

<script>
export default {
    name: 'VideoList',
    // 상위 컴포넌트에서 'videos'라는 이름으로 props를 통해 데이터가 온다.
    props: {
        videos: {
            type: Array
        }
    }
}
</script>

<style>

</style>
```

- 결과 사진

<img width="584" alt="결과 사진" src="https://user-images.githubusercontent.com/60081217/86607453-39b78a80-bfe4-11ea-87f3-ae9f8a4a906a.png">





## VideoListItem.vue

- 영상 리스트 보여주기위한 컴포넌트

  `VideoList.vue`에서 v-for + props 이용해서`VideoListItem`으로 보내기

  - v-for 안에 사용되는 key값은 video.etag를 사용한다.
  - `.etag`는 console을 통해 object안에 하나하나 영상 식별할 수 있는 식별자 역할함을 보고 이를 이용한다.

```jsx
<template>
    <ul>
        <VideoListItem v-for="video in videos" :key="video.etag" :video="video" />
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
    }
}
</script>
```

- 요소 하나를 `props` 로 넘겨준다.
- 상위 컴포넌트에 바인드 한다. `:video="video"`
- key: value 형태로 하위 컴포넌트는  key값을 이용하여 value를 받는다.
- 하위 컴포넌트에서 props를 정의해서 받는다 ⇒ key값(왼쪽) 이용.
- VideoListItem.vue

```jsx
<template>
    <li class="list-group-item">
        {{ video }}
    </li>
</template>

<script>
export default {
    name: 'VideoListItem',
    props: {
        video: {
            type: Object
        }
    }
}
</script>

<style scoped>

</style>
```

### 

### 썸네일 이미지 가져오기

- thumbnail url 이 있는 위치는 : snippet → thumbnails → default → url

- 썸네일 url이 있는 위치는 거의 동일하다 → `computed` 를 이용하자. 왜냐? 참조가 너무 깊어져서 길잖아!
- 템플릿에서 가져다 쓸 수 있도록 고정하는 것!
- VideoListItem.vue

```jsx
<template>
    <li class="list-group-item">
        <img :src="thumbnailUrl" alt="youtube-thumbnail-image">
        <div class="media-body">
            {{ video.snippet.title }}
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
```

- 결과 사진

<img width="1172" alt="떼걸룩 리스트 " src="https://user-images.githubusercontent.com/60081217/86607460-3ae8b780-bfe4-11ea-91f8-901baf0d455c.png">



### 영상이 클릭되면  App.vue 에 알리고 → detail 페이지를 열어주자!

- VideoListItem.vue에서 클릭을 감지하고 이벤트를 보내자 ! (하위 → 상위 : `emit` )

```jsx
<template>
		<!--클릭하면 onVideoSelect 메소드 발생시키자-->
    <li @click="onVideoSelect" class="list-group-item">
        <img :src="thumbnailUrl" alt="youtube-thumbnail-image">
        <div class="media-body">
            {{ video.snippet.title }}
        </div>
    </li>
</template>

<script>
export default {
    name: 'VideoListItem',
    props: { ...
    computed: { ...
    methods: {
				// 선택하면 emit을 통해 이벤트 발생해서 데이터 보내자
        onVideoSelect() {
            this.$emit('video-select', this.video)
        }
    }
}
</script>
```

- VideoListItem의 상위 컴포넌트는 : VIdeoList
- VideoList.vue 에서 App.vue로 emit해줘야 한다!

```jsx
<template>
    <ul class="list-group">
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
```

- App.vue : console.log를 통해 데이터가 잘 도착했는지 확인해 보자!

```jsx
<template>
  <div class="container">
    <SearchBar @input-change="onInputChange"/>
    <VideoList :videos="videos" @video-select="onVideoSelect"/>
    
  </div>
</template>

<script>
import axios from 'axios'
import SearchBar from '@/components/SearchBar.vue'
import VideoList from '@/components/VideoList.vue'
const API_KEY = process.env.VUE_APP_YOUTUBE_API_KEY
const API_URL = '<https://www.googleapis.com/youtube/v3/search>'

export default {
  name: 'App',
  data() {
    return {
      videos: []
    }
  },
  components: {
    SearchBar,
    VideoList    
  },
  methods: {
    onInputChange(inputValue) {...
    },
    onVideoSelect(video) {
      console.log(video) // 이를 통해 잘 전달됐는지 확인하기 
    }
  }
}
</script>

<style>
</style>
```

<img width="1062" alt="snippet확인" src="https://user-images.githubusercontent.com/60081217/86607469-3de3a800-bfe4-11ea-94b8-2509e8073d58.png">

- 잘 도착한다! ⇒ snippet 안에 내용 동일!



### 클릭된 비디오를 디테일 창에서 보여주자!

- 여기까지의 로직

|               | emit |           | emit |      | prop |             |
| ------------- | ---- | --------- | ---- | ---- | ---- | ----------- |
| VideoListItem | =>   | VideoList | =>   | App  | =>   | VideoDetail |

1. VideoDetial.vue 생성 후 기본 설정하기

2. App.vue에 데이터를 바로 prop 할 수 없기에 특정 메서드로 새로운 데이터 값을 넣어준 뒤에 보내줘야 한다.

   **받은 데이터 바로 prop할 수 없다!**

   - App.vue → VideoDetail로 `props` 한다!

```jsx
<template>
  <div class="container">
    <SearchBar @input-change="onInputChange"/>
    <VideoDetail :video="selectedVideo"/>
    <VideoList :videos="videos" @video-select="onVideoSelect"/>
  </div>
</template>

<script>
...
import VideoDetail from '@/components/VideoDetail.vue'
...

export default {
  name: 'App',
  data() {
    return {
      videos: [],
      selectedVideo: {}
    }
  },
  components: {
    SearchBar,
    VideoList,
    VideoDetail
  },
  methods: {
    onInputChange(inputValue) { ...
    onVideoSelect(video) {
      // console.log(video)
      this.selectedVideo = video
    }
  }
}
</script>
```

- VideoDetail.vue

```jsx
<template>
    <div>
        {{ video.snippet.title }}
    </div>
</template>

<script>
export default {
    name: 'VideoDetail',
    props: {
        video: {
            type: Object
        }
    }
}
</script>

<style>

</style>
```

- 이렇게 하고 콘솔을 켜면 에러들이 난다  → ViewDetail에 존재 안하기 때문에 따라서 조건이 필요하다.

<img width="1470" alt="콘솔 에러" src="https://user-images.githubusercontent.com/60081217/86607494-4805a680-bfe4-11ea-8ad2-49f67e9ba04b.png">

- VideoDetail.vue

```jsx
<template>
  <div v-if="video">
      <h4>{{ video.snippet.title }}</h4>
  </div>
</template>
```

<img width="1431" alt="와그작 와그장" src="https://user-images.githubusercontent.com/60081217/86607503-4c31c400-bfe4-11ea-9f91-60da7d54d6b8.png">





### VideoView.vue에 동영상 보이기

- 유투브에서 제공하는 iframe 태그를 쓰자! [유투브 iframe](https://developers.google.com/youtube/iframe_api_reference?hl=ko)

- 햄버거 바 안에 플레이어 매개 변수 들어가면 플레이어 삽입 방법을 알 수 있다.

```html
<iframe id="ytplayer" type="text/html" width="640" height="360"
  src="<https://www.youtube.com/embed/M7lc1UVf-VE?autoplay=1&origin=http://example.com>"
  frameborder="0"></iframe>

<iframe type="text/htm" src="" frameborder="0"></iframe>
```

- bootstrap → embed를 통해 꾸밀 수 있다. → 비율에 따라 움직일 수 있다.

[Bootstrap Embeds 속성](https://getbootstrap.com/docs/4.4/utilities/embed/)

- VideoDetail.vue

```html
<template>
    <div v-if="video">
        <div class="embed-responsive embed-responsive-16by9">
            <iframe type="text/html" class="embed-responsive-item" :src="videoUrl" frameborder="0"></iframe>
        </div>
        <div class="details">
                    <!-- <h4>{{ video.snippet.title }}</h4>  -->
        <h4 v-html="video.snippet.title"></h4>
        <!-- <p>{{ video.snippet.description }}</p> -->
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
```



### 글자가 깨지는 현상?

- v-html`를 이용하여 해결하자

```html
<template>
  <div v-if="video" class="col-lg-8">
      <div class="embed-responsive embed-responsive-16by9">
        <iframe type="text/html" class="embed-responsive-item" :src="videoUrl" frameborder="0"></iframe>
      </div>
      <div class="details">
        <!-- <h4>{{ video.snippet.title }}</h4> 변경전 -->
        <h4 v-html="video.snippet.title"></h4>
        <!-- <p>{{ video.snippet.description }}</p> 변경전-->
        <p v-html="video.snippet.description"></p>
      </div>
  </div>
</template>
```



### 마지막으로 Bootstrap의 'row'를 이용해서 유투브와 똑같이 만들어 준다!

<img width="993" alt="마지막" src="https://user-images.githubusercontent.com/60081217/86607505-4d62f100-bfe4-11ea-871b-72ad45049870.png">

