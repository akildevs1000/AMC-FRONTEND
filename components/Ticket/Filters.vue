<template>
  <tr>
    <td v-for="(header, index) in headers" :key="index">
      <v-container>
        <v-text-field
          v-if="header.filter_type == 'text'"
          clearable
          :hide-details="true"
          outlined
          dense
          autocomplete="off"
          v-model="header.search_value"
          @input="handleChange(header)"
          :type="header.field_type == 'number' ? 'number' : ''"
        ></v-text-field>

        <v-select
          v-if="header.filter_type == 'dropdown'"
          :hide-details="true"
          v-model="header.search_value"
          @input="handleChange(header)"
          outlined
          dense
          item-value="id"
          item-text="name"
          :items="header.dropdownItems"
          solo
          flat
        ></v-select>
      </v-container>
    </td>
  </tr>
</template>

<script>
export default {
  props: ["headers"],
  data: () => ({}),
  methods: {
    handleChange(e) {
      this.$emit("filtered-value", e);
    },
  },
};
</script>
