<template>
    <div class="mt-4">
        <hr>
        <h2 class="font-weight-light">Salvar Tarefa</h2>
        <form @submit.prevent="salvar">
            <div class="row">
                <div :class="classeColuna">
                    <div class="form-group">
                        <label>Título</label>
                        <input 
                        v-model="tarefaLocal.titulo"
                            type="text"
                            class="form-control" 
                            placeholder="Título da tarefa">
                    </div>
                </div>
                <div class="col-sm-2" v-if="tarefa">
                    <div class="form-group">
                        <label>Tarefa concluída?</label>
                        <button 
                        type="button"
                          @click="tarefaLocal.concluido = !tarefaLocal.concluido"
                        class="btn  btn-sm d-block"
                        :class="classeBotao">
                            <i class="fa fa-check"></i>
                        </button>
                    </div>
                </div>
            </div>
            
            <button type="submit" class="btn btn-primary">Salvar</button>
        </form>
    </div>
</template>

<script>
export default {
  props: {
    tarefa: {
      type: Object,
      default: undefined,
    },
  },
  data() {
    return {
      tarefaLocal: Object.assign(
        {},
        { titulo: "", concluido: false },
        this.tarefa
      ),
    };
  },
  computed: {
    classeBotao() {
      return this.tarefa && this.tarefaLocal.concluido
        ? "btn-success"
        : "btn-secondary";
    },
    classeColuna() {
      return this.tarefa ? "col-sm-10" : "col-sm-12";
    },
  },
  methods: {
    salvar(event) {
      const operacao = !this.tarefa ? "criar" : "editar";
      this.$emit(operacao, this.tarefaLocal);
      this.tarefaLocal = {
        titulo: "",
        concluido: false,
      };
    },
  },
  watch: {
    tarefa(tarefaNova, tarefaAntiga) {
      this.tarefaLocal = Object.assign({}, this.tarefa);
    },
  },
};
</script>
