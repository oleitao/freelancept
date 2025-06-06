<template>
  <div
    id="defaultModal"
    tabindex="-1"
    aria-hidden="true"
    class="fixed top-0 left-0 right-0 z-50 w-full p-4 overflow-x-hidden overflow-y-auto md:inset-0 h-[calc(100%-1rem)] md:h-full bg-black bg-opacity-15"
  >
    <div
      class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-full h-full max-w-2xl md:h-auto"
    >
      <!-- Modal content -->
      <div class="relative bg-neutral-200 rounded-lg shadow">
        <!-- Modal header -->
        <div class="flex items-start justify-between p-4 border-b rounded-t">
          <h3 class="text-xl font-semibold text-gray-900">Tax Ranks</h3>
          <button
            type="button"
            class="text-gray-400 bg-transparent hover:text-gray-900 rounded-lg text-sm p-1.5 ml-auto inline-flex items-center"
            data-modal-hide="defaultModal"
            @click="$emit('close')"
          >
            <XMarkIcon class="w-5 h-5" />
            <span class="sr-only">Close modal</span>
          </button>
        </div>

        <!-- Modal body -->
        <div class="p-6 space-y-6">
          Your taxable income (<span class="text-income whitespace-nowrap">{{
            asCurrency(taxableIncome)
          }}</span
          >) is in level
          <span class="text-red-500">{{ taxRank.id }}</span>
          <span class="text-sm" v-html="taxRankMinText"></span
          ><span class="text-sm" v-html="taxRankMaxText"></span>.

          <table class="w-full text-sm text-left text-gray-700 table-auto">
            <thead class="text-xs text-gray-700 uppercase border-b-2">
              <tr>
                <th class="text-center">Level</th>
                <th class="text-center">Minimum</th>
                <th class="text-center">Maximum</th>
                <th class="text-center">Normal Tax</th>
                <th class="text-center">Average Tax</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="item in getTaxRanks"
                :key="item.id"
                :class="{ 'bg-neutral-300': item.id === taxRank.id }"
              >
                <td
                  class="py-1 text-center"
                  :class="{ 'text-red-500': item.id === taxRank.id }"
                >
                  {{ item.id }}
                </td>
                <td class="py-1 text-center whitespace-nowrap">{{ asCurrency(item.min) }}</td>
                <td class="py-1 text-center whitespace-nowrap">{{ asCurrency(item.max) }}</td>
                <td class="py-1 text-center whitespace-nowrap">
                  {{ asPercentage(item.normalTax) }}
                </td>
                <td class="py-1 text-center whitespace-nowrap">
                  {{ asPercentage(item.averageTax) }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { useTaxesStore } from "@/store";
import { asCurrency, asPercentage } from "@/utils";
import { computed } from "vue";
import { storeToRefs } from "pinia";
import { XMarkIcon } from "@heroicons/vue/24/outline";
// store
const { getTaxRanks, taxRank, taxableIncome } = storeToRefs(useTaxesStore());

// taxRank
const taxRankMinText = computed(() => {
  return taxRank.value.min
    ? ` (bigger than <span class="text-neutral-600">${asCurrency(
        taxRank.value.min,
      )}</span>`
    : null;
});
const taxRankMaxText = computed(() => {
  const preText = taxRankMinText.value ? " and" : " (";
  return taxRank.value.max
    ? `${preText} lower than <span class="text-neutral-600">${asCurrency(
        taxRank.value.max,
      )})</span>`
    : ")";
});
</script>
