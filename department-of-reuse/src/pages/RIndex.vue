<template>
  <div>
    <h1 class="text-xl bg-opacity-80 bg-blue-200">
        <span v-if="target == 'researcher'">Researchers</span>
        <span v-if="target == 'artifact'">Artifacts</span> 
         - 
        <span v-if="mode == 'reusing'">Most reusing (R)</span>
        <span v-if="mode == 'reused'">Most reused (R+)</span>
    </h1>
    <div v-if="isLoading">
      <p>Loading data ...</p>
    </div>
    <div v-else class="pb-10">
      <DataTable :rows="rows" :columns="columns" class="mx-auto" />
    </div>
  </div>
</template>
<script lang="ts">
import { ref } from "@vue/reactivity";
import { onBeforeMount, watch } from "@vue/runtime-core";
import RIndex from "@/backend/RIndex";
import reuseJson from "../assets/data/reuse.json";
import Reuse, { ReuseFromJson } from "@/backend/models/Reuse";
import { HistogramEntry } from "../tools/Histogram";
import { Author, Work } from "../clients/crossref";
import DataTable from "@/components/DataTable.vue";
import { useRoute } from "vue-router";

export default {
  name: "RIndex Page",
  components: { DataTable },
  setup() {
    const isLoading = ref(false);
    const route = useRoute();

    const target = ref(route.params.target as string);
    const mode = ref(route.params.mode as string);

    const columns = [
      {
        prop: "name",
        name: "Name",
        class: "text-left align-top overflow-hidden break-all",
      },
      {
        prop: "frequency",
        name: "Frequency",
        class: "text-left align-top",
      },
    ];

    const rows = ref(new Array<any>());
    const reuseData = (reuseJson as Array<any>).map(ReuseFromJson);

    onBeforeMount(async () => {
      await generateRows(reuseData);
    });

    watch(
      () => route.params,
      async (newParams) => {
        newParams;
        target.value = route.params.target as string;
        mode.value = route.params.mode as string;
        await generateRows(reuseData);
      }
    );

    async function generateRows(reuseData: Array<Reuse>) {
      isLoading.value = true;
      const indexer = new RIndex(reuseData);

      const target = route.params.target as string;
      const mode = route.params.mode as string;

      if (target == "researcher") {
        var researchers: Array<HistogramEntry<Author>> = new Array();

        if (mode == "reused") {
          researchers = await indexer.computeAuthorsReused();
        } else if (mode == "reusing") {
          researchers = await indexer.computeAuthorsReusing();
        }

        rows.value = researchers.map((e) => {
          return {
            name: `${e.entry.given} ${e.entry.family}`,
            frequency: e.frequency,
          };
        });
      } else if (target == "artifact") {
        var publications: Array<HistogramEntry<Work>> = new Array();

        if (mode == "reused") {
          publications = await indexer.computeWorksReused();
        } else if (mode == "reusing") {
          publications = await indexer.computeWorksReusing();
        }

        rows.value = publications.map((e) => {
          return {
            name: `${e.entry.title.join(":")} `,
            frequency: e.frequency,
          };
        });
      }
      isLoading.value = false;
    }

    return { isLoading, columns, rows, mode, target };
  },
};
</script>


