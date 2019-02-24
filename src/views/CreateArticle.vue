<template>

    <div class="row">
        <div class="col-md-8 offset-md-2">
            <div class="card my-5">
                <div class="card-body">

                    <picture-input 
                        accept="image/jpeg,image/png" 
                        size="5" 
                        button-class="btn btn-danger"
                        @change="onChange">
                    </picture-input>

                    <div class="input-group my-3">
                        <div class="input-group-prepend">
                            <label class="input-group-text" for="inputGroupSelect01">Categories</label>
                        </div>
                        <select class="form-control custom-select" v-model="category">
                            <option selected>Choose...</option>
                        <option :value="category.id" :key="category.id" v-for="category in categories">
                            {{ category.name }}
                        </option>

                    </select>
                    </div>

                    <input type="text" v-model="title" placeholder="title" class="form-control my-3">
                    <wysiwyg  v-model="content" />
                
                <div class="text-center">

                    <button @click="UploadImagetoCloudinaryAndSaveData()" class="btn btn-success mt-3">
                         <i class="fas fa-spin fa-spinner" v-if="loading"></i>
                                {{ loading ? '' : 'Create'}}
                    </button>

                </div>
                </div>

            </div>
        </div>
    </div>
    
</template>

<script>
import PictureInput from 'vue-picture-input'
import Axios from 'axios'
import config from '@/config.js'
export default {

    beforeRouteEnter(to, from , next) {

        if (!localStorage.getItem('auth')) {

            return next({ path: "/login"})

        }
        next();

    },
  
  components: {
    PictureInput
  },

    data() {

        return {

            title: '',
            content: '',
            image: null,
            categories: {},
            category: '',
            loading: false
        }
    },

    mounted() {

        this.getCategories();
      
    },

    methods: {

        onChange(image) {
            this.image = image;
        },
        UploadImagetoCloudinaryAndSaveData() {

            if(!this.title || !this.category || !this.content || !this.image){
                
               this.$noty.info("Please fill in all the blanks.");
               return

            }

            this.loading = true
            const form = new FormData();

            form.append('file', this.image);
            form.append('upload_preset', process.env.VUE_APP_CLOUDINARY_PRESET);
            form.append('api-key', process.env.VUE_APP_CLOUDINARY_API_KEY);

            Axios.post(process.env.VUE_APP_CLOUDINARY_URL, form)
            .then(response => {

                Axios.post(`${config.apiUrl}/articles`, {

                    title : this.title,
                    imageUrl : response.data.secure_url,
                    content: this.content,
                    category_id: this.category

                }, {
                    headers: {
                            Authorization: `Bearer ${this.$root.auth.token}`
                    }
                }).then(response => {
                    this.loading = false
                    this.$noty.success("Article Created Successfully.");
                    localStorage.setItem('currentUrl', 1);
                    this.$router.push("/home")
                }).catch(() => {
                    this.loading = false
                    this.$noty.error("Opps Something went wrong.") 
                });

            }).catch(() => {
                this.loading = false
                    this.$noty.error("Opss Something went wrong.") 
                });
        },
        getCategories() {

            const categories = localStorage.getItem('categories');
                if(categories){
                    this.categories = JSON.parse(categories);
                    return
                }

            Axios.get(`${config.apiUrl}/categories`).then(response => {
                this.categories = response.data.categories;
                localStorage.setItem("categories", JSON.stringify(this.categories))
            });
        },
       
    }

}
</script>

<style>

</style>
