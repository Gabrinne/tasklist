<template>
    <div>

        <div class="row">
            <div class="col-sm-10">
                <h1 class="font-weight-light">
                    Lista de tarefas
                </h1>
            </div>

            <div class="col-sm-2">
                <button class="btn btn-primary float-right" @click="exibirFormularioCriarTarefa">
                    <i class="fa fa-plus mr-2"></i>
                    <span>Criar</span> 
                </button>
            </div>
            
        </div>


        <ul class="list-group" v-if="tarefas.length > 0">
            <TarefasListaIten
                @concluir="editarTarefa"
                @editar="selecionarTarefaParaEdicao"
                @deletar="deletarTarefa"
                v-for="tarefa in tarefas"
                :key="tarefa.id"
                :tarefa="tarefa" />
        </ul>

        <p v-else>Nenhuma tarefa criada.</p>

        <TarefaSalvar v-if="exibirFormulario" :tarefa="tarefaSelecionada" @criar="criarTarefa" @editar="editarTarefa" />

    </div>
</template>

<script>
import axios from "axios";

import TarefaSalvar from "./TarefaSalvar.vue";
import TarefasListaIten from "./TarefasListaIten.vue";

import config from "./../config/config";

export default {
  components: {
    TarefaSalvar,
    TarefasListaIten,
  },
  data() {
    return {
      tarefas: [],
      exibirFormulario: false,
      tarefaSelecionada: undefined,
    };
  },
  created() {
    axios.get(`${config.apiURL}/tarefas`).then((response) => {
      this.tarefas = response.data;
    });
  },
  methods: {
    criarTarefa(tarefa) {
      axios.post(`${config.apiURL}/tarefas`, tarefa).then((response) => {
        console.log("POST/tarefas", response);
        this.tarefas.push(response.data);
        this.resetar();
      });
    },
    selecionarTarefaParaEdicao(tarefa) {
      this.tarefaSelecionada = tarefa;
      this.exibirFormulario = true;
    },
    editarTarefa(tarefa) {
      console.log("editar:", tarefa);
      axios
        .put(`${config.apiURL}/tarefas/${tarefa.id}`, tarefa)
        .then((response) => {
          console.log(`PUT/tarefas/${tarefa.id}`, response);
          const indice = this.tarefas.findIndex((t) => t.id === tarefa.id);
          this.tarefas.splice(indice, 1, tarefa);
          this.resetar();
        });
    },
    exibirFormularioCriarTarefa(event) {
      if (this.tarefaSelecionada) {
        this.tarefaSelecionada = undefined;
      }
      this.exibirFormulario = !this.exibirFormulario;
    },
    deletarTarefa(tarefa) {
      const confirmar = window.confirm("Deseja deletar a tarefa?");
      if (confirmar) {
        axios
          .delete(`${config.apiURL}/tarefas/${tarefa.id}`)
          .then((response) => {
            console.log(`DELETE/tarefa/${tarefa.id}`, response);
            const indice = this.tarefas.findIndex((t) => t.id === tarefa.id);
            this.tarefas.splice(indice, 1);
          });
      }
    },
    resetar() {
      this.tarefaSelecionada = undefined;
      this.exibirFormulario = false;
    },
  },
};
</script>
