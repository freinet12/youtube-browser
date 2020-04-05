<template>
    <ul>
        <VideoListItem 
            v-for="(video, i) in videos"
            :key="i + '_' +video.etag"
            :video="video"
            @videoSelect="onVideoSelect"
            :class="{'active-video' : video.id.videoId === activeVideo}"
        />
    </ul>
</template>

<script>
import VideoListItem from "@/components/VideoListItem";
export default {
    name: 'VideoList',
    components: {
        VideoListItem
    },
    props: {
        videos: Array
    },

    data(){
        return {
            activeVideo: null
        }
    },

    methods: {
        onVideoSelect(video){
            this.$emit('videoSelect', video);
            this.setActiveVideo(video);
        },
        setActiveVideo(video){
            this.activeVideo = video.id.videoId;
        }

    }
}
</script>

<style scoped>
    ul{
        list-style-type: none;
        padding-left: 0;
        padding-right: 1rem;
        overflow-x: auto;
    }

    .active-video{
        border-right: 3px solid red;
    }
</style>