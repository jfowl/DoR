<template>
  <table class="table-auto text-sm m-2">
    <tr>
      <th
        v-for="column in columns"
        v-bind:key="column"
        class="border bg-blue-500 text-gray-50"
        :class="column.class"
      >
        <button
          v-on:click="if (sortCriterion == column.prop) {sortAscending = !sortAscending; } else { sortAscending = true } sortCriterion = column.prop;"
          class="text-gray-50 font-bold"
        >
          {{ column.name }}
        </button>
        <span v-if="sortCriterion == column.prop && sortAscending" class="float-right">🔽</span>
        <span v-if="sortCriterion == column.prop && !sortAscending" class="float-right">🔼</span>
      </th>
    </tr>
    <tr
      v-for="(row, index) in internalRows"
      v-bind:key="index"
      :class="index % 2 ? 'bg-opacity-80 bg-white' : ''"
    >
      <td v-for="column in columns" v-bind:key="column" :class="column.class">
        {{ row[column.prop] }}
      </td>
    </tr>
  </table>
</template>
<script lang="ts">
import { onBeforeMount, PropType, ref, watch } from "@vue/runtime-core";

export interface ColumnDefinition {
  prop: String;
  name: String;
  class: String;
}

export default {
  props: {
    rows: Array as PropType<Array<any>>,
    columns: Array as PropType<Array<ColumnDefinition>>,
  },
  setup(props: any) {
    const sortCriterion = ref("");
    const sortAscending = ref(true);
    const internalRows = ref([] as Array<any>);

    onBeforeMount(async () => {
      internalRows.value = props.rows;
    });

    watch([sortCriterion, sortAscending], (newValues, prevValues) => {
      newValues;
      prevValues;

      console.log(sortCriterion.value, sortAscending.value);

      internalRows.value = internalRows.value.sort((a, b) => {
        const firstProp = a[sortCriterion.value];
        const secondProp = b[sortCriterion.value];

        return firstProp.localeCompare(secondProp) * (sortAscending.value ? 1 : -1);
      });
    });

    return { sortCriterion, sortAscending, internalRows };
  },
};
</script>
