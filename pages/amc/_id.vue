<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <v-row>
      <v-col cols="12">
        <!-- <pre>
            {{ data }}
          </pre
        > -->
        <v-expansion-panels>
          <v-expansion-panel v-for="(eqCId, i) in data" :key="i">
            <v-expansion-panel-header class="primarywhite--text">
              <h3>{{ eqCId.name }}</h3>
            </v-expansion-panel-header>
            <v-expansion-panel-content class="my-1">
              <v-card
                max-width="600"
                class="mb-2"
                outlined
                elevation="0"
                v-for="(h, hi) in eqCId.headings"
                :key="hi"
              >
                <v-container>
                  <p>
                    <b>{{ hi + 1 }}. {{ h.name }}</b>
                  </p>

                  <v-container v-for="(q, qi) in h.questions" :key="qi">
                    <!-- <p>Question Id. {{ q.id }}</p> -->
                    <p>{{ qi + 1 }}. {{ q.name }}</p>

                    <v-radio-group v-model="q.selectedOption" row>
                      <v-radio
                        v-for="(option, index) in radioOptions"
                        :key="index"
                        :label="option"
                        :value="option"
                      ></v-radio>
                    </v-radio-group>
                    <v-row no-gutters>
                      <v-col cols="6" class="my-1">
                        <v-textarea
                          dense
                          :hide-details="true"
                          outlined
                          v-model="q.remarks"
                          label="Remarks"
                          rows="1"
                          auto-grow
                        ></v-textarea>
                      </v-col>
                    </v-row>
                    <v-row no-gutters>
                      <v-col cols="3">
                        <UploadAttachment
                          @file-selected="
                            (e) => {
                              q.attachment = e;
                            }
                          "
                        />
                      </v-col>
                    </v-row>
                  </v-container>
                </v-container>
              </v-card>
              <v-row no-gutters>
                <v-col cols="9" class="my-1">
                  <v-textarea
                    v-model="payload.summary"
                    dense
                    outlined
                    label="Summary"
                    rows="3"
                    auto-grow
                  ></v-textarea>
                </v-col>
              </v-row>
              <v-row no-gutters class="mt-1">
                <v-col cols="3">
                  <UploadAttachment
                    label="Before Attachment"
                    @file-selected="
                      (e) => {
                        payload.before_attachment = e;
                      }
                    "
                  />
                </v-col>
              </v-row>
              <v-row no-gutters class="mt-1">
                <v-col cols="3">
                  <UploadAttachment
                    label="After Attachment"
                    @file-selected="
                      (e) => {
                        payload.after_attachment = e;
                      }
                    "
                  />
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col cols="9" class="my-1">
                  <v-btn color="primary" @click="submit(eqCId.id)"
                    >Submit</v-btn
                  >
                </v-col>
              </v-row>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
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
    this.$axios.get(this.endpoint).then(({ data }) => (this.data = data));
  },

  methods: {
    submit(cid) {
      this.loading = true;

      let options = {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      };

      let FormEntry = new FormData();

      FormEntry.append(`work_id`, this.$route.params.id);
      FormEntry.append(`work_type`, "amc");
      FormEntry.append(`equipment_category_id`, cid);
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
            this.processCheckList(data.record.id, cid);
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    processCheckList(id, cid) {
      let options = {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      };

      let payload = new FormData();

      this.data.forEach((category) => {
        if (cid == category.id) {
          category.headings.forEach((heading, hi) => {
            heading.questions.forEach((e, i) => {
              payload.append(`headings[${hi}][${i}][form_entry_id]`, id);
              payload.append(`headings[${hi}][${i}][question_id]`, e.id);
              payload.append(
                `headings[${hi}][${i}][selectedOption]`,
                e.selectedOption ?? "N/A"
              );
              payload.append(`headings[${hi}][${i}][remarks]`, e.remarks ?? "");

              if (e.attachment && e.attachment.name) {
                payload.append(
                  `headings[${hi}][${i}][attachment]`,
                  e.attachment
                );
              }
            });
          });
        }
      });

      this.$axios
        .post(`/checklist`, payload, options)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.updateServiceCallStatus();
          }
        })
        .catch((e) => console.log(e));
    },

    updateServiceCallStatus() {
      this.$axios
        .put(`/service_call/${this.$route.params.id}`, { status: "completed" })
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
