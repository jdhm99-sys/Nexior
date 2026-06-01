<template>
  <div>
    <!-- 输入框已完全隐藏 -->
    <!-- <prompt-textarea
      v-model="prompt"
      :title="$t('nanobanana.name.prompt')"
      :info="$t('nanobanana.description.prompt')"
      :placeholder="$t('nanobanana.placeholder.prompt')"
    /> -->

    <!-- 三个预设任务按钮 -->
    <div style="margin-top: 0; display: flex; gap: 12px; flex-wrap: wrap; justify-content: center;">
      <el-button type="primary" @click="setPresetPrompt(preset1)">定位印花</el-button>
      <el-button type="success" @click="setPresetPrompt(preset2)">布匹印花</el-button>
      <el-button type="warning" @click="setPresetPrompt(preset3)">消除布纹</el-button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import PromptTextarea from '@/components/common/PromptTextarea.vue';

export const DEFAULT_PROMPT = '';

export default defineComponent({
  name: 'PromptInput',
  components: {
    PromptTextarea
  },
  data() {
    return {
      preset1: `定位印花：
处理流程与核心要求：
图案识别与提取 (核心)：
精确识别： 对提供的衣物图片进行深度视觉分析，精确识别并隔离出图案的主要单元。
单一完整实例： 提取至少一个最完整、最清晰、无遮挡、无明显形变的图案实例作为基础。
确保提取的单个图案实例与原始图片中的图案在形状、大小、斑点边缘细节、颜色（包括明暗过渡和色调）以及斑点之间的相对位置和间距上，达到像素级的最高程度一致。严禁任何形式的图案修改、平均化或创作。
缺失与空白处理 (严格限制)：
如果在提取过程中图案存在小块缺失、不完整或空白区域，首选且优先从原图其他完整区域复制相似的图案元素进行填充，以维持原始图案的统一性。
谨慎填充： 仅在无法进行复制填充且面积极小、不影响主要图案特征的情况下，才允许进行最保守的内容感知生成式填充。填充内容必须与周围图案无缝融合，严禁引入任何新的图案元素或改变原有图案的随机性和特征。
最终图像生成与质量标准：
输出格式： 创建一个2D平面图像。
分辨率与细节：图像增强细节。图案边缘清晰锐利。 图像分辨率必须达到8K，具备超高清、高细节、照片级写实的视觉品质。最终图像应达到印刷级的专业标准。
图案完整性与比例： 图像中的单元图案整个图案必须保持完全可见，图案的任何部分都不得被图像边缘裁剪。单元图案应被缩放到尽可能大，以充分展示细节。
剥离衣物属性： 在生成过程中，完全忽略并去除原始服装的形状、接缝、褶皱、皱纹以及任何光照、阴影、反光等环境效果。最终图像必须是纯粹、平整的织物图案。
样式与颜色一致性： 生成图案的所有样式和颜色（包括所有色阶和纹理感）必须与原始图片中的图案完全相同，无任何偏差。  不要重复单元，给图案我，不要文字`,

      preset2: `布匹印花
图案识别与提取 (核心)：
精确识别： 对提供的衣物图片进行深度视觉分析，精确识别并隔离出图案的主要重复单元。
单一完整实例： 提取至少一个最完整、最清晰、无遮挡、无明显形变的图案实例作为基础。
像素级复制： 确保提取的单个图案实例与原始图片中的图案在形状、大小、斑点边缘细节、颜色（包括明暗过渡和色调）以及斑点之间的相对位置和间距上，达到像素级的最高程度一致。严禁任何形式的图案修改、平均化或创作。
缺失与空白处理 (严格限制)：
优先复制： 如果在提取过程中图案存在小块缺失、不完整或空白区域，首选且优先从原图其他完整区域复制相似的图案元素进行填充，以维持原始图案的统一性。
谨慎填充： 仅在无法进行复制填充且面积极小、不影响主要图案特征的情况下，才允许进行最保守的内容感知生成式填充。填充内容必须与周围图案无缝融合，严禁引入任何新的图案元素或改变原有图案的随机性和特征。
最终图像生成与质量标准：
输出格式： 创建一个2D平面图像，展现完整的无缝、可平铺的图案。
分辨率与细节：图像增强细节。图案边缘清晰锐利。 图像分辨率必须达到8K，具备超高清、高细节、照片级写实的视觉品质。最终图像应达到印刷级的专业标准。
图案完整性与比例： 图像中的无缝单元图案整个图案必须保持完全可见，图案的任何部分都不得被图像边缘裁剪。无缝单元图案应被缩放到尽可能大，以充分展示细节。
剥离衣物属性： 在生成过程中，完全忽略并去除原始服装的形状、接缝、褶皱、皱纹以及任何光照、阴影、反光等环境效果。最终图像必须是纯粹、平整的织物图案。
样式与颜色一致性： 生成图案的所有样式和颜色（包括所有色阶和纹理感）必须与原始图片中的图案完全相同，无任何偏差。  给图案我，不要文字`,

      preset3: `消除布纹
这是我扫描的布料图案，请对图片进行深度视觉分析，清除布料上的布纹杂点以及可能有的暗纹痕迹，不要改变图内其他内容，重新输出超高清图片`
    };
  },
  computed: {
    prompt: {
      get() {
        return this.$store.state.nanobanana?.config?.prompt;
      },
      set(val: string) {
        this.$store.commit('nanobanana/setConfig', {
          ...this.$store.state.nanobanana?.config,
          prompt: val
        });
      }
    }
  },
  methods: {
    setPresetPrompt(preset: string) {
      this.prompt = preset;
    }
  },
  mounted() {
    if (!this.prompt) {
      this.prompt = DEFAULT_PROMPT;
    }
  }
});
</script>