<template>
  <div class="row">
    <div class="col-12">
      <card>
        <template slot="header">
          <h3 class="card-title">Ficha de inscrição para {{ championship.name }}</h3>

        </template>
        <b-alert variant="danger" :show="showAlert">{{ alertMessage }}</b-alert>
        <b-alert variant="success" :show="showSuccess">{{ successMessage }}</b-alert>
        <div class="row">
          <div class="col-xl-4 col-lg-4 col-md-4">
            <fg-input type="text" label="Nome" placeholder="Nome da equipe" v-model="team.name">
            </fg-input>
          </div>
        </div>
        <h4> Adicionar atleta </h4>
        <div class="row">
          <div class="col-xl-4 col-lg-4 col-md-4">
            <fg-input type="text" label="Nome completo" placeholder="Nome completo do atelta" v-model="player.name">
            </fg-input>
          </div>
          <div class="col-xl-4 col-lg-4 col-md-4">
            <fg-input type="date" label="Data de nascimento" placeholder="Data de nascimento"
              v-model="player.birthdate">
            </fg-input>
          </div>
          <div class="col-xl-4 col-lg-4 col-md-4 mt-2 d-flex align-items-center">
            <div class="align-middle">
              <p-button type="info" round @click.native.prevent="addPlayer">
                Adicionar atleta
              </p-button>
            </div>
          </div>
        </div>
        <h4> Atletas Inscritos</h4>
        <div slot="raw-content" class="table-responsive">
          <new-table :data="table1.data" :columns="table1.columns" type="hover">
          </new-table>
        </div>
        <div class="row">
          <div class="col-xl-4 col-lg-4 col-md-4 mt-2 d-flex align-items-center">
          </div>
        </div>
      </card>
      <div class="align-middle">
        <p-button type="info" round @click.native.prevent="subscribeTeam">
          Concluir inscrição
        </p-button>
      </div>
    </div>
  </div>
</template>

<script>
import { NewTable } from "@/components";
import axios from 'axios';
const tableColumns = [
  { name: 'id', label: 'Id' },
  { name: 'name', label: 'Nome' },
  { name: 'birthdate', label: 'Data de nascimento' },
]

export default {
  components: {
    NewTable,
  },
  data() {
    return {
      team: {
        name: '',
        manager: '',
        players: []
      },
      player: {
        name: '',
        birthdate: ''
      },
      championship: {
        name: 'Copa B13'
      },
      table1: {
        title: "Todos os Campeonatos",
        subTitle: "Listagem de todos os campeonatos em andamento",
        columns: [...tableColumns],
        data: [],
      },
      showAlert: false,
      alertMessage: '',
      showSuccess: false,
      successMessage: '',
      championshipId: ''
    };
  },
  mounted() {
    this.championshipId = this.$route.params.id
  },
  methods: {
    scrollToTop() {
    window.scrollTo(0,0);
  },
    addPlayer() {
      if(this.player.name && this.player.birthdate){
        this.table1.data.push({
          id: this.table1.data.length + 1,
          name: this.player.name,
          birthdate: new Date(this.player.birthdate).toLocaleDateString('en-GB')
        })
        this.player = {
          name: '',
          birthdate: ''
        }
        this.alertMessage = ''
        this.showAlert = false
      }
      else {
        this.showAlert = true
        this.alertMessage = 'Preencha todos os campos'
      }
    },
    async subscribeTeam() {
      if (this.table1.data.length < 5) {
        this.alertMessage = 'Um time deve ter pelo menos 5 atletas para se inscrever em uma competição.'
        this.showAlert = true
        this.showSuccess = false
      } else {
        this.showAlert = false
        this.alertMessage = ''
      }
      try {
        const token = sessionStorage.getItem('token');
        const data = {
          name: this.team.name,
          players: this.table1.data
        }
        const response = await axios.post(`https://b13-championship-manager.onrender.com/championships/${this.championshipId}/team`, data, { validateStatus: () => true, headers: { Authorization: `Bearer ${token}` } });

        if (response.status === 201) {
          this.successMessage = 'Time registrado com sucesso.'
          this.showSuccess = true
          this.scrollToTop()
        }
        else {
          this.failureMessage = response.data.message
          this.showFailure = true
        }
        
      } catch (e) {
        this.failureMessage = 'Falha inesperada'
        this.showFailure = true
      }
    },
  },
};
</script>
<style></style>