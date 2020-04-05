<template>
    <section>
        <p><strong>Top 20 Most Popular Videos</strong></p>
        <div class="row popular-videos" v-if="popularVideos !== null">
            <div 
                class="col-sm-12 col-md-4 col-lg-3"
                v-for="(video, i) in popularVideos"
                :key="i + '_' + video.etag"
                
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
                        <div class="row views">
                            <div class="col-sm-12">
                                {{new Number(video.statistics.viewCount).toLocaleString("en")}} Views
                            </div>
                        </div>
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
            //turn video.id to an object with a videoId prop
            let videoId = video.id;
            video.id = {
                videoId: videoId
            };
           this.$emit('videoSelect', video);
        }
    }
   
}
</script>

<style scoped>
    @media screen and (min-width: 320px){
        .card{
            min-height: auto;
            border: none;
            box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
            background-color: #fff;
            border-radius: 5px;
        
        }

        .card-title{
            font-size: 14px;
            font-weight: bold;
        }

        .card:hover{
            cursor: pointer;
            box-shadow: 0 16px 32px 0 rgba(0, 0, 0, 0.2);
        }

        .views{
            font-size: 13px;
        }
        p{
            font-size: 13px;
        }
    }

    @media screen and (min-width: 800px) {
        .card{
            min-height: 20rem;
            border: none;
            box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.05);
            background-color: #fff;
            border-radius: 5px;
        
        }
    }
    
    
    
</style>