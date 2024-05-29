<template>
  <span>
    <span v-for="(file, index) in defaultItems" :key="index">
      <PreviewUploadPic
        :label="`${file.attachment}`"
        icon="mdi-paperclip"
        :src="`${BACKEND_ABSOLUTE_URL}/checklist/${file.form_entry_id}/${file.attachment}`"
      />
    </span>
    <input
      type="file"
      multiple
      :ref="`fileInput`"
      style="display: none"
      @change="handleFileInputChange"
    />

    <span v-if="isFileSelect">
      <span v-for="(file, index) in selectedFiles" :key="index">
        <PreviewUploadPic
          :label="`pic-${name}.${index + 1}.png ${file.imageSize}`"
          icon="mdi-paperclip"
          :src="file.preview"
        />
        <v-icon class="mr-2" color="primary" @click="deleteItem(index)"
          >mdi-close</v-icon
        >
      </span>
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
      default: "Upload your attachments",
      type: String,
    },
    color: {
      default: "primary",
      type: String,
    },

    name: {
      default: "pic-name",
      type: String,
    },
    defaultAttachments: {
      default: [],
      type: Array,
    },
  },
  data() {
    return {
      isFileSelect: false,
      selectedFiles: [],
      currentComponent: null,
      newDialogKey: 1,
      BACKEND_ABSOLUTE_URL: process.env.BACKEND_ABSOLUTE_URL,
      defaultItems: [],
    };
  },
  created() {
    this.defaultItems = this.defaultAttachments;
  },
  methods: {
    deleteItem(index) {
      this.selectedFiles.splice(index, 1);
    },
    triggerFileInput() {
      this.$refs[`fileInput`].click();
    },

    handleFileInputChange(event) {
      this.defaultItems = [];
      const files = event.target.files;
      if (files.length > 0) {
        this.isFileSelect = true;
        for (let i = 0; i < files.length; i++) {
          this.processFile(files[i], `pic-${this.name}.${i + 1}.png`);
        }
      } else {
        this.isFileSelect = false;
      }
    },

    processFile(file, picName) {
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = (e) => {
        const img = new Image();
        img.src = e.target.result;
        img.onload = () => {
          const canvas = document.createElement("canvas");
          const ctx = canvas.getContext("2d");
          canvas.width = img.width;
          canvas.height = img.height;
          ctx.drawImage(img, 0, 0);

          canvas.toBlob(
            (blob) => {
              const compressedFile = new File([blob], file.name, {
                type: "image/jpeg", // Change to the desired image type if needed
                lastModified: Date.now(),
              });

              const compressedReader = new FileReader();
              compressedReader.readAsDataURL(compressedFile);
              compressedReader.onload = (compressedEvent) => {
                const compressedDataUrl = compressedEvent.target.result;

                // Update the preview and emit events
                const fileSizeInKB = (compressedFile.size / 1024).toFixed(2);
                const imageSize = `(${fileSizeInKB} KB)`;
                this.selectedFiles.push({
                  name: picName,
                  preview: compressedDataUrl,
                  imageSize,
                  key: this.newDialogKey++,
                });

                this.currentComponent = "ViewFile"; // Set the component name to render

                this.$emit("files-selected", this.selectedFiles);
              };
            },
            "image/jpeg",
            0.3
          ); // Adjust the image quality (0.7 is a good balance)
        };
      };
    },
  },
};
</script>
