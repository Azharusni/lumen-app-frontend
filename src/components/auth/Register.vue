<template>

<div class="mt-5">
	<div class="row justify-content-center">
		<div class="col-md-4">
			<h2 class="text-center">Register</h2>
    <form @submit.prevent="registerIt"> 
			<div class="alert alert-danger" v-if="errors.email">
				{{errors.email[0]}} 
			</div>
			<div class="alert alert-danger" v-if="errors.password">
				{{errors.password[0]}} 
			</div>

		<div class="form-group">
			<div class="mb-3">
				<label for="exampleInputEmail1" class="form-label">Username</label>
				<input type="text" class="form-control" id="exampleInputEmail1"  placeholder="username" aria-describedby="emailHelp" v-model="register.username" v-model.trim="$v.username.$model" :class="{'is-invalid':$v.username.$error,'is-valid':!$v.username.$invalid}" >
				<div class="valid-feedback">Your Username is valid</div>
				<div class="invalid-feedback">
					<span v-if="!$v.username.required">Username is required</span>
					<span v-if="!$v.username.minLength">Username must have at least {{$v.username.$params.minLength.min}} letters</span>
					
				</div>
			</div>
		</div>

		<div class="form-group">
			<div class="mb-3">
				<label for="exampleInputEmail1" class="form-label">Email</label>
				<input type="text" class="form-control" id="exampleInputEmail1"  placeholder="Email" aria-describedby="emailHelp" v-model="register.email" v-model.trim="$v.email.$model" :class="{'is-invalid':$v.email.$error,'is-valid':!$v.email.$invalid}" >
				<div class="valid-feedback">Your Email is valid</div>
				<div class="invalid-feedback">
					<span v-if="!$v.email.email">Email is required</span>
					<span v-if="!$v.email.isUnique">This email is already registered</span>
					
				</div>
			</div>
		</div>

		<div class="form-group">
			<div class="mb-3">
				<label for="exampleInputEmail1" class="form-label">Password</label>
				<input type="password" class="form-control" id="password"  placeholder="password" aria-describedby="emailHelp" v-model="register.password" v-model.trim="$v.password.$model" :class="{'is-invalid':$v.password.$error,'is-valid':!$v.password.$invalid}" >
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
		
		<button type="submit" class="w-100 btn btn-lg btn-primary">Register</button>
		<small class="d-block text-center"><router-link to="/login" class="font-weight-bold small" >Already have an account?</router-link></small>
</form>
</div>
</div>
</div>
</template>


<script>
import axios from 'axios'
import{required, minLength, email,} from 'vuelidate/lib/validators'
	export default {
		// mounted() {
        //      let token = localStorage.getItem('token');
        //         if(token){
        //           this.$router.push('/')
        //         }

            
        // },
		data() {
			return {
				register:{
					username:'',
					email:'',
					password:'',
					confirm_password:''
				},
				username:'',
				email:'',
				password:'',
				showpassword:false,
				errors:{
					email:'',
					password:'',
				},
				submitStatus:false
			}
		},
		validations:{
			username:{
				required,
				minLength:minLength(4),
				
			},
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
				minLength:minLength(5)
			}
			
		},


		methods:{
			registerIt() {
				axios.post('http://localhost:8000/register',this.register)
				.then(response => {
					let token=response.data.user.api_token;
					localStorage.setItem('token',token);
					this.$router.push({name:'login'})
				})
				// .catch(error => {
                //     this.errors = error.response.data.errors;
                //     alert('Register failed!Please check your forms!');
                    
                // });

				.catch((error) => {
					if(error.response.status==422){
						// console.log(error.response.data)
						this.errors.email=error.response.data.email;
						this.errors.password=error.response.data.password;
					}
					if(error.response.status==404 || error.response.status==401){
                    alert('Register failed!Please check your forms!');
					}
                    
                });
			},

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
			
			
			
		}
	}
</script>