<template>
  <div class="container">
    <div>
      <img alt="Generatore Password" src="./assets/logo.png">
      <Header msg="Generatore Password"/>
      <PasswordGen />
      <CreditsConsole />
      <Footer />
    </div>
  </div>
</template>

<script>
import PasswordGen from './components/PasswordGenerator.vue'
import Header from './components/HeaderApp.vue'
import Footer from './components/FooterApp.vue'
import CreditsConsole from './components/CreditsConsoleLog.vue'
import 'bulma/css/bulma.css';
import 'bulma-switch/dist/css/bulma-switch.min.css';
import 'bulma-slider/dist/css/bulma-slider.min.css';
import '@fortawesome/fontawesome-free/css/all.css';

export default {
  name: 'App',
  components: {
    PasswordGen,
    Header,
    Footer,
    CreditsConsole
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  padding: 10px;
}
</style>
