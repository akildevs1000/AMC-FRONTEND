<template>
  <v-data-table
    dense
    :headers="headers"
    :items="data"
    model-value="data.id"
    :loading="loadinglinear"
    :options.sync="options"
    :footer-props="{
      itemsPerPageOptions: [100, 500, 1000],
    }"
    class="elevation-1 pa-3"
    :server-items-length="totalRowsCount"
  >
    <template v-slot:item.company="{ item: { contract }, index }">
      <v-card
        elevation="0"
        style="background: none"
        class="d-flex align-center"
      >
        <div class="mt-2">
          <strong> {{ contract.company && contract.company.name }}</strong>
          <p>
            {{ contract.company && contract.company.address }}
          </p>
        </div>
      </v-card>
    </template>

    <template v-slot:item.priority.name="{ item }">
      <v-chip dark small :color="priorityRelatedColor(item.priority.name)">{{
        item.priority.name ?? "---"
      }}</v-chip>
    </template>
    <template v-slot:item.schedule_date="{ item }">
      {{ item.pivot.schedule_date }}
    </template>
    <template v-slot:item.status="{ item }">
      <v-chip dark small :color="statusRelatedColor(item.status)">
        {{ item.status }}</v-chip
      >
    </template>

    <template v-slot:item.options="{ item }">
      <v-menu bottom left>
        <template v-slot:activator="{ on, attrs }">
          <v-btn dark-2 icon v-bind="attrs" v-on="on">
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>
        <v-list width="150" dense>
          <v-list-item @click="navigate(`/amc/${item.id}`)">
            <v-list-item-title> AMC </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </template>
  </v-data-table>
</template>

<script>
let date = new Date();

let d = date.getDate();
let m = (date.getMonth() + 1).toString().padStart(2, "0");
let y = date.getFullYear();
let currentDate = y + "-" + m + "-" + d;

export default {
  props: {
    status: {
      type: String,
      default: false,
    },
  },

  data: () => ({
    currentDate,
    totalRowsCount: 0,
    showFilters: false,
    filters: {},
    isFilter: false,
    loadinglinear: true,
    attrs: [],
    selectedFile: "",
    color: "background",
    response: "",
    upload: {
      name: "",
    },
    payload: {},
    options: {},
    Model: "AMC",
    endpoint: "service_call",
    snackbar: false,
    loading: false,
    response: "",
    data: [],
    errors: [],
    headers: require("../../headers/service_call.json"),
  }),

  async created() {
    this.loading = false;

    this.getDataFromApi();
    //this.getDepartments(options);
  },
  mounted() {},
  watch: {
    options: {
      handler() {
        this.getDataFromApi();
      },
      deep: true,
    },
  },
  methods: {
    navigate(route) {
      // Perform navigation based on item.route (e.g., using Vue Router)
      console.log("Navigate to:", route);
      this.$router.push(route);
    },
    getCapitalFirstLetters(name) {
      const words = name.split(" ");
      const firstLetters = words.map((word) => word.charAt(0).toUpperCase());
      return firstLetters.join("");
    },
    getRandomId() {
      return Math.random().toString(36).substring(2);
    },
    priorityRelatedColor(value) {
      let color = {
        High: "red",
        Medium: "blue",
        Low: "grey",
      };
      return color[value];
    },

    statusRelatedColor(value) {
      let color = {
        Completed: "green",
        Pending: "red",
      };
      return color[value];
    },
    applyFilters() {
      this.getDataFromApi();
    },
    toggleFilter() {
      // this.filters = {};
      this.isFilter = !this.isFilter;
    },
    clearFilters() {
      this.filters = {};

      this.isFilter = false;
      this.getDataFromApi();
    },
    async getDataFromApi() {
      this.loadinglinear = true;
      if (this.status) {
        this.filters.status = this.status;
      }
      const data = await this.$store.dispatch("fetchData", {
        key: "service_calls",
        options: this.options,
        refresh: true,
        endpoint: `get_service_calls_by_technician_id/${this.$auth.user.id}`,
        filters: this.filters,
      });

      this.data = data.data;
      this.totalRowsCount = data.total;
      this.loadinglinear = false;
    },
    handleSuccessResponse(message) {
      this.snackbar = true;
      this.response = message;
      this.getDataFromApi();
    },
  },
};
</script>
