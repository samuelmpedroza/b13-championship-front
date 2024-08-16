<template>
  <card class="card" title="Criar Campeonato">
    <div>
      <form @submit.prevent>
        <div class="row">
          <div class="col-md-5">
            <fg-input
              type="text"
              label="Nome do campeonato"
              placeholder="Nome do campeonato"
              v-model="championship.name"
            >
            </fg-input>
          </div>
          <div class="col-md-3">
            <fg-input
              type="date"
              label="Data de início"
              placeholder="Data de início"
              v-model="championship.initialDate"
            >
            </fg-input>
          </div>
        </div>

        <div class="row">
          <div class="col-md-10">
            <div class="form-group">
              <textarea
                rows="15"
                class="form-control border-input"
                placeholder="(Opcional) descreva aqui regras do campeonato, informações gerais, ou dados de interesse que todos os participantes serão capazes de visualizar."
                v-model="championship.about"
              >
              </textarea>
            </div>
          </div>
        </div>
        <div class="text-center">
          <p-button type="info" round @click.native.prevent="createChampionship">
            Criar Campeonato
          </p-button>
        </div>
        <div class="clearfix"></div>
      </form>
    </div>
  </card>
</template>
<script>
export default {
  data() {
    return {
      championship: {
        name:'',
        initialDate: '',
        about: ''
      },
    };
  },
  methods: {
    updateProfile() {
      alert("Your data: " + JSON.stringify(this.user));
    },
  },
};
</script>
<style></style>
