<template>
    <div>
      <v-icon color="red" small @click="submit"> mdi-delete </v-icon>
      Delete
    </div>
  </template>
  <script>
  export default {
    props: ["id", "endpoint"],
    methods: {
      async submit() {
        try {
          await this.$axios.delete(this.endpoint + "/" + this.id);
          this.$emit("success", "Record has been delete");
        } catch ({ response }) {
          console.log(response);
          // console.error("Error adding income:", response.data.message);
        }
      },
    },
  };
  </script>