<template>
    <div @keyup.enter="login" id="login">
        
        <label>Nombre Usuario:</label>
        <input type="text" v-model="username">

        <label>Contraseña:</label>
        <input type="password" v-model="password">

        <button @click="login" class="l-button">Login</button>
        <div class="errors">
            <ul>
                <li
                v-for="(error, index) of errors"
                :key="index"
                > {{ error }} </li>
            </ul>
        </div>
    </div>
</template>

<script>
import Vue from 'vue'

export default {
    data() {
        return {
            username: '',
            password: '',
            errors: []
        }
    },
    methods: {
        login() {
            this.errors = []
            if(this.username == '' || this.password == '') {
                this.errors.push('Wypełnij pola.')
            }
            else {
                Vue.axios.post('/user/login', {
                    username: this.username,
                    password: this.password
                })
                .then(result => {
                    localStorage.setItem('token', result.data.token)
                    localStorage.setItem('username', this.username)
                    Vue.axios.defaults.headers.Authorization = localStorage.getItem('token')
                    this.username = ''
                    this.password = ''
                    this.$router.push({ name: 'home' })
                    this.$emit('authenticated', true)
                })
                .catch(error => {
                    this.errors = error.response.data.errors
                })
            }
        }
    }
}
</script>

<style>
    #login {
        display: grid;
        background-color: #222;
        border-radius: 5px;
        grid-gap: 5px;
        padding: 10px;
        margin: 5px;
    }
    #login > input, button {
        border: solid #e7e7e7 1px;
        background: transparent;
        border-radius: 10rem;
        outline: none;
    }
    #login > input {
        font-size: 1.25em;
        padding: 5px 0 5px 10px;
    }
    #login > button {
        text-align: center;
        margin-top: 10px;
        width: auto;
        padding: .45em .75em;
        font-size: 1.05em;
        justify-self: right;

    }
    #login ul {
        list-style-type: none;
        padding: 10px;
        margin: 0;
    }
    #login ul li {
        margin-top: 15px;
    }
    #login ul li:first-child {
        margin-top: 0;
    }

    @media screen and (min-width: 800px) {
        #login { width: 400px }
    }
</style>