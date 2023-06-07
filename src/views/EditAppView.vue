<template>
  <div>
    <div class="container">
      <div id="errorContainerEditApp"></div>
      <form class="my-4">
        <div v-for="(field, index) in formFields" :key="index" class="mb-3">
          <label class="form-label">
            {{ field.label }}
            <span v-if="field.required" class="required">*</span>
          </label>
          <template v-if="field.type === 'text'">
            <input
              :type="field.type"
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
            />
          </template>
          <template v-else-if="field.type === 'textarea'">
            <textarea
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
            ></textarea>
          </template>
          <template v-else-if="field.type === 'number'">
            <input
              v-model="formData[field.name]"
              class="form-control"
              :style="field.style"
              type="number"
            />
          </template>
        </div>
        <button @click.prevent="submitFormApp" class="btn btn-primary">Next</button>
      </form>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Footer from '@/components/Footer.vue'
//import this.$http from 'this.$http'

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
    { name: 'serverName', label: 'Server Name', type: 'text', required: true },
    { name: 'dataSource', label: 'Data Source', type: 'text', required: true },
    { name: 'type', label: 'Type', type: 'text', required: true },
    { name: 'currentRamGb', label: 'Ram in GB', type: 'number', required: true }
  ],
  contactsView: [
    { name: 'fullName', label: 'Contact Name', type: 'text', required: true },
    { name: 'title', label: 'Contact Title', type: 'text', required: true },
    { name: 'department', label: 'Contact department', type: 'text', required: true },
    { name: 'email', label: 'Contact email', type: 'text', required: true }
  ]
}

export default {
  components: {
    Navbar,
    Footer
  },
  props: {
    idApp: Number
  },
  data() {
    return {
      formData: {},
      formFields: [],
      endpoint: ''
    }
  },
  watch: {
    idApp: {
      immediate: true,
      handler(newIdApp) {
        this.loadData(newIdApp)
      }
    }
  },
  created() {
    const formConfig = FORM_CONFIGS.applicationsView
    this.formFields = formConfig || []

    this.endpoint = 'http://localhost:8080/api/v1/applications'
  },

  methods: {
    loadData(idApp) {
      if (idApp !== 0) {
        this.$http
          .get(`${this.endpoint}/${idApp}`)
          .then((response) => {
            this.formData = response.data
          })
          .catch((error) => {
            console.log(error)
          })
      }
    },
    submitForm() {
      for (const field of this.formFields) {
        if (field.required && !this.formData[field.name]) {
          const errorMessage = `<div class="alert alert-danger" role="alert" >
      <span class="alert-icon"><span class="visually-hidden">Warning</span></span>
      <p style="font-weight:500; height:8px;">${field.label} is required</p>
    </div>`
          document.getElementById('errorContainerEditApp').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    submitFormApp() {
      if (this.submitForm()) {
        this.$http
          .put(`${this.endpoint}/${this.idApp}`, this.formData)
          .then((response) => {
            console.log(response.data)
            alert('Application' + this.formData.appName + 'has been updated')
            window.location.reload()
            this.$router.push({ path: '/Applications' })
          })
          .catch((error) => {
            console.log(error)
          })
      }
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
