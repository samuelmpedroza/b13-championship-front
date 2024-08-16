<template>
  <div class="row">
    <div class="col-12">
      <card :title="table1.title" :subTitle="table1.subTitle">
        <div slot="raw-content" class="table-responsive">
          <new-table :data="table1.data" :columns="table1.columns" type="hover" :config="table1.config"
            @showChampionshipModal="showModal">
          </new-table>
        </div>
      </card>

    </div>
    <b-modal id="my-championship-modal" hide-footer :title="modalTitle" size="lg">
      <div>
        <span><strong>Informações Gerais</strong></span>
        <p>{{ championshipData.about }}</p>
        <span><strong>Organizado por</strong></span>
        <p>{{ championshipData.manager }}</p>
        <span><strong>Data de início</strong></span>
        <p>{{ formatDate(championshipData.initialDate) }}</p>
        <span><strong>Equipes Inscritas</strong></span>
        <ul class="list">
          <li v-for="(team, index) in championshipData.teams" :key="index">
            <span>{{ team.name }}</span>
            <button class="toggle-btn" @click="toggleDetails(index)">
              <span v-if="!team.showDetails">+</span>
              <span v-else>-</span>
            </button>
            <div class="details" :class="{ show: team.showDetails }">
              <p><strong>Atletas inscritos</strong></p>
              <div v-for="p in team.players" :key="p.name">
                {{ p.name }} - {{  p.birthdate }}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </b-modal>
  </div>
</template>

<script>
  import { NewTable } from "@/components";
  import axios from 'axios';
  export default {
    components: {
      NewTable,
    },
    data() {
      return {
        championshipData: {},
        modalTitle: '',
        showRegister: false,
        table1: {
          title: "Todos os Campeonatos",
          subTitle: "Listagem de todos os campeonatos em andamento (clique para ver detalhes ou se inscrever)",
          config: {
            clickable: true,
            eventName: 'showChampionshipModal'
          },
          columns: [
            { name: 'name', label: 'Nome' },
            { name: 'manager', label: 'Administrador' },
            { name: 'initialDate', label: 'Data de início' },
            { name: "totalTeams", label: 'Times inscritos' }
          ],
          data: [],
        },

      };
    },
    async mounted() {
      const token = sessionStorage.getItem('token');
      try {
        const response = await axios.get('http://localhost:3000/championships/manage', { validateStatus: () => true, headers: { Authorization: `Bearer ${token}` } });
        if (response.status === 200) {
          this.table1.data = response.data.championships
        }
        else {
          // this.failureMessage = response.data.message
          // this.showFailure = true
        }
      } catch (e) {
        console.log('e', e)
        // this.showFailure = true
      }
    },
    methods: {
      async showModal(index) {
        const token = sessionStorage.getItem('token');
        try {
          const response = await axios.get(`http://localhost:3000/championships/detail/${this.table1.data[index].id}`, { validateStatus: () => true, headers: { Authorization: `Bearer ${token}` } });
          if (response.status === 200) {
            this.championshipData = response.data.championship
            this.championshipData.teams = this.championshipData.teams.map(t => ({
              ...t,
              showDetails: false
            }))
            this.modalTitle = `Detalhes do campeonato: ${this.championshipData.name}`
            this.$bvModal.show('my-championship-modal')
          }
        } catch (e) {
          console.log('e', e)
        }
      },
      formatDate(isoDateString) {
        const date = new Date(isoDateString);

        const day = String(date.getUTCDate()).padStart(2, '0');
        const month = String(date.getUTCMonth() + 1).padStart(2, '0'); // Meses são baseados em 0, então somamos 1
        const year = date.getUTCFullYear();

        return `${day}-${month}-${year}`;
      },
      toggleDetails(index) {
        this.championshipData.teams[index].showDetails = !this.championshipData.teams[index].showDetails;
      }
    }
  };
</script>
<style scoped>
        .list {
            list-style-type: none;
            padding: 0;
        }
        .list li {
            margin: 10px 0;
            padding: 10px;
            border: 1px solid #ddd;
            position: relative;
        }
        .list .toggle-btn {
            cursor: pointer;
            position: absolute;
            right: 10px;
            top: 10px;
        }
        .details {
            display: none;
            padding: 10px;
            border-top: 1px solid #ddd;
        }
        .details.show {
            display: block;
        }
</style>