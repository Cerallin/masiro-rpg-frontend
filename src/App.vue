<template>
  <div id="app">
    <nav class="nav flex-sm-row flex-column shadow-sm px-4 py-2">
      <!-- Left Side Of Navbar -->
      <ul class="navbar-nav justify-content-center flex-fill">
        <li
          class="
            nav-item
            d-flex
            justify-content-center justify-content-sm-start
          "
        >
          <a class="navbar-brand mr-0" href="/">
            {{ $config.VUE_APP_TITLE }}
          </a>
        </li>
      </ul>

      <!-- Right Side Of Navbar -->
      <!-- Show on PC -->
      <ul
        class="
          navbar-nav
          flex-sm-row flex-column
          align-items-center
          justify-content-end
          d-none d-sm-flex
        "
      >
        <li
          class="nav-item mx-auto px-2"
          v-for="route in routes"
          :key="route.name"
        >
          <router-link
            v-show="!(route.name === '登录' && userInfo.avatar)"
            class="text-sm-center nav-link"
            :to="route.path || '/'"
          >
            {{ route.name }}
          </router-link>
        </li>
        <li
          class="
            nav-item
            flex-row
            d-flex
            align-items-center
            justify-content-center justify-content-sm-end
          "
        >
          <template v-if="userInfo && userInfo.avatar">
            <b-nav-item-dropdown right>
              <template v-slot:button-content>
                <b-avatar
                  variant="info"
                  :src="userInfo.avatar + '?quality=80'"
                ></b-avatar>
              </template>
              <li class="user-avatar d-flex flex-column justify-content-center">
                <userCard :userInfo="userInfo"></userCard>
              </li>
            </b-nav-item-dropdown>
          </template>
          <template v-else></template>
        </li>
      </ul>
      <!-- Show on phone -->
      <ul class="navbar-nav flex-column align-items-center d-flex d-sm-none">
        <a href="javascript:;" v-b-toggle.collapsed-sm>
          <font-awesome-icon :icon="['fas', visible ? 'minus' : 'bars']">
          </font-awesome-icon>
        </a>
        <b-collapse
          v-model="visible"
          id="collapsed-sm"
          tag="ul"
          class="navbar-nav flex-column align-items-center flex-fill"
        >
          <li
            class="nav-item mx-auto px-2"
            v-for="route in routes"
            :key="route.name"
          >
            <router-link
              v-show="!(route.name === '登录' && userInfo.avatar)"
              class="text-sm-center nav-link"
              :to="route.path || '/'"
            >
              {{ route.name }}
            </router-link>
          </li>
          <li class="nav-item mx-auto px-2">
            <b-button
              v-b-toggle.collapsed-card
              variant="link"
              class="text-decoration-underline shadow-none"
            >
              {{ userInfo.name }}
            </b-button>
          </li>
          <b-collapse id="collapsed-card" class="my-2">
            <b-card>
              <userCard :userInfo="userInfo"></userCard>
            </b-card>
          </b-collapse>
        </b-collapse>
      </ul>
    </nav>
    <router-view />
  </div>
</template>

<script>
import userCard from "@/components/UserCard.vue";

export default {
  data() {
    return {
      routes: [],
      userInfo: {},
      visible: false,
    };
  },
  components: { userCard },
  created() {
    let user = this.$user;
    this.$router.beforeEach((to, from, next) => {
      if (to.path === "/login") next();
      else if (to.path === from.path) next(false);
      else if (user.requireLogin()) next({ path: "/login" });
      else next();
    });
    this.routes = this.$router.getRoutes();
    let self = this;
    this.$user.renew().then((res) => {
      if (!res) {
        self.$router.push({ path: "/login" });
      }

      self.userInfo = self.$user.info;
    });
  },
};
</script>

<style>
body {
  user-select: none;
  -ms-user-select: none;
  -webkit-user-select: none;
}

#collapsed-sm.collapsing {
  display: flex !important;
}

.text-decoration-underline {
  text-decoration: underline !important;
}
</style>