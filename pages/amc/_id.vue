<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>

    <div class="text-center">
      <v-dialog
        class="remove-transparent-bg"
        style="box-shadow: none !important"
        v-model="responseDialog"
        width="500"
      >
        <v-card style="background: none">
          <v-toolbar style="background: none" flat dense>
            <v-spacer></v-spacer>
            <!-- <v-icon @click="dialog = false">mdi-close</v-icon> -->
          </v-toolbar>

          <v-card-text>
            <p class="text-center">
              <v-img
                :src="response_image"
                alt="Avatar"
                height="125px"
                width="125px"
                style="display: inline-block"
              ></v-img>
            </p>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>

    <v-row
      v-for="(newHeading, newHeadingIndex) in newHeadings"
      :key="newHeadingIndex"
    >
      <v-col cols="12">
        <v-toolbar class="primary" rounded dense dark>
          {{ newHeadingIndex + 1 }}. {{ newHeading.heading }}
        </v-toolbar>
        <v-card
          :style="`border:1px solid ${
            errors[
              `checklist.${newHeadingIndex}.questions.${questionIndex}.selectedOption`
            ]
              ? 'red'
              : 'white'
          }`"
          dense
          class="my-2"
          rounded
          v-for="(question, questionIndex) in newHeading.questions"
          :key="questionIndex"
        >
          <v-card-title>
            <small class="mx-1"
              >{{ newHeadingIndex + 1 }}.{{ questionIndex + 1 }}
              {{ question.question }}</small
            >
          </v-card-title>

          <v-container>
            <v-row>
              <v-col
                cols="4"
                class="text-center"
                v-for="(option, optionIndex) in question.options"
                :key="optionIndex"
              >
                <v-card
                  @click="question.selectedOption = option"
                  style="border: 1px #cfc9c9 solid; border-radius: 10px"
                  elevation="0"
                  :class="getCellStyle(question.selectedOption, option)"
                >
                  <v-card-text
                    class="pa-2"
                    :class="
                      question.selectedOption == option ? 'white--text' : ''
                    "
                  >
                    {{ option }}
                  </v-card-text>
                </v-card>
              </v-col>
              <v-col cols="6">
                <span
                  class="red--text"
                  v-if="
                    errors[
                      `checklist.${newHeadingIndex}.questions.${questionIndex}.selectedOption`
                    ]
                  "
                >
                  Option must be selected
                </span>
              </v-col>

              <v-col cols="12" class="text-right">
                <span class="primary--text">
                  <UploadMultipleAttachments
                    :name="`${newHeadingIndex + 1}.${questionIndex + 1}`"
                    label="Take Photo"
                    @files-selected="handleMultipleFileSelection($event)"
                  />
                </span>
                <span
                  @click="
                    () => {
                      question.isRemarks = !question.isRemarks;
                      question.remarks = null;
                    }
                  "
                  class="primary--text"
                >
                  <v-icon class="ml-5" color="primary"
                    >mdi-clipboard-outline</v-icon
                  >
                  Add Note
                </span>
              </v-col>
              <v-col cols="12">
                <v-textarea
                  class="mt-3"
                  outlined
                  v-model="question.remarks"
                  dense
                  :hide-details="true"
                  v-if="question.isRemarks"
                  label="Note"
                  rows="1"
                  auto-grow
                ></v-textarea>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12">
        <v-toolbar class="red" rounded dense dark> Defective Area </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.defective_area ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="payload.defective_area"
                  dense
                  :hide-details="true"
                  label="Remarks"
                  rows="3"
                ></v-textarea>
              </v-col>
              <v-col v-if="errors.defective_area">
                <span class="red--text">
                  {{ errors.defective_area[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark> Report Summary </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.summary ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="payload.summary"
                  dense
                  :hide-details="true"
                  label="Summary"
                  rows="3"
                ></v-textarea>
              </v-col>
              <v-col v-if="errors.summary">
                <span class="red--text">
                  {{ errors.summary[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>

      <v-col cols="12">
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <div>Technician Details</div>
                </v-col>
                <v-col cols="6">
                  <v-text-field
                    readonly
                    :value="$auth.user.name"
                    dense
                    :hide-details="true"
                    label="Name"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    :value="`0553303991`"
                    dense
                    :hide-details="true"
                    label="Phone"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    :value="$auth.user.email"
                    dense
                    :hide-details="true"
                    label="Email"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    v-model="payload.technician_signed_datetime"
                    dense
                    :hide-details="true"
                    label="Date Time"
                  ></v-text-field>
                </v-col>
                <v-col cols="6" class="d-flex justify-center">
                  <v-card
                    elevation="0"
                    class="mt-2"
                    style="width: 175px"
                    v-if="payload.sign"
                  >
                    <v-img :src="payload.sign"></v-img>
                  </v-card>
                </v-col>
                <v-col cols="12">
                  <SignaturePad
                    label="Technician Signature"
                    @sign="
                      (e) => {
                        payload.sign = e;
                      }
                    "
                  />
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
        </v-card>
      </v-col>
      <!-- <v-col v-if="exception">
        <pre>
          {{checkListPayload}}
        </pre>
      </v-col> -->
      <v-col v-if="payload && payload.sign" cols="12" class="text-center">
        <v-btn
          :loading="loading"
          class="green"
          block
          dense
          dark
          @click="validatePayload"
        >
          Submit</v-btn
        >
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    exception: null,
    currentDateTime: new Date(), // Initialize with current date and time
    attachments: [],
    newHeadings: [],
    snack: false,
    checkListPayload: {},
    payload: {
      technician_id: 0,
      work_id: 0,
      equipment_category_id: 0,
      work_type: "amc",
      sign: null,
      defective_area: null,
      summary: "",
    },
    Model: "Equipment",
    options: {},
    endpoint: "equipmentCategoryWithQuestionsList",
    snackbar: false,
    dialog: false,
    data: [],
    equipmentCategory: 0,

    loading: false,
    total: 0,
    selectedOption: null,
    selectedOptionIndex: null,
    radioOptions: ["Yes", "No", "N/A"], // Replace this with your options
    response: "",
    errors: [],
    item: {},
    equipmentCategoryObj: null,

    responseDialog: false,
    response_image: "/success.png",
  }),

  mounted() {
    this.equipmentCategoryObj = this.$route.query;
    let example = this.$route.query.example || false;
    let isExample = example == "true" || example == "1" ? "-example" : "";
    this.newHeadings = require(`@/jsons/questions/${this.equipmentCategoryObj.slug}${isExample}.json`);

    this.newHeadings = require(`@/jsons/questions/${this.equipmentCategoryObj.slug}${isExample}-example.json`);
  },

  created() {
    this.$axios.get(this.endpoint).then(({ data }) => {
      this.data = data;
    });

    this.payload.equipment_category_id = this.$route.query.id;
    this.payload.work_id = this.$route.params.id;
    this.payload.technician_id = this.$auth.user.id;

    this.formattedDateTime();
  },
  methods: {
    formattedDateTime() {
      const days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
      const months = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
      ];
      const dayOfWeek = days[this.currentDateTime.getDay()];
      const dayOfMonth = this.addOrdinalSuffix(this.currentDateTime.getDate());
      const month = months[this.currentDateTime.getMonth()];
      const year = this.currentDateTime.getFullYear();
      const hours = this.padZero(this.currentDateTime.getHours());
      const minutes = this.padZero(this.currentDateTime.getMinutes());

      this.payload.technician_signed_datetime = `${dayOfWeek} ${dayOfMonth} ${month} ${year} ${hours}:${minutes}`;
    },
    addOrdinalSuffix(number) {
      const suffixes = ["th", "st", "nd", "rd"];
      const v = number % 100;
      return number + (suffixes[(v - 20) % 10] || suffixes[v] || suffixes[0]);
    },
    padZero(number) {
      return number.toString().padStart(2, "0");
    },
    handleFileSelection(e, name, question) {
      this.attachments.push({
        name: name,
        attachment: e,
      });
      question["attachment_name"] = name;
    },

    handleMultipleFileSelection(e) {
      e.forEach((v, i) => {
        const attachmentExists = this.attachments.some(
          (att) => att.name === v.name
        );
        if (!attachmentExists) {
          this.attachments.push({
            name: v.name,
            attachment: v.preview,
          });
        }
      });
    },

    getCellStyle(selectedOption, option) {
      if (selectedOption == option) {
        if (["Excellent", "Good", "Yes"].includes(selectedOption)) {
          return "green lighten-2";
        } else if (["N/A"].includes(selectedOption)) {
          return "grey lighten-2";
        } else {
          return "red lighten-2";
        }
      } else {
        return "";
      }
    },

    validatePayload() {
      this.loading = true;
      this.payload.checklist = this.newHeadings;
      this.$axios
        .post(`/form_entry/validate`, this.payload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.submit();
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    submit() {
      this.loading = true;
      this.$axios

        .post(`/form_entry`, this.payload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.processCheckListV1(data.record.id);
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    processCheckListV1(id = 1) {
      this.loading = true;
      this.checkListPayload = {
        form_entry_id: id,
        checklist: this.payload.checklist,
        attachments: this.attachments,
      };

      this.$axios
        .post(`/checklist`, this.checkListPayload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            // this.updateServiceCallStatus();
            this.response_image = "/success.png";
            this.responseDialog = true;
            setTimeout(() => {
              this.responseDialog = false;
              this.$router.push("/checklist");
            }, 3000);
            // window.history.back();
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },
    handleErrorResponse(response) {
      this.response_image = "/fail.png";
      this.exception = response;
      this.loading = false;
      if (!response) {
        return;
      }
      let { status, data } = response;

      if (status && status == 422) {
        this.errors = data.errors;
        return;
      }

      this.responseDialog = true;
      setTimeout(() => {
        this.responseDialog = false;
      }, 3000);
    },
  },
};
</script>
<style>
.v-dialog.v-dialog--active {
  box-shadow: none !important;
}
</style>
