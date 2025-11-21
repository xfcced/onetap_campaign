<script setup lang="ts">
import { ref, computed } from 'vue'

// 一次性生成并发送 - 极简设计
const GENERATE_AND_SEND_URL = 'https://lhjlhjlhj.app.n8n.cloud/webhook/marketing-generate-send'
// const USE_MOCK = true
const USE_MOCK = false

const briefText = ref('')
const uploadedFile = ref<File | null>(null)
const emailAddress = ref('')
const loading = ref(false)
const errorMessage = ref('')
const showSuccess = ref(false)

const canSubmit = computed(() => {
  // 需要邮箱 + (简介文本 OR 文件)
  return emailAddress.value.trim() !== '' &&
    (briefText.value.trim() !== '' || uploadedFile.value !== null) &&
    !loading.value
})

const handleFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  if (target.files && target.files.length > 0) {
    uploadedFile.value = target.files[0]
  }
}

const removeFile = () => {
  uploadedFile.value = null
}

const closeSuccess = () => {
  showSuccess.value = false
}

const submitRequest = async () => {
  if (!canSubmit.value) {
    return
  }

  loading.value = true
  errorMessage.value = ''
  showSuccess.value = false

  try {
    if (USE_MOCK) {
      // Mock API call with delay
      await new Promise(resolve => setTimeout(resolve, 2000))

      // 显示成功消息
      showSuccess.value = true

      // 重置表单
      briefText.value = ''
      uploadedFile.value = null
      emailAddress.value = ''
    } else {
      const formData = new FormData()

      formData.append('email', emailAddress.value.trim())

      if (briefText.value.trim()) {
        formData.append('brief', briefText.value.trim())
      }

      if (uploadedFile.value) {
        formData.append('file', uploadedFile.value)
      }

      const response = await fetch(GENERATE_AND_SEND_URL, {
        method: 'POST',
        body: formData,
      })

      // 只用 200 状态码判断成功，不关心返回内容
      if (!response.ok) {
        throw new Error(`Request failed with status ${response.status}`)
      }

      // 成功！显示消息并重置表单
      showSuccess.value = true

      // 重置表单
      briefText.value = ''
      uploadedFile.value = null
      emailAddress.value = ''
    }
  } catch (error) {
    errorMessage.value = error instanceof Error ? error.message : 'Failed to process request'
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="marketing-tool">
    <div class="container">
      <!-- Logo / Title -->
      <div class="header">
        <div class="icon">✨</div>
        <h1 class="title">AI Marketing Email Generator</h1>
      </div>

      <!-- Success Message -->
      <div v-if="showSuccess" class="success-message">
        <div class="success-icon">✅</div>
        <h3>Request Submitted Successfully!</h3>
        <p>Please check your email inbox in 2-3 minutes.</p>
        <p class="thank-you">Thank you for using our service!</p>
        <button @click="closeSuccess" class="close-success-btn">
          Submit Another Request
        </button>
      </div>

      <!-- Form -->
      <div v-else class="form-container">
        <!-- Error Message -->
        <div v-if="errorMessage" class="error-banner">
          ⚠️ {{ errorMessage }}
        </div>

        <!-- Email Input -->
        <div class="form-group">
          <label class="form-label">
            <span class="label-text">Email Address</span>
            <span class="required">*</span>
          </label>
          <input v-model="emailAddress" type="email" class="form-input" placeholder="your@email.com"
            :disabled="loading" />
        </div>

        <!-- Brief Text Input -->
        <div class="form-group">
          <label class="form-label">
            <span class="label-text">Campaign Brief</span>
            <span class="optional">(Optional if file provided)</span>
          </label>
          <textarea v-model="briefText" class="form-textarea" placeholder="Describe your marketing campaign..." rows="6"
            :disabled="loading"></textarea>
        </div>

        <!-- File Upload -->
        <div class="form-group">
          <label class="form-label">
            <span class="label-text">Upload Document</span>
            <span class="optional">(Optional if brief provided)</span>
          </label>

          <div v-if="uploadedFile" class="file-preview">
            <div class="file-info">
              <span class="file-icon">📄</span>
              <span class="file-name">{{ uploadedFile.name }}</span>
            </div>
            <button @click="removeFile" class="remove-btn" :disabled="loading">
              ✕
            </button>
          </div>

          <label v-else class="file-upload-label" :class="{ disabled: loading }">
            <input type="file" accept=".pdf,.doc,.docx,.txt" @change="handleFileSelect" :disabled="loading"
              style="display: none;" />
            <span class="upload-icon">📎</span>
            <span class="upload-text">Click to upload file</span>
          </label>
        </div>

        <!-- Examples Link -->
        <p class="examples-link">
          <a href="/examples" target="_blank">View Examples</a>
        </p>

        <!-- Submit Button -->
        <button @click="submitRequest" class="submit-btn" :disabled="!canSubmit">
          <span v-if="loading" class="loading-spinner">⏳</span>
          <span v-else>Generate & Send Emails</span>
        </button>

        <!-- Helper Text -->
        <p class="helper-text">
          You must provide an email address and either a campaign brief or a document file.
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.marketing-tool {
  width: 100%;
  min-height: 100vh;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 20px;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}

.container {
  max-width: 600px;
  width: 100%;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 24px;
  padding: 30px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(20px);
}

/* Header */
.header {
  text-align: center;
  margin-bottom: 15px;
}

.icon {
  font-size: 2.5rem;
  margin-bottom: 12px;
  animation: float 3s ease-in-out infinite;
}

@keyframes float {

  0%,
  100% {
    transform: translateY(0px);
  }

  50% {
    transform: translateY(-10px);
  }
}

.title {
  font-size: 1.5rem;
  color: #1a202c;
  margin-bottom: 0;
  font-weight: 700;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* Success Message */
.success-message {
  text-align: center;
  padding: 40px 20px;
  animation: fadeIn 0.5s ease-in;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.9);
  }

  to {
    opacity: 1;
    transform: scale(1);
  }
}

.success-icon {
  font-size: 5rem;
  margin-bottom: 20px;
  animation: bounce 0.6s ease-in-out;
}

@keyframes bounce {

  0%,
  100% {
    transform: translateY(0);
  }

  50% {
    transform: translateY(-20px);
  }
}

.success-message h3 {
  font-size: 1.5rem;
  color: #10b981;
  margin-bottom: 15px;
  font-weight: 700;
}

.success-message p {
  font-size: 1.1rem;
  color: #475569;
  margin: 10px 0;
}

.thank-you {
  margin-top: 20px;
  font-weight: 600;
  color: #667eea;
}

.close-success-btn {
  margin-top: 30px;
  padding: 14px 32px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 1rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s;
}

.close-success-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
}

/* Form */
.form-container {
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.error-banner {
  margin-bottom: 20px;
  padding: 15px;
  background: rgba(239, 68, 68, 0.1);
  border: 2px solid #ef4444;
  border-radius: 12px;
  color: #dc2626;
  font-size: 0.95rem;
  font-weight: 500;
  text-align: center;
}

.form-group {
  margin-bottom: 18px;
}

.form-label {
  display: flex;
  align-items: baseline;
  gap: 8px;
  margin-bottom: 10px;
  font-weight: 600;
  color: #1a202c;
}

.label-text {
  font-size: 1rem;
}

.required {
  color: #ef4444;
  font-size: 1.2rem;
}

.optional {
  font-size: 0.85rem;
  color: #94a3b8;
  font-weight: 400;
}

.form-input,
.form-textarea {
  width: 100%;
  padding: 14px 18px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  font-size: 1rem;
  font-family: inherit;
  color: #1a202c;
  background: white;
  transition: all 0.3s;
  box-sizing: border-box;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.1);
}

.form-input:disabled,
.form-textarea:disabled {
  background: #f1f5f9;
  cursor: not-allowed;
  opacity: 0.6;
}

.form-textarea {
  resize: vertical;
  min-height: 120px;
}

/* File Upload */
.file-preview {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 14px 18px;
  background: #f8fafc;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  gap: 10px;
}

.file-info {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
  min-width: 0;
}

.file-icon {
  font-size: 1.5rem;
  flex-shrink: 0;
}

.file-name {
  flex: 1;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  color: #1a202c;
  font-weight: 500;
}

.remove-btn {
  padding: 6px 12px;
  background: #ef4444;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.2s;
  flex-shrink: 0;
}

.remove-btn:hover:not(:disabled) {
  background: #dc2626;
  transform: scale(1.05);
}

.remove-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.file-upload-label {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 20px;
  border: 2px dashed #cbd5e1;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s;
  background: #f8fafc;
}

.file-upload-label:hover:not(.disabled) {
  border-color: #667eea;
  background: rgba(102, 126, 234, 0.05);
}

.file-upload-label.disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.upload-icon {
  font-size: 1.5rem;
}

.upload-text {
  font-size: 1rem;
  color: #64748b;
  font-weight: 500;
}

/* Examples Link */
.examples-link {
  text-align: center;
  margin-top: 15px;
  margin-bottom: 5px;
  font-size: 0.9rem;
}

.examples-link a {
  color: #667eea;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s;
}

.examples-link a:hover {
  color: #764ba2;
  text-decoration: underline;
}

/* Submit Button */
.submit-btn {
  width: 100%;
  padding: 16px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 1.1rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: 10px;
}

.submit-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
}

.submit-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.loading-spinner {
  display: inline-block;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}

.helper-text {
  margin-top: 20px;
  text-align: center;
  font-size: 0.85rem;
  color: #94a3b8;
  line-height: 1.5;
}

/* Mobile Responsive */
@media (max-width: 640px) {
  .container {
    padding: 30px 20px;
  }

  .title {
    font-size: 1.5rem;
  }

  .subtitle {
    font-size: 0.9rem;
  }

  .icon {
    font-size: 3rem;
  }

  .success-icon {
    font-size: 4rem;
  }
}
</style>
