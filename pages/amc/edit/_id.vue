<template>
  <v-container>
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <!-- <v-card outlined>
      <pre>{{ checkListPayload }}</pre>
    </v-card> -->
    <v-row
      v-for="(newHeading, newHeadingIndex) in newHeadings"
      :key="newHeadingIndex"
    >
      <v-col cols="6" offset="3">
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
                  :class="question.selectedOption == option ? 'primary' : ''"
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
                <span class="primary--text" v-if="question && question.attachment_name">
                  <ViewFile
                    :src="`http://192.168.2.24:8001/checklist/${form_entry_id}/${question.attachment_name}`"
                  />
                </span>
                <span class="grey--text">
                  <v-icon class="ml-5" color="grey"
                    >mdi-clipboard-outline</v-icon
                  >
                  Add Note
                </span>

                <v-textarea
                  readonly
                  v-model="question.remarks"
                  dense
                  :hide-details="true"
                  v-if="question.isRemarks"
                  label="Remarks"
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
      <v-col cols="6" offset="3">
        <v-toolbar class="primary" rounded dense dark>
          Defective Area
        </v-toolbar>
        <v-card dense class="my-2" rounded>
          <v-card-title> Any other defects found? </v-card-title>

          <v-card-text>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  readonly
                  v-model="payload.defective_area"
                  dense
                  :hide-details="true"
                  label="Remarks"
                  rows="1"
                  auto-grow
                ></v-textarea>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="6" offset="3">
        <v-toolbar class="primary" rounded dense dark> Focus Area </v-toolbar>
        <v-card dense class="my-2" rounded>
          <v-card-title>
            Recommendation (take note of blind spots)
          </v-card-title>

          <v-card-text>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  readonly
                  v-model="payload.summary"
                  dense
                  :hide-details="true"
                  label="Remarks"
                  rows="1"
                  auto-grow
                ></v-textarea>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
        <v-divider></v-divider>
      </v-col>

      <v-col cols="6" offset="3">
        <!-- <v-toolbar class="primary" rounded dense dark> Action </v-toolbar> -->
        <v-card dense class="my-2" rounded>
          <v-card-title>Customer action required</v-card-title>

          <v-card-tex>
           <v-container>
            <v-text-field
              v-model="payload.customer_name"
              dense
              :hide-details="true"
              label="Customer Name"
              outlined
            ></v-text-field>
            <br />
            <v-text-field
              v-model="payload.customer_phone"
              dense
              :hide-details="true"
              label="Customer Phone"
              outlined
            ></v-text-field>
           </v-container>
          </v-card-tex>
          <v-card-text>
            <v-btn
              v-if="payload && payload.customer_sign"
              class="primary"
              block
              dense
              dark
              @click="submit"
            >
              Submit
            </v-btn>

            <SignaturePad
              v-if="!payload.customer_sign"
              label="Customer Signature"
              @sign="
                (e) => {
                  payload.customer_sign = e;
                }
              "
            />
          </v-card-text>
        </v-card>
        <v-divider></v-divider>
      </v-col>

      <v-col cols="6" offset="3" class="text-center"> </v-col>
    </v-row>

    <!-- <v-card v-if="equipmentCategoryId" class="mt-2" outlined elevation="0">
      <v-container>
        <p>
          <b>Upload Before Attachment</b>
        </p>
        <v-row no-gutters>
          <v-col cols="12">
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
      </v-container>
    </v-card>

    <v-card v-if="equipmentCategoryId" class="mt-2" outlined elevation="0">
      <v-container>
        <p>
          <b>Upload After Attachment</b>
        </p>
        <v-row no-gutters>
          <v-col cols="12">
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
      </v-container>
    </v-card> -->
  </v-container>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    attachments: [],
    newHeadings: [],
    snack: false,
    checkListPayload: {},
    payload: {
      work_id: 0,
      equipment_category_id: 0,
      sign: null,
      defective_area: "",
      summary: "",
      customer_sign: null,
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
    form_entry_id: 0,
  }),

  created() {
    this.form_entry_id = this.$route.params.id;
    this.$axios.get(`/form_entry/${this.$route.params.id}`).then(({ data }) => {
      this.payload.defective_area = data.defective_area;
      this.payload.summary = data.summary;
      this.newHeadings = data.checklists[0].checklist;
    });
  },

  methods: {
    submit() {
      let payload = {
        customer_name: this.payload.customer_name,
        customer_phone: this.payload.customer_phone,
        customer_sign: this.payload.customer_sign,
      };
      this.loading = true;
      this.$axios
        .post(`/form_entry/customer_update/${this.form_entry_id}`, payload)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            alert("Form has been modified");
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
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
