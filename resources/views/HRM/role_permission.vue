<template>
  <div class="form" id="bar">
    <div class="toplink">
      <h2 class="stuName">HRM</h2>
      <h4 class="stuLink">
        <router-link to="/home" class="home">Home</router-link>> Role Permissions
      </h4>
    </div>
    <hr />
    <confirm :url="delurl"></confirm>
    <div class="row rowContainer" style="align-items: end !important;">
      <div class="col-lg-5 col-md-12" style="padding-left:2px;">
        <div class="card">
          <div class="card-header">
            <h6>Add Role</h6>
          </div>
          <div class="card-body" style="padding:1rem 0;border-bottom: 1px solid #8080808c;">
            <form @submit.prevent="addRole">
              <div class="col-12">
                <label for="name">
                  Name
                  <strong>*</strong>
                </label>
                <input type="text" class="inputbox" v-model="role.name" />
              </div>
              <div class="col-12">
                <button type="submit" class="save">Save</button>
              </div>
            </form>
          </div>
        </div>
      </div>

      <div class="col-lg-7 col-md-12" style="padding-left:0;">
        <div class="card">
          <div class="card-header">
            <h6>Role List</h6>
          </div>
          <div class="card-body">
            <input type="text" placeholder="Search..." class="searchText" />
            <div class="copyRows">
              <div class="row" id="copyRow">
                <div class="col-2">
                  <a href="#" title="Copy">
                    <i class="fa fa-copy"></i>
                  </a>
                </div>
                <div class="col-2">
                  <a href="#" title="Excel">
                    <i class="fa fa-file-excel-o"></i>
                  </a>
                </div>
                <div class="col-2">
                  <a href="#" title="PDF">
                    <i class="fa fa-file-pdf-o"></i>
                  </a>
                </div>
                <div class="col-2">
                  <a href="#" title="Print">
                    <i class="fa fa-print"></i>
                  </a>
                </div>
                <div class="col-2">
                  <a href="#" title="Columns">
                    <i class="fa fa-columns"></i>
                  </a>
                </div>
              </div>
            </div>
            <div class="table-responsive">
              <table class="table table-hover table-striped" id="studenttable">
                <thead>
                  <tr>
                    <th class="all" nowrap>Name</th>
                    <th class="all" nowrap style="text-align: right;">Action</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(rol) in roles" v-bind:key="rol.id" class="active">
                    <td class="all" nowrap>{{rol.name}}</td>
                    <td style="text-align: right;">
                      <i class="fa fa-tag assign" aria-hidden="true">
                        <span class="assignLbl">Assign Permission</span>
                      </i>
                      <i @click="editRole(rol)" class="fa fa-pencil pen">
                        <span class="penLabel">Edit</span>
                      </i>
                      <i
                        data-toggle="modal"
                        data-target="#exampleModalCenter"
                        @click="deleteRole(rol.id)"
                        class="fa fa-trash time"
                      >
                        <span class="timeLabel">Delete</span>
                      </i>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 *  COMPONENTS
 */
import confirm from "../message/confirm.vue";
import { EventBus } from "../../js/event-bus.js";
export default {
  components: {
    confirm
  },
  data() {
    return {
      search: "",
      role: {},
      roles: [],
      isEdit: false,
      delurl: ""
    };
  },
  mounted() {
    this.getRoles();
  },
  created() {
    EventBus.$on("clicked", clickCount => {
      this.getRoles();
    });
    this.getRoles();
  },
  methods: {
    getRoles() {
      this.axios
        .get("/api/roles")
        .then(response => (this.roles = response.data));
    },
    addRole() {
      console.log(JSON.stringify(this.role));
      this.axios
        .post("/api/role/store", this.role)
        .then(response => {
          this.getRoles();
          setTimeout(() => {
            this.role = {};
          }, 100);
        })
        .catch(error => console.log(error));
    },
    editRole(data) {
      console.log(JSON.stringify(data.name));
      let to = this.moveToDown ? this.$refs.description.offsetTop - 60 : 0;
      window.scroll({
        top: to,
        left: 0,
        behavior: "smooth"
      });
      this.moveToDown = !this.moveToDown;
      this.role = {};
      this.moveToDown = !this.moveToDown;
      this.role.id = data.id;
      this.role.name = data.name;
      this.isEdit = true;
    },
    updateRole() {
      console.log(JSON.stringify(this.role));
      this.axios
        .post(`/api/role/update/${this.role.id}`, this.role)
        .then(res => {
          this.isEdit = false;
          this.role = {};
          this.getRoles();
          console.log(JSON.stringify(res));
        });
    },
    deleteRole(id) {
      var funName = "delete"; /**Delete function */
      this.delurl = `role/${funName}/${id}`;
    },
    searchData() {
      if (this.search == "") {
        this.axios.get(`api/departments`).then(response => {
          this.roomtypes = [];
          this.roomtypes = response.data;
        });
      } else {
        this.axios
          .get(`api/department/search`, { params: { keywords: this.search } })
          .then(response => {
            this.roomtypes = [];
            this.roomtypes = response.data;
          });
      }
    }
  }
};
</script>