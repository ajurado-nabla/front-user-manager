<script setup lang="ts">
    import { ref} from 'vue';
    import Crud from './Crud.vue';
    

    const username = ref('');
    const password = ref('');
    const auth = ref<{ isLogged: boolean }>({ isLogged: false });
   

    localStorage.setItem("login", JSON.stringify({ isLogged: false }));
    const loginData = localStorage.getItem("login");
   

    if (loginData) {
    auth.value = JSON.parse(loginData);
    }
    

    
    
    const login = async () => {
        
            const response = await fetch("https://user-manager-na3j.onrender.com/login", {
            method: "POST",
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            
            body: JSON.stringify({
                username: username.value,
                password: password.value
            })
        });
        if(!response.ok){
            console.log("Invalid credentials")
            alert("Invalid credentials, Try again!")
        }else{
           
            auth.value.isLogged = true
            localStorage.setItem("login", JSON.stringify(auth.value))
            localStorage.setItem("currentUsername", username.value);
            
        }
    }  

    

        
        
</script>

<template>
    <h1>Guardian User Manager</h1>
  <div class="login" v-if="!auth.isLogged">
    <h3>Log in</h3>

    <input type="text" name="username" id="username" placeholder="username" v-model="username">
    <input type="password" name="password" id="password" placeholder="password" v-model="password">
    <button @click="login">SEND</button>
    
  </div>
  <div class="alreadylogged" v-else>
    <Crud/>
    </div>
</template>

<style scoped>

.login {
  max-width: 40vw;
  margin: 80px auto;
  padding: 30px;
  background-color: #f7f9fc;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  text-align: center;
  font-family: 'Segoe UI', sans-serif;
}

.login h3 {
  margin-bottom: 20px;
  color: #333;
  font-size: 24px;
}

.login input {
  display: block;
  width: 100%;
  padding: 10px 12px;
  margin-bottom: 15px;
  border: 1px solid #bbb;
  border-radius: 4px;
  font-size: 16px;
  box-sizing: border-box;
  transition: border-color 0.2s ease;
}

.login input:focus {
  border-color: #007bff;
  outline: none;
}

.login button {
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  border: none;
  color: white;
  font-weight: bold;
  font-size: 16px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.login button:hover {
  background-color: #0056b3;
}


</style>