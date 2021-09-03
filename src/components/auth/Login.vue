<template>
<div class="mt-5">
	<div class="row justify-content-center">
		
			<h2 class="text-center">Login</h2>
			
		<div class="col-md-4">
    <form @submit.prevent="logItIn"> 
		<div class="alert alert-danger" v-if="errors.email">
				{{errors.email[0]}} 
			</div>
			<div class="alert alert-danger" v-if="errors.password">
				{{errors.password[0]}} 
			</div>

		<div class="form-group">
			<div class="mb-3">
				<label for="exampleInputEmail1" class="form-label">Email</label>
				<input type="text" class="form-control" id="exampleInputEmail1"  placeholder="Email" aria-describedby="emailHelp" v-model="login.email" v-model.trim="$v.email.$model" :class="{'is-invalid':$v.email.$error,'is-valid':!$v.email.$invalid}" >
				<div class="valid-feedback">Your Email is valid</div>
				<div class="invalid-feedback">
					<span v-if="!$v.email.required">Email is required</span>
					<span v-if="!$v.email.email">Email is required</span>
					<span v-if="!$v.email.isUnique">This email is already registered</span>
				</div>
			</div>
		</div>

		<div class="form-group">
			<div class="mb-3">
				<label for="exampleInputEmail1" class="form-label">Password</label>
				<input type="password" class="form-control" id="password"  placeholder="password" aria-describedby="emailHelp" v-model="login.password" v-model.trim="$v.password.$model" :class="{'is-invalid':$v.password.$error,'is-valid':!$v.password.$invalid}" >
				<div class="valid-feedback">Your password is valid</div>
				<div class="invalid-feedback">
					<span v-if="!$v.password.required">Password is required</span>
					<span v-if="!$v.password.minLength">Password must have at least {{$v.password.$params.minLength.min}} letters</span>
					
				</div>
			</div>
		</div>

		<div  class="form-group">
			<input type="checkbox" class="form-check-input" @click="toggleShowPassword" v-model="showpassword" id="showpassword">
			<label for="showpassword" class="form-check-label">Show password</label>
		</div>
		
		<button type="submit" class="w-100 btn btn-lg btn-primary">Login</button>
		<small class="d-block text-center"><router-link to="/register" class="font-weight-bold small" >Not Regitered ?</router-link></small>
</form>
</div>
</div>
</div>
</template>


<script>
	import axios from 'axios'
	import{required, minLength, email} from 'vuelidate/lib/validators'
	export default {

		mounted() {
             let token = localStorage.getItem('token');
                if(token){
                  this.$router.push('/')
                }

        },
		data() {
			return {
				login:{
					email:"",
					password:"",
					
				},
				email:'',
				password:'',
				showpassword:false,
				errors:{
					email:'',
					password:'',
				},
				submitStatus:''

				
			}
		},
		validations:{
			
			email:{
				required,
				email,
			isUnique (value){
				if(value==='') return true

				var email_regex = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
				return new Promise((resolve)=>{
					setTimeout(()=>{
					resolve(email_regex.test(value))
					}, 350 + Math.random()*300 )
				})
			}

			},
			password:{
				required,
				minLength:minLength(4)
			}
			
		},

		methods:{
			toggleShowPassword(){
				var show =document.getElementById('password')
				if(this.showpassword==false){
					this.showpassword=true
					show.type ='text'
				}else{
					this.showpassword=false
					show.type= "password"
				}
			},
			
			logItIn(){		
				
				axios.post('http://localhost:8000/login',this.login)
				.then(response => {
					let token=response.data.user.api_token;
					localStorage.setItem('token',token);			
					this.$router.push('/');
				})
				
				.catch((error) => {
					if(error.response.status==422){
						console.log(error.response.data)
						// this.email = error.response.data.email;
						// this.password = error.response.data.password;
						this.errors.email=error.response.data.email;
						this.errors.password=error.response.data.password;
					}
					if(error.response.status==404 || error.response.status==401){
                    alert('Email/Password tidak valid!');
					}
                    
                });
				
					
				
			},
			
		}
	}
</script>