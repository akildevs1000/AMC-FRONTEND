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
                <span
                  class="primary--text"
                  v-if="question && question.attachment_name"
                >
                  <ViewFile
                    icon="mdi-paperclip"
                    :label="question.attachment_name"
                    :src="`checklist/${form_entry_id}/${question.attachment_name}`"
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
        <v-toolbar class="red" rounded dense dark> Defective Area </v-toolbar>
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  readonly
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

      <v-col cols="12" v-if="payload.technician">
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <div>Technician Details</div>
                </v-col>
                <v-col cols="6">
                  <v-text-field
                    v-model="payload.technician.name"
                    label="Name"
                    dense
                    :hide-details="true"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    v-model="payload.technician.phone_number"
                    label="Phone"
                    dense
                    :hide-details="true"
                  ></v-text-field
                  ><br />
                  <v-text-field
                    v-model="payload.technician.email"
                    label="Email"
                    dense
                    :hide-details="true"
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
              </v-row>
            </v-container>
          </v-card-text>
        </v-card>
      </v-col>

      <v-col cols="12">
        <v-card dense class="my-2" rounded>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <div>Customer Details</div>
                </v-col>
                <v-col cols="6">
                  <v-text-field
                    v-model="payload.customer_name"
                    label="Name"
                    dense
                    :hide-details="true"
                  ></v-text-field>
                  <br />
                  <v-text-field
                    v-model="payload.customer_phone"
                    label="Phone"
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
        </v-card>
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
      defective_area: "",
      summary: "",
      customer_sign: null,
    },
    Model: "Equipment",
    options: {},
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
      this.payload = data;
      this.newHeadings = data.checklist.checklist;
    });
  },

  methods: {
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
  },
};
</script>
