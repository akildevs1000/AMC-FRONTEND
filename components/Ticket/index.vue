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
    <template v-slot:item.company="{ item: { company }, index }">
      <v-card
        elevation="0"
        style="background: none"
        class="d-flex align-center"
      >
        <div class="mt-2">
          <strong> {{ company.name }}</strong>
          <p>
            {{ company.address }}
          </p>
        </div>
      </v-card>
    </template>

    <template v-slot:item.schedule_date="{ item }">
      {{ item.pivot.schedule_date }}
    </template>

    <template v-slot:item.status="{ item }">
      <v-chip dark small :color="statusRelatedColor(item.status)">
        {{ item.status }}</v-chip
      >
    </template>

    <template v-slot:item.attachment="{ item }">
      <ViewAttachment v-if="item.attachment" :src="item.attachment" />
    </template>

    <template v-slot:item.options="{ item }">
      <v-menu bottom left>
        <template v-slot:activator="{ on, attrs }">
          <v-btn dark-2 icon v-bind="attrs" v-on="on">
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>
        <v-list dense>
          <v-list-item
            v-for="(listItem, index) in equipmentCategoryList"
            :key="index"
            @click="navigate(listItem, item.id, item.company)"
          >
            <v-list-item-title>
              {{ listItem.name }}
            </v-list-item-title>
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
    Model: "Ticket",
    endpoint: "ticket",
    snackbar: false,
    loading: false,
    response: "",
    data: [],
    errors: [],
    headers: require("../../headers/ticket.json"),
    equipmentCategoryList: [],
  }),

  async created() {
    this.loading = false;

    this.getDataFromApi();
    this.$axios
      .get(`equipmentCategoryList`)
      .then(({ data }) => (this.equipmentCategoryList = data));
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
    navigate(selectedEquipment, id, company) {
      const route = {
        path: `/tickets/${id}`,
        query: {
          id: selectedEquipment.id,
          name: selectedEquipment.name,
          slug: selectedEquipment.slug,
          company_name: company.name,
          company_logo: company.logo,
          company_email: company.email,
          company_makani_number: company.makani_number,
          company_address: company.address,
          company_show_member_from: company.show_member_from,
          company_show_expiry: company.show_expiry,
          company_contact_number: company.contact_number,

          company_id: company.id,
        },
      };

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
        Open: "green",
        Pending: "red",
        "Quotation Advised": "orange",
        "In Progress": "blue",
        Close: "grey",
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
        endpoint: `get_tickets_by_technician_id/${this.$auth.user.id}`,
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
