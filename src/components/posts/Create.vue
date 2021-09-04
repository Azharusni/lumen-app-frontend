<template>
    <div class="posts">
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <div class="container-fluid">
            
            <div class="collapse navbar-collapse" id="navbarCollapse">
                <ul class="navbar-nav me-auto mb-2 mb-md-0">
                <li class="nav-item">
                     <router-link :to="{name:'posts'}" class="nav-link active" aria-current="page" href="#">Home</router-link>
                </li>
                
                </ul>
                <form class="d-flex">
                <div>
                            <router-link  :class="['btn btn-outline-warning right']" to="/logout"><i class="bi bi-box-arrow-right">Logout</i></router-link>
                </div>
                </form>
            </div>
            </div>
         </nav>
        <div class="container mt-5">
            <div class="row justify-content-center">
                <div class="col-md-12">
                    <div class="card">
                        <div class="card-header">
                            TAMBAH POST
                        </div>
                        <div class="card-body">
                            <form @submit.prevent="PostStore">
                                
                                <div class="form-group">
                                    <label>TITLE</label>
                                    <input type="text" class="form-control" v-model="post.title" v-model.trim="$v.title.$model" :class="{'is-invalid':$v.title.$error}" placeholder="Masukkan Title">  
                                    <div class="invalid-feedback">
                                        <span v-if="!$v.title.required">Title is required</span>
                                       <span v-if="!$v.title.minLength">Title must have at least {{$v.title.$params.minLength.min}} letters</span>
                                       <span v-if="!$v.title.maxLength">Title must have at most {{$v.title.$params.maxLength.max}} letters</span>
                                    </div>
                                     <span style="color:red" class="alert" v-if="errors.title">{{errors.title[0]}} </span>
                                   
                                   
                                </div>

                                <div class="form-group">
                                    <label>KONTEN</label>
                                    <textarea class="form-control" v-model="post.content" rows="5"
                                              placeholder="Masukkan Konten" v-model.trim="$v.content.$model" :class="{'is-invalid':$v.content.$error}"></textarea>
                                               <div class="invalid-feedback">
                                                    <span v-if="!$v.content.required">Content is required</span>
                                                <span v-if="!$v.content.minLength">Content must have at least {{$v.content.$params.minLength.min}} letters</span>
                                                <span v-if="!$v.content.maxLength">Content must have at most {{$v.content.$params.maxLength.max}} letters</span>
                                        
                                        
                                    </div>
                                    <span style="color:red" class="alert" v-if="errors.title">{{errors.title[0]}} </span>
                                    
                                </div>
                                <router-link class="btn btn-warning" :to="{name:'posts'}" >Kembali</router-link>
                                <button type="submit" class="btn btn-md btn-success mr-2">SIMPAN</button>
                                <button type="reset" class="btn btn-md btn-danger">RESET</button>

                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>


<script>
    import axios from 'axios'
	import{required, minLength, maxLength} from 'vuelidate/lib/validators'
    

    export default {
        mounted() {
             let token = localStorage.getItem('token');
                if(!token){
                  this.$router.push('/login')
                }
            
        },
        
        data() {
            return {
                post: {
                    title:null,
                    content:null
                },
               
                title:'',
                content:'',
                errors:{
                    title:'',
                    content:''
                }
                
            }
        },
        validations:{
			
			title:{
				required,
				minLength:minLength(8),
                maxLength:maxLength(50)

			},
			content:{
				required,
				minLength:minLength(10),
                maxLength:maxLength(1000)
			}
			
		},
        methods: {

            
            PostStore() {
                let token =localStorage.getItem('token')
                axios.post('http://localhost:8000/post?api_token='+token, this.post)
                    .then((response) => {
                        this.$router.push({
                            name: 'posts'
                        });
                        this.post=response.data.data;
                        window.alert("Data Berhasil ditambahkan");
                    })

                    // .catch(error => {
                    // console.log('Error');
                    // error
                    // });
                    .catch((error) => {
					if(error.response.status==422){
						console.log(error.response.data)
						// this.email = error.response.data.email;
						// this.password = error.response.data.password;
						this.errors.title=error.response.data.title;
						this.errors.content=error.response.data.content;
					}
					
                    
                });
            }
        }
    }
</script>