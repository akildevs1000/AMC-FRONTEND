<template>
  <span>
    <input
      type="file"
      :ref="`fileInput`"
      style="display: none"
      @change="handleFileInputChange"
    />

    <v-icon @click="triggerFileInput" :color="color"
      >mdi-camera-outline</v-icon
    >
    {{ isFileSelect ? "File Selected" : label }}
  </span>
</template>

<script>
export default {
  props: {
    label: {
      default: "Upload your attachment",
      type: String,
    },
    color: {
      default: "primary",
      type: String,
    },
  },
  data: () => ({
    isFileSelect: false,
    preview: null,
  }),
  methods: {
    triggerFileInput() {
      this.$refs[`fileInput`].click();
    },
    handleFileInputChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.isFileSelect = true;
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = (e) => {
          // this.$emit("file-preview", e.target.result);
          this.preview = e.target.result;

          this.$emit("file-selected", e.target.result);
          // this.$emit("file-selected", file);
        };
      } else {
        this.isFileSelect = false;
      }
    },
  },
};
</script>
