<template>
  <div>
    <div class="container">
      <div id="errorENVEdit"></div>
      <form class="mt-4">
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
          <template v-else-if="field.type === 'select'">
            <select v-model="formData[field.name]" class="form-select" :style="field.style">
              <option v-for="option in field.options" :key="option.value" :value="option.value">
                {{ option.label }}
              </option>
            </select>
          </template>
        </div>
        <button @click.prevent="submitFormENV" class="btn btn-primary">Next</button>
      </form>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Footer from '@/components/Footer.vue'
//import this.$http from 'this.$http'

const FORM_CONFIGS = {
  environmentView: [
    { name: 'environmentName', label: 'ENV Name', type: 'text', required: true },
    { name: 'description', label: 'notes', type: 'textarea', required: true },
    {
      name: 'type',
      label: 'Type',
      type: 'select',
      required: true,
      options: [
        { label: 'PROD', value: 'PROD' },
        { label: 'PREPROD', value: 'PREPROD' },
        { label: 'UAT', value: 'UAT' },
        { label: 'QA', value: 'QA' },
        { label: 'DEV', value: 'DEV' },
        { label: 'STAGING', value: 'STAGING' },
        { label: 'TEST', value: 'TEST' }
      ]
    }
  ]
}

export default {
  components: {
    Navbar,
    Footer
  },
  props: {
    idEnv: Number
  },
  data() {
    return {
      formData: {},
      formFields: [],
      endpoint: ''
    }
  },
  watch: {
    idEnv: {
      immediate: true,
      handler(newIdEnv) {
        this.loadData(newIdEnv)
      }
    }
  },

  created() {
    const formConfig = FORM_CONFIGS.environmentView
    this.formFields = formConfig || []

    this.endpoint = 'http://localhost:8080/api/v1/environments'
  },

  methods: {
    loadData(idEnv) {
      if (idEnv !== 0) {
        this.$http
          .get(`${this.endpoint}/${idEnv}`)
          .then((response) => {
            this.formData = response.data
          })
          .catch((error) => {
            console.log(error)
          })
      }
    },
    submitForm() {
      // Check if required fields are empty
      for (const field of this.formFields) {
        if (field.required && !this.formData[field.name]) {
          const errorMessage = `<div class="alert alert-danger" role="alert" >
      <span class="alert-icon"><span class="visually-hidden">Warning</span></span>
      <p style="font-weight:500; height:8px;">${field.label} is required</p>
    </div>`
          document.getElementById('errorENVEdit').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    submitFormENV() {
      // Check if required fields are empty
      if (this.submitForm()) {
        this.$http
          .put(`${this.endpoint}/${this.idEnv}`, this.formData)
          .then((response) => {
            console.log(response.data)
            alert('Environment' + this.formData.environmentName + 'has been updated')
            window.location.reload()
            this.$router.push({ path: '/environments' })
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
