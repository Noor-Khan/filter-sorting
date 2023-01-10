<script setup lang="ts">
import { computed, ref } from "vue";
const props = defineProps({
  header: Array,
  data: Array,
});
const emit = defineEmits<{
  (e: "delete", obj: Object): void;
}>();

// const rows = ref([title: 'a', c])
const search = ref("");
const removeArr = ref([]);
const delIndex = ref(null);
const currentSort = ref("Title");
const currentSortDir = ref("asc");
const isDelete = ref(false);
const rowId = ref(null);
const showRow = (id) => {
  rowId.value = id;
};
const sorted = (s) => {
  //if s == current sort, reverse
  if (s.toLowerCase() === currentSort.value) {
    currentSortDir.value = currentSortDir.value === "asc" ? "desc" : "asc";
  }
  currentSort.value = s.toLowerCase();
};
const deleteItem = (data) => {
  delIndex.value = data.item;
  removeArr.value.push(data.item);
};

console.log(props);

const filteredItems = computed(() => {
  let result = props.data;
  result = result.sort((a, b) => {
    let modifier = 1;
    if (currentSortDir.value === "desc") modifier = -1;
    // console.log(currentSort.value === "Creator");

    if (a[currentSort.value] < b[currentSort.value]) return -1 * modifier;
    if (a[currentSort.value] > b[currentSort.value]) return 1 * modifier;

    console.log(currentSort.value, "sd");
  });
  console.log(result);
  result = result.filter((item) => {
    return item.title.toLowerCase().indexOf(search.value.toLowerCase()) > -1;
  });
  console.log(removeArr.value.length, "item");

  result = result.filter(function (item) {
    return removeArr.value.indexOf(item) == -1;
  });
  console.log(delIndex.value !== null, "del");
  return result;
});
</script>

<template>
  <div class="px-4">
    <div class="mt-8 flex flex-col">
      <div class="-my-2 -mx-4 overflow-x-auto sm:-mx-6 lg:-mx-8">
        <div class="inline-block min-w-full py-2 align-middle md:px-6 lg:px-8">
          <div class="mt-6 mb-4">
            <input
              v-model="search"
              type="text"
              name="search"
              id="search"
              placeholder="Search"
              class="border-2 rounded-md border-gray-300 py-3 px-2 w-full focus:ring-indigo-400 focus:border-indigo-400"
            />
          </div>
          <div
            class="overflow-hidden shadow ring-1 ring-black ring-opacity-5 md:rounded-lg"
          >
            <table class="min-w-full divide-y divide-gray-300">
              <thead class="bg-gray-50">
                <tr>
                  <th
                    v-for="item in header"
                    @click="sorted(item.title)"
                    :key="item.id"
                    scope="col"
                    class="cursor-pointer py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6"
                  >
                    {{ item.title }}
                  </th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200 bg-white">
                <tr
                  v-for="(item, index) in filteredItems"
                  :key="item.id"
                  @mouseover="showRow(index)"
                  @mouseleave="showRow(null)"
                >
                  <td
                    class="whitespace-nowrap py-4 pl-4 pr-3 text-sm font-medium text-gray-900 sm:pl-6"
                  >
                    {{ item.title }} - {{ item.id }}
                  </td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">
                    {{ item.creator.name }}
                  </td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">
                    <p v-for="person in item.recipients" :key="person">
                      {{ person }}
                    </p>
                  </td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">
                    {{ item.interval }}
                  </td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">
                    {{ item.next_delivery }}
                  </td>

                  <td
                    class="relative whitespace-nowrap py-4 pl-3 pr-4 text-right text-sm font-medium sm:pr-6"
                  >
                    <div class="w-20">
                      <button
                        v-show="index === rowId"
                        @click="deleteItem({ item, index })"
                        class="text-green-600 hover:text-green-900 w-28"
                      >
                        <svg
                          style="width: 24px; height: 24px"
                          viewBox="0 0 24 24"
                        >
                          <path
                            fill="currentColor"
                            d="M9,3V4H4V6H5V19A2,2 0 0,0 7,21H17A2,2 0 0,0 19,19V6H20V4H15V3H9M7,6H17V19H7V6M9,8V17H11V8H9M13,8V17H15V8H13Z"
                          />
                        </svg>
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
