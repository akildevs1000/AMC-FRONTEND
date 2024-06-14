<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <!-- <v-card outlined>
      <v-container>
        <pre>{{ payload.attachments }}</pre>
      </v-container>
    </v-card> -->
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

          <v-card-text>
            <v-row>
              <v-col
                cols="4"
                class="text-center"
                v-for="(option, optionIndex) in question.options"
                :key="optionIndex"
              >
                <v-card
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
                <span v-if="payload && payload.id" class="primary--text">
                  <ViewMultipleFiles
                    label="Photos"
                    :form_entry_id="payload.id"
                    :attachments="
                      getRelatedPics(
                        `${newHeadingIndex + 1}.${questionIndex + 1}`
                      )
                    "
                  />
                </span>
                <span class="grey--text">
                  <v-icon class="ml-5" color="grey"
                    >mdi-clipboard-outline</v-icon
                  >
                  Add Note
                </span>

                <v-textarea
                  class="mt-3"
                  outlined
                  readonly
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
        <v-toolbar class="blue" rounded dense dark> Report Summary </v-toolbar>
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  readonly
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
        <v-divider></v-divider>
      </v-col>

      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark>
          Customer Comments
        </v-toolbar>
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="payload.customer_note"
                  dense
                  :hide-details="true"
                  label="Customer Note"
                  rows="3"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
        <v-divider></v-divider>
      </v-col>

      <v-col cols="12">
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <div>Customer action required</div>
                </v-col>
                <v-col cols="6">
                  <v-text-field
                    v-model="payload.customer_name"
                    label="Customer Name"
                    dense
                    :hide-details="true"
                  ></v-text-field>
                  <!-- <v-autocomplete
                    @change="getRelatedCustomerInfo(payload.manager_id)"
                    v-model="payload.manager_id"
                    label="Select Customer"
                    :items="customers"
                    item-text="name"
                    item-value="id"
                    dense
                    :hide-details="true"
                  ></v-autocomplete> -->
                  <!-- <br />
                  <v-text-field
                    v-model="payload.customer_email"
                    label="Email"
                    dense
                    :hide-details="true"
                  ></v-text-field> -->
                  <br />
                  <v-text-field
                    v-model="payload.customer_phone"
                    label="Phone"
                    dense
                    :hide-details="true"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    v-model="payload.customer_position"
                    label="Position"
                    dense
                    :hide-details="true"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    readonly
                    v-model="payload.customer_signed_datetime"
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
                    v-if="payload.customer_sign"
                  >
                    <v-img :src="payload.customer_sign"></v-img>
                  </v-card>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-text>
            <SignaturePad
              label="Signature"
              @sign="
                (e) => {
                  payload.customer_sign = e;
                }
              "
            />
          </v-card-text>
        </v-card>
      </v-col>

      <v-col cols="12" class="text-center">
        <v-card-text>
          <v-btn
            :loading="loading"
            v-if="payload && payload.customer_sign"
            class="green"
            block
            dense
            dark
            @click="submit"
          >
            Submit
          </v-btn>
        </v-card-text>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    loading: false,
    currentDateTime: new Date(), // Initialize with current date and time
    attachments: [],
    newHeadings: [],
    snack: false,
    checkListPayload: {},
    payload: {
      work_id: 0,
      equipment_category_id: 0,
      sign: null,
      summary: "",
      customer_sign: null,
    },
    customers: [],
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
    form_entry_id: 0,
    newDialogKey: 1,
    myAttachments: [],
    responseDialog: false,
    response_image: "/success.png",
  }),

  created() {
    this.form_entry_id = this.$route.params.id;
    this.$axios.get(`/form_entry/${this.$route.params.id}`).then(({ data }) => {
      this.payload = data;
      this.newHeadings = data.checklist.checklist;
      this.payload.customer_signed_datetime = this.formattedDateTime();

      this.myAttachments = this.payload.attachments;
    });

    this.$axios
      .get(`/manager?company_id=${this.$route.query.company_id}`)
      .then(({ data }) => {
        this.customers = data.data;
      })
      .catch((e) => console.log(e));
  },

  methods: {
    getRandomId() {
      return ++this.newDialogKey;
    },
    getRelatedPics(item) {
      return this.myAttachments.filter((e) =>
        e.attachment.includes(`pic-${item}`)
      );
    },
    getRelatedCustomerInfo(id) {
      let found = this.customers.find((e) => e.id == id);
      this.payload.customer_name = found.name;
      this.payload.customer_email = found.email;
      this.payload.customer_position = found.position;
      this.payload.customer_phone = found.phone_number;
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
    submit() {
      this.loading = true;
      let payload = {
        customer_name: this.payload.customer_name,
        customer_phone: this.payload.customer_phone,
        customer_position: this.payload.customer_position,
        customer_sign: this.payload.customer_sign,
        customer_note: this.payload.customer_note,
        customer_signed_datetime: this.payload.customer_signed_datetime,
      };
      this.loading = true;
      this.$axios
        .post(`/form_entry/customer_update/${this.form_entry_id}`, payload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            // this.sendWhatsapp();
            // alert("Checklist has been signed");
            this.response_image = "/success.png";
            this.responseDialog = true;
            setTimeout(() => {
              this.responseDialog = false;
              this.$router.push("/checklist");
            }, 3000);
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },
    updateServiceCallStatus() {
      this.$axios
        .put(`/service_call/${this.form_entry_id}`, { status: "Completed" })
        .then(({ data }) => {
          this.errors = [];
          // this.sendWhatsapp();
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },
    sendWhatsapp() {
      this.whatsappPayload = {
        number: this.payload.customer_phone,
        message: `🔧 *Service Update* 🔧

Hello *${this.payload.customer_name}*,

This is confirmation message from Akil Security regarding the update of the Service.

Reference Number # *${this.form_entry_id}*,

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
          alert("Checklist has been signed");
          window.history.back();
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