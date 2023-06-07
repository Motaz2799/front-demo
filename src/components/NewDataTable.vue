<template>
  <div>
    <table class="table table-responsive table-hover">
      <thead>
        <tr>
          <th v-for="header in headers" :key="header">{{ header.label }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in paginatedItems" :key="item.id">
          <td v-for="header in headers" :key="header.name">
            <template v-if="header.name === 'environment'">
              <template v-if="item[header.name]">
                {{ item[header.name].environmentName }}
              </template>
              <template v-else>
                <img
                  src="../assets/Add.svg"
                  type="button"
                  @click="addEnv(item.id)"
                  data-bs-toggle="modal"
                  data-bs-target="#AddEnvironment"
                  alt="Add Environment"
                  class="img-fluid add-image"
                />
              </template>
            </template>
            <template
              v-else-if="header.name === 'applicationSrc' || header.name === 'applicationTarget'"
            >
              {{ item[header.name].appName }}
            </template>
            <template v-else-if="header.name === 'datacenter'">
              <template v-if="item[header.name]">
                {{ item[header.name].name }}
              </template>
              <template v-else>
                <img
                  src="../assets/Add.svg"
                  type="button"
                  @click="addDc(item.id)"
                  data-bs-toggle="modal"
                  data-bs-target="#AddDatacenter"
                  alt="Add Datacenter"
                  class="img-fluid add-image"
                />
              </template>
            </template>
            <template v-else>
              {{ item[header.name] }}
            </template>
          </td>
          <td>
            <div class="btn-group">
              <button
                class="btn btn-secondary btn-sm dropdown-toggle"
                type="button"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                Actions
              </button>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" @click="removeItem(item.id)">Remove </a></li>
              </ul>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <nav aria-label="Page navigation example">
      <ul class="pagination justify-content-end">
        <li class="page-item" :class="{ disabled: page === 1 }">
          <a class="page-link" href="#" @click.prevent="changePage(page - 1)">Previous</a>
        </li>
        <li
          v-for="pageNumber in totalPages"
          :key="pageNumber"
          class="page-item"
          :class="{ active: pageNumber === page }"
        >
          <a class="page-link" href="#" @click.prevent="changePage(pageNumber)">{{ pageNumber }}</a>
        </li>
        <li class="page-item" :class="{ disabled: page === totalPages }">
          <a class="page-link" href="#" @click.prevent="changePage(page + 1)">Next</a>
        </li>
      </ul>
    </nav>
  </div>

  <div
    class="modal fade"
    id="AddEnvironment"
    aria-hidden="true"
    aria-labelledby="exampleModalToggleLabel"
    tabindex="-1"
  >
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Environment</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal">
            <span class="visually-hidden">Close</span>
          </button>
        </div>
        <div class="modal-body">
          <Combobox
            :options="environments"
            :multiple="false"
            @option-selected="onEnvironmentSelected"
            addRessource="Add Environment"
          ></Combobox>
        </div>
        <div class="modal-footer">
          <button @click="AddEnvironment" class="btn btn-primary">Submit</button>
        </div>
      </div>
    </div>
  </div>

  <div
    class="modal fade"
    id="AddDatacenter"
    aria-hidden="true"
    aria-labelledby="exampleModalToggleLabel"
    tabindex="-1"
  >
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Datacenter</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal">
            <span class="visually-hidden">Close</span>
          </button>
        </div>
        <div class="modal-body">
          <Combobox
            :options="datacenters"
            :multiple="false"
            @option-selected="onDatacenterSelected"
          ></Combobox>
        </div>
        <div class="modal-footer">
          <button @click="AddDatacenter" class="btn btn-primary">Submit</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
//import this.$http from 'this.$http'
import Combobox from './ComboBox.vue'

export default {
  components: {
    Combobox
  },
  props: {
    appId: Number,
    headers: {
      type: Array,
      required: true
    },
    apiUrl: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      serverID: 0,
      environmentToAdd: 0,
      datacenterToAdd: 0,
      items: [],
      noEnv: 'No Environment',
      noDc: 'No Datacenter',
      datacenters: [],
      environments: [],
      page: 1,
      pages: 0,
      pageSize: 5
    }
  },
  watch: {
    apiUrl: {
      immediate: true,
      handler() {
        this.fetchItems()
      }
    }
  },
  computed: {
    paginatedItems() {
      return this.paginateItems()
    },
    totalPages() {
      return Math.ceil(this.items.length / this.pageSize)
    }
  },

  methods: {
    paginateItems() {
      const startIndex = (this.page - 1) * this.pageSize
      const endIndex = startIndex + this.pageSize
      return this.items.slice(startIndex, endIndex)
    },
    changePage(pageNumber) {
      if (pageNumber >= 1) {
        this.page = pageNumber
      } else {
        this.page = 1
      }
    },
    removeItem(idRessource) {
      if (this.apiUrl.includes('servers')) {
        const confirmed = window.confirm('Are you sure you want to delete this resource?')
        if (!confirmed) {
          return
        }
        this.$http
          .put(
            `http://localhost:8080/api/v1/applications/${this.appId}/server/unlink/${idRessource}`
          )
          .then(() => {
            window.location.reload()
          })
          .catch((error) => {
            console.error(error)
          })
      } else if (this.apiUrl.includes('interfaces')) {
        const confirmed = window.confirm('Are you sure you want to delete this resource?')
        if (!confirmed) {
          return
        }
        this.$http
          .delete(`http://localhost:8080/api/v1/interfaces/${idRessource}`)
          .then(() => {
            window.location.reload()
          })
          .catch((error) => {
            console.error(error)
          })
      }
    },
    addEnv(id) {
      this.serverID = id
      console.log(id)
      this.$http.get('http://localhost:8080/api/v1/environments/all').then((response) => {
        const Environments = response.data.map((environment) => {
          return {
            id: environment.id,
            name: environment.environmentName
          }
        })
        this.environments = Environments
      })
    },
    addDc(id) {
      this.serverID = id
      console.log(id)
      this.$http.get('http://localhost:8080/api/v1/datacenters/all').then((response) => {
        const DCs = response.data.map((datacenter) => {
          return {
            id: datacenter.id,
            name: datacenter.name
          }
        })
        this.datacenters = DCs
      })
    },
    fetchItems() {
      this.$http
        .get(this.apiUrl)
        .then((response) => {
          this.items = response.data
          console.log(this.items)
        })
        .catch((error) => {
          console.error(error)
        })
    },
    onEnvironmentSelected(env) {
      this.environmentToAdd = env.id
    },
    onDatacenterSelected(Dc) {
      this.datacenterToAdd = Dc.id
    },
    AddEnvironment() {
      this.$http
        .put(
          `http://localhost:8080/api/v1/servers/${this.serverID}/environment/link/${this.environmentToAdd}`
        )
        .catch(console.error)
      window.location.reload()
    },
    AddDatacenter() {
      this.$http
        .put(
          `http://localhost:8080/api/v1/servers/${this.serverID}/datacenter/link/${this.datacenterToAdd}`
        )
        .catch(console.error)
      window.location.reload()
    }
  }
}
</script>
<style>
.add-image {
  width: 27px;
  height: 27px;
}
.wide-column {
  display: flex;
}
</style>
