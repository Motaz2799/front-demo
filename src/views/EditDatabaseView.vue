<template>
  <div>
    <div class="container">
      <div id="errorContainerEditDB"></div>
      <form class="mt-5">
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
        <button @click.prevent="submitFormDB" class="btn btn-primary">Next</button>
      </form>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Footer from '@/components/Footer.vue'
//import this.$http from 'this.$http'

const FORM_CONFIGS = {
  databasesView: [
    { name: 'nameDb', label: 'Database Name', type: 'text', required: true },
    { name: 'versionDb', label: 'Database Version', type: 'text', required: true }
  ]
}

export default {
  components: {
    Navbar,
    Footer
  },
  props: {
    idDb: Number
  },
  data() {
    return {
      formData: {},
      formFields: [],
      endpoint: ''
    }
  },
  watch: {
    idDb: {
      immediate: true,
      handler(newIdDb) {
        this.loadData(newIdDb)
      }
    }
  },

  created() {
    const formConfig = FORM_CONFIGS.databasesView
    this.formFields = formConfig || []

    this.endpoint = 'http://localhost:8080/api/v1/databases'
  },

  methods: {
    loadData(idDb) {
      if (idDb !== 0) {
        this.$http
          .get(`${this.endpoint}/${idDb}`)
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
          document.getElementById('errorContainerEditDB').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    submitFormDB() {
      if (this.submitForm()) {
        this.$http
          .put(`${this.endpoint}/${this.idDb}`, this.formData)
          .then((response) => {
            console.log(response.data)
            alert('Database has been updated')
            window.location.reload()
            this.$router.push({ path: '/databases' })
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
