<template>
  <div id="app">
    <slider class='main' v-model='valor' :min='50' :max='100' :tooltip='tooltip' :reverse='true' label='Dê sua nota a nosso atendimento'/>
    <h1>Slider Colorido</h1>
    <small>Um componente slider feito em vuejs. Baseado em: <a href="https://codepen.io/mukealicious/pen/jWoeZY">https://codepen.io/mukealicious/pen/jWoeZY</a> </small>
    <small>Por: Filipe Roberto Silva</small>
    <h2>Nota: {{valor}}</h2>
  </div>
</template>

<script>
import Slider from './components/Slider.vue'

export default {
  name: 'App',
  components: {
    Slider
  },
  data() {
    return {
      valor: 0,
      tooltip: {
        low: 'Que bom que gostou de nosso aplicativo! 😁',
        med: 'Entendido! Temos o que melhorar 🔨',
        high: 'Que pena que não gostou do atendimento 😩 Estamos trabalhando para poder lhe atender melhor',
      }
    }
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
}
html,
body {
  font-family: Century Gothic,CenturyGothic,AppleGothic,sans-serif;
  color: #404040;
  height: 100%;
  margin: 0;
}

body {
  padding: 0 20px;
  color: #716894;
}

.main {
  width: 400px;
  margin: auto;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
