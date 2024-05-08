<template>
  <v-dialog v-model="dialog" height="700" width="700">
    <template v-slot:activator="{ on, attrs }">
      <!-- <v-icon color="primary" v-bind="attrs" v-on="on">mdi-attachment</v-icon> -->
      <v-icon color="primary" v-bind="attrs" v-on="on"
        >mdi-camera-outline</v-icon
      >
      View Photo
      <!-- <v-icon color="primary" v-bind="attrs" v-on="on">mdi-file-pdf</v-icon>
      <v-icon color="primary" v-bind="attrs" v-on="on">mdi-paperclip</v-icon> -->
    </template>

    <v-card flat>
      <v-card-title>{{ label }} <v-spacer></v-spacer> 
          
          <v-icon @click="dialog = false" color="primary"
          >mdi-close-circle-outline</v-icon
        >
      
      </v-card-title>

      <v-img :src="src" :alt="altText"></v-img>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      required: true,
      default: "Customer Signature",
    },
    src: {
      type: String,
      required: true,
      default: "",
    },
    altText: {
      type: String,
      default: "Image",
    },
  },
  data: () => ({
    dialog: false,
  }),
};
</script>
