<script setup lang="ts">
import {
  HoverCard,
  HoverCardContent,
  HoverCardTrigger,
} from '@/components/ui/hover-card';
import {
  codeBlockThemeOptions,
  colorOptions,
  fontFamilyOptions,
  fontSizeOptions,
  legendOptions,
} from '@/config';

import { useDisplayStore, useStore } from '@/stores';
import { storeToRefs } from 'pinia';
import { ref, toRaw } from 'vue';
import StyleOptionMenu from './StyleOptionMenu.vue';

const store = useStore();
const { toggleShowCssEditor } = useDisplayStore();

const {
  defaultCssThemes,
  theme,
  fontFamily,
  fontSize,
  primaryColor,
  codeBlockTheme,
  legend,
  isMacCodeBlock,
  isAiRealtime,
  cssEditor,
} = storeToRefs(store);

const {
  resetStyleConfirm,
  themeChanged,
  fontChanged,
  sizeChanged,
  colorChanged,
  codeBlockThemeChanged,
  legendChanged,
  macCodeBlockChanged,
  toggleAiRealtime,
} = store;

const colorPicker = ref<(HTMLElement & { show: () => void }) | null>(null);

function showPicker() {
  colorPicker.value?.show();
}

// 自定义CSS样式
function customStyle() {
  toggleShowCssEditor();
  setTimeout(() => {
    toRaw(cssEditor.value)!.dispatch();
  }, 50);
}
</script>

<template>
  <MenubarMenu>
    <MenubarTrigger> 样式 </MenubarTrigger>
    <MenubarContent class="w-56" align="start">
      <StyleOptionMenu
        title="主题"
        :options="defaultCssThemes"
        :current="theme"
        :change="themeChanged"
      />
      <MenubarSeparator />
      <StyleOptionMenu
        title="字体"
        :options="fontFamilyOptions"
        :current="fontFamily"
        :change="fontChanged"
      />
      <StyleOptionMenu
        title="字号"
        :options="fontSizeOptions"
        :current="fontSize"
        :change="sizeChanged"
      />
      <StyleOptionMenu
        title="主题色"
        :options="colorOptions"
        :current="primaryColor"
        :change="colorChanged"
      />
      <StyleOptionMenu
        title="代码块主题"
        :options="codeBlockThemeOptions"
        :current="codeBlockTheme"
        :change="codeBlockThemeChanged"
      />
      <StyleOptionMenu
        title="图注格式"
        :options="legendOptions"
        :current="legend"
        :change="legendChanged"
      />
      <MenubarSeparator />
      <MenubarItem @click.self.prevent="showPicker">
        <HoverCard :open-delay="100">
          <HoverCardTrigger class="w-full flex">
            <el-icon class="mr-2 h-4 w-4" />
            自定义主题色
          </HoverCardTrigger>
          <HoverCardContent side="right" class="w-min">
            <ElColorPicker
              ref="colorPicker"
              v-model="primaryColor"
              :teleported="false"
              show-alpha
              class="ml-auto"
              style="height: 2em"
              @change="colorChanged"
              @click="showPicker"
            />
          </HoverCardContent>
        </HoverCard>
        <!-- <el-icon class="mr-2 h-4 w-4" />
        自定义主题色
        <el-color-picker
          ref="colorPicker"
          v-model="primaryColor"
          :teleported="false"
          show-alpha
          class="ml-auto"
          style="height: 2em"
          @change="colorChanged"
          @click="showPicker"
        /> -->
      </MenubarItem>
      <MenubarItem @click="customStyle">
        <el-icon class="mr-2 h-4 w-4" />
        自定义 CSS
      </MenubarItem>
      <MenubarSeparator />
      <MenubarItem @click="macCodeBlockChanged">
        <el-icon class="mr-2 h-4 w-4" :class="{ 'opacity-0': !isMacCodeBlock }">
          <ElIconCheck />
        </el-icon>
        Mac 代码块
      </MenubarItem>
      <MenubarItem @click="() => toggleAiRealtime()">
        <el-icon class="mr-2 h-4 w-4" :class="{ 'opacity-0': !isAiRealtime }">
          <ElIconCheck />
        </el-icon>
        AI 实时提示
      </MenubarItem>
      <MenubarSeparator />
      <MenubarItem divided @click="resetStyleConfirm">
        <el-icon class="mr-2 h-4 w-4" />
        重置
      </MenubarItem>
    </MenubarContent>
  </MenubarMenu>
</template>
