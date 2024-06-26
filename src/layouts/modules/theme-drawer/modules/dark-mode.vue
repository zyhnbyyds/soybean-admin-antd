<script setup lang="ts">
import { computed } from 'vue';
import type { SegmentedOption } from 'ant-design-vue/es/segmented/src/segmented';
import { themeSchemaRecord } from '@/constants/app';
import { useThemeStore } from '@/store/modules/theme';
import SettingItem from '../components/setting-item.vue';

defineOptions({
  name: 'DarkMode'
});

const themeStore = useThemeStore();

const icons: Record<UnionKey.ThemeScheme, string> = {
  light: 'material-symbols:sunny',
  dark: 'material-symbols:nightlight-rounded',
  auto: 'material-symbols:hdr-auto'
};

function getSegmentOptions() {
  const opts: SegmentedOption[] = Object.keys(themeSchemaRecord).map(item => {
    const key = item as UnionKey.ThemeScheme;
    return {
      value: item,
      payload: {
        icon: icons[key]
      }
    };
  });

  return opts;
}

const options = computed(() => getSegmentOptions());

function handleSegmentChange(value: string | number) {
  themeStore.setThemeScheme(value as UnionKey.ThemeScheme);
}

const showSiderInverted = computed(() => !themeStore.darkMode && themeStore.layout.mode.includes('vertical'));
</script>

<template>
  <ADivider>{{ $t('theme.themeSchema.title') }}</ADivider>
  <div class="flex-col-stretch gap-16px">
    <div class="i-flex-center">
      <ASegmented :value="themeStore.themeScheme" :options="options" class="bg-layout" @change="handleSegmentChange">
        <template #label="{ payload }">
          <ButtonIcon :icon="payload.icon" class="h-28px text-icon-small" />
        </template>
      </ASegmented>
    </div>
    <Transition name="sider-inverted">
      <SettingItem v-if="showSiderInverted" :label="$t('theme.sider.inverted')">
        <ASwitch v-model:checked="themeStore.sider.inverted" />
      </SettingItem>
    </Transition>
  </div>
</template>

<style scoped>
.sider-inverted-enter-active,
.sider-inverted-leave-active {
  --uno: h-22px transition-all-300;
}

.sider-inverted-enter-from,
.sider-inverted-leave-to {
  --uno: translate-x-20px opacity-0 h-0;
}
</style>
