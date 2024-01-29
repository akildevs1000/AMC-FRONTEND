<template>
  <div>
    <v-card outlined>
      <v-toolbar dense flat class="grey lighten-2">
        <b> Signature Pad</b>
      </v-toolbar>
      <VueSignaturePad height="500px" ref="signaturePad" />
      <v-card-actions>
        <div class="mt-1">
          <v-btn small class="primary" @click="save">Save Signature</v-btn>
          <v-btn small class="primary" @click="clear">Clear</v-btn>
        </div>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
export default {
  data: () => ({}),
  methods: {
    clear() {
      this.$refs.signaturePad.clearSignature();
      this.$emit("sign", null);
    },
    save() {
      const { data } = this.$refs.signaturePad.saveSignature();
      this.$emit("sign", data);
    },
  },
};
</script>
