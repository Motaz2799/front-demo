<template>
  <div>
    <div class="container">
      <div id="errorInterfaceEdit"></div>
      <div class="mt-5">
        <div class="row align-items-center">
          <div class="col-md-6">
            <label for="dc_select" class="form-label">Select an application:</label>
            <ComboBox
              :options="applicationsSource"
              :multiple="false"
              @option-selected="onSourceAppSelected"
              :defaultValue="defaultValueSource"
            ></ComboBox>
          </div>
          <div class="col-md-6">
            <label for="dc_select" class="form-label">Select an application:</label>
            <ComboBox
              :options="applicationsTarget"
              :multiple="false"
              @option-selected="onTargetAppSelected"
              :defaultValue="defaultValueTarget"
            ></ComboBox>
          </div>
        </div>
      </div>
      <form class="my-4">
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
          <template v-else-if="field.type === 'select'">
            <label class="form-label">
              {{ field.label }}
              <span v-if="field.required" class="required">*</span>
            </label>
            <select
              v-model="formData[field.name]"
              class="form-select"
              :style="field.style"
              :required="field.required"
            >
              <option v-for="option in field.options" :key="option.value" :value="option.value">
                {{ option.label }}
              </option>
            </select>
          </template>
        </div>
        <button @click.prevent="submitFormInterface" class="btn btn-primary">Next</button>
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
  props: {
    idInterface: Number
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
      applications: [],
      selectedAppSrc: 0,
      selectedAppTarget: 0,
      defaultValueSource: null,
      defaultValueTarget: null,
      applicationsSource: [],
      applicationsTarget: []
    }
  },
  watch: {
    idInterface: {
      immediate: true,
      handler(newIdInterface) {
        if (newIdInterface !== 0) {
          this.loadData(newIdInterface)
        }
      }
    }
  },

  mounted() {
    const FORM_CONFIGS = {
      interfacesView: [
        { name: 'applicationSrc', label: 'Source app', type: 'selectApps', required: true },
        { name: 'applicationTarget', label: 'Target app', type: 'selectApps', required: true },
        { name: 'protocol', label: 'Protocol', type: 'text', required: true },
        { name: 'dataFormat', label: 'Data format', type: 'text', required: true },
        {
          name: 'notes',
          label: 'Notes',
          type: 'textarea',
          required: true,
          style: { height: '100px' }
        },
        {
          name: 'flow',
          label: 'Flow',
          type: 'select',
          required: true,
          options: [
            { label: 'EXTERNAL', value: 'EXTERNAL' },
            { label: 'INTERNAL', value: 'INTERNAL' }
          ]
        },
        {
          name: 'frequency',
          label: 'Frequency',
          type: 'select',
          required: true,
          options: [
            { label: 'HOURLY', value: 'HOURLY' },
            { label: 'DAILY', value: 'DAILY' },
            { label: 'WEEKLY', value: 'WEEKLY' },
            { label: 'MONTHLY', value: 'MONTHLY' },
            { label: 'ON_DEMAND', value: 'ON_DEMAND' }
          ]
        },
        {
          name: 'processingMode',
          label: 'Processing mode',
          type: 'select',
          required: true,
          options: [
            { label: 'REAL_TIME', value: 'REAL_TIME' },
            { label: 'BATCH', value: 'BATCH' }
          ]
        }
      ]
    }

    const formConfig = FORM_CONFIGS.interfacesView
    this.formFields = formConfig || []

    this.endpoint = 'http://localhost:8080/api/v1/interfaces'
  },

  methods: {
    loadData(id) {
      if (id !== 0) {
        this.$http.get('http://localhost:8080/api/v1/applications/all').then((response) => {
          const apps = response.data.map((application) => {
            return {
              id: application.id,
              name: application.appName
            }
          })
          this.applications = apps
          this.applicationsTarget = this.application
          this.applicationsTarget = this.applications.filter(
            (app) => app.id !== this.formData.applicationSrc.id
          )
          this.applicationsSource = this.applications
          this.applicationsSource = this.applications.filter(
            (app) => app.id !== this.formData.applicationTarget.id
          )
        })

        this.$http
          .get(`${this.endpoint}/${id}`)
          .then((response) => {
            this.formData = response.data
            this.defaultValueSource = {
              id: this.formData.applicationSrc.id,
              name: this.formData.applicationSrc.appName
            }
            this.defaultValueTarget = {
              id: this.formData.applicationTarget.id,
              name: this.formData.applicationTarget.appName
            }
            this.selectedAppSrc = this.formData.applicationSrc.id
            this.selectedAppTarget = this.formData.applicationTarget.id
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
          document.getElementById('errorInterfaceEdit').innerHTML = errorMessage

          return false
        }
      }
      return true
    },
    submitFormInterface() {
      if (this.submitForm()) {
        const idsrc = this.selectedAppSrc
        const idtarget = this.selectedAppTarget
        const protocol = this.formData.protocol
        const dataFormat = this.formData.dataFormat
        const notes = this.formData.notes
        const flow = this.formData.flow
        const frequency = this.formData.frequency
        const processingMode = this.formData.processingMode
        const x = {
          applicationSrc: idsrc.id ? { id: idsrc.id } : { id: idsrc },
          applicationTarget: idtarget.id ? { id: idtarget.id } : { id: idtarget },
          protocol: protocol,
          dataFormat: dataFormat,
          notes: notes,
          flow: flow,
          frequency: frequency,
          processingMode: processingMode
        }
        this.$http
          .put(`${this.endpoint}/${this.idInterface}`, x)
          .then((response) => {
            console.log(response.data)
            alert('Interface' + this.formData + 'has been updated')
            window.location.reload()
            this.$router.push({ path: '/interfaces' })
          })
          .catch((error) => {
            console.log(error)
          })
      }
    },
    onSourceAppSelected(selectedOption) {
      this.selectedAppSrc = selectedOption.id
      this.applicationsTarget = this.applications.filter((app) => app.id !== selectedOption.id)
    },
    onTargetAppSelected(selectedOption) {
      this.selectedAppTarget = selectedOption.id
      this.applicationsSource = this.applications.filter((app) => app.id !== selectedOption.id)
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
