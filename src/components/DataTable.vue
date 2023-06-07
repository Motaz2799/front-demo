<template>
  <div>
    <caption class="wide-column">
      <div>{{ title }}</div>
      <div class="btn-group dropend">
        <button
          type="button"
          class="menuItem-active-link btn btn-sm btn-secondary dropdown-toggle"
          data-bs-toggle="dropdown"
          aria-expanded="false"
        >
          {{ button }}
        </button>
        <ul class="dropdown-menu">
          <li>
            <a
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggle"
              >Via excel</a
            >
          </li>
          <li>
            <a
              v-if="button === 'Add Database'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleDB"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Application'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleApplication"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Server'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleServer"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Contacts'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleContact"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Interface'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleInterface"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Datacenter'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleDatacenter"
              >Via form</a
            >
            <a
              v-else-if="button === 'Add Environments'"
              class="dropdown-item"
              type="button"
              data-bs-toggle="modal"
              data-bs-target="#exampleModalToggleEnv"
              >Via form</a
            >
          </li>
        </ul>
      </div>
    </caption>

    <input
      type="text"
      class="form-control"
      placeholder="Live Search..."
      v-model="searchQuery"
      style="width: 204px; height: 35px"
    />

    <table id="sortable" class="table table-hover tablesorter">
      <thead>
        <tr>
          <th
            class="cursor"
            v-for="(column, index) in visibleColumns"
            :key="index"
            @click="sortBy(column.name)"
          >
            {{ column.label }}
            <span
              v-if="sortColumn === column.name"
              v-bind:class="[sortDirection === 'asc' ? 'asc' : 'desc']"
            ></span>
          </th>
          <th class="d-flex justify-content-between align-items-center">
            <div class="btn-group">
              <button
                type="button"
                class="btn btn-secondary"
                data-bs-toggle="dropdown"
                data-bs-display="static"
                aria-expanded="false"
              >
                ...
              </button>
              <ul class="dropdown-menu dropdown-menu-end dropdown-menu-lg-start">
                <div v-for="(column, index) in columns" :key="index">
                  <label class="dropdown-item">
                    <input
                      type="checkbox"
                      v-model="visibleColumnsNames"
                      v-bind:value="column.name"
                    />
                    {{ column.label }}
                  </label>
                </div>
              </ul>
            </div>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in filteredData" v-bind:key="row.id">
          <td
            v-if="button === 'Add Application'"
            class="cursor"
            v-for="(column, index) in visibleColumns"
            :key="index"
            @click="goToDashbord(row.id)"
            title="Click to view dashboard application"
          >
            {{ row[column.name] }}
          </td>
          <td v-else class="cursor" v-for="(column, index) in visibleColumns" :key="index + 1">
            {{ row[column.name] }}
          </td>
          <td>
            <div class="btn-group">
              <button type="button" class="btn btn-secondary">Actions</button>
              <button
                type="button"
                class="btn btn-secondary dropdown-toggle dropdown-toggle-split"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                <span class="visually-hidden">Toggle Dropdown</span>
              </button>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" @click="deleteRessource(row.id)">Delete</a></li>
                <li>
                  <a
                    v-if="button === 'Add Database'"
                    @click="setIdDB(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditDB"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Server'"
                    @click="setIdServer(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditServer"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Contacts'"
                    @click="setIdContact(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditContact"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Interface'"
                    @click="setIdInterface(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditInterface"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Datacenter'"
                    @click="setIdDatacenter(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditDatacenter"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Environments'"
                    @click="setIdEnv(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditEnv"
                    >Edit</a
                  >
                  <a
                    v-else-if="button === 'Add Application'"
                    @click="setIdApp(row.id)"
                    class="dropdown-item"
                    data-bs-toggle="modal"
                    data-bs-target="#exampleModalToggleEditApp"
                    >Edit</a
                  >
                </li>

                <li v-if="title === 'List of Applications'">
                  <a class="dropdown-item" @click="onclickDownload(row)">Download</a>
                </li>
              </ul>
            </div>
          </td>
        </tr>
        <tr v-if="filteredData.length === 0">
          <td colspan="999">No matching items found.</td>
        </tr>
      </tbody>
    </table>

    <div id="AddExcel">
      <div
        class="modal fade"
        id="exampleModalToggle"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Upload Via Excel</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <UploadExcel></UploadExcel>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddApplication">
      <div
        class="modal fade"
        id="exampleModalToggleApplication"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Application</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <AddApplication @new-data="handleNewData"></AddApplication>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditApplication">
      <div
        class="modal fade"
        id="exampleModalToggleEditApp"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Application</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditApplication :idApp="idAppToEdit"></EditApplication>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddDataBase">
      <div
        class="modal fade"
        id="exampleModalToggleDB"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Database</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <MapDB></MapDB>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditDataBase">
      <div
        class="modal fade"
        id="exampleModalToggleEditDB"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Database</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditDB :idDb="idDbToEdit"></EditDB>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddServer">
      <div
        class="modal fade"
        id="exampleModalToggleServer"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Server</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <AddServerView></AddServerView>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditServer">
      <div
        class="modal fade"
        id="exampleModalToggleEditServer"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Server</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditServer :idServ="idServToEdit"></EditServer>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddContact">
      <div
        class="modal fade"
        id="exampleModalToggleContact"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Contact</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <AddContact></AddContact>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditContact">
      <div
        class="modal fade"
        id="exampleModalToggleEditContact"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Contact</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditContact :idContact="idContactToEdit"></EditContact>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddInterface">
      <div
        class="modal fade"
        id="exampleModalToggleInterface"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Add Interface</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <AddInterface></AddInterface>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditInterface">
      <div
        class="modal fade"
        id="exampleModalToggleEditInterface"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Interface</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditInterface :idInterface="idInterfaceToEdit"></EditInterface>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddDatacenter">
      <div
        class="modal fade"
        id="exampleModalToggleDatacenter"
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
              <AddDatacenter></AddDatacenter>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditDatacenter">
      <div
        class="modal fade"
        id="exampleModalToggleEditDatacenter"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Datacenter</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditDatacenter :idDc="idDCToEdit"></EditDatacenter>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showAddEnv">
      <div
        class="modal fade"
        id="exampleModalToggleEnv"
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
              <AddEnv></AddEnv>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showEditEnv">
      <div
        class="modal fade"
        id="exampleModalToggleEditEnv"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Edit Environment</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <EditEnv :idEnv="idEnvToEdit"></EditEnv>
            </div>
            <div class="modal-footer"></div>
          </div>
        </div>
      </div>
    </div>

    <div id="showColumns">
      <div
        class="modal fade"
        id="exampleModalToggleShow"
        aria-hidden="true"
        aria-labelledby="exampleModalToggleLabel"
        tabindex="-1"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title h5" id="exampleModalToggleLabel">Select Columns</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal">
                <span class="visually-hidden">Close</span>
              </button>
            </div>
            <div class="modal-body">
              <div v-for="(column, index) in columns" :key="index">
                <label>
                  <input type="checkbox" v-model="visibleColumnsNames" v-bind:value="column.name" />
                  {{ column.label }}
                </label>
              </div>
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-sm btn-primary"
                data-bs-dismiss="modal"
                @click="saveVisibleColumns"
              >
                Save
              </button>
              <button type="button" class="btn btn-sm btn-secondary" data-bs-dismiss="modal">
                Cancel
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import UploadExcel from '../views/UploadExcel.vue'
import MapDB from '../views/MapDB.vue'
import AddServerView from '../views/AddServerView.vue'
import AddContact from '../views/MapAppContacts.vue'
import AddInterface from '../views/AddInterfaceView.vue'
import AddDatacenter from '../views/AddDataCenterView.vue'
import AddEnv from '../views/AddEnvironmentView.vue'
import EditDB from '../views/EditDatabaseView.vue'
import EditServer from '../views/EditServerView.vue'
import EditContact from '../views/EditContactView.vue'
import EditInterface from '../views/EditInterfaceView.vue'
import EditDatacenter from '../views/EditDataCenterView.vue'
import EditEnv from '../views/EditEnvironmentView.vue'
import AddApplication from '../views/MapAppServers.vue'
import EditApplication from '../views/EditAppView.vue'
//import this.$http from 'this.$http'

export default {
  components: {
    UploadExcel,
    MapDB,
    AddServerView,
    AddContact,
    AddInterface,
    AddDatacenter,
    AddEnv,
    AddApplication,
    EditDB,
    EditServer,
    EditContact,
    EditInterface,
    EditDatacenter,
    EditEnv,
    EditApplication
  },
  name: 'DataTable',

  props: {
    endpoint: {
      type: String,
      required: true
    },
    delete: {
      type: String,
      required: true
    },
    edit: {
      type: String,
      required: true
    },
    columns: {
      type: Array,
      required: true
    },
    title: {
      type: String,
      default: 'Ressources List'
    },
    addLink: {
      type: String,
      default: ''
    },
    button: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      data: [],
      idDbToEdit: 0,
      idServToEdit: 0,
      idDCToEdit: 0,
      idEnvToEdit: 0,
      idAppToEdit: 0,
      idContactToEdit: 0,
      idInterfaceToEdit: 0,
      sortColumn: '',
      sortDirection: 'asc',
      visibleColumnsNames: [],
      noDC: 'no Dc ',
      noENV: 'no ENV ',
      searchQuery: ''
    }
  },
  created() {
    this.$http
      .get(this.endpoint)
      .then((response) => {
        console.log(response.data)
        //testing on delete !!! not endpoint
        if (this.delete == 'http://localhost:8080/api/v1/interfaces') {
          this.data = response.data.map((item) => ({
            id: item.id,
            applicationSrc: item.applicationSrc.appName,
            applicationTarget: item.applicationTarget.appName,
            protocol: item.protocol,
            dataFormat: item.dataFormat,
            notes: item.notes,
            flow: item.flow,
            frequency: item.frequency,
            processingMode: item.processingMode
          }))
          this.visibleColumnsNames = this.columns.map((column) => column.name)
        } else if (this.delete == 'http://localhost:8080/api/v1/servers') {
          console.log(response.data)
          this.data = response.data.map((item) => ({
            id: item.id,
            environment: item.environment ? item.environment.environmentName : this.noENV,
            datacenter: item.datacenter ? item.datacenter.name : this.noDC,
            serverName: item.serverName,
            dataSource: item.dataSource,
            type: item.type,
            role: item.role,
            currentNumberOfCores: item.currentNumberOfCores,
            currentRamGb: item.currentRamGb,
            currentDiskGb: item.currentDiskGb,
            powerStatus: item.powerStatus,
            serverNotes: item.serverNotes,
            ipAddress: item.ipAddress,
            operatingSystem: item.operatingSystem
          }))
          this.visibleColumnsNames = this.columns.map((column) => column.name)
        } else {
          this.data = response.data
          this.visibleColumnsNames = this.columns.map((column) => column.name)
        }
      })
      .catch((error) => {
        console.error(error)
      })
  },
  computed: {
    filteredData() {
      let filtered = this.data

      if (this.searchQuery) {
        filtered = filtered.filter((row) => {
          for (const column of this.visibleColumns) {
            const cellValue = String(row[column.name]).toLowerCase()
            if (cellValue.includes(this.searchQuery.toLowerCase())) {
              return true
            }
          }
          return false
        })
      }

      if (this.sortColumn) {
        const column = this.columns.find((c) => c.name === this.sortColumn)
        filtered = filtered.sort((a, b) => {
          const valueA = a[this.sortColumn]
          const valueB = b[this.sortColumn]
          if (typeof valueA === 'string' && typeof valueB === 'string') {
            return valueA.localeCompare(valueB)
          } else {
            return valueA - valueB
          }
        })

        if (this.sortDirection === 'desc') {
          filtered = filtered.reverse()
        }
      }

      return filtered
    },
    visibleColumns() {
      return this.columns.filter((column) => this.visibleColumnsNames.includes(column.name))
    }
  },
  methods: {
    sortBy(column) {
      if (this.sortColumn === column) {
        this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc'
      } else {
        this.sortColumn = column
        this.sortDirection = 'asc'
      }
    },
    onclickDownload(row) {
      fetch(`http://127.0.0.1:5000/api/v1/generate_report/${row.id}/word`, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/pdf'
        }
      })
        .then((response) => response.blob())
        .then((blob) => {
          const url = window.URL.createObjectURL(blob)
          const link = document.createElement('a')
          link.href = url
          link.target = '_blank'
          link.download = `${row.appName}.pdf`
          link.click()
        })
        .catch((error) => {
          console.error('Erreur lors du téléchargement du PDF', error)
        })
    },
    deleteRessource(id) {
      const confirmed = window.confirm('Are you sure you want to delete this resource?')
      if (!confirmed) {
        return
      }
      this.$http
        .delete(this.delete + '/' + id)
        .then(() => {
          window.location.reload()
        })
        .catch((error) => {
          console.error(error)
        })
    },
    getEditRoute(id) {
      let viewName = ''
      let params

      params = { id: id }

      switch (this.edit) {
        case '/applications/edit/':
          viewName = 'EditAppView'
          break
        case '/servers/edit/':
          viewName = 'EditServerView'
          break
        case '/databases/edit/':
          viewName = 'EditDatabaseView'
          break
        case '/interfaces/edit/':
          viewName = 'EditInterfaceView'
          break
        case '/contacts/edit/':
          viewName = 'EditContactView'
          break
        case '/datacenters/edit/':
          viewName = 'EditDataCenterView'
          break
        case '/environments/edit/':
          viewName = 'EditEnvironmentView'
          break
        default:
          console.error(`Unknown edit value: ${this.edit}`)
      }

      return { name: viewName, params }
    },
    goToDashbord(id) {
      if (this.title === 'List of Applications') {
        this.$router.push({ path: `/applications/${id}` })
      }
    },
    setIdDB(id) {
      this.idDbToEdit = id
    },
    setIdServer(id) {
      this.idServToEdit = id
    },
    setIdContact(id) {
      this.idContactToEdit = id
    },
    setIdInterface(id) {
      this.idInterfaceToEdit = id
    },
    setIdDatacenter(id) {
      this.idDCToEdit = id
    },
    setIdEnv(id) {
      this.idEnvToEdit = id
    },
    setIdApp(id) {
      this.idAppToEdit = id
    },
    handleNewData(newData) {
      this.data.push(newData)
    }
  }
}
</script>
<style>
.cursor {
  cursor: pointer;
}

.input-group {
  margin-bottom: 1rem;
}

.wide-column {
  display: flex;
  align-items: center;
}

.menuItem-active-link {
  margin-left: 10px;
}
</style>
