<template>
  <v-app>
    <!-- Navigation drawer -->
    <v-navigation-drawer dark color="primaru" v-model="drawer" app>
      <!-- Your menu items -->
      <v-list>
        <v-list-item
          v-for="(item, index) in items"
          :key="index"
          @click="navigate(item)"
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>
          <v-list-item-title>{{ item.title }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <!-- Main content area -->
    <v-app-bar app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Welcome, {{$auth.user.name}}</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn text @click="logout"><v-icon>mdi-logout</v-icon></v-btn>
    </v-app-bar>

    <!-- Your main content goes here -->
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      drawer: false, // Controls the drawer visibility
      items: [
        // Your menu items
        // { title: "Home", icon: "mdi-home", route: "/" },
        { title: "Home", icon: "mdi-home", route: "/" },
        { title: "AMC", icon: "mdi-file-document", route: "/amc" },
        { title: "Ticket", icon: "mdi-ticket", route: "/tickets" },
        { title: "Checklist", icon: "mdi-clipboard", route: "/checklist" },
      ],
    };
  },
  methods: {
    navigate(item) {
      // Perform navigation based on item.route (e.g., using Vue Router)
      console.log("Navigate to:", item.route);
      this.$router.push(item.route);
    },
    logout() {
      console.log(this.$auth);
      this.$axios.get(`/technician/logout`).then(({ res }) => {
        this.$auth.logout();
      });
    },
  },
};
</script>

<style>
/* Add any custom styles here */
</style>
