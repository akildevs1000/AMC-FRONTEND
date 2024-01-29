<template>
  <SignaturePad
    @sign="
      (e) => {
        payload.sign = e;
      }
    "
  />
</template>
<script>
export default {
  props: ["id"],
  data: () => ({
    EQCID: 0,
    snack: false,
    payload: {
      sign: null,
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
    item: {},
  }),

  created() {
    this.$axios.get(this.endpoint).then(({ data }) => {
      this.data = data;
      console.log(data);
    });

    this.$axios
      .get(`service_call/${this.$route.params.id}`)
      .then(({ data }) => {
        console.log((this.item = data));
      });
  },

  methods: {
    setEqcid(val) {
      this.EQCID = val;
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
      FormEntry.append(`work_type`, "amc");
      FormEntry.append(`equipment_category_id`, this.EQCID);
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
      if (this.payload.sign && this.payload.sign.name) {
        FormEntry.append(`sign`, this.payload.sign);
      }

      this.$axios
        .post(`/form_entry`, FormEntry, options)
        .then(({ data }) => {
          this.loading = false;
          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.processCheckList(data.record.id);
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
            this.updateServiceCallStatus();
          }
        })
        .catch((e) => console.log(e));
    },

    updateServiceCallStatus() {
      this.$axios
        .put(`/service_call/${this.$route.params.id}`, { status: "Completed" })
        .then(({ data }) => {
          this.errors = [];
          this.sendWhatsapp();
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
