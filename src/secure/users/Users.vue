<template>
  <div class="d-flex justify-content-between my-3">
    <h2>Users</h2>
    <router-link
      to="/users/create"
      v-if="AuthenticatedUser.canEdit('users')"
      class="btn btn-primary"
      >create User</router-link
    >
  </div>
  <div class="table-responsive">
    <table class="table table-striped table-sm">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Name</th>
          <th scope="col">Email</th>
          <!-- <th scope="col">Role</th> -->
          <th scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.first_name }} {{ user.last_name }}</td>
          <td>{{ user.email }}</td>
          <!-- <td>{{ user.role.name }}</td> -->
          <td>
            <div
              class="btn-group mr-2"
              v-if="AuthenticatedUser.canEdit('users')"
            >
              <router-link
                :to="`/users/${user.id}/edit`"
                class="btn btn-sm btn-outline-secondary"
                @click="alert('hi')"
                >Edit
              </router-link>
              <a
                href="javascript:void(0)"
                class="btn btn-sm btn-outline-secondary"
                @click="del(user.id)"
              >
                Delete</a
              >
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <nav aria-label="...">
    <ul class="pagination">
      <li class="page-item">
        <a
          class="page-link"
          @click="prev"
          href="javascript:void(0)"
          tabindex="-1"
          >Previous</a
        >
      </li>
      <li class="page-item">
        <a class="page-link" @click="next" href="javascript:void(0)">Next</a>
      </li>
    </ul>
  </nav>
</template>

<script lang="ts">
// import { onMounted } from "@vue/runtime-core";
import { ref, onMounted, computed } from "@vue/runtime-core";
import axios from "axios";
import { User } from "@/classes/user";
import { Entity } from "@/interfaces/entity";
import { useStore } from "vuex";

export default {
  name: "Users",
  setup() {
    const users = ref([]);
    const page = ref(1);
    const lastPage = ref(0);
    const store = useStore();

    const AuthenticatedUser = computed(() => store.state.User.user);
    const load = async () => {
      const response = await axios.get(
        `${process.env.VUE_APP_BASE_URL}/users?page=${page.value}`
      );
      console.log(response.data.data);
      users.value = response.data.data;
      lastPage.value = response.data.meta.last_page;
    };
    const next = async () => {
      if (page.value === lastPage.value) return;
      page.value++;
      await load();
    };

    const prev = async () => {
      if (page.value == 1) return;
      page.value--;
      await load();
    };

    const del = async (id: number) => {
      if (confirm(`Are you sure you want to delete this record?`)) {
        await axios.delete(`${process.env.VUE_APP_BASE_URL}/users/${id}`);
        // delete users.value[id];
        users.value = users.value.filter((e: Entity) => e.id !== id);
      }
    };
    onMounted(load);

    return {
      users,
      AuthenticatedUser,
      next,
      prev,
      del,
    };
  },
};
</script>

<style></style>
