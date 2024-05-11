<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
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

          <v-card-text>
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
                <span class="primary--text">
                  <UploadAttachment
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
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12">
        <v-toolbar class="red" rounded dense dark> Defective Area </v-toolbar>
        <v-card dense class="my-2" rounded>
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
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark> Report Summary </v-toolbar>
        <v-card dense class="my-2" rounded>
          <!-- <v-card-title>
            <small class="mx-1">
              Recommendation (take note of blind spots)</small
            >
          </v-card-title> -->

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
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>

      <v-col cols="12">
        <v-card dense class="my-2" rounded>
          <v-card-tex>
            <v-container>
              <div>Technician Details</div>
              <v-row>
                <v-col cols="6">
                  <v-card
                    elevation="0"
                    class="mt-2"
                    style="width: 175px"
                    v-if="payload.sign"
                  >
                    <v-img :src="payload.sign"></v-img>
                  </v-card>
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
              </v-row>
            </v-container>
          </v-card-tex>
          <v-card-text>
            <SignaturePad
              label="Technician Signature"
              @sign="
                (e) => {
                  payload.sign = e;
                }
              "
            />
          </v-card-text>
        </v-card>
      </v-col>

      <v-col v-if="payload && payload.sign" cols="12" class="text-center">
        <v-btn class="green" block dense dark @click="submit"> Submit </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    currentDateTime: new Date(), // Initialize with current date and time
    attachments: [],
    newHeadings: require("../../headers/questions.json"),
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
  }),

  created() {
    this.$axios.get(this.endpoint).then(({ data }) => {
      this.data = data;
    });

    let splitIds = this.$route.params.id.split("_");
    this.payload.equipment_category_id = splitIds[0];
    this.payload.work_id = splitIds[1];
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
      this.checkListPayload = {
        form_entry_id: id,
        checklist: this.newHeadings,
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
            alert("Form has been added");
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    processCheckList(id) {
      let options = {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      };

      let payload = new FormData();

      this.data.forEach((category) => {
        if (this.EQCID == category.id) {
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
            // this.updateServiceCallStatus();
            alert("Form has been added");
          }
        })
        .catch((e) => console.log(e));
    },

    updateServiceCallStatus() {
      this.$axios
        .put(`/service_call/${this.$route.params.id}`, { status: "Completed" })
        .then(({ data }) => {
          this.errors = [];
          // this.sendWhatsapp();
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },

    sendWhatsapp() {
      this.whatsappPayload = {
        number: this.item.contract.company.contact_number,
        message: `🔧 *Service Update* 🔧

Hello *${this.item.contract.company.name}*,

This is confirmation message from Akil Security regarding the update of the Service.

Reference Number # *${this.item.id}*,

🔍 *Summary	:*
${this.payload.summary}

📞 *Contact:*
If you have any questions or concerns, feel free to reach out to me at +971 52 904 8025 or reply to this message.

Thank you for your patience!

Best regards,
Akil Security
`,
      };
      this.$axios
        .post(`/sendWhatsapp`, this.whatsappPayload)
        .then(({ data }) => {
          this.errors = [];
          alert("Form has been added");
          console.log(data);
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
