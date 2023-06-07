<template>
  <div class="mb-3">
    <div class="container">
      <div id="errorContainerContact"></div>
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
        <label class="form-label">Select Applications</label>
        <ComboBox :options="applications" @option-selected="onApplicationSelected"></ComboBox>
        <button @click.prevent="submitApplications" class="btn btn-primary">Next</button>
      </form>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import Footer from '@/components/Footer.vue'
//import this.$http from 'this.$http'
import ComboBox from '../components/ComboBox.vue'

export default {
  components: {
    Navbar,
    Footer,
    ComboBox
  },

  data() {
    return {
      applications: [],
      databases: [],
      selectedApplications: [],
      formData: {},
      responseContact: {},
      formFields: [
        { name: 'fullName', label: 'Contact Name', type: 'text', required: true },
        { name: 'title', label: 'Contact Title', type: 'text', required: true },
        { name: 'department', label: 'Contact department', type: 'text', required: true },
        { name: 'email', label: 'Contact email', type: 'text', required: true }
      ],
      currentStep: 1,
      steps: [
        { title: 'Map Applications', isComplete: true },
        { title: 'Create Contacts', isComplete: false }
      ],
      addLink: 'serversView' // Ajouter la propriété addLink ici
    }
  },
  created() {
    this.$http
      .get('http://localhost:8080/api/v1/applications/all')
      .then((response) => {
        const APs = response.data.map((application) => {
          return {
            id: application.id,
            name: application.appName
          }
        })
        this.applications = APs
      })
      .catch((error) => {
        console.log(error)
      })
  },
  methods: {
    submitApplications() {
      if (this.submitForm() == true) {
        this.responseContact = JSON.stringify(this.formData)
        if (this.selectedApplications.length === 0) {
          console.log(this.responseContact)
          this.$http
            .post('http://localhost:8080/api/v1/contacts', this.responseContact, {
              headers: {
                'Content-Type': 'application/json'
              }
            })
            .then((response) => {
              console.log(response.data)
              alert('Resource created successfully!')
              window.location.reload()
              this.$router.push('/contacts')
            })
            .catch((error) => {
              console.error(error)
            })
        } else {
          console.log(this.responseContact)
          this.$http
            .post('http://localhost:8080/api/v1/contacts', this.responseContact, {
              headers: {
                'Content-Type': 'application/json'
              }
            })
            .then((response) => {
              const contactID = response.data.id
              this.selectedApplications.forEach((application) => {
                const applicationId = application.id
                this.$http
                  .put(
                    `http://localhost:8080/api/v1/contacts/${contactID}/application/link/${applicationId}`,
                    this.responseContact,
                    {
                      headers: {
                        'Content-Type': 'application/json'
                      }
                    }
                  )
                  .then(() => {
                    alert('Resource created successfully!')
                    window.location.reload()
                    this.$router.push('/contacts')
                  })
              })
            })
        }
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
          document.getElementById('errorContainerContact').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    onApplicationSelected(selectedOptions) {
      this.selectedApplications = selectedOptions
    }
  }
}
</script>
