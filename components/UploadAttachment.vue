<template>
  <div>
    <input
      type="file"
      :ref="`fileInput`"
      style="display: none"
      @change="handleFileInputChange"
    />
    <v-btn dense outlined @click="triggerFileInput">
      <v-icon left>mdi-file-upload</v-icon>
      {{ isFileSelect ? "File Selected" : label }}
    </v-btn>
  </div>
</template>

<script>
export default {
  props: {
    label: {
      default: "Upload your attachment",
      type: String,
    },
  },
  data: () => ({
    isFileSelect: false,
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
          //   this.$emit("file-preview", e.target.result);
          this.$emit("file-selected", file);
        };
      } else {
        this.isFileSelect = false;
      }
    },
  },
};
</script>
