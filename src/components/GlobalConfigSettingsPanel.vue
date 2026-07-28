<script setup>
import { computed, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import { useTimelineStore } from '@/stores/timelineStore';
import EditGlobalModifiersDialog from './EditGlobalModifiersDialog.vue';
import { formatGlobalModifierLabel } from '@/data/globalConfig';

const store = useTimelineStore();
const { t } = useI18n({ useScope: 'global' });

const isEditDialogVisible = ref(false);

const customModifiers = computed(() =>
  (store.globalConfig?.customModifiers || []).filter(mod => mod?.kind === 'operatorStat'),
);

const summaryRows = computed(() =>
  customModifiers.value.map(mod => ({
    id: mod.id,
    label: formatGlobalModifierLabel(mod, t),
    value: mod.value,
  })),
);
</script>

<template>
  <div class="global-config-settings">
    <div class="panel-title">{{ t('globalConfig.customSection') }}</div>

    <div class="stats-summary">
      <div v-if="summaryRows.length === 0" class="empty-hint">
        {{ t('globalConfig.customEmpty') }}
      </div>
      <div v-for="row in summaryRows" :key="row.id" class="summary-row">
        <span class="summary-label">{{ row.label }}</span>
        <span class="summary-value">{{ row.value }}</span>
      </div>
      <button
        type="button"
        class="ea-btn ea-btn--sm ea-btn--glass-rect ea-btn--accent-gold stats-edit-btn"
        @click="isEditDialogVisible = true"
      >
        {{ t('globalConfig.editCustom') }}
      </button>
    </div>

    <EditGlobalModifiersDialog v-model:visible="isEditDialogVisible" />
  </div>
</template>

<style scoped>
.global-config-settings {
  height: 100%;
  overflow: auto;
  padding: 12px 12px 16px;
  box-sizing: border-box;
  background: #252526;
  color: rgba(255, 255, 255, 0.86);
}

.panel-title {
  font-size: 13px;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.78);
  margin-bottom: 12px;
}

.stats-summary {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.summary-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  padding: 8px 10px;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.06);
}

.summary-label {
  min-width: 0;
  flex: 1;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.7);
}

.summary-value {
  flex-shrink: 0;
  font-size: 12px;
  font-variant-numeric: tabular-nums;
  color: rgba(255, 255, 255, 0.88);
}

.empty-hint {
  font-size: 12px;
  color: rgba(255, 255, 255, 0.4);
  padding: 4px 0 2px;
}

.stats-edit-btn {
  align-self: stretch;
  margin-top: 4px;
}
</style>
