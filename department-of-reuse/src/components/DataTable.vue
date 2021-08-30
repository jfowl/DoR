<template>
  <table class="table-auto text-sm m-2">
    <tr>
      <th
        v-for="column in internalColumns"
        v-bind:key="column"
        class="border bg-blue-500 text-gray-50"
        :class="column.class"
      >
        <button class="float-left" v-if="hasFilter">🔎</button>
        <input type="search" v-model="column.filter" class="text-black w-3/4 p-1" v-if="hasFilter">
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
  filter: String;
}

export default {
  props: {
    rows: Array as PropType<Array<any>>,
    columns: Array as PropType<Array<ColumnDefinition>>,
    hasFilter : {
        type: Boolean,
        default: false
    }
  },
  setup(props: any) {
    const sortCriterion = ref("");
    const sortAscending = ref(true);
    const internalRows = ref([] as Array<any>);
    const internalColumns = ref([] as Array<ColumnDefinition>);

    onBeforeMount(async () => {
      internalRows.value = props.rows;
      internalColumns.value = props.columns;
      internalColumns.value.map(e => {e.filter = ""; return e});
    });

    watch([sortCriterion, sortAscending, internalColumns], (newValues, prevValues) => {
      newValues;
      prevValues;

      internalRows.value = props.rows.filter((r : any) => {
          const mismatchedFilters = internalColumns.value
                .filter(c => !(c.filter === undefined) && c.filter != "")
                .filter(c => {
                    const prop : string = `${c.prop}`;
                    console.log("My prop: ", prop, c.filter);
                    if (r[prop].includes(c.filter)) return false;
                    else return true;
                });
          return mismatchedFilters.length == 0 ? true : false;
      })

      if(sortCriterion.value != "") {
        internalRows.value = internalRows.value.sort((a, b) => {
            const firstProp = a[sortCriterion.value];
            const secondProp = b[sortCriterion.value];

            if (typeof(firstProp) == "string") {
                return firstProp.localeCompare(secondProp) * (sortAscending.value ? 1 : -1);
            } else {
                if (firstProp < secondProp) return (sortAscending.value ? -1 : 1);
                if (secondProp > firstProp) return (sortAscending.value ? 1 : -1);
                return 0;
            }
            
        });
      }
    },
    { deep: true });

    return { sortCriterion, sortAscending, internalRows, internalColumns };
  },
};
</script>
