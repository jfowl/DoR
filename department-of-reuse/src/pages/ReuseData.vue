<template>
  <div>
    <h1 class="text-xl bg-opacity-80 bg-blue-200">Collected Reuse Data</h1>
    <div v-if="isLoading">
      <p>Loading data ...</p>
    </div>
    <div v-else class="pb-10">
        <DataTable :rows="rows" :columns="columns" />
    </div>
  </div>
</template>
<script lang="ts">
import DataTable from "@/components/DataTable.vue";
import { defineComponent, onBeforeMount, ref } from "vue";

import reuseJson from "../assets/data/reuse.json";
import Reuse, { ReuseFromJson } from "../backend/models/Reuse";


export default defineComponent({
  name: "ReuseData",
  components: { DataTable },
  setup() {

    const isLoading = ref(false);
    const rows = ref(new Array<Reuse>());

    onBeforeMount(async () => {
        isLoading.value = true;
        
        const reuseData = (reuseJson as Array<any>).map(ReuseFromJson);

        rows.value = reuseData;

        isLoading.value = false;
    })
    
    const columns = [
        {
          prop: "sourceDOI",
          name: "Source Paper",
          class: "text-left align-top",
        },
        {
          prop: "type",
          name: "Type",
          class: "text-left align-top",
        },
        {
          prop: "reusedDOI",
          name: "Target Paper",
          class: "text-left align-top",
        },
        {
          prop: "alternativeID",
          name: "Alternative ID",
          class: "text-left overflow-hidden break-all",
        }
    ];

    /*
      sourceDOI: string,
  type : ReuseType,
  comment: string,
  sourceReference: string,
  reusedDOI: string,
  alternativeID: string,
  sourceReferenceDetail: string,
  contributor: string
    */

    return { isLoading, columns, rows };
  },
});
</script>
<style scoped>
/* weird bugfix for revogrid */
.main-viewport {
    align-items: flex-start; 
}
</style>