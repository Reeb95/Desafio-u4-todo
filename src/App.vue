<template>
  <div id="app">

    <nav v-if="!authenticated">
          <router-link to="/login">Login</router-link>
          <router-link to="/register">Registrarse</router-link>
    </nav>

    <nav v-else>
        <router-link to="/">Tareas</router-link>
        <router-link to="/login" @click.native="logout()">Cerrar Sesión</router-link>
    </nav>

    <div class="content">
      <transition name="fade" mode="out-in">
        <router-view @authenticated="setAuthenticated" /> 
      </transition>
    </div>
  </div>
</template>

<script>
import router from './router'
import store from './store'


router.beforeEach((to, from, next) => {
  // no iniciado de sesión, solo puede ver vistas inicio de sesión y registro
  if(!store.methods.isLogged() && ['login', 'register'].some((v) => { return v == to.name })) {
    next()
  }

  // se redireccionará a Home si ha iniciado sesión quiere ver vistas de inicio de sesión / registro / nulas
  else if(store.methods.isLogged() && ['login', 'register', null].some((v) => { return v == to.name })) {
    router.replace({ name: 'home' })
  }

  //
  else if(store.methods.isLogged()) {
    next()
  }

  //se redireccionará a iniciar sesión si no se cumple ninguna de las condiciones
  else {
    router.replace({ name: 'login' })
  }
})

export default {
  name: 'App',
  data() {
    return {
      authenticated: false
    }
  },
  computed: {
    isLoggedIn() {
      return localStorage.getItem('token') !== null ? true : false
    }
  },
  methods: {
    setAuthenticated(status) {
      this.authenticated = status
    },
    checkAndSetkAuthentication() {
      this.authenticated = localStorage.getItem('token') !== null ? true : false
    },
    logout() {
      store.methods.clearUserData()
      this.authenticated = false
      this.$router.replace({ name: 'login' })
    }
  },
  created() {
    this.checkAndSetkAuthentication()
  },
  updated() {
    this.checkAndSetkAuthentication()
  }
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Work+Sans');

  * {
    font-family: 'Work Sans', sans-serif;
    text-decoration: none;
    color: #e7e7e7
  }

  body {
    background-color: rgb(16, 23, 128);
    margin: 0;
    padding: 0;
  }

  nav {
    position: sticky;
    left: 0;
    top: 0;
    background-color: rgba(0, 132, 255, 0.75);
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(50px, auto));
    grid-gap: 12px;
    justify-content: right;
    text-align: center;
    margin-bottom: 1em;
    padding: 10px 20px;
  }

   a::after {
    content: "";
    display: block;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 2px;
    background-color: #e7e7e7;
    transform: scaleX(0);
    transition: all .25s;
    opacity: 0;
  }

   a:hover::after {
    transform: scaleX(1);
    opacity: 1;
  }

  .fade-enter-active, .fade-leave-active {
    transition: all .25s;
  }

  .fade-enter {
    opacity: 0;
    transform: translateY(-10px);
  }

  .fade-leave-to {
    opacity: 0;
    transform: translateY(10px);
  }

  .content {
    display: grid;
  }
  @media screen and (min-width: 825px) {
    .content > * {
      justify-self: center;
      width: 800px;
    }
    nav {
      max-width: 1200px;
      margin: 0 auto;
    }
  }
</style>