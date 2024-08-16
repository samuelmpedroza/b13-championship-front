<template>
  <div class="wrapper" :class="showModal ? 'blur-background' : ''">
      <side-bar>
          <template slot="links">
              <!-- <sidebar-link to="/dashboard" name="Visão geral" icon="ti-panel" /> -->
              <sidebar-link to="/championships" name="Ver Campeonatos" icon="ti-view-list-alt" />
              <sidebar-link to="/new-championship" name="Novo Campeonato" icon="ti-plus" />
              <sidebar-link to="/my-championships" name="Gerenciar campeonatos" icon="ti-clip" />
              <!-- <sidebar-link to="/dashboard" name="Dashboard" icon="ti-panel" /> -->
              <!-- <sidebar-link to="/stats" name="User Profile" icon="ti-user" /> -->
              <!-- <sidebar-link
          to="/table-list"
          name="Table List"
          icon="ti-view-list-alt"
        />
        <sidebar-link to="/typography" name="Typography" icon="ti-text" />
        <sidebar-link to="/icons" name="Icons" icon="ti-pencil-alt2" />
        <sidebar-link to="/maps" name="Map" icon="ti-map" />
        <sidebar-link to="/notifications" name="Notifications" icon="ti-bell" /> -->
          </template>
          <mobile-menu>
              <li class="nav-item">
                  <a class="nav-link">
                      <i class="ti-panel"></i>
                      <p>Stats</p>
                  </a>
              </li>
              <drop-down class="nav-item" title="5 Notifications" title-classes="nav-link" icon="ti-bell">
                  <a class="dropdown-item">Notification 1</a>
                  <a class="dropdown-item">Notification 2</a>
                  <a class="dropdown-item">Notification 3</a>
                  <a class="dropdown-item">Notification 4</a>
                  <a class="dropdown-item">Another notification</a>
              </drop-down>
              <li class="nav-item">
                  <a class="nav-link">
                      <i class="ti-settings"></i>
                      <p>Settings</p>
                  </a>
              </li>
              <li class="divider"></li>
          </mobile-menu>
      </side-bar>
      <div class="main-panel">
          <top-navbar></top-navbar>

          <dashboard-content @click.native="toggleSidebar"> </dashboard-content>

          <!-- <content-footer></content-footer> -->
          <b-modal id="my-modal" hide-header no-close-on-esc no-close-on-backdrop ok-only
              :ok-title="showRegister ? 'Criar Conta' : 'Acessar'" ok-variant="info" @ok="okHandler">
              <div v-if="!showRegister">
                  <h4>Acessar Conta</h4>
                  <b-alert variant="success" :show="showSuccess">Conta criada com sucesso. Faça login para
                      continuar</b-alert>
                  <b-form-input v-model="username" type="email" placeholder="Email"></b-form-input>
                  <b-form-input v-model="password" type="password" placeholder="Senha" class="mt-3"></b-form-input>
                  <!-- <b-row align-h="center" class="mt-1"> Esqueci minha senha </b-row> -->
                  <p class="mt-1">Ainda não possui uma conta? <b-link @click="showRegister = true">Cadastrar</b-link>
                  </p>
              </div>
              <div v-else>
                  <h4>Criar Conta</h4>
                  <b-alert variant="danger" :show="showFailure">{{ failureMessage }}</b-alert>
                  <b-form-input v-model="username" type="email" placeholder="Email"></b-form-input>
                  <b-form-input v-model="password" type="password" placeholder="Senha" class="mt-3"></b-form-input>
                  <b-form-input v-model="confirmPassword" type="password" placeholder="Confirmação de Senha"
                      class="mt-3"></b-form-input>
                  <p class="mt-1">Já possui uma conta? <b-link @click="showRegister = false">Fazer login</b-link></p>
              </div>
          </b-modal>
      </div>
  </div>
</template>
<style lang="scss"></style>
<script>
  import TopNavbar from "./TopNavbar.vue";
  import ContentFooter from "./ContentFooter.vue";
  import DashboardContent from "./Content.vue";
  import MobileMenu from "./MobileMenu";
  import { Modal } from 'bootstrap'
  import axios from 'axios';

  export default {
      components: {
          TopNavbar,
          ContentFooter,
          DashboardContent,
          MobileMenu,
      },
      data() {
          return {
              showRegister: false,
              username: '',
              password: '',
              confirmPassword: '',
              showModal: true,
              showSuccess: false,
              showFailure: false,
              failureMessage: ''
          }
      },
      mounted() {
          const token = sessionStorage.getItem('token');
          if (!token) {
              this.$bvModal.show('my-modal')
          } else {
              this.showModal = false
          }
      },
      methods: {
          toggleSidebar() {
              if (this.$sidebar.showSidebar) {
                  this.$sidebar.displaySidebar(false);
              }
          },
          async okHandler(bvModalEvent) {
              bvModalEvent.preventDefault()
              if (this.showRegister) {
                  await this.signup()
              }
              else {
                  await this.login()
              }
          },
          async signup() {
              this.showFailure = false
              const data = {
                  username: this.username,
                  password: this.password,
                  confirmPassword: this.confirmPassword
              }
              try {
                  const response = await axios.post('http://localhost:3000/auth/signup', data, { validateStatus: () => true });
                  if (response.status === 201) {
                      this.showSuccess = true;
                      this.showRegister = false;
                  }
                  else {
                      this.failureMessage = response.data.message
                      this.showFailure = true
                  }
              } catch (e) {
                  this.showFailure = true
              }
          },
          async login() {
              const data = {
                  username: this.username,
                  password: this.password,
              }
              try {
                  const response = await axios.post('http://localhost:3000/auth/login', data, { validateStatus: () => true });
                  if (response.status === 200) {
                      sessionStorage.setItem('token', response.data.token);
                      this.$nextTick(() => {
                          this.$bvModal.hide('my-modal')
                          this.showModal = false
                          this.$router.push({ path: 'championships' })
                      })
                  }
                  else {
                      this.failureMessage = response.data.message
                      this.showFailure = true
                  }
              } catch (e) {
                  this.showFailure = true
              }

          }
      },
  };
</script>

<style scoped>
  .blur-background {
      filter: blur(5px);
  }
</style>