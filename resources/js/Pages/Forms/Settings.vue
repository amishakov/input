<template>
  <app-layout title="Form Settings">
    <div class="mx-auto w-full max-w-5xl px-4">
      <TabGroup
        :vertical="true"
        as="div"
        class="mx-auto mt-8 grid w-full grid-cols-12 gap-x-6"
        :selectedIndex="selectedTabIndex"
        @change="tabChanged"
      >
        <div class="col-span-4 pt-8">
          <TabList
            as="nav"
            class="divide-y divide-grey-50 overflow-hidden rounded border border-grey-200 bg-white"
            aria-label="Sidebar"
          >
            <Tab
              v-for="item in navigation"
              v-slot="{ selected }"
              :key="item.name"
              as="template"
            >
              <button
                :class="[
                  selected
                    ? 'bg-grey-50'
                    : 'text-grey-600 hover:bg-grey-100 hover:text-grey-900',
                  'relative block w-full px-3 py-3 text-left text-sm font-medium outline-none',
                ]"
              >
                <div
                  v-show="selected"
                  class="absolute inset-y-0 left-0 w-1 bg-blue-400"
                />
                <div class="flex items-center justify-between">
                  <span class="truncate">{{ item.name }}</span>
                  <D9Icon
                    class="mr-1 text-grey-500"
                    v-if="item.icon"
                    :name="item.icon"
                  />
                </div>
              </button>
            </Tab>
          </TabList>
        </div>

        <TabPanels as="div" class="col-span-8">
          <TabPanel v-for="item in navigation" :key="item.name">
            <h3 class="border-b border-grey-300 pb-2 text-xl font-medium">
              {{ item.name }}
            </h3>

            <div class="mt-6 pb-6">
              <component :is="item.component" />
            </div>
          </TabPanel>
        </TabPanels>
      </TabGroup>
    </div>
  </app-layout>
</template>

<script setup lang="ts">
import AppLayout from "@/Layouts/AppLayout.vue";
import { TabGroup, TabList, Tab, TabPanels, TabPanel } from "@headlessui/vue";
import { D9Icon } from "@deck9/ui";
import Appearance from "@/components/Factory/Settings/Appearance.vue";
import CompletionPage from "@/components/Factory/Settings/CompletionPage.vue";
import Delete from "@/components/Factory/Settings/Delete.vue";
import Embed from "@/components/Factory/Settings/Embed.vue";
import Options from "@/components/Factory/Settings/Options.vue";
import Privacy from "@/components/Factory/Settings/Privacy.vue";
import TemplateExport from "@/components/Factory/Settings/TemplateExport.vue";
import { useForm } from "@/stores";
import { onBeforeUnmount, ref } from "vue";

const props = defineProps<{
  form: FormModel;
}>();
const store = useForm();

const selectedTabIndex = ref(0);

const navigation: Array<{
  name: string;
  component: any;
  slug: string;
  icon?: string;
}> = [
  { name: "Options", component: Options, slug: "options" },
  { name: "Privacy", component: Privacy, slug: "privacy" },
  {
    name: "Completion Page",
    component: CompletionPage,
    slug: "completion-page",
  },
  { name: "Appearance", component: Appearance, slug: "appearance" },
  { name: "Embed", component: Embed, slug: "embeds" },
  {
    name: "Template Export / Import",
    component: TemplateExport,
    slug: "template-export-import",
  },
  { name: "Delete", component: Delete, slug: "delete" },
];

const tabChanged = (index: any) => {
  const item = navigation[index];
  location.hash = item.slug;
  selectedTabIndex.value = index;
};

const foundIndex = navigation.findIndex(
  (item) => item.slug === location.hash.replace("#", ""),
);

if (foundIndex !== -1) {
  tabChanged(foundIndex);
} else {
  tabChanged(0);
}

onBeforeUnmount(() => {
  store.clearForm();
});

store.$patch({
  form: props.form,
});
</script>
