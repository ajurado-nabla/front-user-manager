<script setup lang="ts">
import { ref } from 'vue'
import { computed } from 'vue'

    interface User {
        id: number,
        active: boolean,
        username: string,
        password: string,
        firstname: string,
        lastname: string,
        company: string,
        windfarms: [],
    }
    const users = ref<User[]>([]);
    const editedUsers = ref<Record<number, Partial<User>>>({});
    const visibleForms = ref<Record<number, boolean>>({});
    const activeUsers = computed(() =>
        users.value.filter(user => (user.active === true ) && (user.username !== currentUsername)
        
    ));
    const currentUsername = localStorage.getItem("currentUsername");

    

    const showAllUsers = async () => {
        const response = await fetch("https://user-manager-na3j.onrender.com/getall");

        if (response.ok) {
            const data = await response.json();
            users.value = data;

            
            editedUsers.value = {};
            data.forEach((user: User) => {
            editedUsers.value[user.id] = { ...user };
            
            });
        } else {
            console.log("Error al cargar usuarios");
        }
    };

    const editUsers = async (id : number) => {

            const updatedUser = editedUsers.value[id];
        
            const response = await fetch(`https://user-manager-na3j.onrender.com/${id}`, {
            method: "PUT",
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(updatedUser)
            

        });
            if(!response.ok){
                console.log("Invalid credentials")
                
            }else{

             users.value = await response.json()
             
            }
    }  

    const deleteUsers = async (id : number) => {

        const response = await fetch(`https://user-manager-na3j.onrender.com/${id}`, {
            method: "DELETE",
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(deleteUsers)
        });

        if(!response.ok){
                console.log(`error at deleting user number ${id}`)
                
            }else{

             users.value = await response.json()
             await showAllUsers();
            }
    }

    function toggleForm(userId: number) {
    visibleForms.value[userId] = !visibleForms.value[userId];
    }

    showAllUsers()

</script>
<template>
    

    <div class="allusers">
  <ul>
    <li class="eachuser" v-for="user in activeUsers" :key="user.id">
      <div class="user-header">
        <h3>{{ user.username }}</h3>
        <div class="user-actions">
          <button class="update-button" @click="toggleForm(user.id)">Update</button>
          <button class="delete-button" @click="deleteUsers(user.id), showAllUsers()">Delete</button>
        </div>
      </div>

      <form v-if="visibleForms[user.id]" @submit.prevent="editUsers(user.id)">
        <div class="form-group">
          <label>Username:</label>
          <input type="text" v-model="editedUsers[user.id].username" :placeholder="user.username" />
        </div>
        <div class="form-group">
          <label>Password:</label>
          <input type="password" v-model="editedUsers[user.id].password" placeholder="****" />
        </div>
        <div class="form-group">
          <label>Active:</label>
          <input type="text" v-model="editedUsers[user.id].active" />
        </div>
        <div class="form-group">
          <label>Company:</label>
          <input type="text" v-model="editedUsers[user.id].company" :placeholder="user.company" />
        </div>
        <div class="form-group">
          <label>Firstname:</label>
          <input type="text" v-model="editedUsers[user.id].firstname" :placeholder="user.firstname" />
        </div>
        <div class="form-group">
          <label>Lastname:</label>
          <input type="text" v-model="editedUsers[user.id].lastname" :placeholder="user.lastname" />
        </div>

        <button type="submit" class="send-button">Send</button>
      </form>
    </li>
  </ul>
</div>

</template>
<style scoped>

.allusers {
  width: 40vw;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', sans-serif;
}

ul {
  list-style-type: none;
  padding: 0;
}

.eachuser {
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  margin-bottom: 20px;
  padding: 15px;
  border-radius: 8px;
}

.user-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.user-actions button {
  margin-left: 10px;
  padding: 6px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.update-button {
  background-color: #007bff;
  color: white;
}

.delete-button {
  background-color: #dc3545;
  color: white;
}

form {
  margin-top: 15px;
  padding-top: 15px;
  border-top: 1px solid #ccc;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 4px;
}

.form-group input {
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.send-button {
  background-color: #28a745;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  width: 20vw;
}


</style> 