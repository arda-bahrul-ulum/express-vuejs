<script setup>
//import sidebar component
import SidebarMenu from "../../../components/SidebarMenu.vue";

//import ref and onMounted
import { ref, onMounted } from "vue";

//import js cookie
import Cookies from "js-cookie";

//import api
import api from "../../../services/api";

//get token from cookies
const token = Cookies.get("token");

//define state
const users = ref([]);

//method fetchDataPosts
const fetchDataUsers = async () => {
  //fetch data
  api.defaults.headers.common["Authorization"] = token;
  await api
    .get("/api/users")

    .then((response) => {
      //set response data to state "users"
      users.value = response.data.data;
    });
};

//run hook "onMounted"
onMounted(() => {
  //call method "fetchDataUsers"
  fetchDataUsers();
});

//method deleteUser
const deleteUser = async (id) => {
  //delete post with API
  api.defaults.headers.common["Authorization"] = token;
  await api.delete(`/api/users/delete/${id}`).then(() => {
    //call method "fetchDataUsers"
    fetchDataUsers();
  });
};
</script>

<template>
  <div class="container mt-5 mb-5">
    <div class="row">
      <div class="col-md-3">
        <SidebarMenu />
      </div>
      <div class="col-md-9">
        <div class="card border-0 rounded shadow-sm">
          <div
            class="card-header d-flex justify-content-between align-items-center"
          >
            <span>USERS</span>
            <router-link
              :to="{ name: 'admin.users.create' }"
              class="btn btn-sm btn-success rounded shadow-sm border-0"
              >ADD USER</router-link
            >
          </div>
          <div class="card-body">
            <table class="table table-bordered">
              <thead class="bg-dark text-white">
                <tr>
                  <th scope="col">Full Name</th>
                  <th scope="col">Email Address</th>
                  <th scope="col" style="width: 18%">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="users.length == 0">
                  <td colspan="4" class="text-center">
                    <div class="alert alert-danger mb-0">
                      Data Belum Tersedia!
                    </div>
                  </td>
                </tr>
                <tr v-else v-for="(user, index) in users" :key="index">
                  <td>{{ user.name }}</td>
                  <td>{{ user.email }}</td>
                  <td class="text-center">
                    <router-link
                      :to="{
                        name: 'admin.users.edit',
                        params: { id: user.id },
                      }"
                      class="btn btn-sm btn-primary rounded-sm shadow border-0 me-2"
                      >EDIT</router-link
                    >
                    <button
                      @click="deleteUser(user.id)"
                      class="btn btn-sm btn-danger rounded-sm shadow border-0"
                    >
                      DELETE
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
