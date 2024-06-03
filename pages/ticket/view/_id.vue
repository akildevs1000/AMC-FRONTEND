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
    <v-toolbar class="primary my-2" rounded dense dark>
      <b>Company Details</b>
    </v-toolbar>
    <v-card v-if="item" class="my-2">
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="3">
              <div class="text-center">
                <v-avatar size="150">
                  <v-img :src="item.company_logo"> </v-img>
                </v-avatar>
              </div>
            </v-col>
            <v-col cols="9" dense>
              <v-row>
                <v-col cols="6" dense>
                  <v-text-field
                    label="Name"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_name"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>
                <v-col cols="6" dense>
                  <v-text-field
                    label="Email"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_email"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>
                <v-col cols="6" dense>
                  <v-text-field
                    label="Member From"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_show_member_from"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>

                <v-col cols="6" dense>
                  <v-text-field
                    label="Expiry"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_show_expiry"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>
                <v-col cols="6" dense>
                  <v-text-field
                    label="Contact Number"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_contact_number"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>
                <v-col cols="6" dense>
                  <v-text-field
                    label="Address"
                    dense
                    outlined
                    type="text"
                    v-model="item.company_address"
                    :hide-details="true"
                    readonly
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
    </v-card>
    <v-row>
      <v-col cols="12">
        <v-toolbar class="primary" rounded dense dark>
          Ticket Details
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.description ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12">
                <table>
                  <tr>
                    <td>Title</td>
                    <td>
                      <v-text-field
                        dense
                        outlined
                        type="text"
                        v-model="item.title"
                        :hide-details="!errors.title"
                        :error-messages="
                          errors && errors.title ? errors.title[0] : ''
                        "
                        readonly
                      ></v-text-field>
                    </td>
                  </tr>
                  <tr>
                    <td>Attachment</td>
                    <td>
                      <ViewAttachment
                        v-if="item.attachment"
                        :src="item.attachment"
                      />
                    </td>
                  </tr>
                  <tr>
                    <td>Ticket Open DateTime</td>
                    <td>{{ item.ticket_open_date_time }}</td>
                  </tr>
                  <tr>
                    <td>Ticket Close DateTime</td>
                    <td>{{ payload.ticket_close_date_time }}</td>
                  </tr>
                </table>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark>
          Issue Reported by Customer
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.description ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="item.description"
                  dense
                  :hide-details="true"
                  label="Issue Reported by Customer"
                  rows="3"
                ></v-textarea>
              </v-col>
              <v-col v-if="errors.description">
                <span class="red--text">
                  {{ errors.description[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark>
          Problem Describe by Technician
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.description ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="payload.description"
                  dense
                  :hide-details="true"
                  label="Problem description"
                  rows="3"
                ></v-textarea>
              </v-col>
              <v-col v-if="errors.description">
                <span class="red--text">
                  {{ errors.description[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark>
          Action Taken By Technician
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.action_taken ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-right">
                <v-textarea
                  outlined
                  v-model="payload.action_taken"
                  dense
                  :hide-details="true"
                  label="Action Taken By Technician"
                  rows="3"
                ></v-textarea>
              </v-col>
              <v-col v-if="errors.action_taken">
                <span class="red--text">
                  {{ errors.action_taken[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12">
        <v-toolbar class="blue" rounded dense dark>
          Recommendation/ suggestion to customer
        </v-toolbar>
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
                  label="Recommendation/Suggestion"
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
      <v-col cols="4">
        <v-toolbar class="blue" rounded dense dark> Status </v-toolbar>
        <v-card
          dense
          rounded
          :style="`border:1px solid ${errors.status ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-rights">
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
              </v-col>
              <v-col v-if="errors.status">
                <span class="red--text">
                  {{ errors.status[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="4">
        <v-toolbar class="blue" rounded dense dark>
          Before Attachment
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.summary ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-center">
                <UploadAttachment
                  label=""
                  @file-selected="
                    (e) => {
                      payload.before_attachment = e;
                    }
                  "
                />
              </v-col>
              <v-col v-if="errors.before_attachment">
                <span class="red--text">
                  {{ errors.before_attachment[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="4">
        <v-toolbar class="blue" rounded dense dark>
          After Attachment
        </v-toolbar>
        <v-card
          dense
          class="my-2"
          rounded
          :style="`border:1px solid ${errors.summary ? 'red' : 'white'}`"
        >
          <v-container>
            <v-row>
              <v-col cols="12" class="text-center">
                <UploadAttachment
                  label=""
                  @file-selected="
                    (e) => {
                      payload.after_attachment = e;
                    }
                  "
                />
              </v-col>
              <v-col v-if="errors.after_attachment">
                <span class="red--text">
                  {{ errors.after_attachment[0] }}
                </span>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
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
    myAttachments: [],
    item: {},
    form_entry_id: 0,
  }),

  created() {
    this.item = this.$route.query;

    this.form_entry_id = this.$route.params.id;

    this.$axios.get(`/form_entry/${this.$route.params.id}`).then(({ data }) => {
      this.payload = data;
      this.myAttachments = data.attachments;
      this.newHeadings = data.checklist.checklist;
    });
  },

  methods: {
    getRelatedPics(item) {
      return this.myAttachments.filter((e) =>
        e.attachment.includes(`pic-${item}`)
      );
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
  },
};
</script>
<style scoped>
@import url("..././../assets/tableStyles.css");
</style>
