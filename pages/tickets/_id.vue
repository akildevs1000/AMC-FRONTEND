<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <v-card v-if="payload.id">
      <v-card-title>
        Ticket
        <v-chip
          class="mx-2"
          dark
          small
          :color="statusRelatedColor(payload.status)"
          >{{ payload.status }}</v-chip
        >

        <v-spacer></v-spacer>
        <SnippetsBack />
      </v-card-title>

      <v-card-text>
        <table>
          <tr>
            <th>Reference #</th>
            <td>
              <b>{{ payload.id }}</b>
            </td>
          </tr>
          <tr>
            <th>Title</th>
            <td>
              <v-text-field
                dense
                outlined
                type="text"
                v-model="payload.title"
                :hide-details="!errors.title"
                :error-messages="errors && errors.title ? errors.title[0] : ''"
                readonly
              ></v-text-field>
            </td>
          </tr>
          <tr>
            <th>Description</th>
            <td>
              <v-textarea
                rows="3"
                dense
                outlined
                type="text"
                v-model="payload.description"
                :hide-details="!errors.description"
                :error-messages="
                  errors && errors.description ? errors.description[0] : ''
                "
                readonly
              ></v-textarea>
            </td>
          </tr>
          <tr>
            <th>Prority</th>
            <td>{{ payload.priority.name }}</td>
          </tr>
          <tr>
            <th>Attachment</th>
            <td>
              <ViewAttachment
                v-if="payload.attachment"
                :src="payload.attachment"
              />
            </td>
          </tr>
          <tr>
            <th>History</th>
            <td>
              <TimeLine
                v-if="payload.ticket_history"
                :id="payload.id"
                :data="payload.ticket_history"
              />
            </td>
          </tr>
          <tr>
            <th>Ticket Open DateTime</th>
            <td>{{ payload.ticket_open_date_time }}</td>
          </tr>
          <tr>
            <th>Ticket Close DateTime</th>
            <td>{{ payload.ticket_close_date_time }}</td>
          </tr>
          <tr>
            <th>Summary</th>
            <td>
              <v-textarea
                placeholder="Write comments"
                rows="3"
                dense
                outlined
                type="text"
                v-model="payload.summary"
                :hide-details="!errors.summary"
                :error-messages="
                  errors && errors.summary ? errors.summary[0] : ''
                "
              ></v-textarea>
            </td>
          </tr>
          <tr>
            <th>Before Attachment</th>
            <td>
              <UploadAttachment
                label="Upload Before Attachment"
                @file-selected="
                  (e) => {
                    payload.before_attachment = e;
                  }
                "
              />
            </td>
          </tr>
          <tr>
            <th>After Attachment</th>
            <td>
              <UploadAttachment
                label="Upload After Attachment"
                @file-selected="
                  (e) => {
                    payload.after_attachment = e;
                  }
                "
              />
            </td>
          </tr>
          <tr>
            <th>Status</th>
            <td>
              <v-select
                :items="[
                  `Open`,
                  `In Progress`,
                  `Pending`,
                  `Close`,
                  `Quotation Advised`,
                ]"
                dense
                outlined
                v-model="payload.status"
                :hide-details="!errors.status"
                :error-messages="
                  errors && errors.status ? errors.status[0] : ''
                "
              ></v-select>
            </td>
          </tr>
        </table>
        <v-row>
          <v-col cols="12" class="text-right mt-5">
            <v-btn small :loading="loading" @click="submit" class="primary"
              >Submit</v-btn
            >
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    valid: false,
    popupDeviceId: null,
    editDialog: false,
    filters: {},
    isFilter: false,
    totalRowsCount: 0,
    snack: false,
    payload: {
      summary: "write your summary here",
    },
    Model: "Equipment",
    options: {},
    endpoint: "equipmentCategoryWithQuestions",
    snackbar: false,
    dialog: false,
    data: [],
    loading: false,
    total: 0,
    selectedOption: null,
    radioOptions: ["Yes", "No", "N/A"], // Replace this with your options
    response: "",
    errors: [],
  }),

  created() {
    this.$axios
      .get(`ticket/${this.$route.params.id}`)
      .then(({ data }) => (this.payload = data));
    this.$axios.get(this.endpoint).then(({ data }) => (this.data = data));
  },

  methods: {
    statusRelatedColor(value) {
      let color = {
        Open: "green",
        "In Progress": "blue",
        Close: "grey",
      };
      return color[value];
    },
    submit() {
      this.loading = true;

      let options = {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      };

      let FormEntry = new FormData();

      FormEntry.append(`work_id`, this.$route.params.id);
      FormEntry.append(`work_type`, "ticket");
      FormEntry.append(`equipment_category_id`, 0);
      FormEntry.append(`summary`, this.payload.summary ?? "");
      FormEntry.append(`technician_id`, this.$auth.user.id);

      if (
        this.payload.before_attachment &&
        this.payload.before_attachment.name
      ) {
        FormEntry.append(`before_attachment`, this.payload.before_attachment);
      }
      if (this.payload.after_attachment && this.payload.after_attachment.name) {
        FormEntry.append(`after_attachment`, this.payload.after_attachment);
      }

      this.$axios
        .post(`/form_entry`, FormEntry, options)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.updateServiceCallStatus();
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    updateServiceCallStatus() {
      this.$axios
        .put(`/ticket/${this.$route.params.id}`, { status: this.payload.status })
        .then(({ data }) => {
          this.errors = [];
          alert("Form has been added");
          this.$router.push("/");
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    handleErrorResponse(response) {
      console.log(response);
      this.loading = false;
      if (!response) {
        return;
      }
      let { status, data } = response;

      if (status && status == 422) {
        this.errors = data.errors;
        return;
      }
    },
  },
};
</script>
<style scoped>
@import url("../../assets/tableStyles.css");
</style>
