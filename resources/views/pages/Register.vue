<template>
    <div class="mx-auto w-4/12 mt-10 bg-blue-200 p-4 rounded-lg">
        <div class="bg-white shadow-lg rounded-lg px-8 pt-6 pb-8 mb-2 flex flex-col">
            <h1 class="text-gray-600 py-5 font-bold text-3xl"> Create account </h1>
            <ul class="list-disc text-red-400" v-for="(value, index) in errors" :key="index"
                v-if="typeof errors === 'object'">
                <li>{{ value[0] }}</li>
            </ul>
            <p class="list-disc text-red-400" v-if="typeof errors === 'string'">{{ errors }}</p>
            <form method="post" @submit.prevent="handleLogin">
                <div class="mb-4">
                    <label class="block text-grey-darker text-sm font-bold mb-2" for="name">
                        Name
                    </label>
                    <input class="shadow appearance-none border rounded w-full py-2 px-3 text-grey-darker" id="name"
                        type="text" v-model="form.name" required />
                </div>
                <div class="mb-4">
                    <label class="block text-grey-darker text-sm font-bold mb-2" for="username">
                        Email Address
                    </label>
                    <input class="shadow appearance-none border rounded w-full py-2 px-3 text-grey-darker" id="username"
                        type="text" v-model="form.email" required />
                </div>
                <div class="mb-4">
                    <label class="block text-grey-darker text-sm font-bold mb-2" for="password">
                        Password
                    </label>
                    <input class="shadow appearance-none border border-red rounded w-full py-2 px-3 text-grey-darker mb-3"
                        id="password" type="password" v-model="form.password" required />
                </div>
                <div class="flex items-center justify-between">
                    <button class="bg-blue-500 hover:bg-blue-900 text-white font-bold py-2 px-4 rounded" type="submit">
                        Register
                    </button>
                    <router-link class="inline-block align-baseline font-bold text-sm text-blue hover:text-blue-darker"
                        to="/">
                        Login
                    </router-link>
                </div>
            </form>
        </div>
    </div>
</template>
<script setup>
import { ref, reactive, computed, watch, inject } from "vue";
import { useRouter, useRoute } from "vue-router";
import axios from 'axios';
import { useToast } from "vue-toast-notification";
import { useStore } from 'vuex';

const errors = ref();
const store = useStore();
const toast = useToast();
const router = useRouter();
const form = reactive({
    name: '',
    email: '',
    password: '',
});
// const handleLogin = async () => {
//     try {
//         const result = await axios.post('/api/auth/register', form)
//         if (result.status === 200 && F && result.data.token) {
//             localStorage.setItem('APP_DEMO_USER_TOKEN', result.data.token)
//             await router.push('/home')
//         }
//     } catch (e) {
//         if (e && e.response.data && e.response.data.errors) {
//             errors.value = Object.values(e.response.data.errors)
//         } else {
//             errors.value = e.response.data.message || ""
//         }
//     }
// }
function handleLogin() {
    axios.get('/sanctum/csrf-cookie').then((r) => {
        axios
            .post('/api/auth/register', {
                name: form.name,
                email: form.email,
                password: form.password,
            })
            .then(
                (res) => {
                    if (res.data.message) {
                        toast.success(res.data.message, {
                            position: 'top'
                        })
                    }
                    localStorage.setItem('APP_DEMO_USER_TOKEN', res.data.token);
                    store.dispatch('setToken', res.data.token);
                    store.dispatch('setUser', res.data.myUser);
                    router.push("/home");
                },
                (e) => {
                    const error = e.response.data.message;
                    if (error) {
                        toast.error(error, {
                            position: 'top'
                        })
                    } else {
                        toast.error(e, {
                            position: 'top'
                        });
                    }
                }
            )
    })
}
</script>