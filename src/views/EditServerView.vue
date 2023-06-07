<template>
  <div>
    <div class="container">
      <div id="errorContainerEditServer"></div>
      <div class="mt-5">
        <div class="row justify-content-center">
          <div class="col-md-6">
            <label for="dc_select" class="form-label">Select a Datacenter:</label>
            <ComboBox
              :options="datacenters"
              :multiple="false"
              @option-selected="onDatacenterSelected"
              :defaultValue="defaultValueDC"
            ></ComboBox>
          </div>
          <div class="col-md-6">
            <label for="env-select" class="form-label">Select an Environment:</label>
            <ComboBox
              :options="environments"
              :multiple="false"
              @option-selected="onEnvironmentSelected"
              :defaultValue="defaultValueENV"
            ></ComboBox>
          </div>
        </div>
      </div>
      <form class="my-4" v-if="idServ !== 0">
        <div v-for="(field, index) in formFields" :key="index" class="mb-3">
          <template v-if="field.type === 'text'">
            <label class="form-label">
              {{ field.label }}
              <span v-if="field.required" class="required">*</span>
            </label>
            <input
              :type="field.type"
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
            />
          </template>
          <template v-else-if="field.type === 'textarea'">
            <label class="form-label">
              {{ field.label }}
              <span v-if="field.required" class="required">*</span>
            </label>
            <textarea
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
            ></textarea>
          </template>
          <template v-else-if="field.type === 'number'">
            <label class="form-label">
              {{ field.label }}
              <span v-if="field.required" class="required">*</span>
            </label>
            <input
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
              type="number"
            />
          </template>
        </div>
        <button @click.prevent="submitFormServer" class="btn btn-primary">Next</button>
      </form>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Footer from '@/components/Footer.vue'
//import this.$http from 'this.$http'
import ComboBox from '../components/ComboBox.vue'

const FORM_CONFIGS = {
  applicationsView: [
    { name: 'appName', label: 'Application Name', type: 'text', required: true },
    {
      name: 'appDescription',
      label: 'Application Description',
      type: 'textarea',
      required: true,
      style: { height: '100px' }
    }
  ],
  serversView: [
    { name: 'datacenter', label: 'Datacenter', type: 'selectDCs', required: false },
    { name: 'environment', label: 'Environment', type: 'selectENVs', required: false },

    { name: 'serverName', label: 'Server Name', type: 'text', required: true },
    { name: 'ipAddress', label: 'Adress IP', type: 'text', required: true },
    { name: 'dataSource', label: 'Data Source', type: 'text', required: true },
    { name: 'type', label: 'Type', type: 'text', required: true },
    { name: 'currentRamGb', label: 'Ram in GB', type: 'number', required: true },
    { name: 'currentNumberOfCores', label: 'Numbre Of Cores', type: 'number', required: true },
    { name: 'operatingSystem', label: 'Operating System', type: 'text', required: true },
    {
      name: 'serverNotes',
      label: 'Notes',
      type: 'textarea',
      required: true,
      style: { height: '100px' }
    }
  ]
}

export default {
  props: {
    idServ: Number
  },
  components: {
    Navbar,
    Footer,
    ComboBox
  },
  data() {
    return {
      formData: {},
      formFields: [],
      endpoint: '',
      datacenters: [],
      environments: [],
      defaultValueDC: null,
      defaultValueENV: null,
      selectedDataCenter: 0,
      selectedEnvironment: 0,
      formDataMinusEnvDc: {}
    }
  },
  watch: {
    idServ: {
      immediate: true,
      handler(newIdServ) {
        if (newIdServ !== 0) {
          this.loadData(newIdServ)
        }
      }
    }
  },

  created() {
    const formConfig = FORM_CONFIGS.serversView
    this.formFields = formConfig || []

    this.endpoint = 'http://localhost:8080/api/v1/servers'
  },
  mounted() {
    this.$http.get('http://localhost:8080/api/v1/environments/non-archived').then((response) => {
      const Environments = response.data.map((environment) => {
        return {
          id: environment.id,
          name: environment.environmentName
        }
      })
      this.environments = Environments
    }),
      this.$http.get('http://localhost:8080/api/v1/datacenters/non-archived').then((response) => {
        const DCs = response.data.map((datacenter) => {
          return {
            id: datacenter.id,
            name: datacenter.name
          }
        })
        this.datacenters = DCs
      })

    if (this.idServ !== 0) {
      this.$http
        .get(`${this.endpoint}/${this.idServ}`)
        .then((response) => {
          this.formData = response.data
          this.defaultValueDC = {
            id: this.formData.datacenter.id,
            name: this.formData.datacenter.name
          }
          this.selectedDataCenter = this.formData.datacenter.id
          this.defaultValueENV = {
            id: this.formData.environment.id,
            name: this.formData.environment.environmentName
          }
          this.selectedEnvironment = this.formData.environment.id
        })
        .catch((error) => {
          console.log(error)
        })
    }
  },

  methods: {
    loadData(idDb) {
      this.$http
        .get(`${this.endpoint}/${idDb}`)
        .then((response) => {
          this.formData = response.data
          this.defaultValueDC = {
            id: this.formData.datacenter.id,
            name: this.formData.datacenter.name
          }
          this.selectedDataCenter = this.formData.datacenter.id
          this.defaultValueENV = {
            id: this.formData.environment.id,
            name: this.formData.environment.environmentName
          }
          this.selectedEnvironment = this.formData.environment.id
        })
        .catch((error) => {
          console.log(error)
        })
    },
    envDcAdd(resp, dcId, envId) {
      return new Promise((resolve) => {
        if (dcId && envId) {
          this.$http
            .put(`http://localhost:8080/api/v1/servers/${resp}/datacenter/link/${dcId}`)
            .then(() => {
              this.$http
                .put(`http://localhost:8080/api/v1/servers/${resp}/environment/link/${envId}`)
                .then(() => {
                  resolve()
                })
                .catch(console.error)
            })
            .catch(console.error)
        } else if (dcId) {
          this.$http
            .put(`http://localhost:8080/api/v1/servers/${resp}/datacenter/link/${dcId}`)
            .catch(console.error)
        } else if (envId) {
          this.$http
            .put(`http://localhost:8080/api/v1/servers/${resp}/environment/link/${envId}`)
            .catch(console.error)
        } else {
          console.log('nothing to map')
        }
      })
    },
    submitForm() {
      for (const field of this.formFields) {
        if (field.required && !this.formData[field.name]) {
          const errorMessage = `<div class="alert alert-danger" role="alert" >
      <span class="alert-icon"><span class="visually-hidden">Warning</span></span>
      <p style="font-weight:500; height:8px;">${field.label} is required</p>
    </div>`
          document.getElementById('errorContainerEditServer').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    submitFormServer() {
      if (this.submitForm()) {
        if (this.selectedDataCenter !== 0 && this.selectedEnvironment !== 0) {
          this.envDcAdd(this.idServ, this.selectedDataCenter, this.selectedEnvironment)
            .then(() => {
              this.formData.datacenter = this.datacenters.find(
                (dc) => dc.id === this.selectedDataCenter
              )
              this.formData.environment = this.environments.find(
                (env) => env.id === this.selectedEnvironment
              )
              this.$http
                .put(`${this.endpoint}/${this.idServ}`, this.formData)
                .then((response) => {
                  console.log(response.data)
                  alert('Server ' + this.formData.serverName + ' has been updated')
                  window.location.reload()
                  this.$router.push({ path: '/servers' })
                })
                .catch((error) => {
                  console.log(error)
                })
            })
            .catch((error) => {
              console.log(error)
            })
        } else {
          this.$http
            .put(`${this.endpoint}/${this.idServ}`, this.formData)
            .then((response) => {
              console.log(response.data)
              alert('Server ' + this.formData.serverName + ' has been updated')
              window.location.reload()
              this.$router.push({ path: '/servers' })
            })
            .catch((error) => {
              console.log(error)
            })
        }
      }
    },
    onDatacenterSelected(selectedOption) {
      this.selectedDataCenter = selectedOption.id
    },
    onEnvironmentSelected(selectedOption) {
      this.selectedEnvironment = selectedOption.id
    }
  }
}
</script>

<style scoped>
.required {
  color: red;
}
</style>

<style>
.form-label {
  font-weight: bold;
}

.form-control {
  margin-bottom: 1rem;
}

.btn-primary {
  margin-top: 1rem;
}
</style>
