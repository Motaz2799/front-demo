<template>
  <Navbar></Navbar>
  <div class="row">
    <div class="col-md-3 col-lg-3">
      <Sidebar
        :appName="applicationName"
        @linkClicked="handleLinkClicked"
        @categoryChanged="handlecategoryChanged"
      ></Sidebar>
    </div>
    <div class="col-md-9 col-lg-9">
      <div v-if="selectedLink === 'Assessment' || Object.keys(categoryToshow).length !== 0">
        <AssessmentForm :currentCategory="categoryToshow" :appId="appId" />
      </div>
      <div v-else-if="selectedLink === 'Landing'">
        <landingDashboard :id="appId" :appName="applicationName"></landingDashboard>
      </div>
      <div v-else>
        <DashboardContent
          :selectedLink="selectedLink"
          :id="appId"
          :appName="applicationName"
        ></DashboardContent>
      </div>
    </div>
  </div>
  <Footer></Footer>
</template>
<script>
//import this.$http from 'this.$http'
import DashboardContent from '@/components/DashboardContent.vue'
import landingDashboard from '../components/landingDashboard.vue'
import Sidebar from '@/components/Sidebar.vue'
import Footer from '../components/Footer.vue'
import Navbar from '@/components/Navbar.vue'
import AssessmentForm from '../components/AssessmentForm.vue'

export default {
  components: {
    DashboardContent,
    Sidebar,
    Navbar,
    Footer,
    AssessmentForm,
    landingDashboard
  },
  data() {
    return {
      applicationName: '',
      selectedLink: 'Landing',
      appId: 0,
      categoryToshow: {}
    }
  },
  created() {
    this.fetchApplicationName()
  },
  methods: {
    fetchApplicationName() {
      const id = this.$route.params.id
      this.appId = id
      console.log(id + ' is the id')
      this.$http
        .get(`http://localhost:8080/api/v1/applications/${id}`)
        .then((response) => {
          this.applicationName = response.data.appName
        })
        .catch((error) => {
          console.error(error)
        })
    },
    handleLinkClicked(link) {
      this.selectedLink = link
      console.log(link)
    },
    handlecategoryChanged(category) {
      this.categoryToshow = category
      console.log(category)
    }
  }
}
</script>
