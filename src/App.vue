<template>
  <el-config-provider :locale="zhCn">
    <div class="app-container">
      <el-container>
        <el-header class="header" style="height: 48px; padding: 0 16px;">
          <h1 style="font-size: 20px; margin: 0;">推特卡片生成器</h1>
          <el-switch
            v-model="isDarkMode"
            class="theme-switch"
            :active-icon="Moon"
            :inactive-icon="Sunny"
            @change="toggleTheme"
          />
        </el-header>
        <el-main style="padding: 16px;">
          <el-row :gutter="16" class="main-content">
            <el-col :span="12">
              <div class="editor-container">
                <h2 style="font-size: 16px; margin: 0 0 12px;">编辑区</h2>
                <CardEditor @update="updatePreview" />
              </div>
            </el-col>
            <el-col :span="12">
              <div class="preview-container">
                <h2 style="font-size: 16px; margin: 0 0 12px;">预览区</h2>
                <div class="card-preview" ref="previewRef">
                  <CardPreview :data="previewData" />
                </div>
                <div class="button-group" style="margin-top: 12px;">
                  <el-button type="primary" @click="copyImage">复制图片</el-button>
                  <el-button type="primary" @click="exportImage">导出图片</el-button>
                </div>
              </div>
            </el-col>
          </el-row>
        </el-main>
      </el-container>
    </div>
  </el-config-provider>
</template>

<style scoped>
.app-container {
  height: 100vh;
  overflow: hidden;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid var(--el-border-color-light);
}

.main-content {
  height: calc(100vh - 48px);
}

.editor-container,
.preview-container {
  height: 100%;
  overflow-y: auto;
}

.card-preview {
  margin-bottom: 12px;
}

.button-group {
  display: flex;
  gap: 8px;
}
</style>

<script setup lang="ts">
import { ref } from 'vue'
import { Moon, Sunny } from '@element-plus/icons-vue'
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'
import html2canvas from 'html2canvas'
import { ElMessage } from 'element-plus'
import CardEditor from './components/CardEditor.vue'
import CardPreview from './components/CardPreview.vue'

interface CardData {
  avatar: string
  username: string
  handle: string
  content: string
  logo: 'x' | 'twitter' | 'twitter-blue'
  date: string
  theme: string
  replies: string
  retweets: string
  likes: string
  views: string
}

const previewData = ref<CardData>({
  avatar: '',
  username: '',
  handle: '',
  content: '',
  logo: 'x',
  date: new Date().toISOString().slice(0, 16).replace('T', ' '),
  theme: 'linear-gradient(120deg, #f6d365 0%, #fda085 100%)',
  replies: '0',
  retweets: '0',
  likes: '0',
  views: '0'
})

const updatePreview = (data: CardData) => {
  previewData.value = data
}

const isDarkMode = ref(false)
const previewRef = ref<HTMLElement | null>(null)

const toggleTheme = (value: boolean) => {
  const html = document.documentElement
  if (value) {
    html.classList.add('dark')
    document.body.setAttribute('class', 'dark')
  } else {
    html.classList.remove('dark')
    document.body.removeAttribute('class')
  }
}

const exportImage = async () => {
  if (!previewRef.value) return
  
  try {
    // 等待所有图片加载完成
    const images = previewRef.value.getElementsByTagName('img')
    await Promise.all(Array.from(images).map(img => {
      if (img.complete) return Promise.resolve()
      return new Promise((resolve, reject) => {
        img.onload = resolve
        img.onerror = reject
      })
    }))

    const canvas = await html2canvas(previewRef.value, {
      useCORS: true,
      logging: false,
      scale: 3, // 提高缩放比例以获得更好的图片质量
      backgroundColor: null
    })

    // 创建下载链接
    const link = document.createElement('a')
    link.download = 'twitter-card.png'
    link.href = canvas.toDataURL('image/png')
    link.click()

    ElMessage.success('导出成功')
  } catch (error) {
    console.error('导出失败:', error)
    ElMessage.error('导出失败')
  }
}

const copyImage = async () => {
  if (!previewRef.value) return

  try {
    const canvas = await html2canvas(previewRef.value, {
      useCORS: true,
      logging: false,
      scale: 3,
      backgroundColor: null
    })

    canvas.toBlob(async (blob) => {
      if (blob) {
        try {
          await navigator.clipboard.write([
            new ClipboardItem({
              'image/png': blob
            })
          ])
          ElMessage.success('复制成功')
        } catch (error) {
          console.error('复制失败:', error)
          ElMessage.error('复制失败')
        }
      }
    }, 'image/png')
  } catch (error) {
    console.error('生成图片失败:', error)
    ElMessage.error('生成图片失败')
  }
}
</script>

<style scoped>
.app-container {
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.el-container {
  height: 100%;
}

.el-main {
  padding: 16px;
  height: calc(100vh - 60px);
  overflow: hidden;
}

.main-content {
  height: 100%;
}

.header {
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  background-color: var(--el-bg-color);
  border-bottom: 1px solid var(--el-border-color-light);
}

.header h1 {
  margin: 0;
  font-size: 24px;
  color: var(--el-text-color-primary);
}

.theme-switch {
  margin-left: auto;
}

.editor-container,
.preview-container {
  background-color: var(--el-bg-color);
  border-radius: 8px;
  padding: 12px;
  height: 100%;
  overflow: hidden;
}

.editor-container h2,
.preview-container h2 {
  margin-top: 0;
  margin-bottom: 12px;
  color: var(--el-text-color-primary);
}

.card-preview {
  margin-bottom: 12px;
}

.button-group {
  display: flex;
  gap: 12px;
  justify-content: center;
}
</style>