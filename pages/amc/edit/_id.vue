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
              <v-col cols="12" class="text-right">
                <!-- <span v-if="payload" class="primary--text">
                  <ViewMultipleFiles
                    label="Photos"
                    :form_entry_id="payload.id"
                    :attachments="
                      getRelatedPics(
                        `${newHeadingIndex + 1}.${questionIndex + 1}`
                      )
                    "
                  />
                </span> -->
                <span class="primary--text">
                  <UploadMultipleAttachments
                    :name="`${newHeadingIndex + 1}.${questionIndex + 1}`"
                    label="Take Photo"
                    @files-selected="handleMultipleFileSelection($event)"
                    :defaultAttachments="
                      getRelatedPics(
                        `${newHeadingIndex + 1}.${questionIndex + 1}`
                      )
                    "
                  />
                </span>
                <!-- <span class="primary--text">
                  <UploadAttachment
                    :displayPreview="false"
                    label="Take Photo"
                    :name="`pic-${newHeadingIndex + 1}.${
                      questionIndex + 1
                    }.png`"
                    @file-selected="
                      handleFileSelection(
                        $event,
                        `pic-${newHeadingIndex + 1}.${questionIndex + 1}.png`,
                        question
                      )
                    "
                  />
                </span> -->
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
          <v-card-text>
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
                <v-col v-if="errors.defective_area">
                  <span class="red--text">
                    {{ errors.defective_area[0] }}
                  </span>
                </v-col>
              </v-col>
            </v-row>
          </v-card-text>
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
          <v-card-text>
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
                <v-col v-if="errors.summary">
                  <span class="red--text">
                    {{ errors.summary[0] }}
                  </span>
                </v-col>
              </v-col>
            </v-row>
          </v-card-text>
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
                    v-model="$auth.user.name"
                    dense
                    :hide-details="true"
                    label="Name"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    v-model="$auth.user.phone_number"
                    dense
                    :hide-details="true"
                    label="Phone"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    v-model="$auth.user.email"
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

                  <!-- <pre>
                    {{ attachments }}
                  </pre> -->
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-text>
            <SignaturePad
              label="Technician Signature"
              @sign="
                (e) => {
                  payload.sign = e;
                  isSigned = true;
                }
              "
            />
          </v-card-text>
        </v-card>
      </v-col>
      <v-col v-if="isSigned" cols="12" class="text-center">
        <v-btn
          :loading="loading"
          class="green"
          block
          dense
          dark
          @click="submit"
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
    isSigned: false,
    currentDateTime: new Date(), // Initialize with current date and time
    attachments: [],
    newHeadings: [],
    snack: false,
    checkListPayload: {},
    payload: {
      defective_area: null,
      summary: null,
      sign: null,
      technician_signed_datetime: null,
    },
    newDialogKey: 1,
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
    attachments: [],
    item: {},
    form_entry_id: 0,
    showJson: {},
    BACKEND_ABSOLUTE_URL: process.env.BACKEND_ABSOLUTE_URL,
    responseDialog: false,
    response_image: "/success.png",
  }),

  created() {
    this.$axios.get(this.endpoint).then(({ data }) => {
      this.data = data;
    });

    this.form_entry_id = this.$route.params.id;

    this.$axios.get(`/form_entry/${this.$route.params.id}`).then(({ data }) => {
      this.payload = {
        defective_area: data.defective_area,
        summary: data.summary,
        sign: data.sign,
        technician_signed_datetime: this.formattedDateTime(),
      };
      this.attachments = data.attachments;
      this.newHeadings = data.checklist.checklist;
    });
  },
  methods: {
    getRelatedPics(item) {
      return this.attachments.filter((e) =>
        e.attachment.includes(`pic-${item}`)
      );
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
    getRandomId() {
      return ++this.newDialogKey;
    },
    setDefaultImage(question) {
      if (question && question.attachment_name) {
        return `${BACKEND_ABSOLUTE_URL}/checklist/${this.form_entry_id}/${question.attachment_name}`;
      }

      return null;
    },
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

      return `${dayOfWeek} ${dayOfMonth} ${month} ${year} ${hours}:${minutes}`;
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
      question["displayAttachmentPreview"] = false;
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

    submit() {
      this.loading = true;
      this.loading = true;

      this.payload.checklist = this.newHeadings;
      this.payload.attachments = this.attachments;

      this.$axios
        .put(`/form_entry/${this.form_entry_id}`, this.payload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.response_image = "/success.png";
            this.responseDialog = true;
            setTimeout(() => {
              this.responseDialog = false;
              this.$router.push("/checklist");
            }, 3000);
            // this.$router.push("/checklist");
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    handleErrorResponse(response) {
      this.response_image = "/fail.png";
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
