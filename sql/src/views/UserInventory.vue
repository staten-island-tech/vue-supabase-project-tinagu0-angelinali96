<template>
    <h1>User Inventory</h1>
<div class="card">
        <DataView :value="unique" :layout="layout" :dataKey="'?'">
            <template #header>
        <div class="flex justify-content-end">
            <DataViewLayoutOptions v-model="layout" />
        </div>
    </template>
    <template #list="slotProps">
        <div class="grid grid-nogutter">
            <div v-for="(item, index) in slotProps.items" :key="index" class="col-12">
                <div class="flex flex-column sm:flex-row sm:align-items-center p-4 gap-3" :class="{ 'border-top-1 surface-border': index !== 0 }">
                    <div class="md:w-10rem relative" v-badge="uniqueObtainedCount[index]">
                        <img class="block xl:block mx-auto border-round w-full" :src="item.image" :alt="item.name" />
                        <Tag :value="item.inventoryStatus" class="absolute" style="left: 4px; top: 4px"></Tag>
                    </div>
                    <div class="flex flex-column md:flex-row justify-content-between md:align-items-center flex-1 gap-4">
                        <div class="flex flex-row md:flex-column justify-content-between align-items-start gap-2">
                            <div>
                                <div class="text-lg font-medium text-900 mt-2">{{ item.name }}</div>
                            </div>
                            <div class="surface-100 p-1" style="border-radius: 30px">
                                <div class="surface-0 flex align-items-center gap-2 justify-content-center py-1 px-2" style="border-radius: 30px; box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.04), 0px 1px 2px 0px rgba(0, 0, 0, 0.06)">
                                    <span class="text-900 font-medium text-sm">{{ item.star }}</span>
                                    <i class="pi pi-star-fill text-yellow-500"></i>
                                </div>
                            </div>
                            <div class="flex flex-column md:align-items-end gap-5">
                            <div class="flex flex-row-reverse md:flex-row gap-2">
                                <Button label="Show Stats" @click="openStats(index)" />
                                <Dialog v-model:visible="visible" :style="{ width: '50rem', height: '30rem' }">
                                    <label>{{ stat.name }}</label>
                                    <div class="flex align-items-center gap-3 mb-3">
                                        <p>power: {{ stat.power }}</p>
                                        <p>charisma: {{ stat.stats.charisma }}</p>
                                        <p>coolness: {{ stat.stats.coolness }}</p>
                                        <p>dexterity: {{ stat.stats.dexterity }}</p>
                                        <p>intelligence: {{ stat.stats.intelligence }}</p>
                                        <p>knowledge: {{ stat.stats.knowledge }}</p>
                                        <p>strength: {{ stat.stats.strength }}</p>
                                    </div>
                             </Dialog>
                            </div>
                        </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </template>

    <template #grid="slotProps">
        <div class="grid grid-nogutter">
            <div v-for="(item, index) in slotProps.items" :key="index" class="col-12 sm:col-6 md:col-4 xl:col-6 p-2">
                <div class="p-4 border-1 surface-border surface-card border-round flex flex-column">
                    <div class="surface-50 flex justify-content-center border-round p-3" v-badge="uniqueObtainedCount[index]">
                        <div class="relative mx-auto">
                            <img class="border-round w-full" :src="item.image" :alt="item.name" style="max-width: 300px"/>
                            <Tag :value="item.inventoryStatus" class="absolute" style="left: 4px; top: 4px"></Tag>
                        </div>
                    </div>
                    <div class="pt-4">
                        <div class="flex flex-row justify-content-between align-items-start gap-2">
                            <div>
                                <div class="text-lg font-medium text-900 mt-1">{{ item.name }}</div>
                            </div>
                            <div class="surface-100 p-1" style="border-radius: 30px">
                                <div class="surface-0 flex align-items-center gap-2 justify-content-center py-1 px-2" style="border-radius: 30px; box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.04), 0px 1px 2px 0px rgba(0, 0, 0, 0.06)">
                                    <span class="text-900 font-medium text-sm">{{ item.star }}</span>
                                    <i class="pi pi-star-fill text-yellow-500"></i>
                                </div>
                            </div>
                        </div>
                        <div class="flex flex-column gap-4 mt-4">
                            <div class="flex gap-2">
                                <div class="flex flex-row-reverse md:flex-row gap-2">
                                <Button label="Show Stats" @click="openStats(index)" />
                                <Dialog v-model:visible="visible" :style="{ width: '50rem', height: '30rem' }">
                                    <header>{{ stat.name }}</header>
                                    <div>
                                        <li>power: {{ stat.power }}</li>
                                        <li>charisma: {{ stat.stats.charisma }}</li>
                                        <li>coolness: {{ stat.stats.coolness }}</li>
                                        <li>dexterity: {{ stat.stats.dexterity }}</li>
                                        <li>intelligence: {{ stat.stats.intelligence }}</li>
                                        <li>knowledge: {{ stat.stats.knowledge }}</li>
                                        <li>strength: {{ stat.stats.strength }}</li>
                                    </div>
                             </Dialog>
                            </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </template>
        </DataView>
    </div>
</template>

<script setup lang="ts">
import DataView from 'primevue/dataview';
import DataViewLayoutOptions from 'primevue/dataviewlayoutoptions'
import Tag from 'primevue/tag';
import Button from 'primevue/button';

import Dialog from 'primevue/dialog';

import { onMounted, ref} from "vue";
// @ts-ignore
import getCurrentUser from '@/db/getCurrentUser'
// @ts-ignore
import getGacha from '@/db/gacha/getGacha'
import type { Ref } from 'vue'
import type { CardWStat } from '@/lib/interfaces';

const layout: Ref<'grid'|'list'> = ref('grid');

// @ts-ignore
// import { useUserStore } from '@/db/pinia/stores/userStore'

const gacha = ref();
const unique = ref([])
const uniqueObtainedCount = ref([]);

const stat:Ref<CardWStat> = ref({
    subject: '',
    star: 0,
    power: 0,
    name: '',
    role: '',
    image: '',
    stats: {
      charisma: 0,
      coolness: 0,
      dexterity: 0,
      intelligence: 0,
      knowledge: 0, 
      strength: 0
    }
})

const visible = ref(false)

function openStats(index:number) {
    visible.value = true
    console.log(unique.value)
    // @ts-ignore
    stat.value = unique.value[index]
    console.log(stat.value)
}

onMounted(async() => {
    const id = await (await getCurrentUser()).id
    console.log(id)
    let inv = await getGacha(id)
    gacha.value = JSON.parse(inv)
//finds duplicate values
gacha.value.filter((o:CardWStat) => {
   if(unique.value.find((i:CardWStat) => i.name === o.name)) {
     return true
   } else {
    // @ts-ignore
   unique.value.push(o)
   return false;
   }
})
unique.value.forEach((item:CardWStat) => { // get count of how many cards obtained
    const amt = gacha.value.filter((card:CardWStat) => card.name === item.name)
    // @ts-ignore
    uniqueObtainedCount.value.push(amt.length)
});
}
);

</script>

<style scoped>

</style>