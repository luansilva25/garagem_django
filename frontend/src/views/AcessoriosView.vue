<script setup>
import AcessorioAPI from "@/api/acessorios";
import UserAPI from "@/api/usuario";
import { logged } from "@/pinia";
import { computed, onMounted, ref } from "vue";
    const api = new AcessorioAPI()
    const usuarioapi = new UserAPI()

    const acessorios = ref([])
    const novoacessorio = ref(null)
    const msg = ref(null)
    const errormsg = ref(null)
    const userid = ref(null)
    const store = logged()

    const isuserlogged = computed(() =>{
        return store.showlog
    })
    const usuario = computed(() =>{
        return store.showuser
    })
    
    async function enviar(){
        if(novoacessorio.value){
            await api.CriarAcessorio(novoacessorio.value, userid.value)
            msg.value = "acessorio enviado com sucesso"

            setTimeout(() =>{
                msg.value = null
                location.reload()
            }, 2000)
        }
        else if(isuserlogged.value){
            errormsg.value = "logue-se para adicionar novos acessorios"

            setTimeout(() =>{
                errormsg.value = null
            }, 2000)
        }
        else{
            errormsg.value = "preencha o campo corretamente"

            setTimeout(() =>{
                errormsg.value = null
            }, 2000)
        }
    }

    async function exluir(id){
        await api.ExcluirAcessorio(id)

        setTimeout(() =>{
            location.reload()
        }, 500)
    }

    onMounted( async () =>{
        acessorios.value = await api.ListarAcessorios()
        const apiusuario = await usuarioapi.ListarUsuarios()
        const iduser = apiusuario.find(user => user.username === usuario.value)
        userid.value = iduser.id
    })
</script>
<template>
<div class="container">
    <div class="msg" v-if="msg">
        <p>{{msg}}</p>
    </div>
    <div class="errormsg" v-if="errormsg">
        <p>{{errormsg}}</p>
    </div>
    <h1>acessorios</h1>
    <input type="text" placeholder="novo acessorio" v-model="novoacessorio">
    <button @click="enviar">enviar</button>
    <ul class="lista">
        <li v-for="acessorio in acessorios" :key="acessorio.id">{{acessorio.descricao}} <button @click="exluir(acessorio.id)">X</button></li>
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