<script setup>
import { computed, onMounted, ref, watch } from 'vue'
import MarcaAPI from '../api/marca'
import { logged } from '@/pinia';
import UserAPI from '@/api/usuario';

const api = new MarcaAPI()
const usuarioapi = new UserAPI()

const userid = ref(null)

const novamarca = ref(null)
const marcas = ref([])
const nacionalidade = ref(null)
const msg = ref(null)
const errormsg = ref(null)
const store = logged()

const isuserlogged = computed(() =>{
  return store.showlog
})
const usuario = computed(() =>{
  return store.showuser
})

async function enviar() {
  if (marcas.value && nacionalidade.value && userid.value) {
    await api.NovaMarca(novamarca.value, nacionalidade.value, userid.value)
    msg.value = 'marca enviada com sucesso'

    setTimeout(() => {
      msg.value = null
      location.reload()
    }, 2000)
  } 
  else if(!isuserlogged.value){
    errormsg.value = "logue-se para adicionar uma marca"

    setTimeout(() =>{
        errormsg.value = null
    }, 2000)
  }
  else {
    errormsg.value = "preencha os campos corretamente"

    setTimeout(() =>{
        errormsg.value = null
    }, 2000)
  }
}

async function excluir(id){
   await api.ExcluirMarca(id)

   setTimeout(() =>{
      location.reload()
   }, 500)
}

onMounted(async () => {
  marcas.value = await api.ListarMarcas()
  const apiusuario = await usuarioapi.ListarUsuarios()
  const iduser = apiusuario.find(user => user.username === usuario.value)
  userid.value = iduser.id
})

watch(marcas, (marca) => {
  console.log(marca)
})
</script>
<template>
  <div class="container">
    <div class="msg" v-if="msg">
      <p>{{ msg }}</p>
    </div>
    <div class="errormsg" v-if="errormsg">
        <p>{{errormsg}}</p>
    </div>
    <h1>marca</h1>
    <input type="text" placeholder="nova marca" v-model="novamarca" />
    <input type="text" placeholder="nacionalidade(opcional)" v-model="nacionalidade" />
    <button @click="enviar">enviar</button>
    <ul class="lista">
      <li v-for="marca in marcas" :key="marca.id">{{ marca.nome }} - {{ marca.nacionalidade }} <button @click="excluir(marca.id)">X</button></li>
    </ul>
  </div>
</template>
<style scoped>
.container {
  display: flex;
  flex-direction: column;
  gap: 20px;
  justify-content: center;
  align-items: center;
  width: 100%;
}
.lista {
  display: flex;
  flex-direction: column;
  list-style: none;
  gap: 20px;
  font-size: 20px;
}
.container input {
  width: 300px;
  padding-left: 2px;
  border: none;
  border-bottom: 1px solid black;
  outline: 0;
  padding: 5px;
  font-size: 15px;
}
.msg {
  background-color: rgb(1, 255, 141);
  display: flex;
  justify-content: center;
  border: 2px solid black;
  width: 300px;
  padding: 15px;
}
.msg p {
  color: black;
}
.errormsg{
    background-color: red;
    display: flex;
    justify-content: center;
    border: 2px solid black;
    width: 300px;
    padding: 15px;
  }
  .errormsg p {
    color: black;
  }
  li button{
    width: 30px;
    background-color: red;
    color: white;
    height: 30px;
    border: none;
    border-radius: 5px ;
  }
</style>
