<script setup>
import { onMounted, ref, computed } from "vue";
import DataTable from "./DataTable.vue";
const items = ref([]);
const activeTab = ref("data");
const header = ref([
  {
    title: "Title",
    value: "title",
  },
  {
    title: "Creator",
    value: "creator",
  },
  {
    title: "Recipients",
    value: "recipients",
  },
  {
    title: "Interval",
    value: "interval",
  },
  {
    title: "Next Delivery",
    value: "delivery",
  },
]);
const tabs = ref([]);
const fetchData = async () => {
  const response = await fetch("/data/db.json");
  items.value = await response.json();
};

const dataTriggered = computed(() => {
  return items.value.filter((item) => item.trigger === "data");
});
const timeTriggered = computed(() => {
  return items.value.filter((item) => item.trigger === "time");
});
const oneTimeTriggered = computed(() => {
  return items.value.filter((item) => item.trigger === "one-time");
});
const selectTab = (selectedTab) => {
  activeTab.value = selectedTab.trigger;
  let result = tabs.value.forEach((tab) => {
    tab.current = tab.trigger === selectedTab.trigger;
  });
};

const getTableData = (tab) => {
  return tab.trigger === "data" && tab.current === true
    ? dataTriggered.value
    : tab.trigger === "time" && tab.current === true
    ? timeTriggered.value
    : oneTimeTriggered.value;
};
const deleteItem = (tab) => {
  if (tab.item.trigger === "data") {
    dataTriggered.value = dataTriggered.value.splice(tab.index, 1);
  }
  console.log(tab.item.trigger, "asdf");
};

onMounted(async () => {
  await fetchData();
  let result = items.value.map((item) => {
    return {
      trigger: item.trigger,
    };
  });
  tabs.value = result.filter(
    (v, i, a) =>
      a.findIndex((v2) => v.trigger === v2.trigger && v.value === v2.value) ===
      i
  );
  tabs.value[0].current = true;
});
</script>

<template>
  <div>
    <div>
      <div class="border-b border-gray-200">
        <nav class="-mb-px flex space-x-8" aria-label="Tabs">
          <a
            v-for="tab in tabs"
            :key="tab.trigger"
            @click="selectTab(tab)"
            class="capitalize cursor-pointer"
            :class="[
              tab.current
                ? 'border-indigo-500 text-indigo-600'
                : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300',
              'whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm',
            ]"
            >{{ tab.trigger }}
            {{
              tab.trigger === "data"
                ? `Triggered (${dataTriggered.length})`
                : tab.trigger === "time"
                ? `Triggered (${timeTriggered.length})`
                : oneTimeTriggered.length
            }}</a
          >
        </nav>
      </div>
    </div>
    <div>
      <div v-for="tab in tabs" :key="tab.trigger">
        <DataTable
          v-if="activeTab === tab.trigger"
          :header="header"
          :data="getTableData(tab)"
          @delete="deleteItem($event)"
        />
      </div>
    </div>
  </div>
</template>
