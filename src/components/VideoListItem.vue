<template>
    <li @click="onVideoSelect">
        <b-media class="media">
            <template v-slot:aside>
                <b-img 
                    :src="thumbnailUrl" 
                    alt="Thumbnail"
                    class="thumbnail"
                />
            </template>
            <p class="title" v-html="video.snippet.title">
            </p>
        </b-media>
    </li>
</template>

<script>
export default {
    name: 'VideoListItem',
    props: {
        video: Object
    },

    data(){
        return {

        }
    },

    computed: {
        thumbnailUrl(){
            return this.video.snippet.thumbnails.default.url
        }
    },

    methods: {
        onVideoSelect(){
           this.$emit('videoSelect', this.video);
        }
    }
}
</script>

<style scoped>
    .title{
        font-size: 14px;
    }

    li{
        margin-bottom: 1.5rem;
        background-color: #fff;
        box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
        border-radius: 5px;
        position: relative;
        padding-right: .5rem;
        
    }

    .thumbnail{
        border-top-left-radius: 5px;
        border-bottom-left-radius: 5px;
        height: auto;
    }

    li:hover{
        cursor: pointer;
        background-color:#F6F9FC;
    }

    .subtitle{
        font-size: 12px;
    }
</style>