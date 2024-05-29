<template>
  <span>
    <span
      v-for="(singlePic, singlePicIndex) in items"
      :key="singlePicIndex"
      class="primary--text"
    >
      <v-icon color="primary" @click="openNewDialog(singlePic)"
        >mdi-paperclip</v-icon
      >
      {{ singlePic.attachment }}
    </span>

    <v-dialog v-model="dialog" height="700" width="700">
      <v-card>
        <v-toolbar flat dense
          >{{ item.attachment }} <v-spacer></v-spacer>
          <v-icon @click="dialog = false" color="primary"
            >mdi-close-circle-outline</v-icon
          >
        </v-toolbar>
        <v-img
          :src="`${BACKEND_ABSOLUTE_URL}/checklist/${item.form_entry_id}/${item.attachment}`"
        ></v-img>
      </v-card>
    </v-dialog>
  </span>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      required: true,
      default: "",
    },
    attachments: {
      type: Array,
      default: [],
    },
    form_entry_id: {
      default: 0,
    },
  },
  data: () => ({
    dialog: false,
    item: {},
    items: [],
    incrementalId: 1,
    BACKEND_ABSOLUTE_URL: process.env.BACKEND_ABSOLUTE_URL,
  }),
  created() {
    this.items = this.attachments;
  },
  methods: {
    getRandomId() {
      return ++this.incrementalId;
    },

    openNewDialog(e) {
      this.dialog = true;
      this.item = e;
    },
  },
};
</script>
