<template>
    <ul>
        <VideoListItem 
            v-for="video in videos"
            :key="video.etag"
            :video="video"
            @videoSelect="onVideoSelect"
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

    methods: {
        onVideoSelect(video){
            this.$emit('videoSelect', video);
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
</style>