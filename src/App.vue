<template>
    <div class="wrapper" :class="{'hide-aside' : videos.length === 0}">
        <header>
            <a href="/">
                <span class="youtube">
                    <i class='fab fa-youtube'></i> YouTube Browser
                </span>
            </a>
            <SearchBar
                @searchChange="onSearchChange" 
            />
        </header>
        <main>
            <section class="videoWrapper">
                <VideoDetail 
                    :video="selectedVideo"
                />
                
                <!--<PopularVideos
                    v-if="popularVideos.length > 0"
                    :popularVideos="popularVideos"
                    @videoSelect="onVideoSelect"
                />-->
                
            </section>
        </main>
        <aside v-show="videos.length > 0">
            <p>Search Results</p>
            <VideoList 
                :videos="videos"
                @videoSelect="onVideoSelect"
            />
        </aside>
    </div>
</template>

<script>
import SearchBar from '@/components/SearchBar';
import VideoList from '@/components/VideoList';
import VideoDetail from '@/components/VideoDetail';
import PopularVideos from '@/components/PopularVideos';
import axios from 'axios';
const API_KEY = process.env.VUE_APP_YOUTUBE_API_KEY;

export default {
    name: 'App',
    components: {
        SearchBar,
        VideoList,
        VideoDetail,
        PopularVideos
    },

    data(){
        return {
            videos: [],
            popularVideos: [],
            selectedVideo: null,
            
        }
    },

    created(){
        this.getPopularVideos();
    },

    methods: {
        getPopularVideos(){
            axios.get('https://www.googleapis.com/youtube/v3/videos', {
                params: {
                    key: API_KEY,
                    part: 'snippet',
                    chart: 'mostPopular',
                    maxResults: 20
                }
            }).then( (response) => {
                //console.log(response);
                this.popularVideos = response.data.items;
            }) 
        },

        onSearchChange(searchTerm){
            axios.get('https://www.googleapis.com/youtube/v3/search', {
                params: {
                    key: API_KEY,
                    type: 'video',
                    part: 'snippet',
                    chart: 'mostPopular',
                    q: searchTerm,
                    maxResults: 10
                }
            }).then( (response) => {
                //console.log(response);
                this.videos = response.data.items;
            })
        },

        onVideoSelect(video){
            //console.log(video);
            this.selectedVideo = video;
        }
    }
}
</script>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@700&display=swap');
    .wrapper{
        display: grid;
        grid-template-areas: 
            'header header header header header'
            'main main main main aside'
            'main main main main aside';
        gap: 2rem;
        grid-template-columns: auto auto auto auto 20vw;
        grid-template-rows: 5rem auto 3rem;
        overflow-x: hidden;
        height: 100vh;
        width: 100vw;
        background-color: #EDEEF4;
        overflow-y: hidden;
    }

    header{
        grid-area: header;
        background-color: #fff;
        box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.05);
        position: fixed;
        width: 100vw;
        z-index: 2;
        
    }
    main{
        grid-area: main;
        padding-left: 10rem;
        overflow-y: auto;
        overflow-x:hidden;
    }

    .videoWrapper{
        box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
        padding-bottom: 2rem;
        background-color: #fff;
        border-radius: 5px;
    }
    aside{
        grid-area: aside;
        overflow-y: scroll;
    }

    .youtube{
        font-family: 'Roboto', sans-serif;
        position: absolute;
        top: 1.5rem;
        font-size: 14px;
        left: 2rem;
        cursor: pointer;
    }

    .youtube i{
        font-size: 30px;
        position: relative;
        top: .4rem;
        color: red;
    }

    .hide-aside{
        display: grid;
        grid-template-areas: 
            'header header header header header'
            'main main main main main'
            'main main main main main';
        gap: 2rem;
        overflow-y: auto;
    }

    .hide-aside main{
        padding-right: 10rem;
        padding-bottom: 2rem;
    }

    a{
        color: inherit;
    }
    a:hover{
        color: inherit;
    }

    .popular-videos{
        /*overflow-y: scroll;
        overflow-x: hidden;*/
    }
</style>