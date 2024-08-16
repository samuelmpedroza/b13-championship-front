<template>
  <table class="table" :class="tableClass">
    <thead>
      <slot name="columns">
        <th v-for="column in columns" :key="column.name">{{ column.label }}</th>
      </slot>
    </thead>
    <tbody>
      <tr v-for="(item, index) in data" :key="index" @click="config.clickable ? emitEvent(index) : null">
        <slot :row="item">
          <td
            v-for="(column, index) in columns"
            :key="index"
            v-if="hasValue(item, column.name)"
          >
            {{ itemValue(item, column.name) }}
          </td>
        </slot>
      </tr>
    </tbody>
  </table>
</template>
<script>
export default {
  name: "new-table",
  props: {
    columns: Array,
    data: Array,
    type: {
      type: String, // striped | hover
      default: "striped",
    },
    title: {
      type: String,
      default: "",
    },
    subTitle: {
      type: String,
      default: "",
    },
    config: {
      type: Object,
      default: () => {}
    }
  },
  computed: {
    tableClass() {
      return `table-${this.type}`;
    },
  },
  methods: {
    hasValue(item, column) {
      return item[column] !== "undefined";
    },
    itemValue(item, column) {
      return item[column];
    },
    emitEvent(index) {
      this.$emit(`${this.config.eventName}`, index)
    }
  },
};
</script>
<style></style>
