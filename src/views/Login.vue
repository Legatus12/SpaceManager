<template>
    <div class="page bg-green">
        <div class="login">
            <form @submit.prevent="login()">
            <h1>Space Manager</h1>
                <input v-model="username" id="username" type="text" placeholder="usuario">
                <input v-model="password" id="password" type="password" placeholder="contraseña">
                <button>iniciar sesión</button>
                <p class="message">{{ message }}</p>
                <p class="signup" @click="router.push({ name: 'signup' })">o regístrate</p>
            </form>
        </div>
    </div>
</template>


<script setup>

/* imports */
import { ref } from 'vue'

import { useRouter } from 'vue-router'

import { useStore } from '@/stores/user.js'

import { getUser } from '@/firebase.js'

//

const router = useRouter()

const user = useStore()

//

const message = ref("")

const username = ref("")
const password = ref("")

const login = () => {
    if(username.value != ''){
        getUser(username.value, (userDocs) => {
            if(userDocs.size == 1){
                userDocs.forEach(userDoc => {
                    if(password.value === userDoc.data().password){
                        user.setID(userDoc.id)
                        user.setUsername(userDoc.data().username)
                        router.push({ name: 'dashboard' })
                    } else message.value = 'contraseña incorrecta'
                })
            } else message.value = 'el usuario no existe'
        })
    } else message.value = 'el campo usuario está vacío'
}

</script>


<style scoped>

.login{ @apply text-white }

form{ @apply flex flex-col items-center gap-12 text-xl }

h1{ @apply font-bold }

input{ @apply bg-green border-solid border-b-2 border-white focus:outline-none text-white placeholder:text-lightgray placeholder:font-light p-4 }

button{ @apply w-full bg-white text-[#232323] focus:outline-none focus:bg-lightgray hover:bg-lightgray p-2 rounded-xl shadow-2xl }

.message{ @apply text-darkgreen italic h-4 }

.signup{ @apply w-fit font-light hover:underline cursor-pointer }

</style>