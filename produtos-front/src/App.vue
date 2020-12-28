<template>
  <div id="app">

    <nav>
      <div class="nav-wrapper blue darken-1">
        <a href="#" class="brand-logo center">Produtos Front</a>
      </div>
    </nav>

    <div class="container">

      <ul>
        <li v-for="(error, index) of errors" :key="index">
          campo <b> {{ error.field }} </b> - {{ error.defaultMessage }}
        </li>
      </ul>
      
      <form @submit.prevent="salvar">

          <label>Nome</label>
          <input type="text" placeholder="Nome" v-model="produto.nome">
          <label>Quantidade</label>
          <input type="number" placeholder="QTD" v-model="produto.quantidade">
          <label>Valor</label>
          <input type="text" placeholder="Valor" v-model="produto.valor">

          <button class="waves-effect waves-light btn-small">Salvar<i class="material-icons left">save</i></button>

      </form>

      <table>

        <thead>

          <tr>
            <th>ID</th>
            <th>NOME</th>
            <th>QUANTIDADE</th>
            <th>VALOR</th>
          </tr>

        </thead>

        <tbody>

          <tr v-for="produto of produtos" :key="produto.id">

            <td>{{ produto.nome }}</td>
            <td>{{ produto.quantidade }}</td>
            <td>{{ produto.valor }}</td>
            <td>
              <button @click="editat(produto)" class="waves-effect btn-small blue darken-1"><i class="material-icons">create</i></button>
              <button @click="remover(produto)" class="waves-effect btn-small red darken-1"><i class="material-icons">delete_sweep</i></button>
            </td>

          </tr>

        </tbody>
      
      </table>

    </div>

  </div>
</template>

<script>
import Produtos from './services/produtos'
export default {
  data () {
    return {
      produto: {
        id:'',
        nome: '',
        quantidade: '',
        valor:''
      },
      produtos:[],
      errors:[]
    }
  },

  mounted () {
    this.listar()
  },
  methods:{
    listar(){
      Produtos.listar().then(resposta => {
        this.produtos = resposta.data
      })
    },

    salvar(){

      if(this.produto.id){

        Produtos.salvar(this.produto).then(resposta => {
          this.produto = {}
          //Gambiarra feita pra não deixar a resposta sem uso
          //porque estava dando erro
          console.log(resposta.data)
          alert('Salvo com sucesso!')
          this.listar()
          this.errors = []
        }).catch(e => {
          console.log(e.response.data.errors)
          this.errors = e.response.data.errors
        })
      }else{
        Produtos.atualizar(this.produto).then(resposta => {
          this.produto = {}
          //Gambiarra feita pra não deixar a resposta sem uso
          //porque estava dando erro
          console.log(resposta.data)
          alert('Atualizado com Sucesso!')
          this.listar()
          this.errors = []
        }).catch(e => {
          console.log(e.response.data.errors)
          this.errors = e.response.data.errors
        })      
      }
    },

    editar(produto){
      this.produto = produto
    },

    remover(produto) {

      //Mensagem de confirmação
      if(confirm('Desja realmente excluir o produto?')){
        Produtos.apagar(produto).then(resposta => {
          console.log(resposta)
          this.listar();
          this.errors = []
        }).catch(e => {
          this.errors = e.response.data.errors
        })
      }      
    }
  } 
}
</script>

<style>

</style>
