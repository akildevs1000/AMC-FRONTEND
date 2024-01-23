<template>
  <v-container>
    <v-row>
      <v-col cols="4">
        <v-autocomplete
          label="Select Job Type"
          dense
          outlined
          v-model="filters.work_type"
          :items="[
            { id: ``, name: `Select All` },
            { id: `amc`, name: `AMC` },
            { id: `ticket`, name: `Ticket` },
          ]"
          item-value="id"
          item-text="name"
          :hide-details="true"
        ></v-autocomplete>
      </v-col>
      <v-col cols="4">
        <v-autocomplete
          label="Select Equipment Category"
          dense
          outlined
          v-model="filters.equipment_category_id"
          :items="[{ id: ``, name: `Select All` }, ...equipmentCategoryList]"
          item-value="id"
          item-text="name"
          :hide-details="true"
        ></v-autocomplete>
      </v-col>
      <v-col cols="4">
        <CustomDateFilter
          @filter-attr="filterAttr"
          :defaultFilterType="1"
          height="40px"
        />
      </v-col>
      <v-col cols="3">
        <v-btn
          color="primary"
          label="Select Technician"
          dense
          @click="getDataFromApi()"
          >Generate Report</v-btn
        >
      </v-col>
      <v-col cols="12">
        <v-data-table
          dense
          :headers="headers"
          :items="data"
          model-value="data.id"
          :loading="loading"
          :options.sync="options"
          :footer-props="{
            itemsPerPageOptions: [100, 500, 1000],
          }"
          class="elevation-1"
          :server-items-length="totalRowsCount"
        >
          <!-- <template
        v-slot:item.company="{
          item: {
            service_call: { contract },
          },
          index,
        }"
      >
        <v-card
          elevation="0"
          style="background: none"
          class="d-flex align-center"
        >
          <v-avatar class="mr-1">
            <img
              :src="
                contract.company && contract.company.logo
                  ? contract.company.logo
                  : '/no-image.png'
              "
              alt="Avatar"
            />
          </v-avatar>
          <div class="mt-2">
            <strong> {{ contract.company && contract.company.name }}</strong>
            <p>
              {{ contract.company && contract.company.address }}
            </p>
          </div>
        </v-card>
      </template> -->
          <template v-slot:item.summary="{ item }">
            <ReadMore :text="item.summary" />
          </template>

          <template v-slot:item.before_attachment="{ item }">
            <ViewAttachment
              v-if="item.before_attachment"
              :src="item.before_attachment"
            />
          </template>
          <template v-slot:item.after_attachment="{ item }">
            <ViewAttachment
              v-if="item.after_attachment"
              :src="item.after_attachment"
            />
          </template>

          <template v-slot:item.service_call="{ item }">
            Reference Id: {{ item.service_call_id }}
            <!-- <v-chip dark small :color="statusRelatedColor(item.service_call.status)">{{
                item.service_call
              }}</v-chip> -->
          </template>

          <template v-slot:item.options="{ item }">
            <v-menu bottom left>
              <template v-slot:activator="{ on, attrs }">
                <v-btn dark-2 icon v-bind="attrs" v-on="on">
                  <v-icon>mdi-dots-vertical</v-icon>
                </v-btn>
              </template>
              <v-list width="150" dense>
                <v-list-item>
                  <v-list-item-title>
                    <FormEntryTableView :items="item.checklists" />
                  </v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  components: {},
  props: ["id"],
  data: () => ({
    totalRowsCount: 0,
    filters: {},
    loading: true,
    options: {},
    Model: "Reports",
    endpoint: "form_entry",
    data: [],
    errors: [],
    companies: [],
    equipmentCategoryList: [],
    technicians: [],
    headers: [
      {
        text: "Job Type",
        align: "left",
        sortable: true,
        key: "work_type",
        value: "work_type",
        filterable: true,
        filterSpecial: false,
      },
      {
        text: "Equipment Category",
        align: "left",
        sortable: true,
        key: "equipment_category.name",
        value: "equipment_category.name",
        filterable: true,
        filterSpecial: false,
      },
      {
        text: "Before Attachment",
        align: "left",
        sortable: true,
        key: "before_attachment",
        value: "before_attachment",
        filterable: true,
        filterSpecial: false,
      },
      {
        text: "After Attachment",
        align: "left",
        sortable: true,
        key: "after_attachment",
        value: "after_attachment",
        filterable: true,
        filterSpecial: false,
      },
      {
        text: "Created Date",
        align: "left",
        sortable: true,
        key: "date",
        value: "date",
        filterable: true,
        filterSpecial: false,
      },
      {
        text: "Action",
        align: "left",
        sortable: false,
        key: "options",
        value: "options",
      },
    ],
  }),

  async created() {
    this.$axios.get(`equipmentCategoryList`).then(({ data }) => {
      this.equipmentCategoryList = data;
    });

    this.getDataFromApi();
    //this.getDepartments(options);
  },
  watch: {
    options: {
      handler() {
        this.getDataFromApi();
      },
      deep: true,
    },
  },
  methods: {
    exportCSV() {
      if (this.totalRowsCount === 0) {
        alert("No record to download");
        return;
      }

      const csvData = this.generateCSVData();
      this.downloadCSV(csvData);
    },

    generateCSVData() {
      const csvHeader = "Date,Summary,Equipment Category,Technician\n";
      const csvRows = this.data.map(
        (e) =>
          `${e.date},"${e.summary}","${e.equipment_category.name}","${e.technician.name}"\n`
      );

      return csvHeader + csvRows.join("");
    },

    downloadCSV(csvData) {
      const csvBlob = new Blob([csvData], { type: "text/csv;charset=utf-8" });
      const csvUrl = URL.createObjectURL(csvBlob);

      const downloadLink = document.createElement("a");
      downloadLink.href = csvUrl;
      downloadLink.download = "download.csv";

      document.body.appendChild(downloadLink);
      downloadLink.click();

      document.body.removeChild(downloadLink);

      // Clean up URL.createObjectURL resources
      URL.revokeObjectURL(csvUrl);
    },
    reload() {
      this.filters = {};
      this.getDataFromApi();
    },
    filterAttr({ from, to }) {
      this.filters = {
        ...this.filters,
        from,
        to,
      };
      this.getDataFromApi();
    },
    statusRelatedColor(value) {
      let color = {
        completed: "green",
        pending: "red",
      };
      return color[value];
    },
    async getDataFromApi() {
      this.loading = true;
      this.filters.technician_id = this.$auth.user.id;
      const data = await this.$store.dispatch("fetchData", {
        key: "reports",
        options: this.options,
        refresh: true,
        endpoint: this.endpoint,
        filters: this.filters,
      });

      this.data = data.data;
      this.totalRowsCount = data.total;
      this.loading = false;
    },
  },
};
</script>
