<template>
    <div>
<h2>You are lgoout</h2>
    </div>
</template>

<script>
// import axios from 'axios'
export default {
    mounted() {
             let token = localStorage.getItem('token');
                if(!token){
                  this.$router.push('/login')
                }
        },
    created() {
        let token = localStorage.getItem('token');
        if(!token){
            return false;
        }

        // axios.get("http://localhost:8000/logout?api_token="+token)
        
        localStorage.removeItem('token');
        this.$router.push({name:'login'})
        
    }
}
</script>