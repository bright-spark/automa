<template>
  <div class="flex items-center">
    <ui-input
      v-model="fileName"
      :placeholder="t('common.fileName')"
      :title="t('common.fileName')"
    />
    <div class="flex-grow"></div>
    <ui-popover>
      <template #trigger>
        <ui-button variant="accent">
          <span>{{ t('log.exportData.title') }}</span>
          <v-remixicon name="riArrowDropDownLine" class="ml-2 -mr-1" />
        </ui-button>
      </template>
      <ui-list class="space-y-1">
        <ui-list-item
          v-for="type in dataExportTypes"
          :key="type.id"
          v-close-popover
          class="cursor-pointer"
          @click="exportData(type.id)"
        >
          {{ t(`log.exportData.types.${type.id}`) }}
        </ui-list-item>
      </ui-list>
    </ui-popover>
  </div>
  <shared-codemirror
    :model-value="jsonData"
    :class="editorClass"
    lang="json"
    readonly
    class="mt-4"
  />
</template>
<script setup>
import { ref } from 'vue';
import { useI18n } from 'vue-i18n';
import { dataExportTypes } from '@/utils/shared';
import dataExporter, { generateJSON } from '@/utils/data-exporter';
import SharedCodemirror from '@/components/newtab/shared/SharedCodemirror.vue';

const props = defineProps({
  log: {
    type: Object,
    default: () => ({}),
  },
  editorClass: {
    type: String,
    default: '',
  },
});

const { t } = useI18n();

const data = Array.isArray(props.log.data)
  ? props.log.data
  : generateJSON(Object.keys(props.log.data), props.log.data);
const dataStr = JSON.stringify(data, null, 2);
const jsonData =
  dataStr.length >= 5e4 ? `${dataStr.slice(0, 5e4)}\n...` : dataStr;

const fileName = ref(props.log.name);

function exportData(type) {
  dataExporter(data, { name: fileName.value, type }, true);
}
</script>
