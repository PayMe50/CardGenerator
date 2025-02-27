<template>
  <div class="card-preview-content" :style="{ background: data.theme }">
    <div class="card-preview-inner" :style="{
      background: data.theme,
      backgroundImage: `url(${(() => {
        switch (data.logo) {
          case 'x':
            return '/x-logo.svg'
          case 'twitter':
            return '/twitter-logo.svg'
          case 'twitter-blue':
            return '/twitter-logo-blue.svg'
          default:
            return '/twitter-logo.svg'
        }
      })()})`,
      backgroundPosition: '90% 10%',
      backgroundRepeat: 'no-repeat',
      backgroundSize: '120px auto',
      backgroundBlendMode: 'multiply',
      opacity: 0.95
    }">
      <div class="card-header">
        <div class="user-info">
          <img v-if="data.avatar" :src="data.avatar" class="avatar" />
          <div v-else class="avatar-placeholder" />
          <div class="user-details">
            <div class="user-info-line">
              <span class="username">{{ data.username || '用户名' }}</span>
              <span class="date">· {{ formatDate(data.date) }}</span>
            </div>
            <div class="handle">@{{ data.handle || 'handle' }}</div>
          </div>
        </div>
      </div>

      <div class="tweet-content">
        {{ data.content || '在这里输入推文内容...' }}
      </div>

      <div class="tweet-meta">
        <div class="tweet-stats">
          <div class="tweet-stats-left">
            <div class="stat-item">
              <el-icon><ChatRound /></el-icon>
              <span>{{ formatNumber(data.replies || '0') }}</span>
            </div>
            <div class="stat-item">
              <svg class="retweet-icon" viewBox="0 0 24 24" width="18" height="18">
                <path fill="currentColor" d="M4.5 3.88l4.432 4.14-1.364 1.46L5.5 7.55V16c0 1.1.896 2 2 2H13v2H7.5c-2.209 0-4-1.79-4-4V7.55L1.432 9.48.068 8.02 4.5 3.88zM16.5 6H11V4h5.5c2.209 0 4 1.79 4 4v8.45l2.068-1.93 1.364 1.46-4.432 4.14-4.432-4.14 1.364-1.46 2.068 1.93V8c0-1.1-.896-2-2-2z"/>
              </svg>
              <span>{{ formatNumber(data.retweets || '0') }}</span>
            </div>
            <div class="stat-item">
              <svg class="heart-icon" viewBox="0 0 24 24" width="18" height="18">
                <path fill="currentColor" d="M16.697 5.5c-1.222-.06-2.679.51-3.89 2.16l-.805 1.09-.806-1.09C9.984 6.01 8.526 5.44 7.304 5.5c-1.243.07-2.349.78-2.91 1.91-.552 1.12-.633 2.78.479 4.82 1.074 1.97 3.257 4.27 7.129 6.61 3.87-2.34 6.052-4.64 7.126-6.61 1.111-2.04 1.03-3.7.477-4.82-.561-1.13-1.666-1.84-2.908-1.91zm4.187 7.69c-1.351 2.48-4.001 5.12-8.379 7.67l-.503.3-.504-.3c-4.379-2.55-7.029-5.19-8.382-7.67-1.36-2.5-1.41-4.86-.514-6.67.887-1.79 2.647-2.91 4.601-3.01 1.651-.09 3.368.56 4.798 2.01 1.429-1.45 3.146-2.1 4.796-2.01 1.954.1 3.714 1.22 4.601 3.01.896 1.81.846 4.17-.514 6.67z"/>
              </svg>
              <span>{{ formatNumber(data.likes || '0') }}</span>
            </div>
            <div class="stat-item">
              <svg class="views-icon" viewBox="0 0 24 24" width="18" height="18">
                <path fill="currentColor" d="M8.75 21V3h2v18h-2zM18 21V8.5h2V21h-2zM4 21l.004-10h2L6 21H4zm9.248 0v-7h2v7h-2z"/>
              </svg>
              <span>{{ formatNumber(data.views || '0') }}</span>
            </div>
          </div>
          <div class="tweet-stats-right">
            <div class="stat-item">
              <svg class="bookmark-icon" viewBox="0 0 24 24" width="18" height="18">
                <path fill="currentColor" d="M4 4.5C4 3.12 5.119 2 6.5 2h11C18.881 2 20 3.12 20 4.5v18.44l-8-5.71-8 5.71V4.5zM6.5 4c-.276 0-.5.22-.5.5v14.56l6-4.29 6 4.29V4.5c0-.28-.224-.5-.5-.5h-11z"/>
              </svg>
            </div>
            <div class="stat-item">
              <svg class="share-icon" viewBox="0 0 24 24" width="18" height="18">
                <path fill="currentColor" d="M12 2.59l5.7 5.7-1.41 1.42L13 6.41V16h-2V6.41l-3.3 3.3-1.41-1.42L12 2.59zM21 15l-.02 3.51c0 1.38-1.12 2.49-2.5 2.49H5.5C4.11 21 3 19.88 3 18.5V15h2v3.5c0 .28.22.5.5.5h12.98c.28 0 .5-.22.5-.5L19 15h2z"/>
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ElIcon } from 'element-plus'
import { ChatRound } from '@element-plus/icons-vue'
import { computed } from 'vue'

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

const props = defineProps<{
  data: CardData
}>()

// 计算文字颜色
const textColor = computed(() => {
  // 将背景色转换为RGB值
  const getColorBrightness = (color: string) => {
    // 处理渐变色，取第一个颜色
    if (color.includes('gradient')) {
      color = color.match(/rgba?\([^)]+\)|#[a-f\d]{3,8}/gi)?.[0] || '#ffffff'
    }
    
    let r, g, b
    
    // 处理 rgb/rgba 格式
    if (color.startsWith('rgb')) {
      [r, g, b] = color.match(/\d+/g)?.map(Number) || [255, 255, 255]
    }
    // 处理十六进制格式
    else if (color.startsWith('#')) {
      const hex = color.replace('#', '')
      const bigint = parseInt(hex, 16)
      r = (bigint >> 16) & 255
      g = (bigint >> 8) & 255
      b = bigint & 255
    }
    // 默认白色
    else {
      r = g = b = 255
    }
    
    // 计算亮度 (基于 YIQ 颜色空间)
    return (r * 299 + g * 587 + b * 114) / 1000
  }
  
  const brightness = getColorBrightness(props.data.theme)
  // 调整亮度阈值和文字颜色，使其更加美观
  return brightness > 140 ? '#1a1a1a' : '#ffffff'
})

const formatNumber = (num: string) => {
  const n = parseInt(num, 10)
  if (n >= 1000000) {
    return (n / 1000000).toFixed(1) + 'M'
  } else if (n >= 1000) {
    return (n / 1000).toFixed(1) + 'K'
  }
  return num
}

const formatDate = (dateStr: string) => {
  if (!dateStr) return ''
  
  const date = new Date(dateStr)
  const now = new Date()
  const diff = now.getTime() - date.getTime()
  
  // 转换为秒
  const seconds = Math.floor(diff / 1000)
  if (seconds < 60) {
    return `${seconds}秒前`
  }
  
  // 转换为分钟
  const minutes = Math.floor(seconds / 60)
  if (minutes < 60) {
    return `${minutes}分钟前`
  }
  
  // 转换为小时
  const hours = Math.floor(minutes / 60)
  if (hours < 24) {
    return `${hours}小时前`
  }
  
  // 转换为天
  const days = Math.floor(hours / 24)
  if (days < 7) {
    return `${days}天前`
  }
  
  // 超过7天显示具体日期
  return date.toLocaleDateString('zh-CN', {
    month: 'short',
    day: 'numeric'
  })
}
</script>

<style scoped>
.card-preview-content {
  border-radius: 16px;
  padding: 16px;
  color: v-bind(textColor);
}

.dark .card-preview-content {
  color: #e7e9ea;
  background: #000 !important;
}

.dark .card-preview-inner {
  background: #15202b !important;
}

.dark .username {
  color: #fff;
}

.dark .handle {
  color: #71767b;
}

.dark .tweet-content {
  color: #fff;
}

.card-preview-inner {
  background: #fff;
  border-radius: 12px;
  padding: 16px;
}

.dark .card-preview-inner {
  background: #000;
}

.logo-container {
  margin-bottom: 12px;
}

.platform-logo {
  height: 24px;
  width: auto;
}

.card-header {
  margin-bottom: 12px;
}

.user-info {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}

.avatar,
.avatar-placeholder {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background-color: #cfd9de;
}

.avatar {
  object-fit: cover;
}

.user-details {
  flex: 1;
}

.user-info-line {
  display: flex;
  align-items: center;
  gap: 4px;
}

.username {
  font-weight: 700;
  font-size: 15px;
  color: #ffffff;
}

.date {
  color: #536471;
  font-size: 15px;
}

.dark .date {
  color: #71767b;
}

.handle {
  color: #ffffff;
  opacity: 0.6;
  font-size: 14px;
}

.tweet-content {
  font-size: 16px;
  line-height: 1.5;
  margin: 12px 0;
  white-space: pre-wrap;
  word-wrap: break-word;
  color: #ffffff;
}

.tweet-meta {
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: #536471;
  font-size: 14px;
  margin-top: 12px;
  padding: 4px 0;
}

.tweet-stats {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}

.tweet-stats-left {
  display: flex;
  align-items: center;
  gap: 24px;
}

.tweet-stats-right {
  display: flex;
  align-items: center;
  gap: 4px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 4px;
  color: v-bind(textColor);
  opacity: 0.6;
  cursor: pointer;
  padding: 2px 6px;
  border-radius: 9999px;
  transition: all 0.2s ease;
}

.stat-item:hover {
  background-color: rgba(83, 100, 113, 0.1);
}

.stat-item:has(.el-icon):hover,
.stat-item:has(.bookmark-icon):hover,
.stat-item:has(.share-icon):hover {
  color: rgb(29, 155, 240) !important;
  background-color: rgba(29, 155, 240, 0.1);
}

.stat-item:has(.retweet-icon):hover {
  color: #00BA7C !important;
  background-color: rgba(0, 186, 124, 0.1);
}

.stat-item:has(.heart-icon):hover {
  color: #F91880 !important;
  background-color: rgba(249, 24, 128, 0.1);
}

.stat-item:has(.views-icon):hover {
  color: rgb(29, 155, 240) !important;
  background-color: rgba(29, 155, 240, 0.1);
}
.stat-item .el-icon,
.stat-item svg {
  width: 18px;
  height: 18px;
  color: inherit;
  fill: currentColor;
}

.dark .stat-item,
.dark .stat-item .el-icon,
.dark .stat-item svg {
  color: #71767b;
  fill: currentColor;
}

.dark .stat-item:hover {
  background-color: rgba(113, 118, 123, 0.1);
}

.retweet-icon,
.heart-icon {
  color: inherit;
  fill: currentColor;
}

.dark .retweet-icon,
.dark .heart-icon {
  color: #71767b;
  fill: currentColor;
}

.dark .tweet-meta {
  color: #71767b;
}
</style>