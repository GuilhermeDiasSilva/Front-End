<template>
  <div>
    <div>
      <b-navbar type="dark" variant="success">
        <b-navbar-brand >Produtos</b-navbar-brand>
      </b-navbar>
    </div>

    <div class="container">
      <table class="table-sm table table-striped">
        <thead>
          <tr>
            <th scope="col">Id</th>
            <th scope="col">Nome</th>
            <th scope="col">Descricao</th>
            <th scope="col">Marca</th>
            <th scope="col">Quantidade</th>
            <th scope="col">Data</th>
            <th scope="col">Funções</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="produto of result" :key="produto.id">
            <th scope="row">{{ produto.id }}</th>
            <td>{{ produto.nome }}</td>
            <td>{{ produto.descricao }}</td>
            <td>{{ produto.marca }}</td>
            <td>{{ produto.quantidade }}</td>
            <td>{{ produto.data }}</td>
            <td>
              <b-button v-b-modal.modal-2 class="btn btn-warning" @click.prevent="editar(produto.id)">Editar</b-button >
              <b-button
                @click.prevent="excluir(produto.id)"
                class="btn btn-danger">Excluir</b-button>
            </td>
          </tr>
        </tbody>
      </table>

      <b-button v-b-modal.modal-1 class="btn btn-success sm">Adicionar Novo Produto</b-button>

      <b-modal id="modal-1" title="Produtos" :hide-footer="true" ref="salvar">
        <div class="modal-body" @submit.prevent="salvar">
          <div class="row">
            <div class="col-12 col-md-6">
              <label for="id">ID:</label>
              <input
                type="text"
                name="id"
                id="id"
                class="form-control"
                readonly
              />
            </div>
            <div class="col-md-6">
              <div class="form-group">
                <label for="">Nome</label>
                <input
                  id="txtNome"
                  type="text"
                  class="form-control"
                  placeholder="Digite o Nome..." v-model="txt.nome"
                />
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6">
              <div class="form-group">
                <label for="">Descrição</label>
                <input
                  id="txtSobrenome"
                  type="text"
                  class="form-control"
                  placeholder="Digite a Descrição..." v-model="txt.descricao"
                />
              </div>
            </div>

            <div class="col-md-6">
              <div class="form--group">
                <label for="">Marca</label>
                <input
                  id="txtMarca"
                  type="text"
                  class="form-control"
                  placeholder="Digite a Marca..." v-model="txt.marca"/>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6">
              <div class="form-group">
                <label for="">Quantidade</label>
                <input
                  id="txtQuantidade"
                  type="number"
                  class="form-control"
                  placeholder="Digite a Quantidade..." v-model="txt.quantidade"
                />
              </div>
            </div>
            <div class="col-md-6">
              <div class="form-group">
                <label for="">Data</label>
                <input
                  id="txtFormacao"
                  type="date"
                  class="form-control" v-model="txt.data"/>
              </div>
            </div>
          </div>
        </div>
        <b-button v-b-modal.modal-1 class="btn btn-success" @click="salvar">Salvar</b-button >
      </b-modal>


      <b-modal id="modal-2" title="Editar Produtos" :hide-footer="true" ref="atualizar">
        <div class="row">
          <div class="col-12 col-md-6">
            <label for="id">ID:</label>
            <input type="text" name="id" id="id" class="form-control" readonly v-model="txt.id"/>
          </div>
          <div class="col-md-6">
            <div class="form-group">
              <label for="">Nome</label>
              <input  v-model="txt.nome"  id="nome" type="text" class="form-control" placeholder="Digite o Nome..." />
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-6">
            <div class="form-group">
              <label for="">Descrição</label>
              <input v-model="txt.descricao" id="sobrenome" type="text" class="form-control" placeholder="Digite a Descrição..."/>
            </div>
          </div>

          <div class="col-md-6">
            <div class="form--group">
              <label for="">Marca</label>
              <input v-model="txt.marca" id="marca" type="text" class="form-control" placeholder="Digite a Marca..." />
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-6">
            <div class="form-group">
              <label for="">Quantidade</label>
              <input v-model="txt.quantidade" id="quantidade" type="number" class="form-control" placeholder="Digite a Quantidade..."/>
            </div>
          </div>
          <div class="col-md-6">
            <div class="form-group">
              <label for="">Data</label>
              <input v-model="txt.data" id="data" type="date" class="form-control" />
            </div>
          </div>
        </div>
        <b-button class="mt-3" variant="success" @click.prevent="atualizar(txt.id)" block>atualizar</b-button>
      </b-modal>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { api } from "../services/config";

export default {
  data() {
    return {
      result: null,
      txt:{
        id:null,
        nome: null,
        descricao: null,
        marca: null,
        quantidade:null,
        data: null,
      }
      
    };
  },

  mounted() { //primeiro a iniciar 
    this.getProdutos();
  },
  methods: {
    getProdutos() {
      api.get("produtos")
      .then((response) => {
        this.result = response.data;
      });
    },
    excluir(id) {
      api.get(`produto/del/${id}`)
      .then(() => {
        this.getProdutos(); // retornando os produtos
      });
    },
    salvar() {
      api.post(`produto/novo`, this.txt)
      .then(() => {
        this.$refs['salvar'].hide()
        this.getProdutos();
      });
    },   
    editar(id){
      api.get(`produto/${id}`)
      .then((response)=>{
       const produto = response.data
       this.txt.id = produto.id,
       this.txt.nome = produto.nome,
       this.txt.descricao = produto.descricao,
       this.txt.marca = produto.marca,
       this.txt.quantidade = produto.quantidade,
       this.txt.data = produto.data
      })
    },
    atualizar(id){
      console.log(id)
      api.post(`produto/update/${id}`, this.txt)
      .then(()=>{
        this.$refs['atualizar'].hide()
        this.getProdutos();
      })
    },
    
  },
};
</script>
