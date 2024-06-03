<template>
  <div v-if="item.id">
    <div class="text-center ma-5">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <v-toolbar class="primary my-2" rounded dense dark>
      <b>Company Details</b>
    </v-toolbar>
    <v-card v-if="equipmentCategoryObj" class="my-2">
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="3">
              <div class="text-center">
                <v-avatar size="150">
                  <v-img :src="equipmentCategoryObj.company_logo"> </v-img>
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
                    v-model="equipmentCategoryObj.company_name"
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
                    v-model="equipmentCategoryObj.company_email"
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
                    v-model="equipmentCategoryObj.company_show_member_from"
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
                    v-model="equipmentCategoryObj.company_show_expiry"
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
                    v-model="equipmentCategoryObj.company_contact_number"
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
                    v-model="equipmentCategoryObj.company_address"
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
                    <td>Reference #</td>
                    <td>
                      {{ item.id }}
                    </td>
                  </tr>
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
                    <td>Description</td>
                    <td>
                      <v-textarea
                        rows="3"
                        dense
                        outlined
                        type="text"
                        v-model="item.description"
                        :hide-details="!errors.description"
                        :error-messages="
                          errors && errors.description
                            ? errors.description[0]
                            : ''
                        "
                        readonly
                      ></v-textarea>
                    </td>
                  </tr>
                  <!-- <tr>
                <td>Prority</td>
                <td>{{ item?.priority?.name }}</td>
              </tr> -->
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
          Problem description
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
                  readonly
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
        <v-toolbar class="blue" rounded dense dark> Actual Problem </v-toolbar>
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
          Action needs to be taken
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
                  label="Action needs to be taken"
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
          Recommendation/Suggestion
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
              <v-col cols="6" class="text-right">
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
        <v-card dense class="my-2" rounded>
          <v-container>
            <v-row>
              <v-col cols="12" class="text-rights">
                <SignaturePad
                  label="Technician Signature"
                  @sign="
                    (e) => {
                      payload.sign = e;
                    }
                  "
                />
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

      <v-col cols="12" v-if="payload.sign">
        <v-card dense class="my-2" rounded>
          <v-container>
            <v-row>
              <v-col cols="12" class="">
                <v-btn :loading="loading" @click="submit" block color="primary"
                  >Submit</v-btn
                >
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    newHeadings: require("@/jsons/questions/ticket.json"),
    valid: false,
    popupDeviceId: null,
    editDialog: false,
    filters: {},
    isFilter: false,
    totalRowsCount: 0,
    snack: false,
    payload: {
      sign: null,
      equipment_category_id: ``,
      summary: "write your summary here",
    },
    item: {},
    Model: "Equipment",
    options: {},
    endpoint: "equipmentCategoryList",
    snackbar: false,
    dialog: false,
    loading: false,
    total: 0,
    selectedOption: null,
    radioOptions: ["Yes", "No", "N/A"], // Replace this with your options
    response: "",
    errors: [],
    whatsappPayload: {},
    equipmentCategoryObj: {},
  }),
  created() {
    this.equipmentCategoryObj = this.$route.query;

    this.payload.equipment_category_id = this.$route.query.id;

    this.$axios.get(`ticket/${this.$route.params.id}`).then(({ data }) => {
      this.item = data;
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
      FormEntry.append(
        `equipment_category_id`,
        this.payload.equipment_category_id
      );
      FormEntry.append(`summary`, this.payload.summary ?? "");
      FormEntry.append(`technician_id`, this.$auth.user.id);
      FormEntry.append(`actual_problem`, "-----");
      FormEntry.append(`action_taken`, this.payload.action_taken);
      FormEntry.append(`description`, this.payload.description);
      FormEntry.append(
        `ticket_close_date_time`,
        this.payload.ticket_close_date_time
      );

      if (this.payload.after_attachment && this.payload.after_attachment.name) {
        FormEntry.append(`after_attachment`, this.payload.after_attachment);
      }

      if (
        this.payload.before_attachment &&
        this.payload.before_attachment.name
      ) {
        FormEntry.append(`before_attachment`, this.payload.before_attachment);
      }

      FormEntry.append(`sign`, this.payload.sign);

      this.$axios
        .post(`/form_entry`, FormEntry, options)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            alert("Form has been added");
            // this.updateServiceCallStatus();
          }
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },
    updateServiceCallStatus() {
      this.$axios
        .put(`/ticket/${this.$route.params.id}`, {
          status: this.payload.status,
        })
        .then(({ data }) => {
          this.errors = [];
          this.sendWhatsapp();
        })
        .catch(({ response }) => this.handleErrorResponse(response));
    },
    sendWhatsapp() {
      this.whatsappPayload = {
        number: this.item.company.contact_number,
        message: `🔧 *Service Update* 🔧

Hello *${this.item.company.name}*,

This is confirmation message from Akil Security regarding the update of the Service.

Reference Number # *${this.item.id}*,

🛠️ *Problem Identified:*
${this.payload.description}

🔍 *Observations & Comments	:*
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
<style scoped>
@import url("@/assets/tableStyles.css");
</style>
