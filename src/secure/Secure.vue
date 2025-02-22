<template>
  <Header></Header>
  <div class="container-fluid">
    <div class="row">
      <Menu></Menu>

      <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4">
        <router-view v-if="user?.id" />
        <!-- Dashboard Component -->
      </main>
    </div>
  </div>
</template>

<script lang="ts">
import { onMounted, ref } from "vue";
import Header from "@/secure/components/Header.vue";
import Menu from "@/secure/components/Menu.vue";
import axios from "axios";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import { User } from "@/classes/user";

export default {
  components: {
    Menu,
    Header,
  },

  setup() {
    const router = useRouter();
    const user = ref(null);
    const store = useStore();

    onMounted(async () => {
      try {
        const response = await axios.get(
          `${process.env.VUE_APP_BASE_URL}/user`
        );
        const u = response.data.data;
        // console.log(u);
        await store.dispatch(
          "User/setUser",
          new User(
            u.id,
            u.first_name,
            u.last_name,
            u.email,
            u.role,
            u.permissions
          )
        );
        user.value = u;
      } catch (e) {
        await router.push("/login");
      }
    });

    return {
      user,
    };
  },

  name: "Secure",
};
</script>

<style>
body {
  font-size: 0.875rem;
}

.feather {
  width: 16px;
  height: 16px;
  vertical-align: text-bottom;
}

/*
 * Sidebar
 */

.sidebar {
  position: fixed;
  top: 0;
  /* rtl:raw:
  right: 0;
  */
  bottom: 0;
  /* rtl:remove */
  left: 0;
  z-index: 100; /* Behind the navbar */
  padding: 48px 0 0; /* Height of navbar */
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, 0.1);
}

@media (max-width: 767.98px) {
  .sidebar {
    top: 5rem;
  }
}

.sidebar-sticky {
  position: relative;
  top: 0;
  height: calc(100vh - 48px);
  padding-top: 0.5rem;
  overflow-x: hidden;
  overflow-y: auto; /* Scrollable contents if viewport is shorter than content. */
}

.sidebar .nav-link {
  font-weight: 500;
  color: #333;
}

.sidebar .nav-link .feather {
  margin-right: 4px;
  color: #727272;
}

.sidebar .nav-link.active {
  color: #2470dc;
}

.sidebar .nav-link:hover .feather,
.sidebar .nav-link.active .feather {
  color: inherit;
}

.sidebar-heading {
  font-size: 0.75rem;
  text-transform: uppercase;
}

/*
 * Navbar
 */

.navbar-brand {
  padding-top: 0.75rem;
  padding-bottom: 0.75rem;
  font-size: 1rem;
  background-color: rgba(0, 0, 0, 0.25);
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, 0.25);
}

.navbar .navbar-toggler {
  top: 0.25rem;
  right: 1rem;
}

.navbar .form-control {
  padding: 0.75rem 1rem;
  border-width: 0;
  border-radius: 0;
}

.form-control-dark {
  color: #fff;
  background-color: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.1);
}

.form-control-dark:focus {
  border-color: transparent;
  box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.25);
}
</style>
