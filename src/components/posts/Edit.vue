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
                            EDIT CATATAN
                        </div>
                        <div class="card-body">
                            <form @submit.prevent="PostUpdate">
                                
                                <div class="form-group">
                                    <label>TITLE</label>
                                    <input type="text" class="form-control" v-model="post.title" v-model.trim="$v.title.$model" :class="{'is-invalid':$v.title.$error}" placeholder="Masukkan Title">  
                                    <div class="invalid-feedback">
                                        <span v-if="!$v.title.required">Title is required</span>
                                       <span v-if="!$v.title.minLength">Title must have at least {{$v.title.$params.minLength.min}} letters</span>
                                       <span v-if="!$v.title.maxLength">Title must have at most {{$v.title.$params.maxLength.max}} letters</span>   
                                    </div>
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
                                    
                                </div>
                                <div class="form-group">
                                    <button type="submit" class="btn btn-md btn-success mr-2">UPDATE</button>
                                    <button type="reset" class="btn btn-md btn-danger">RESET</button>
                                    <router-link class="btn btn-warning" :to="{name:'posts'}" >Kembali</router-link>
                                </div>

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
                this.getPostById()
        },
        data() {
            return {
                post: {
                    title:'',
                    content:''
                },
                title:'',
                content:'',
                errors:{}
               
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
            getPostById(){
                let id= this.$route.params.id
                let token = localStorage.getItem('token');
                axios.get('http://localhost:8000/post/'+id+'?api_token='+token)
                // .then(({data})=>(this.post=data))
                .then(response => {
                    this.title=response.data.post.title,
                    this.content=response.data.post.content,
                    this.post.title=response.data.post.title,
                    this.post.content=response.data.post.content
                //untuk debug melalui console log.
                //  console.log(response.data.post.title)
                 
            })
                .catch(console.log('error'))
            },

            PostUpdate() {
                 let token =localStorage.getItem('token')
                axios.put(`http://localhost:8000/post/${this.$route.params.id}?api_token=`+token, this.post)
                    .then(response => {
                        this.post=response.data.data;
                        // this.title=response.data.data.title,
                        // this.content=response.data.data.content,
                        this.$router.push({
                            name: 'posts'
                        });
                        window.alert("Data Berhasil diedit");
                    }).catch(error => {
                        console.log(error.response.status)
                        console.log(error.response.data)
                    this.errors = error.response.data.message;
                    alert('Data tidak dapat disimpan');
                    this.$router.push('/');
                });
            }
        }
    }
</script>