<template>
  <div class="card-editor">
    <el-form :model="formData" label-position="top" size="small">
      <el-form-item label="头像" style="margin-bottom: 12px;">
        <el-upload
          class="avatar-uploader"
          :show-file-list="false"
          :auto-upload="false"
          accept="image/*"
          @change="handleAvatarChange"
        >
          <img v-if="formData.avatar" :src="formData.avatar" class="avatar" />
          <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
        </el-upload>
      </el-form-item>

      <el-row :gutter="12">
        <el-col :span="12">
          <el-form-item label="用户名" style="margin-bottom: 12px;">
            <el-input v-model="formData.username" placeholder="请输入用户名" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="账号" style="margin-bottom: 12px;">
            <el-input v-model="formData.handle" placeholder="请输入账号">
              <template #prefix>@</template>
            </el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item label="推文内容" style="margin-bottom: 12px;">
        <el-input
          v-model="formData.content"
          type="textarea"
          :rows="6"
          placeholder="请输入推文内容"
          :maxlength="1000"
          show-word-limit
        />
      </el-form-item>

      <el-form-item label="Logo" style="margin-bottom: 12px;">
        <el-radio-group v-model="formData.logo">
          <el-radio label="x">X (黑色)</el-radio>
          <el-radio label="twitter">Twitter (黑色)</el-radio>
          <el-radio label="twitter-blue">Twitter (蓝色)</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-row :gutter="12">
        <el-col :span="12">
          <el-form-item label="发布时间" style="margin-bottom: 12px;">
            <el-date-picker
              v-model="formData.date"
              type="datetime"
              class="datetime-input"
              placeholder="选择日期和时间"
              format="YYYY-MM-DD HH:mm"
              value-format="YYYY-MM-DD HH:mm"
              style="width: 100%;"
            />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="主题" style="margin-bottom: 12px;">
            <el-button @click="dialogVisible = true" style="width: 100%;">
              选择主题
              <el-icon class="el-icon--right"><Brush /></el-icon>
            </el-button>
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item label="互动数据" style="margin-bottom: 12px;">
        <el-row :gutter="12">
          <el-col :span="6">
            <el-input v-model="formData.replies" placeholder="回复数" @focus="$event.target.select()">
              <template #prefix>回复</template>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-input v-model="formData.retweets" placeholder="转发数" @focus="$event.target.select()">
              <template #prefix>转发</template>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-input v-model="formData.likes" placeholder="点赞数" @focus="$event.target.select()">
              <template #prefix>点赞</template>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-input v-model="formData.views" placeholder="查看数" @focus="$event.target.select()">
              <template #prefix>查看</template>
            </el-input>
          </el-col>
        </el-row>
      </el-form-item>
    </el-form>

    <el-dialog
      v-model="dialogVisible"
      title="选择主题"
      width="500px"
      align-center
    >
      <div class="theme-grid">
        <div
          v-for="theme in themes"
          :key="theme.value"
          class="theme-item"
          :class="{ active: formData.theme === theme.value }"
          @click="handleThemeChange(theme.value)"
        >
          <div class="theme-preview" :style="{ background: theme.value }" />
          <span class="theme-label">{{ theme.label }}</span>
        </div>
      </div>
      <div class="custom-color">
        <span class="custom-color-label">自定义颜色：</span>
        <el-color-picker
          v-model="customColor"
          show-alpha
          @change="handleCustomColorChange"
        />
      </div>
    </el-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, defineEmits, watch } from 'vue'
import { Plus, Brush } from '@element-plus/icons-vue'
import type { UploadFile } from 'element-plus'

const dialogVisible = ref(false)
const customColor = ref('')

interface FormData {
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

const themes = [
  { label: '日落渐变', value: 'linear-gradient(120deg, #f6d365 0%, #fda085 100%)' },
  { label: '清新渐变', value: 'linear-gradient(120deg, #84fab0 0%, #8fd3f4 100%)' },
  { label: '粉蓝渐变', value: 'linear-gradient(120deg, #a6c0fe 0%, #f68084 100%)' },
  { label: '紫蓝渐变', value: 'linear-gradient(120deg, #e0c3fc 0%, #8ec5fc 100%)' },
  { label: '薄暮渐变', value: 'linear-gradient(120deg, #ff9a9e 0%, #fad0c4 100%)' },
  { label: '极光渐变', value: 'linear-gradient(120deg, #43e97b 0%, #38f9d7 100%)' },
  { label: '深蓝', value: '#15202b' },
  { label: '紫粉渐变', value: 'linear-gradient(120deg, #c471f5 0%, #fa71cd 100%)' }
]

const handleThemeChange = (value: string) => {
  formData.value.theme = value
  dialogVisible.value = false
}

const handleCustomColorChange = (value: string) => {
  if (value) {
    formData.value.theme = value
    dialogVisible.value = false
  }
}

const emit = defineEmits(['update'])

const formData = ref<FormData>({
  avatar: '',
  username: '',
  handle: '',
  content: '',
  logo: 'x',
  date: new Date().toISOString().slice(0, 16).replace('T', ' '),
  theme: themes[0].value,
  replies: '0',
  retweets: '0',
  likes: '0',
  views: '0'
})

const handleAvatarChange = (file: UploadFile) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    formData.value.avatar = e.target?.result as string
    emit('update', formData.value)
  }
  reader.readAsDataURL(file.raw!)
}

// 监听表单数据变化
watch(formData, (newValue) => {
  emit('update', newValue)
}, { deep: true })
</script>

<style scoped>
.avatar-uploader {
  width: 80px;
  height: 80px;
  border: 1px dashed var(--border-color);
  border-radius: 50%;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader:hover {
  border-color: var(--el-color-primary);
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 80px;
  height: 80px;
  text-align: center;
  line-height: 80px;
}

.avatar {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.datetime-input {
  width: 100%;
}

.datetime-input :deep(.el-date-editor) {
  width: 100%;
}

.theme-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
  margin-bottom: 16px;
}

.theme-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  padding: 6px;
  border-radius: 6px;
  transition: all 0.3s ease;
}

.theme-item:hover {
  background-color: var(--el-color-primary-light-9);
}

.theme-item.active {
  background-color: var(--el-color-primary-light-8);
}

.theme-preview {
  width: 50px;
  height: 50px;
  border-radius: 6px;
  margin-bottom: 6px;
  border: 1px solid var(--border-color);
}

.theme-label {
  font-size: 14px;
  color: var(--el-text-color-regular);
}

.custom-color-label {
  font-size: 14px;
  margin-right: 12px;
  color: var(--el-text-color-regular);
}
</style>