---
name: "el-upload"
description: "用于通过点击拖放上传文件上传组件。当用户需要上传文件、图片文档,并需要预览、验证和进度跟踪时调用---

# 上传组件

上传组件允许用户通过点击拖放上传文支持文件验证、预览和进度跟踪
## 何时使用

- 上传单个多个文- 带预览图片上传
- 带裁剪头像上传
- 带验证文档上传
- 拖放文件上传

## 基本用法

```vue
<template>
  <el-upload
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    :on-preview="handlePreview"
    :on-remove="handleRemove"
    :before-remove="beforeRemove"
    multiple
    :limit="3"
    :on-exceed="handleExceed"
    :file-list="fileList"
  >
    <el-button type="primary">Click to upload</el-button>
    <template #tip>
      <div class="el-upload__tip">
        jpg/png files with a size less than 500KB
      </div>
    </template>
  </el-upload>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

const fileList = ref([])

const handlePreview = (file) => {
  console.log('Preview:', file)
}

const handleRemove = (file, fileList) => {
  console.log('Remove:', file, fileList)
}

const beforeRemove = (file, fileList) => {
  return ElMessageBox.confirm(
    `Cancel the transfer of ${file.name} ?`
  ).then(() => true, () => false)
}

const handleExceed = (files, fileList) => {
  ElMessage.warning(
    `The limit is 3, you selected ${files.length} files this time`
  )
}
</script>
```

## 用户头像上传

```vue
<template>
  <el-upload
    class="avatar-uploader"
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    :show-file-list="false"
    :on-success="handleAvatarSuccess"
    :before-upload="beforeAvatarUpload"
  >
    <img v-if="imageUrl" :src="imageUrl" class="avatar" />
    <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
  </el-upload>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage } from 'element-plus'
import { Plus } from '@element-plus/icons-vue'

const imageUrl = ref('')

const handleAvatarSuccess = (response, file) => {
  imageUrl.value = URL.createObjectURL(file.raw)
}

const beforeAvatarUpload = (rawFile) => {
  if (rawFile.type !== 'image/jpeg') {
    ElMessage.error('Avatar picture must be JPG format!')
    return false
  }
  if (rawFile.size / 1024 / 1024 > 2) {
    ElMessage.error('Avatar picture size can not exceed 2MB!')
    return false
  }
  return true
}
</script>

<style>
.avatar-uploader .avatar {
  width: 178px;
  height: 178px;
  display: block;
}
.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}
.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  text-align: center;
  line-height: 178px;
}
</style>
```

## 照片
```vue
<template>
  <el-upload
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    list-type="picture-card"
    :on-preview="handlePictureCardPreview"
    :on-remove="handleRemove"
  >
    <el-icon><Plus /></el-icon>
  </el-upload>
  
  <el-dialog v-model="dialogVisible">
    <img w-full :src="dialogImageUrl" alt="Preview Image" />
  </el-dialog>
</template>

<script setup>
import { ref } from 'vue'
import { Plus } from '@element-plus/icons-vue'

const dialogImageUrl = ref('')
const dialogVisible = ref(false)

const handlePictureCardPreview = (file) => {
  dialogImageUrl.value = file.url
  dialogVisible.value = true
}

const handleRemove = (file, fileList) => {
  console.log('Remove:', file, fileList)
}
</script>
```

## 自定义缩略图

```vue
<template>
  <el-upload
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    list-type="picture-card"
  >
    <template #file="{ file }">
      <div>
        <img class="el-upload-list__item-thumbnail" :src="file.url" alt="" />
        <span class="el-upload-list__item-actions">
          <span
            class="el-upload-list__item-preview"
            @click="handlePictureCardPreview(file)"
          >
            <el-icon><zoom-in /></el-icon>
          </span>
          <span
            class="el-upload-list__item-delete"
            @click="handleDownload(file)"
          >
            <el-icon><Download /></el-icon>
          </span>
          <span
            class="el-upload-list__item-delete"
            @click="handleRemove(file)"
          >
            <el-icon><Delete /></el-icon>
          </span>
        </span>
      </div>
    </template>
  </el-upload>
</template>

<script setup>
import { ZoomIn, Download, Delete } from '@element-plus/icons-vue'

const handlePictureCardPreview = (file) => {
  console.log('Preview:', file)
}

const handleDownload = (file) => {
  console.log('Download:', file)
}

const handleRemove = (file) => {
  console.log('Remove:', file)
}
</script>
```

## 拖放

```vue
<template>
  <el-upload
    class="upload-dragger"
    drag
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    multiple
  >
    <el-icon class="el-icon--upload"><upload-filled /></el-icon>
    <div class="el-upload__text">
      Drop file here or <em>click to upload</em>
    </div>
    <template #tip>
      <div class="el-upload__tip">
        jpg/png files with a size less than 500KB
      </div>
    </template>
  </el-upload>
</template>

<script setup>
import { UploadFilled } from '@element-plus/icons-vue'
</script>
```

## 手动上传

```vue
<template>
  <el-upload
    ref="uploadRef"
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
    :auto-upload="false"
  >
    <template #trigger>
      <el-button type="primary">select file</el-button>
    </template>
    <el-button class="ml-3" type="success" @click="submitUpload">
      upload to server
    </el-button>
    <template #tip>
      <div class="el-upload__tip">
        jpg/png files with a size less than 500KB
      </div>
    </template>
  </el-upload>
</template>

<script setup>
import { ref } from 'vue'

const uploadRef = ref()

const submitUpload = () => {
  uploadRef.value.submit()
}
</script>
```

## 目录上传

```vue
<template>
  <el-upload
    directory
    action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
  >
    <el-button type="primary">Upload Directory</el-button>
  </el-upload>
</template>
```

## 自定HTTP 请求

```vue
<template>
  <el-upload
    action="#"
    :http-request="customRequest"
    :on-success="handleSuccess"
    :on-error="handleError"
  >
    <el-button type="primary">Upload with custom request</el-button>
  </el-upload>
</template>

<script setup>
const customRequest = (options) => {
  const { action, file, data, headers, onProgress, onSuccess, onError } = options
  
  const formData = new FormData()
  formData.append('file', file)
  
  Object.keys(data || {}).forEach(key => {
    formData.append(key, data[key])
  })
  
  const xhr = new XMLHttpRequest()
  xhr.open('POST', action, true)
  
  Object.keys(headers || {}).forEach(key => {
    xhr.setRequestHeader(key, headers[key])
  })
  
  xhr.upload.onprogress = (e) => {
    if (e.lengthComputable) {
      onProgress({ percent: (e.loaded / e.total) * 100 })
    }
  }
  
  xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) {
      onSuccess(JSON.parse(xhr.response))
    } else {
      onError(new Error('Upload failed'))
    }
  }
  
  xhr.onerror = () => {
    onError(new Error('Network error'))
  }
  
  xhr.send(formData)
  
  return xhr
}

const handleSuccess = (response) => {
  console.log('Success:', response)
}

const handleError = (error) => {
  console.error('Error:', error)
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| action | 请求 URL(必需) | `string` | `#` |
| headers | 请求| `Headers \| Record<string, any>` | |
| method | 上传请求方法 | `string` | `'post'` |
| multiple | 允许多个文件 | `boolean` | `false` |
| data | 额外请求数| `Record<string, any> \| (rawFile) => Awaitable<Record>` | `{}` |
| name | 上传文件键| `string` | `'file'` |
| with-credentials | 发cookies | `boolean` | `false` |
| show-file-list | 显示已上传文件列| `boolean` | `true` |
| drag | 启用拖放 | `boolean` | `false` |
| accept | 接受文件类型| `string` | `''` |
| crossorigin | 原生 crossorigin 属性| `'' \| 'anonymous' \| 'use-credentials'` | |
| on-preview | 点击已上传文件时钩| `(uploadFile: UploadFile) => void` | |
| on-remove | 文件移除时钩子 | `(uploadFile, uploadFiles) => void` | |
| on-success | 上传成功时钩子 | `(response, uploadFile, uploadFiles) => void` | |
| on-error | 上传错误时钩子 | `(error, uploadFile, uploadFiles) => void` | |
| on-progress | 上传进度时钩子 | `(evt, uploadFile, uploadFiles) => void` | |
| on-change | 文件选择/更改时钩子 | `(uploadFile, uploadFiles) => void` | |
| on-exceed | 超过限制时钩子 | `(files, uploadFiles) => void` | |
| before-upload | 上传前钩子 | `(rawFile) => Awaitable<void \| boolean \| File \| Blob>` | |
| before-remove | 移除前钩子 | `(uploadFile, uploadFiles) => Awaitable<boolean>` | |
| file-list / v-model:file-list | 已上传文件 | `UploadUserFile[]` | `[]` |
| list-type | 文件列表类型 | `'text' \| 'picture' \| 'picture-card'` | `'text'` |
| auto-upload | 选择时自动上| `boolean` | `true` |
| http-request | 覆盖默认 xhr 行为 | `(options) => XMLHttpRequest \| Promise` | |
| disabled | 禁用上传 | `boolean` | `false` |
| limit | 允许最大上传数 | `number` | |
| directory | 支持目录上传 | `boolean` | `false` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义默认内容| |
| trigger | 触发文件对话框内容 | |
| tip | 提示内容| |
| file | 自定义缩略图模板 | `{ file: UploadFile, index: number }` |

### 暴露(方法)

| Name | Description | Type |
|------|-------------|------|
| abort | 取消上传请求 | `(file?: UploadFile) => void` |
| submit | 手动上传文件列表 | `() => void` |
| clearFiles | 清除文件列表 | `(status?: UploadStatus[]) => void` |
| handleStart | 手动选择文件 | `(rawFile: UploadRawFile) => void` |
| handleRemove | 手动移除文件 | `(file, rawFile?) => void` |

### UploadFile 类型

```ts
interface UploadFile {
  name: string
  percentage?: number
  status: 'ready' | 'uploading' | 'success' | 'fail'
  size?: number
  response?: unknown
  uid: number
  url?: string
  raw?: UploadRawFile
}
```

## 常见模式

### 文件验证

```vue
<template>
  <el-upload
    action="/api/upload"
    :before-upload="validateFile"
    :on-error="handleError"
  >
    <el-button type="primary">Upload</el-button>
  </el-upload>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const validateFile = (file) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'application/pdf']
  const maxSize = 5 * 1024 * 1024
  
  if (!allowedTypes.includes(file.type)) {
    ElMessage.error('Invalid file type!')
    return false
  }
  
  if (file.size > maxSize) {
    ElMessage.error('File size exceeds 5MB!')
    return false
  }
  
  return true
}

const handleError = (error) => {
  ElMessage.error('Upload failed: ' + error.message)
}
</script>
```

### 进度跟踪

```vue
<template>
  <el-upload
    action="/api/upload"
    :on-progress="handleProgress"
    :on-success="handleSuccess"
  >
    <el-button type="primary">Upload</el-button>
  </el-upload>
  
  <el-progress
    v-if="uploading"
    :percentage="progress"
    :status="uploadStatus"
  />
</template>

<script setup>
import { ref } from 'vue'

const uploading = ref(false)
const progress = ref(0)
const uploadStatus = ref('')

const handleProgress = (event) => {
  uploading.value = true
  progress.value = Math.round(event.percent)
}

const handleSuccess = () => {
  uploadStatus.value = 'success'
  setTimeout(() => {
    uploading.value = false
    progress.value = 0
    uploadStatus.value = ''
  }, 1000)
}
</script>
```

### 替换之前文
```vue
<template>
  <el-upload
    action="/api/upload"
    :limit="1"
    :on-exceed="handleExceed"
  >
    <el-button type="primary">Upload (replaces previous)</el-button>
  </el-upload>
</template>

<script setup>
import { ref } from 'vue'

const fileList = ref([])

const handleExceed = (files) => {
  fileList.value = []
  fileList.value.push(files[0])
}
</script>
```

## 组件交互

- **Form** 配合进行基于表单文件上- **Dialog** 组合用于上传预览模态框
- **Progress** 配合显示上传进度
- **Image** 集成用于图片预览

## 最佳实践
1. 始终`before-upload` 中验证文件类型和大小
2. 使用 `http-request` 进行自定义上传实现3. 优雅地处理错误并提供用户反馈
4. 使用 `limit` `on-exceed` 进行文件数量限制
5. 考虑 `auto-upload="false"` 以进行手动上传控6. 使用 `list-type="picture-card"` 用于图片画廊
