<template>
    <div id="app" class="container">
        <div class="form-group">
            <b-button v-b-modal.modal-add-user>Add new user</b-button>
        </div>

        <div class="form-group">
            <input v-model="search" v-on:keyup="filterUsers" class="form-control" placeholder="Search by name">
        </div>

        <table class="table table-striped table-bordered">
            <thead class="thead-dark">
            <tr>
                <th v-for="(column, index) in columns"
                    :key="index">
                    <a href="#"
                       @click="paginatedData.sort(sort_by(column))"
                       :class="{active: sortKey === column}">
                        {{ column }}
                        <span v-if="sortKey === column">{{reverse ? '+' :'-'}}</span>
                    </a>
                </th>
                <th></th>
            </tr>
            </thead>

            <tbody>
            <template v-if="filteredUsers.length > 0">
                <tr v-for="(user, index) in paginatedData"
                    :key="index">
                    <td>{{ user.id }}</td>
                    <td>{{ user.name }}</td>
                    <td>{{ user.authority }}</td>
                    <td>{{ user.age }}</td>
                    <td>{{ user.registered }}</td>
                    <td>{{ user.workplace }}</td>
                    <td>
                        <b-button v-b-modal.modal-edit-user @click="handleUser(user.id)">Edit</b-button>
                    </td>
                </tr>
            </template>
            </tbody>
        </table>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li :class="[{'disabled': currentPage === 0}, 'page-item']">
                    <a class="page-link" href="#" @click="currentPage--">Prev</a>
                </li>
                <li :class="[{active: currentPage === page - 1}, 'page-item']" v-for="(page, index) in numPages" :key="index">
                    <a class="page-link" href="#" @click="currentPage = page - 1">{{page}}</a>
                </li>
                <li :class="[{'disabled': currentPage === numPages - 1}, 'page-item']">
                    <a class="page-link" href="#" @click="currentPage++">Next</a>
                </li>
            </ul>
        </nav>

        <h3 v-if="filteredUsers.length === 0" class="text-center">No results!</h3>

        <b-modal id="modal-add-user"
                 title="Add new user"
                 @ok="addUser">
            <form action="">
                <div class="form-group">
                    <label>Name</label>
                    <input type="text" class="form-control" v-model="newUser.name" required>
                </div>

                <div class="form-group">
                    <label>Authority</label>
                    <select class="custom-select"
                            v-model="authority"
                            multiple>
                        <option v-for="(o, i) in authorityOptions"
                                :key="i"
                                :value="{id: o.id, name: o.name}">{{o.name}}
                        </option>
                    </select>
                </div>

                <div class="form-group">
                    <label>Age</label>
                    <input type="number" class="form-control" v-model="newUser.age" required>
                </div>

                <div class="form-group">
                    <label>Workplace</label>
                    <input type="text" class="form-control" v-model="newUser.workplace" required>
                </div>
            </form>
        </b-modal>

        <b-modal id="modal-edit-user"
                 title="Edit user"
                 @ok="editUser"
                 @hidden="clearModal">
            <form action="">
                <div class="form-group">
                    <label>Name</label>
                    <input type="text" class="form-control" v-model="newUser.name" required>
                </div>

                <div class="form-group">
                    <label>Authority</label>
                    <select class="custom-select"
                            v-model="authority"
                            multiple>
                        <option v-for="(o, i) in authorityOptions"
                                :key="i"
                                :value="{id: o.id, name: o.name}">{{o.name}}
                        </option>
                    </select>
                </div>

                <div class="form-group">
                    <label>Age</label>
                    <input type="number" class="form-control" v-model="newUser.age" required>
                </div>

                <div class="form-group">
                    <label>Workplace</label>
                    <input type="text" class="form-control" v-model="newUser.workplace" required>
                </div>
            </form>
        </b-modal>

    </div>
</template>

<script>
  export default {
    name: 'app',
    data() {
      return {
        currentPage: 0,
        recordsPerPage: 3,
        sortKey: null,
        reverse: true,
        search: '',
        columns: [],
        newUser: {
          name: 'Ahim',
          age: 28,
          workplace: 'Cég 3'
        },
        authority: [],
        authorityOptions: [
          {
            id: 0,
            name: "Leave empty"
          },
          {
            id: 1,
            name: "Módosítás"
          }, {
            id: 2,
            name: "Olvasás"
          }
        ],
        filteredUsers: null,
        users: [
          {
            "id": 1,
            "name": "László",
            "authority": [{
              "id": 1,
              "name": "Módosítás"
            }, {
              "id": 2,
              "name": "Olvasás"
            }
            ],
            "age": 25,
            "registered": "2019-01-31 06:45:51.557Z",
            "workplace": {
              "id": 12,
              "name": "Cég 1"
            },
          },
          {
            "id": 2,
            "name": "Péter",
            "authority": [{
              "id": 2,
              "name": "Olvasás"
            }
            ],
            "age": 44,
            "registered": "2011-01-31 09:23:51.234Z",
            "workplace": {
              "id": 34,
              "name": "Cég 2"
            },
          },
          {
            "id": 3,
            "name": "Zénó",
            "authority": [{
              "id": 2,
              "name": "Olvasás"
            }
            ],
            "age": 25,
            "registered": "2015-03-12 06:42:51.345Z",
            "workplace": {
              "id": 34,
              "name": "Cég 2"
            },
          },
          {
            "id": 4,
            "name": "Zakariás",
            "authority": [{
              "id": 1,
              "name": "Módosítás"
            }, {
              "id": 2,
              "name": "Olvasás"
            }
            ],
            "age": 88,
            "registered": "2000-01-12 12:44:55.111Z",
            "workplace": {
              "id": 12,
              "name": "Cég 1"
            },
          },
          {
            "id": 5,
            "name": "Emma",
            "age": 25,
            "registered": "2014-06-16 12:12:34.557Z",
            "workplace": {
              "id": 12,
              "name": "Cég 1"
            },
          },
          {
            "id": 6,
            "name": "Jenő",
            "authority": [{
              "id": 1,
              "name": "Módosítás"
            }, {
              "id": 2,
              "name": "Olvasás"
            }
            ],
            "age": 32,
            "registered": "2019-09-23 12:23:51.557Z",
            "workplace": {
              "id": 12,
              "name": "Cég 1"
            },
          },
          {
            "id": 7,
            "name": "Mira",
            "authority": [],
            "age": 55,
            "registered": "2013-11-12 10:23:51.557Z",
            "workplace": {
              "id": 12,
              "name": "Cég 1"
            },
          }
        ]
      }
    },
    beforeMount() {
      this.setColumns();
      this.filterUsers();
    },
    computed: {
      paginatedData() {
        const start = this.currentPage * this.recordsPerPage;
        const end = start + this.recordsPerPage;

        return this.filteredUsers.slice(start, end);
      },
      numPages() {
        let l = this.filteredUsers.length;
        let s = this.recordsPerPage;

        return Math.ceil(l / s);
      },
    },
    methods: {
      setColumns() {
        this.users.map(user => {
          for (let index in user) {
            if (!this.columns.includes(index)) {
              this.columns.push(index)
            }
          }
        });
      },

      filterUsers() {
        this.filteredUsers = JSON.parse(JSON.stringify(this.users));

        this.filteredUsers.map(user => {
          if (typeof user.authority !== 'undefined' && user.authority.length > 0) {
            let authorityString = '';
            user.authority.map((item) => {
              if (authorityString !== '') {
                authorityString += ','
              }
              authorityString += (item.name);
            });

            user.authority = authorityString;
            authorityString = '';
          } else {
            user.authority = '';
          }

          if (typeof user.workplace !== 'undefined') {
            user.workplace = user.workplace.name;
          } else {
            user.workplace = '';
          }
        });

        this.filteredUsers = this.filteredUsers.filter((user) => {
          return user.name.toLowerCase().indexOf(this.search) !== -1;
        });
      },

      sort_by(field) {
        this.reverse = (this.sortKey === field) ? !this.reverse : true;
        this.sortKey = field;

        return (a, b) => {
          let
            A = a[field].toString(),
            B = b[field].toString();

          return ((A < B) ? -1 : ((A > B) ? 1 : 0)) * [-1, 1][+!!this.reverse];
        }
      },

      addUser() {
        this.newUser.id = this.users.length + 1;
        this.newUser.authority = this.authority;
        this.newUser.registered = new Date();
        this.newUser.workplace = {id: 1, name: this.newUser.workplace};

        this.users.push(this.newUser);
        this.newUser = {};
        this.authority = [];

        this.filterUsers();
      },

      editUser() {
        this.users.map(user => {

          if (user.id === this.newUser.id) {
            user.name = this.newUser.name;
            user.age = this.newUser.age;
            user.workplace = {id: 1, name: this.newUser.workplace};

            if (
              typeof user.authority !== 'undefined' &&
              this.authority[0].id === 0 &&
              this.authority.length > 0
            ) {
              this.authority.shift();
            }

            user.authority = this.authority;
          }
        });

        this.filterUsers();
        this.newUser = {};
        this.authority = [];
      },

      handleUser(id) {
        this.users.map(user => {
          if (user.id === id) {
            this.newUser = JSON.parse(JSON.stringify(user));
            this.newUser.workplace = user.workplace.name;

            if (typeof user.authority !== 'undefined' && user.authority.length > 0) {
              this.authority = user.authority;
            } else {
              this.authority.push(this.authorityOptions[0]);
            }
          }
        });

        this.$bvModal.show('modal-edit-user');
      },

      clearModal() {
        this.newUser = {};
        this.authority = [];
      }
    }
  }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        margin-top: 60px;
    }

    .table th a {
        text-decoration: none;
        color: white;
    }

    .table th a.active {
        color: aqua;
    }

</style>
