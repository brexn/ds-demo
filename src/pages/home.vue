<template>
  <div class="home-page">
    <!-- 顶部背景图片 -->
    <div class="top-bg-container">
      <!-- Tab导航组件 -->
      <div class="tab-navigation">
        <div
          v-for="(tab, index) in tabs"
          :key="index"
          class="tab-item"
          :class="{ active: activeTab === index }"
          @click="switchTab(index)"
        >
          {{ tab.name }}
        </div>
      </div>
    </div>

    <!-- 页面内容区域 -->
    <div class="tab-content">
      <!-- 显示替换图片 -->
      <div v-if="showModal" class="replacement-image">
        <img
          :src="imageData[selectedImageIndex].subSrc"
          :alt="imageData[selectedImageIndex].title"
          @click="handleSubImageClick"
        />
        <!-- 右上角半透明蒙层 -->
        <div class="sub-corner-element" @click="handleSubCornerClick"></div>
      </div>

      <!-- 动态内容显示 -->
      <div v-else-if="currentTabContent" class="operation-data-image">
        <img
          :src="currentTabContent.bgImage"
          :alt="currentTabContent.name"
          @click="currentTabContent.popupType && showPopup(currentTabContent.popupType)"
        />
      </div>

      <!-- 首页内容：6张图片网格布局 -->
      <div v-else-if="activeTab === 0" class="image-grid">
        <div v-for="(image, index) in imageData" :key="index" class="image-block" @click="handleImageClick(index)">
          <img :src="image.src" :alt="image.title" class="grid-image" />
          <!-- 右上角半透明背景元素 -->
          <div class="corner-element" @click.stop="showTip(index)"></div>
          <!-- 左下角提示图片 -->
          <div v-if="showTipIndex === index" class="tip-image" @click.stop="hideTip">
            <img src="@/assets/h_tip.png" alt="提示" />
          </div>
        </div>
      </div>

      <!-- 其他tab的内容 -->
      <div v-else class="other-content">
        {{ tabs[activeTab].content }}
      </div>
    </div>

    <!-- 统一弹窗组件 -->
    <div v-if="showPopupModal" class="popup-overlay" @click="closePopup">
      <div class="popup-content" @click.stop>
        <img
          :src="popupImages[currentPopupType]"
          :alt="`弹窗图片${currentPopupType}`"
          class="popup-image"
          @click="closePopup"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Tab数据配置
const tabs = ref([
  {
    name: '首页',
    content: '首页内容区域',
    bgImage: null,
    popupType: null
  },
  {
    name: '运营数据',
    content: '运营数据内容区域',
    bgImage: new URL('@/assets/bg_1.png', import.meta.url).href,
    popupType: null
  },
  {
    name: '实时监控',
    content: '实时监控内容区域',
    bgImage: new URL('@/assets/bg_2.png', import.meta.url).href,
    popupType: 1
  },
  {
    name: '直播复盘',
    content: '直播复盘内容区域',
    bgImage: new URL('@/assets/bg_3.png', import.meta.url).href,
    popupType: 1
  },
  {
    name: '人资数据',
    content: '人资数据内容区域',
    bgImage: new URL('@/assets/bg_4.png', import.meta.url).href,
    popupType: 2
  }
])

// 弹窗图片配置
const popupImages = {
  1: new URL('@/assets/pop_1.png', import.meta.url).href,
  2: new URL('@/assets/pop_2.png', import.meta.url).href
}

// 状态管理
const activeTab = ref(0)
const showModal = ref(false)
const selectedImageIndex = ref(0)
const showTipIndex = ref(-1)
const showPopupModal = ref(false)
const currentPopupType = ref(null)

// 图片数据配置
const imageData = ref([
  {
    src: new URL('@/assets/s_1.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_1.png', import.meta.url).href,
    title: '核心指标'
  },
  {
    src: new URL('@/assets/s_2.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_2.png', import.meta.url).href,
    title: '营收与转化'
  },
  {
    src: new URL('@/assets/s_3.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_3.png', import.meta.url).href,
    title: '流量与留存'
  },
  {
    src: new URL('@/assets/s_4.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_4.png', import.meta.url).href,
    title: '主播分析'
  },
  {
    src: new URL('@/assets/s_5.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_5.png', import.meta.url).href,
    title: '财务分析'
  },
  {
    src: new URL('@/assets/s_6.png', import.meta.url).href,
    subSrc: new URL('@/assets/sub_6.png', import.meta.url).href,
    title: '用户画像'
  }
])

// 计算属性
const currentTabContent = computed(() => {
  const tab = tabs.value[activeTab.value]
  return tab && tab.bgImage ? tab : null
})

// 方法定义
const switchTab = (index) => {
  activeTab.value = index
}

const handleImageClick = (index) => {
  selectedImageIndex.value = index
  showModal.value = true
}

const handleSubImageClick = () => {
  // 如果是第6张图片的sub图片（sub_6），跳转到指定链接
  if (selectedImageIndex.value === 5) {
    window.open('https://tg.lingshan.ai/dashboard/live-monitoring', '_blank')
  }
  // 其他图片点击无反应
}

const handleSubCornerClick = () => {
  // 右上角蒙层点击关闭弹窗
  closeModal()
}

const closeModal = () => {
  showModal.value = false
}

const showTip = (index) => {
  showTipIndex.value = index
}

const hideTip = () => {
  showTipIndex.value = -1
}

const showPopup = (popupType) => {
  currentPopupType.value = popupType
  showPopupModal.value = true
}

const closePopup = () => {
  showPopupModal.value = false
  currentPopupType.value = null
}
</script>

<style lang="scss" scoped>
.home-page {
  width: 100%;
  min-height: 100vh;
  padding-bottom: 20px;
}

.top-bg-container {
  width: 100%;
  position: relative;
  display: flex;
  justify-content: center;
  min-height: 225px;
  background-image: url('@/assets/top_bg.png');
  background-size: 100% auto;
  background-repeat: no-repeat;
}

.tab-navigation {
  display: flex;
  gap: 40px;
  position: relative;
  z-index: 2;
  background: rgba(255, 255, 255, 0.1);
  padding: 15px 30px;
  border-radius: 15px;
  height: fit-content;
}

.tab-item {
  color: #999;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  padding: 8px 16px;
  border-radius: 15px;
  transition: all 0.3s ease;
  position: relative;
  user-select: none;

  &:hover {
    color: #666;
    background: rgba(255, 255, 255, 0.1);
  }

  &.active {
    color: #333;
    font-weight: 600;

    &::before {
      content: '';
      position: absolute;
      top: 3px;
      right: 3px;
      transform: translateX(-50%);
      width: 12px;
      height: 12px;
      background-image: url('@/assets/tab_icon.png');
      background-size: contain;
      background-repeat: no-repeat;
      background-position: center;
    }
  }
}

.tab-content {
  width: 80%;
  margin: 0 auto;
  position: relative;
  top: -80px;
  z-index: 10;
}

.image-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 1fr);
  gap: 10px;
}

.image-block {
  position: relative;
  cursor: pointer;
}

.grid-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.corner-element {
  position: absolute;
  top: 0;
  right: 0;
  width: 200px;
  height: 100px;
  z-index: 10;
  cursor: pointer;
}

.tip-image {
  position: absolute;
  right: -220px;
  top: 80px;
  z-index: 15;
  cursor: pointer;

  img {
    width: auto;
    height: auto;
    max-width: 400px;
    display: block;
  }
}

.replacement-image,
.operation-data-image {
  width: 100%;
  height: 100%;
  min-height: 80vh;
  position: relative;

  img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    display: block;
    cursor: pointer;
  }
}

.sub-corner-element {
  position: absolute;
  top: 0;
  right: 0;
  width: 200px;
  height: 200px;
  z-index: 100;
  cursor: pointer;
  // background: rgba(255, 0, 0, 0.5);
  border-radius: 0 0 0 10px;
  box-sizing: border-box;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  cursor: pointer;
}

.popup-content {
  position: relative;
  max-width: 90%;
  max-height: 90%;
  cursor: default;
}

.popup-image {
  height: 500px;
  object-fit: contain;
  display: block;
  cursor: pointer;
}
</style>
