<template>
    
    <div class="row my-5">
        <div class="col-md-6 offset-md-3">
            <div class="form-group">
                <div class="card">
                    <div class="card-body">
                        <h3 class="text-center my-4">Sign Up</h3>
                         <div class="form-group">
                            <input v-bind:class="{'is-invalid':errors.name, 'is-valid': !errors.name && this.submitted}"  v-model="name" type="text" placeholder="Name" class="form-control">
                            <div class="errors" v-if="errors.name">
                                <small class="text-danger" v-for="error in errors.name" :key="error"> {{ error }} </small>
                            </div>
                        </div>
                        <div class="form-group">
                            <input v-bind:class="{'is-invalid':errors.email, 'is-valid': !errors.email && this.submitted}" type="email" v-model="email" placeholder="Email" class="form-control">
                            <div class="errors" v-if="errors.email">
                                <small class="text-danger" v-for="error in errors.email" :key="error"> {{ error }} </small>
                            </div>
                        </div>

                          <div class="form-group">
                            <input type="password" v-bind:class="{'is-invalid':errors.password, 'is-valid': !errors.password && this.submitted}" v-model="password" placeholder="Password" class="form-control">
                            <div class="errors" v-if="errors.password">
                                <small class="text-danger" v-for="error in errors.password" :key="error"> {{ error }} </small>
                            </div>
                          </div>
                        
                        <div class="form-group text-center">
                            <button @click="registerUser()" :disabled="loading" class="btn btn-success form-control">
                                <i class="fas fa-spin fa-spinner" v-if="loading"></i>
                                {{ loading ? '' : 'Sign Up'}}
                            </button>
                        </div> 
                    </div>
                </div>
            </div>
        </div>
    </div>


</template>

<script>
import Axios from 'axios'
import config from '@/config.js'
export default {

        beforeRouteEnter(to, from , next) {

        if (localStorage.getItem('auth')) {

            return next({ path: "/home"})

        }

        next();

    },

    data() {

        return{

            name: '',
            email: '',
            password: '',
            errors: {},
            submitted: false,
            loading: false
            
        }

       
    },

    methods: {

        registerUser() {
            this.loading = true
           Axios.post(`${config.apiUrl}/auth/register`, {

               
               name: this.name,
               email: this.email,
               password: this.password
           }).then(response => {
                    this.loading = false
                    this.submitted = true;
                    const { data } = response.data;
                    localStorage.setItem('auth', JSON.stringify(data))
                    this.$root.auth = data;
                    this.$noty.success("Account created succesfully.")

                    this.$router.push('/home');
                }).catch(({ response }) => {
                    this.loading = false
                    this.submitted = true;
                    this.errors = response.data;
                });
        }

    }
}
</script>

