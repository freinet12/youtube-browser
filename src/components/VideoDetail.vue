<template>
    <section>
        <div class="detail" v-if="video !== null">
            <b-embed
                type="iframe"
                aspect="21by9"
                :src="videoUrl"
                allowfullscreen
                autoplay='1'
            />
            <div class="details">
                <h6 v-html="video.snippet.title"></h6>
                <p>
                    {{video.snippet.channelTitle}}
                </p>
            </div>
        </div>
    </section>
    
</template>

<script>
export default {
    name: 'VideoDetail',
    props: {
        video: Object || null
    },

    computed: {
        videoUrl(){
            const { videoId } = this.video.id;
            return `https://www.youtube.com/embed/${videoId}?autoplay=1`;
        }
    }

}
</script>

<style scoped>
    .details{
        padding: 2rem 2rem 0;

    }
</style>