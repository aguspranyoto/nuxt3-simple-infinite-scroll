<template>
  <div class="p-4">
    <p class="text-lg font-medium mb-4">
      Showing {{ isEven ? "even" : "odd" }} items
    </p>
    <button
      @click="handleChange"
      class="mb-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors duration-300"
    >
      Change Filter
    </button>
    <div
      v-bind="containerProps"
      class="overflow-y-auto border border-gray-300 rounded-lg"
      style="height: 300px"
    >
      <div v-bind="wrapperProps">
        <div
          v-for="item in list"
          :key="item.index"
          class="flex items-center justify-center h-10 border-b border-gray-200 last:border-b-0"
        >
          Row: {{ item.data }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { useToggle, useVirtualList, useInfiniteScroll } from "@vueuse/core";

const [isEven, toggle] = useToggle();
const allItems = ref(Array.from(Array(20).keys()));
const filteredList = computed(() =>
  allItems.value.filter((i) => (isEven.value ? i % 2 === 0 : i % 2 === 1))
);

const { list, containerProps, wrapperProps } = useVirtualList(filteredList, {
  itemHeight: 42,
});

useInfiniteScroll(
  containerProps.ref,
  () => {
    // load more
    allItems.value.push(
      ...Array.from(Array(20).keys(), (i) => i + allItems.value.length)
    );
  },
  {
    distance: 10,
  }
);

const handleChange = () => {
  isEven.value = !isEven.value;
};
</script>
