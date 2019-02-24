<template>
<div> 
    <div class="d-flex justify-content-between mt-4">
        <button @click="getPrevPageArticles()" :disabled="articles.prev_page_url === null" class="btn btn-warning">Prev Page</button>
        <span>{{ articles.current_page }}</span>
        <button @click="getNextPageArticles()" :disabled="articles.next_page_url === null" class="btn btn-warning">Next Page</button>
    </div>
     <div class="d-flex justify-content-between">
        <button @click="getFirstPageArticles()" :disabled="articles.prev_page_url === null" class="btn btn-sm mt-3 btn-outline-primary">First</button>

    <button @click="getLastPageArticles()" :disabled="articles.next_page_url === null" class="btn btn-sm mt-3 btn-outline-primary last-button">Last</button>
    </div>



    <div class="row" v-if="!loading">
    <div class="col-md-8 offset-md-2" v-for="article in articles.data" :key="article.id">
        <Article :article="article"></Article>
    </div>

</div>

<div class="loader text-center" v-else>
       <i class="fas fa-4x fa-spin fa-spinner"></i>
</div>
    
</div>

</template>

<script>
import Axios from 'axios';
import config from '@/config.js';
import Article from '@/components/Article.vue';

export default {

  
    components : {

        Article
    },

    data() {
        return {
            
            articles : {},
            loading: true,
            currentUrl: 1
        }
    },

    mounted() {
        
        this.getCurrentUrl();
        this.getArticles();
       
    },

    methods: {

        getArticles(url = `${config.apiUrl}/articles/?page=`+this.currentUrl) {
            this.loading= true

            Axios.get(url).then(response => {
                    this.loading= false
                this.articles = response.data.data;
                localStorage.setItem('currentUrl', JSON.stringify(this.articles.current_page))
            });
        },
        getNextPageArticles() {
            
            this.getArticles(this.articles.next_page_url);
            
        },
        getPrevPageArticles() {
            this.getArticles(this.articles.prev_page_url);


        },
        getFirstPageArticles() {
            this.getArticles(`${config.apiUrl}/articles/?page=1`)
        },
        getLastPageArticles() {
            this.getArticles(`${config.apiUrl}/articles/?page=`+this.articles.last_page)
        },
        getCurrentUrl() {

            this.currentUrl = localStorage.getItem('currentUrl');
            this.currentUrl=(JSON.parse(this.currentUrl));
            
        }
    }

}
</script>



