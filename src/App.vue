<template>
    <div class="wrapper" :class="{'hide-aside' : videos.length === 0}">
        <header>
            <div class="row">
                <div class="col-sm-2 youtube-col">
                    <a href="/">
                        <span class="youtube">
                            <i class='fab fa-youtube'></i>
                        </span>
                        <span class="youtube-text">
                            YouTube
                        </span>
                        <span class="browser-text">
                            Browser
                        </span>
                    </a>
                </div>
                <div class="col-sm-8 text-center">
                    <SearchBar @searchChange="onSearchChange" />
                </div>
                <div class="col-sm-2 github-col">
                    <a href="https://github.com/freinet12/youtube-browser" target="_blank">
                        
                        <span>
                            <i class="fab fa-github"></i>
                        </span>
                        <span class="github-text">
                            Clone It
                        </span>
                        
                    </a>
                </div>
            </div>
        </header>
        <main :class="{'expand-main' : videos.length > 0}">
            <section class="videoPlayerWrapper" v-if="selectedVideo !== null">
                <VideoDetail :video="selectedVideo" />
            </section>
            <section class="popularVideos" 
                v-if="!hidePopularVideos"
            >
                <PopularVideos 
                    v-if="popularVideos.length > 0" :popularVideos="popularVideos"
                    @videoSelect="onVideoSelect" 
                />
            </section>
            <section class="mobileSearchResults" v-if="isMobile">
                <VideoList 
                :videos="videos" 
                @videoSelect="onVideoSelect" />
            </section>
        </main>
        <aside v-show="videos.length > 0 && !isMobile" id="videoList">
            <p>Search Results</p>
            <VideoList :videos="videos" @videoSelect="onVideoSelect" />
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

        data() {
            return {
                videos: [],
                popularVideos: [],
                selectedVideo: null,
                nextPageToken: null,
                isMobile: false,
                hidePopularVideos: false
            }
        },

        created() {
            this.getPopularVideos();
            this.getScreenSize();

        },

        methods: {
            getScreenSize(){
                let screenSize = screen.width;
                if (screenSize < 600){
                    this.isMobile = true;
                } else {
                    this.isMobile = false;
                }
            },
            getPopularVideos() {
                axios.get('https://www.googleapis.com/youtube/v3/videos', {
                    params: {
                        key: API_KEY,
                        part: 'snippet,statistics',
                        chart: 'mostPopular',
                        maxResults: 20
                    }
                }).then((response) => {
                    this.popularVideos = response.data.items;
                })
            },

            async searchVideos(searchTerm, page) {
                let res = await axios.get('https://www.googleapis.com/youtube/v3/search', {
                        params: {
                            key: API_KEY,
                            type: 'video',
                            part: 'snippet',
                            pageToken: page || null,
                            q: searchTerm,
                            maxResults: 50
                        }
                    })
                    .then(response => response)
                    .catch(err => err);
                return res;

            },

            async onSearchChange(searchTerm) {
                let videos = await this.searchVideos(searchTerm);
                if (!videos.data) {
                    console.error(videos);
                } else {
                    this.videos = videos.data.items;
                    this.nextPageToken = videos.data.nextPageToken;
                    if (this.isMobile){
                        this.hidePopularVideos = true;
                    }
                    this.startInfiniteScroll(searchTerm);
                }

            },

            async startInfiniteScroll(searchTerm) {
                let vm = this;
                let videoList = document.getElementById('videoList');
                videoList.addEventListener('scroll', async function () {
                    let currentScrollPosition = Math.ceil(videoList.scrollTop +
                        videoList.clientHeight);

                    //if we've reached the bottom of the current video list, fetch some more
                    if (currentScrollPosition >= videoList.scrollHeight) {
                        let moreVideos = await vm.searchVideos(searchTerm, vm
                            .nextPageToken);
                        vm.nextPageToken = moreVideos.data.nextPageToken;
                        //push each new item to our videos list
                        for (let i = 0; i < moreVideos.data.items.length; i++) {
                            vm.videos.push(moreVideos.data.items[i]);
                        }
                    }

                });
            },

            onVideoSelect(video) {
                this.selectedVideo = video;
            }
        }
    }

</script>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@700&display=swap');

    @media screen and (min-width: 320px) {
        .wrapper {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main main'
                'main main main main main';
            gap: 2rem;
            grid-template-columns: auto auto auto auto auto;
            grid-template-rows: auto auto 3rem;
            overflow-x: hidden;
            height: 100vh;
            width: 100vw;
            background-color: #EDEEF4;
            overflow-y: hidden;
        }

        header {
            grid-area: header;
            background-color: #fff;
            box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.05);
            position: fixed;
            width: 100vw;
            z-index: 2;
        }

        main {
            grid-area: main;
            padding-left: 1rem;
            padding-right: 1rem;
            overflow-y: auto;
            overflow-x: hidden;
            padding-top: 8.5rem;
            max-width: 100vw;
        }

        .expand-main {
            padding-left: 2rem;
        }

        .videoPlayerWrapper {
            box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
            padding-bottom: .5rem;
            background-color: #fff;
            border-radius: 5px;
            margin-bottom: 2rem;
        }

        aside {
            grid-area: aside;
            overflow-y: scroll;
            overflow-x: hidden;
        }

        .mobileSearchResults{
            width: 100%;
        }

        .youtube-col{
            border-bottom: 2px solid #EDEEF4;
            padding-bottom: .6rem;
        }

        .youtube{
           font-family: 'Roboto', sans-serif;
           padding-left: 1rem;
        }
        .github-col{
            position: relative;
            padding-left: 2rem;
            font-size: 12px;
            bottom: .6rem;
            
        }
        .github-col i {
            font-size: 20px;
            position: relative;
            top: .1rem;
        }

        .youtube i {
            font-size: 30px;
            position: relative;
            top: .4rem;
            color: red;
        }

        .hide-aside {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main main'
                'main main main main main';
            gap: 2rem;
            overflow-y: auto;
        }

        a {
            color: inherit;
        }

        a:hover {
            color: inherit;
            text-decoration: none;
        }

        main::-webkit-scrollbar,
        aside::-webkit-scrollbar {
            width: .3rem;
        }

        main::-webkit-scrollbar-track,
        aside::-webkit-scrollbar-track {
            -webkit-box-shadow: inset 0 0 1px rgba(0, 0, 0, 0.3);
        }

        main::-webkit-scrollbar-thumb,
        aside::-webkit-scrollbar-thumb {
            background-color: darkgrey;
            outline: 1px solid slategrey;
        }
    }

    @media screen and (min-width: 573px) {
        
        main {
            padding-top: 6rem;
        }

        .youtube-col{
            border-bottom: 2px solid #EDEEF4;
            padding-bottom: .6rem;
        }

        .youtube{
           font-family: 'Roboto', sans-serif;
           padding-left: 1rem;
           position: relative;
           left: 1rem;
        }

        .github{
            position: relative;
            padding-left: 1rem;
            font-size: 1px;
            bottom: 1rem;
            top: .5rem;
        }
        .github i {
            font-size: 30px;
            position: relative;
            top: .1rem;
        }

        .youtube i {
            font-size: 30px;
            position: relative;
            top: .4rem;
            color: red;
        }

        .hide-aside {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main main'
                'main main main main main';
            gap: 2rem;
            overflow-y: auto;
        }

        a {
            color: inherit;
        }

        a:hover {
            color: inherit;
            text-decoration: none;
        }

        main::-webkit-scrollbar,
        aside::-webkit-scrollbar {
            width: .3rem;
        }

        main::-webkit-scrollbar-track,
        aside::-webkit-scrollbar-track {
            -webkit-box-shadow: inset 0 0 1px rgba(0, 0, 0, 0.3);
        }

        main::-webkit-scrollbar-thumb,
        aside::-webkit-scrollbar-thumb {
            background-color: darkgrey;
            outline: 1px solid slategrey;
        }
    }

    @media screen and (min-width: 768px) {
        
        main {
            padding-top: 4rem;
        }

        .youtube-col{
            border-bottom: none;
        }

        .youtube-col a{
           font-family: 'Roboto', sans-serif;
           padding-left: 0;
           position: relative;
           top: .5rem;
        }

        .youtube-text{
           display: none;
        }
        .browser-text{
            display: inline;
            position: relative;
            left: 1rem;
            font-family: 'Roboto', sans-serif;
        }

        .github-col a{
            position: relative;
            padding-left: 0;
            right: 2rem;
            top: 1.5rem;
        }
        .github-text{
            font-size: 18px;
        }
        .github-col i {
            font-size: 30px;
            position: relative;
            top: .3rem;
        }

        .youtube i {
            font-size: 30px;
            position: relative;
            top: .3rem;
            color: red;
        }

        .hide-aside {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main main'
                'main main main main main';
            gap: 2rem;
            overflow-y: auto;
        }

    }

    @media screen and (min-width: 1024px) {
        .wrapper {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main aside'
                'main main main main aside';
            gap: 2rem;
            grid-template-columns: auto auto auto auto 25vw;
            grid-template-rows: 3rem auto 3rem;
            overflow-x: hidden;
            height: 100vh;
            width: 100vw;
            background-color: #EDEEF4;
            overflow-y: hidden;
        }

        main {
            padding-top: 1rem;
            padding-left: 10rem;
        }

        aside{
            padding-top: 1rem;
        }

    }

    @media screen and (min-width: 1440px) {
        
        .youtube-col{
            border-bottom: none;
            position: relative;
            top: .3rem;
        }

        .youtube-col a{
           font-family: 'Roboto', sans-serif;
           padding-left: 0;
           position: relative;
           top: .5rem;
        }

        .youtube-text{
           display: inline;
           position: relative;
           left: 1rem;        
        }
        .browser-text{
            display: inline;
            position: relative;
            left: 1rem;
            font-family: 'Roboto', sans-serif;
        }

        .github-col a{
            position: relative;
            padding-left: 0;
            top: 1.3rem;
        }
        .github-text{
            font-size: 18px;
        }
        .github-col i {
            font-size: 30px;
            position: relative;
            top: .3rem;
        }

        .youtube i {
            font-size: 30px;
            position: relative;
            top: .3rem;
            color: red;
        }

        .hide-aside {
            display: grid;
            grid-template-areas:
                'header header header header header'
                'main main main main main'
                'main main main main main';
            gap: 2rem;
            overflow-y: auto;
        }

        .hide-aside main {
            padding-right: 10rem;
            padding-bottom: 2rem;
        } 
    }

    

</style>
