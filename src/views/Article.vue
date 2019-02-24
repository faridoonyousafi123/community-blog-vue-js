<template>

<div class="container">
    <div class="row my-5">
        
        <div class="col-md-10 offset-md-1">
            <router-link to="/home" class="btn btn-warning mb-3">Back</router-link>
                <div class="card" v-if="!loading">
                    <img :src="article.imageUrl" v-if="!loading" alt="" height="420px" class="card-img-top">
                    <div class="card-body">
                        <h1 class="card-title text-center my-3">{{ article.title }} </h1>
                        <div class="article-content"  v-html="article.content"></div>

                        <div class="comments">
                                <vue-disqus shortname="Community-Blog" :identifier="article.slug" :url="url"></vue-disqus>
                        </div>
                    </div>


                </div>
             <div class="loader text-center" v-else>
                        <i class="fas fa-4x fa-spin fa-spinner"></i>
             </div>
        </div>
    </div>
</div>    
</template>

<script>
import Axios from 'axios'
import config from '@/config.js'



export default {
    

    data() {
        return {

            article: {},
            loading: true,
            url: window.location.href,
            
        }
    },
    mounted() {

        this.getArticle();
        
    },

    methods: {

        getArticle() {

            Axios.get(`${config.apiUrl}/article/${this.$route.params.slug}`).then(response => {
                this.loading = false
                this.article = response.data.data;
            });


        },

        
        
    }
}
</script>

<style>

</style>
