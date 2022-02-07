<template>
  <div v-if="loading" class="container">
    <ul v-if="getUsers.length" class="list">
      <li
          class="li"
          v-for="user in getUsers"
          :key="user"
      >
        <Card :user="user" @render="forseRender"/>
      </li>
    </ul>
    <h1 v-else>Пользователей нет</h1>
    <button class="update-button" @click="updateUsers">Обновить</button>
  </div>
  <div v-else class="loader"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Card from "@/components/Card.vue";
import axios from "axios";
import {User} from "@/types";


export default defineComponent({
  name: 'App',
  data: () => ({
    loading: false,
    key: 0
  }),
  components: {
    Card
  },
  methods: {
    forseRender() {
      this.key++
    },
    sleep(ms: number): Promise<number> {
      return new Promise(
          resolve => setTimeout(resolve, ms)
      );
    },
    async updateUsers() {
      this.loading = false
      await axios.get("https://reqres.in/api/users?page=2")
          .then(response => {
            let users: User[] = response.data.data
            users = this.validation(users)
            localStorage.setItem("users", JSON.stringify(users))
          })
          .catch(e => {
            throw e
          })
      await this.sleep(1000)
      this.forseRender()
      this.loading = true
    },
    validationName(name: string): boolean{
      let pattern = new RegExp("^[A-Za-z]+$", "i")
      return pattern.test(name)
    },
    validationEmail(email: string): boolean{
      let pattern = new RegExp("^(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))$", "i");
      return pattern.test(email)
    },
    validationLink(link: string): boolean{
      var pattern = new RegExp('^(https?:\\/\\/)?'+
          '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.?)+[a-z]{2,}|'+
          '((\\d{1,3}\\.){3}\\d{1,3}))'+
          '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*'+
          '(\\?[;&a-z\\d%_.~+=-]*)?'+
          '(\\#[-a-z\\d_]*)?$','i');
      return pattern.test(link)
    },
    validation(users: User[]): User[]{
      let newUsers: User[] = users.filter(user => {
        if (!this.validationName(user.first_name)) {
          console.warn("Неправильное имя", user)
          return false
        }
        if (!this.validationName(user.last_name)) {
          console.warn("Неправильная фамилия", user)
          return false
        }
        if (!this.validationEmail(user.email)) {
          console.warn("Неправильный email", user)
          return false
        }
        if (!this.validationLink(user.avatar)) {
          console.warn("Неправильный ссылка на аватар", user)
          return false
        }
        return true
      })
      return newUsers
    }
  },
  computed:{
    getUsers(): User[] {
      // eslint-disable-next-line no-unused-vars
      let l = this.key
      let data = localStorage.getItem("users")
      let users: User[] = data ? JSON.parse(data) : null
      return users
    }
  },
  async mounted() {
    let data = localStorage.getItem("users")
    let users: User[] = data ? JSON.parse(data) : null
    if (!users) {
      this.loading = false
      await axios.get("https://reqres.in/api/users?page=2")
          .then(response => {
            users = response.data.data
            users = this.validation(users)
            localStorage.setItem("users", JSON.stringify(users))
          })
          .catch(e => {
            throw e
          })
      await this.sleep(1000)
    }
    this.loading = true
  }
})
</script>

<style>
.container {
  margin: auto;
  width: 70%;
  border-radius: 20px;
  background-color: #b5bfe4;
}

.list {
  margin: 20px;
  padding: 0;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.li {
  margin: 20px;
  align-self: center;
  list-style-type: none;
}
.update-button {
  background-color: rgb(255 236 175);
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  margin-bottom: 20px;
  font-family: inherit;
  font-size: inherit;
}
.update-button:active {
  opacity: 0.7;
}


.loader {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.loader div {
  animation: loader 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  transform-origin: 40px 40px;
}
.loader div:after {
  content: " ";
  display: block;
  position: absolute;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #b5bfe4;
  margin: -4px 0 0 -4px;
}
.loader div:nth-child(1) {
  animation-delay: -0.036s;
}
.loader div:nth-child(1):after {
  top: 63px;
  left: 63px;
}
.loader div:nth-child(2) {
  animation-delay: -0.072s;
}
.loader div:nth-child(2):after {
  top: 68px;
  left: 56px;
}
.loader div:nth-child(3) {
  animation-delay: -0.108s;
}
.loader div:nth-child(3):after {
  top: 71px;
  left: 48px;
}
.loader div:nth-child(4) {
  animation-delay: -0.144s;
}
.loader div:nth-child(4):after {
  top: 72px;
  left: 40px;
}
.loader div:nth-child(5) {
  animation-delay: -0.18s;
}
.loader div:nth-child(5):after {
  top: 71px;
  left: 32px;
}
.loader div:nth-child(6) {
  animation-delay: -0.216s;
}
.loader div:nth-child(6):after {
  top: 68px;
  left: 24px;
}
.loader div:nth-child(7) {
  animation-delay: -0.252s;
}
.loader div:nth-child(7):after {
  top: 63px;
  left: 17px;
}
.loader div:nth-child(8) {
  animation-delay: -0.288s;
}
.loader div:nth-child(8):after {
  top: 56px;
  left: 12px;
}
@keyframes loader {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
