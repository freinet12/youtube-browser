<template>
    <section>
        <p>Top 20 Most Popular Videos</p>
        <div class="row popular-videos" v-if="popularVideos !== null">
            <div 
                class="col-sm-12 col-md-4 col-lg-3"
                v-for="video in popularVideos"
                :key="video.etag"
            >
                <b-card
                    @click="onVideoSelect(video)"
                    :title="video.snippet.title"
                    :img-src="video.snippet.thumbnails.medium.url"
                    :img-alt="video.snippet.title"
                    img-top
                    tag="article"
                    class="mb-4"
                >
                    <b-card-text>
                        {{video.snippet.channelTitle}}
                    </b-card-text>
                </b-card>   
            </div>
        </div>
    </section>
</template>

<script>
export default {
    name: "PopularVideos",
    props: {
        popularVideos: Array || null
    },

    methods: {
        onVideoSelect(video){
           this.$emit('videoSelect', video);
        }
    }

    
}
</script>

<style scoped>
    .card{
        min-height: 22rem;
        border: none;
        box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
        background-color: #fff;
        border-radius: 5px;
    }

    .card-title{
        font-size: 14px;
    }

    .card:hover{
        cursor: pointer;
        box-shadow: 0 16px 32px 0 rgba(0, 0, 0, 0.2);
    }

    
    
</style>