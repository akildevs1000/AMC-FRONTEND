<template>
  <v-dialog v-model="dialog">
    <template v-slot:activator="{ on }">
      <v-btn block color="primary" dark v-on="on">{{ label }}</v-btn>
    </template>
    <v-card outlined>
      <v-toolbar dense flat class="grey lighten-2">
        <b>Signature Pad</b>
        <v-spacer></v-spacer>
        <v-icon @click="dialog = false">mdi-close-circle-outline</v-icon>
      </v-toolbar>
      <Component :is="comp" ref="signaturePad" height="500px" />
      <div class="mb-1 white text-center">
        <v-btn small class="primary" @click="save">Save </v-btn>
        <v-btn small class="primary" @click="clear">Clear</v-btn>
      </div>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: "Customer Signature",
    },
  },
  data: () => ({
    dialog: false,
    comp: null,
  }),
  watch: {
    dialog() {
      setTimeout(() => (this.comp = "VueSignaturePad"), 100);
    },
  },
  methods: {
    clear() {
      this.$refs.signaturePad.clearSignature();
      this.$emit("sign", null);
    },
    save() {
      const { data } = this.$refs.signaturePad.saveSignature();
      this.$emit("sign", data);
      this.dialog = false;
    },
  },
};
</script>
