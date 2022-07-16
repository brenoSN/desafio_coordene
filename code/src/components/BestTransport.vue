<template>

  <div class="title">
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-navbar-brand class="ml-2">
        <img class="logo" src = "../assets/logo.png">
        <b>{{ appName }}</b>
      </b-navbar-brand>
    </b-navbar>
    <div class = "content">
    <p id="highlightedText"> Como é o frete que você precisa?</p>
    
    <fieldset class = "field">
      <label> Selecione o destino do frete </label>
      <br>
      <select name = "cities" id = "city">

      </select>
      <br>
    </fieldset>
    <div class = "field">
      <label for = "weight"> Peso </label>
      <br>
      <input type = "number" step = "0.01" id = "weight" placeholder = "Insira aqui o peso da carga em Kg">
    </div>
    <button class = "button" id = "analyze"> Analisar</button>
    <div class = "result">
      <p id="highlightedText"> Estas são as melhores alternativas de frete que encontramos para você</p>
     </div>
  </div>

  </div>


</template>

<script>

function buttonClicked(){
    console.log("Button clicked");
}

window.onload=function(){
  var btn = document.getElementById("analyze");
  btn.addEventListener("click", buttonClicked, true);
}

import {
  BNavbar,
  BNavbarBrand,
} from 'bootstrap-vue'

import axios from 'axios'

export default {
  components: {
    BNavbar,
    BNavbarBrand,
  },
  data() {
    const appName = ''
    return {
      appName,
      api_data:undefined,
    }
  },
  created() {
    // Implemente aqui o GET dos dados da API REST
    // para que isso ocorra na inicialização da pagina
    this.appName = 'MELHOR FRETE'
    axios.get("http://localhost:3000/transport").then((response) => {
      this.api_data = response.data
      this.initializeSelect()
    })
  },
  
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    methodFoo() {
      console.log(this.appName)
    },

    initializeSelect() {
      var cities = new Set()
      var options = "<option>Selecione aqui o destino do frete</option>";
      for(const dt of  this.api_data){
        cities.add(dt.city)
      }
      for (const city of cities) {
        options += "<option>"+ city +"</option>";
      }
      document.getElementById("city").innerHTML = options;
    }

  },
  
}


</script>

<style scoped>

body{
    background-color: #F0F8FF;
    font-family: sans-serif;
    font-size: 1em;
    color: #59429d;
    margin-left: 20%;
    margin-right: 20%;
    margin-top: 2%;
    justify-content: center;
}

.title .navbar {
  background-color: #00aca6 !important;
  
}

.title .navbar-brand {
  margin-left: 20px;
}

.logo {
    z-index: 9999;
    height: 5rem;
    width: 5rem; 
    padding: 8px;
    top:10px; 
    left:10px;
}

#highlightedText {
    font-family: sans-serif;
    font-weight: bold;
    color: #000000;
}

.field {
  margin-top: 0.75cm;
}

.content {
  justify-content: center;
  margin-left: 40%;
  padding-top: 1cm;
}


label {
  font-family: sans-serif;
  font-size: 1em;
  color: #000000;
}

input, select, button {
    font-family: sans-serif;
    font-size: 1em;
    color: #000000;
    border-radius: 5px;
}

input, select {
  margin: 5px;
  width: 10cm;
  padding: 2px;
}

/* Elemento de classe "botao" */
.button {
    font-size: 1.2em;
    background: #00aca6;
    border: 0;
    margin-bottom: 1em;
    margin-top: 0.85cm;
    color: #ffffff;
    padding: 0.2em 0.6em;
    box-shadow: 2px 2px 2px rgba(0,0,0,0.2);
    text-shadow: 1px 1px 1px rgba(0,0,0,0.5);
}

/* Elemento de classe "botao" com o estado da pseudoclasse "hover" */
.button:hover {
    background: #029792;
    box-shadow: inset 2px 2px 2px rgba(0,0,0,0.2);
    text-shadow: none;
}

/* Elementos de classe botão e de tag <select> */
.button, select{
    cursor: pointer;
}

.result {
  margin-top: 10%;
  text-align: left 
}

</style>
