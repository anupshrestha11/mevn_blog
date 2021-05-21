<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row>
          <v-spacer> </v-spacer>
          <v-col cols="12" md="6" lg="4">
            <v-form submit.prevent="save()">
              <v-card>
                <v-card-title>
                  Add New User
                </v-card-title>
                <v-card-text>
                  <div>
                    <v-text-field label="Name" v-model="name"></v-text-field>
                  </div>
                  <div class="mt-4">
                    <v-slider
                      label="Age"
                      thumb-label="always"
                      v-model="age"
                    ></v-slider>
                  </div>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="success" @click="save()">
                    Add
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-form>
          </v-col>
          <v-spacer> </v-spacer>
        </v-row>
        <v-row>
          <v-spacer> </v-spacer>
          <v-col cols="12" md="6" lg="4">
            <v-card>
              <v-card-text>
                <v-data-table :items="users" :headers="headers">
                  <template v-slot:item.actions="{ item }">
                    <v-icon small class="mr-2" @click="editItem(item)">
                      mdi-pencil
                    </v-icon>
                    <v-icon small @click="deleteItem(item)">
                      mdi-delete
                    </v-icon>
                  </template>
                </v-data-table>
              </v-card-text>
            </v-card>
          </v-col>
          <v-spacer> </v-spacer>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { HTTP } from "@/http/http.js";
export default {
  name: "App",

  data: () => ({
    users: [],
    headers: [
      { text: "Name", value: "name" },
      { text: "Age", value: "age" },
      { text: "Actions", value: "actions", sortable: false }
    ],
    name: null,
    age: null
  }),
  mounted() {
    this.load();
  },
  methods: {
    load() {
      HTTP.get("/")
        .then(res => {
          this.users = res.data.reverse();
        })
        .catch(err => console.log(err));
    },
    save() {
      HTTP.post("/", { name: this.name, age: this.age })
        .then(res => {
          this.users.unshift(res.data);
        })
        .catch(err => console.log(err));
    },
    editItem(item) {
      return item;
    },
    deleteItem(item) {
      HTTP.delete("/" + item._id)
        .then(() => {
          let index = this.users.findIndex(ele => ele._id === item._id);
          this.users.splice(index, 1);
        })
        .catch(err => console.log(err));
    }
  }
};
</script>
