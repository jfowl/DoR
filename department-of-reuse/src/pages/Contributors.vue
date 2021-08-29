<template>
    <div >
        <h1 class="text-xl bg-opacity-80 bg-blue-200">Contributors</h1>
        
        <p class="mt-5 mb-5">We are thankful to our data collectors to take the time and contribute to the effort. </p>

        <p class="bg-yellow-300">
            ANONYMIZED
        </p>

    </div>
</template>
<script lang="ts">
import { defineComponent } from 'vue'

import reuseJson from '@/assets/data/reuse.json';
import { ReuseFromJson } from '../backend/models/Reuse';
import { Histogram } from '../tools/Histogram';

export default defineComponent({
    name: "Contributors",
    components: {  },
    setup() {
        const reuseData = (reuseJson as Array<any>).map(ReuseFromJson);

        const uniqueContributorDoiPair = Array.from(new Set(reuseData.map(r => { return JSON.stringify({name: r.contributor, doi: r.sourceDOI })}))).map(s => JSON.parse(s));

        const contributors = new Histogram(x => x, uniqueContributorDoiPair.filter(p => p.name != null).map(r => r.name)).histogram();


        return { contributors };
    },
})
</script>
