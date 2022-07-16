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
      <br>
      <p class = "resultInfo" id = "lowestPrice">
        Frete mais barato:
      </p>
      <p class = "resultInfo" id = "fastShipping">
        Frete mais rapido:
      </p>
     </div>

  </div>
  </div>

</template>

<script>

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
    const city = ''
    const weight = 0
    const heavy = 100
    return {
      appName,
      api_data:undefined,
      heavy,
      city,
      weight
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

  mounted () {
    this.initializeButton()
  }
  ,
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    methodFoo() {
      console.log(this.appName)
    },

    onAnalyseClicked() {
      var select = document.getElementById("city")
      var weightElement = document.getElementById("weight")
      
      if (select && select.selectedIndex > 0 && weightElement) {
        this.weight = parseFloat(weightElement.value)
        this.city = select.options[select.selectedIndex].text
        var result = this.analyse()
        this.outputResult(result)
      }
    },

    analyse() {
      // returns the index of the date array referring to the lowest price and fastest shipping
      var lowestPriceIndex = -1
      var fastShippingIndex = -1
      var lowestPrice = 999.9
      var fastShipping = 999.9
      var i = 0
      if (this.city != '' && this.weight > 0) {
        for (const dt of this.api_data) {
          
          if (dt.city === this.city) {
            
            if (parseFloat(dt.lead_time.slice(0, -1)) < fastShipping) {
              fastShipping = parseFloat(dt.lead_time.slice(0, -1))
              fastShippingIndex = i
            }
            if (this.weight > this.heavy) {
              if (parseFloat(dt.cost_transport_heavy.slice(2)) < lowestPrice) {
                lowestPrice = parseFloat(dt.cost_transport_heavy.slice(2))
                lowestPriceIndex = i
              }
            }
            else {
              if (parseFloat(dt.cost_transport_light.slice(2)) < lowestPrice) {
                lowestPrice = parseFloat(dt.cost_transport_light.slice(2))
                lowestPriceIndex = i
              }
            }
          }
          i++
        }
      }
      if (this.api_data[lowestPriceIndex].lead_time === this.api_data[fastShippingIndex].lead_time) {
        fastShippingIndex = lowestPriceIndex;
      }
      return {
        lowestPriceIndex,
        fastShippingIndex
      }
    },
    
    getCostTotal(transport) {
      if (this.weight > this.heavy) {
        return transport.cost_transport_heavy.slice(2) * this.weight
      }
      return (transport.cost_transport_light.slice(2) * this.weight).toFixed(2)
    },
    
    outputResult(result) {
      // displays the analysis result in the interface
      var lowestPriceOutText = "Frete mais barato: <b> Transportadora "
        + this.api_data[result.lowestPriceIndex].name
        + " - R$ " + this.getCostTotal(this.api_data[result.lowestPriceIndex])
        + " - " + this.api_data[result.lowestPriceIndex].lead_time + "</b>"
      var fastShippingText = "Frete mais rápido: <b>Transportadora "
        + this.api_data[result.fastShippingIndex].name
        + " - R$ " + this.getCostTotal(this.api_data[result.fastShippingIndex])
        + "- " + this.api_data[result.fastShippingIndex].lead_time + "</b>"
      document.getElementById("lowestPrice").innerHTML = lowestPriceOutText
      document.getElementById("fastShipping").innerHTML = fastShippingText
      
    },


    initializeSelect() {
      // initialize the select with the available cities
      var cities = new Set()
      var options = "<option>Selecione aqui o destino do frete</option>";
      for(const dt of  this.api_data){
        cities.add(dt.city)
      }
      for (const city of cities) {
        options += "<option>"+ city +"</option>";
      }
      document.getElementById("city").innerHTML = options;
    },

    initializeButton() {
      var btn = document.getElementById("analyze");
      btn.addEventListener("click", this.onAnalyseClicked, true);
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

.resultInfo {
  font-family: sans-serif;
  font-size: 1em;
  color: #000000;
  border-style: dotted;
  border-radius: 5px;
  border-width: 1px;
  background: #E4E6F5;
  width: 15cm;
  padding: 5px;
}

input, select {
  margin: 5px;
  width: 10cm;
  padding: 2px;
  background: #E4E6F5;
}

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

.button:hover {
    background: #029792;
    box-shadow: inset 2px 2px 2px rgba(0,0,0,0.2);
    text-shadow: none;
}

.button, select{
    cursor: pointer;
}

.multiple option{
   height: 30px;
}
.result {
  margin-top: 10%;
  text-align: left 
}

</style>
