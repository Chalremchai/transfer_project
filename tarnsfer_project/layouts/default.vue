<template>
  <v-app dark>
    <!-- <v-navigation-drawer
      v-model="drawer"
      :clipped="clipped"
      fixed
      app
      :mini-variant.sync="miniVariant"
      permanent
    >
      <v-col class="text-center">
          <img height="50%" width="100%" src="~/assets/logo.png" @click="to"/>

      </v-col>

      <NavigationSideBar />
    </v-navigation-drawer>

    <v-app-bar :clipped-left="clipped" fixed app elevation="0">
      <v-app-bar-nav-icon @click.stop="miniVariant = !miniVariant" />
      <v-spacer></v-spacer>
      <v-btn icon @click="toggleTheme">
        <v-icon v-if="themes == true"> mdi-white-balance-sunny </v-icon>
        <v-icon v-if="themes != true"> mdi-moon-waning-crescent </v-icon>
      </v-btn>
    </v-app-bar> -->

    <!-- <v-system-bar color="deep-purple darken-3"></v-system-bar> -->

    <v-app-bar elevation="0" app>
      <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

      <v-spacer></v-spacer>
      <v-btn icon @click="toggleTheme">
        <v-icon v-if="themes == true"> mdi-white-balance-sunny </v-icon>
        <v-icon v-if="themes != true"> mdi-moon-waning-crescent </v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-col class="text-center">
        <img height="50%" width="100%" src="~/assets/logo.png" @click="to" />
      </v-col>

      <NavigationSideBar />
    </v-navigation-drawer>
    <v-main class="">
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <!-- <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light> mdi-repeat </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer> -->

    <!-- <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer> -->
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      image: "",
      clipped: false,
      drawer: true,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
    };
  },
  computed: {
    themes() {
      return this.$vuetify.theme.dark;
    },
  },
  mounted() {
    const theme = localStorage.getItem("useDarkTheme");
    if (theme) {
      if (theme == "true") {
        this.$vuetify.theme.dark = true;
      } else this.$vuetify.theme.dark = false;
    }
  },
  methods: {
    toggleTheme() {
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
      localStorage.setItem("useDarkTheme", this.$vuetify.theme.dark.toString());
    },
    to() {
      this.$router.push("/");
    },
  },
};
</script>
<style>
.v-image {
  width: 100ex;
  background-image: url("~/assets/logo.png");
  background-repeat: no-repeat;
  background-position: center;
  background-size: 100%;
}
.VuetifyRotateY {
  height: 80px;

  transform: rotateY(560deg);
  animation: turn 1.5s ease-out forwards 1s;
}

@keyframes turn {
  100% {
    transform: rotateY(0deg);
  }
}
</style>
