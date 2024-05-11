<template>
  <span>
    <input
      type="file"
      :ref="`fileInput`"
      style="display: none"
      @change="handleFileInputChange"
    />

    <span v-if="isFileSelect">
      <component
        :label="name"
        :key="newDialogKey"
        icon="mdi-paperclip"
        :is="currentComponent"
        :src="preview"
      />
    </span>
    <v-icon class="ml-5" @click="triggerFileInput" :color="color"
      >mdi-camera-outline</v-icon
    >
    {{ label }}
  </span>
</template>

<script>
export default {
  props: {
    label: {
      default: "Upload your attachment",
      type: String,
    },
    name: {
      default: "Photo",
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
    currentComponent: null,
    viewFileSrc: "",
    newDialogKey: 1,
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

          this.$emit("file-preview", e.target.result);
          this.$emit("file-selected", e.target.result);

          ++this.newDialogKey;
          this.currentComponent = "ViewFile"; // Set the component name to render

          // this.$emit("file-selected", file);
        };
      } else {
        this.isFileSelect = false;
      }
    },
  },
};
</script>
