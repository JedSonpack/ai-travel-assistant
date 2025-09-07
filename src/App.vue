<template>
  <div id="app">
    <div class="chat-container">
      <!-- 左侧导航栏 -->
      <aside class="sidebar">
        <div class="sidebar-header">
          <button class="new-chat-btn" @click="createNewChat">
            <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
            </svg>
            新建对话
          </button>
        </div>
        
        <div class="chat-history">
          <div class="history-title">历史对话</div>
          <div 
            v-for="session in chatSessions" 
            :key="session.id"
            class="chat-session"
            :class="{ active: session.id === currentSessionId }"
            @click="switchSession(session.id)"
          >
            <svg class="session-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z" />
            </svg>
            <div class="session-info">
              <div class="session-title">{{ session.title }}</div>
              <div class="session-preview">{{ session.lastMessage }}</div>
            </div>
          </div>
        </div>
      </aside>

      <!-- 主聊天区域 -->
      <main class="chat-main">
        <div class="chat-header">
          <h1 class="app-title">
            <svg class="title-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2 2 2 0 012 2v2.945M8 3.935V5.5A2.5 2.5 0 0010.5 8h.5a2 2 0 012 2 2 2 0 104 0 2 2 0 012-2h1.064M15 20.488V18a2 2 0 012-2h3.064M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            AI Travel Assistant
          </h1>
          <div class="header-subtitle">您的智能旅行规划助手</div>
        </div>

        <div class="chat-messages" ref="messagesContainer">
          <div v-if="!currentMessages.length" class="welcome-message">
            <div class="welcome-icon">🗺️</div>
            <h2>欢迎使用 AI Travel Assistant</h2>
            <p>我可以帮您：</p>
            <div class="feature-grid">
              <div class="feature-card">
                <span class="feature-icon">📍</span>
                <span>规划旅行路线</span>
              </div>
              <div class="feature-card">
                <span class="feature-icon">🏨</span>
                <span>推荐住宿餐饮</span>
              </div>
              <div class="feature-card">
                <span class="feature-icon">🎫</span>
                <span>景点门票信息</span>
              </div>
              <div class="feature-card">
                <span class="feature-icon">🚗</span>
                <span>交通出行建议</span>
              </div>
            </div>
          </div>

          <div v-for="message in currentMessages" :key="message.id" class="message-wrapper">
            <div class="message" :class="message.role">
              <div class="message-avatar">
                <span v-if="message.role === 'user'">👤</span>
                <span v-else>🤖</span>
              </div>
              <div class="message-content">
                <div class="message-text">{{ message.content }}</div>
                <div class="message-time">{{ formatTime(message.timestamp) }}</div>
              </div>
            </div>
          </div>

          <div v-if="isLoading" class="message-wrapper">
            <div class="message assistant">
              <div class="message-avatar">🤖</div>
              <div class="message-content">
                <div class="typing-indicator">
                  <span></span>
                  <span></span>
                  <span></span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 输入区域 -->
        <div class="chat-input-container">
          <div class="input-wrapper">
            <textarea 
              v-model="inputMessage"
              @keydown.enter.prevent="handleEnter"
              placeholder="输入您的旅行问题..."
              class="chat-input"
              rows="1"
              ref="inputField"
            ></textarea>
            <button 
              @click="sendMessage" 
              :disabled="!inputMessage.trim() || isLoading"
              class="send-button"
            >
              <svg class="send-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                  d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" />
              </svg>
            </button>
          </div>
          <div class="input-hint">按 Enter 发送，Shift + Enter 换行</div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import { ref, computed, nextTick, onMounted } from 'vue'
import { mockChatSessions, mockAIResponses, generateMockResponse } from './utils/mockData'

export default {
  name: 'App',
  setup() {
    const chatSessions = ref(mockChatSessions)
    const currentSessionId = ref(1)
    const inputMessage = ref('')
    const isLoading = ref(false)
    const messagesContainer = ref(null)
    const inputField = ref(null)

    const currentMessages = computed(() => {
      const session = chatSessions.value.find(s => s.id === currentSessionId.value)
      return session ? session.messages : []
    })

    const formatTime = (timestamp) => {
      const date = new Date(timestamp)
      const hours = date.getHours().toString().padStart(2, '0')
      const minutes = date.getMinutes().toString().padStart(2, '0')
      return `${hours}:${minutes}`
    }

    const scrollToBottom = () => {
      nextTick(() => {
        if (messagesContainer.value) {
          messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
        }
      })
    }

    const createNewChat = () => {
      const newId = Math.max(...chatSessions.value.map(s => s.id)) + 1
      const newSession = {
        id: newId,
        title: `新对话 ${newId}`,
        lastMessage: '开始新的对话...',
        messages: [],
        timestamp: Date.now()
      }
      chatSessions.value.unshift(newSession)
      currentSessionId.value = newId
    }

    const switchSession = (sessionId) => {
      currentSessionId.value = sessionId
      scrollToBottom()
    }

    const handleEnter = (event) => {
      if (!event.shiftKey) {
        sendMessage()
      }
    }

    const sendMessage = async () => {
      if (!inputMessage.value.trim() || isLoading.value) return

      const session = chatSessions.value.find(s => s.id === currentSessionId.value)
      if (!session) return

      // 添加用户消息
      const userMessage = {
        id: Date.now(),
        role: 'user',
        content: inputMessage.value,
        timestamp: Date.now()
      }
      session.messages.push(userMessage)
      session.lastMessage = inputMessage.value

      // 如果是新对话，更新标题
      if (session.messages.length === 1) {
        session.title = inputMessage.value.slice(0, 20) + (inputMessage.value.length > 20 ? '...' : '')
      }

      const messageText = inputMessage.value
      inputMessage.value = ''
      scrollToBottom()

      // 模拟AI响应
      isLoading.value = true
      
      // 模拟网络延迟
      await new Promise(resolve => setTimeout(resolve, 1000 + Math.random() * 1500))

      // 生成AI响应
      const aiResponse = generateMockResponse(messageText)
      const assistantMessage = {
        id: Date.now() + 1,
        role: 'assistant',
        content: aiResponse,
        timestamp: Date.now()
      }
      
      session.messages.push(assistantMessage)
      session.lastMessage = aiResponse.slice(0, 50) + '...'
      isLoading.value = false
      scrollToBottom()
    }

    onMounted(() => {
      if (inputField.value) {
        inputField.value.focus()
      }
    })

    return {
      chatSessions,
      currentSessionId,
      currentMessages,
      inputMessage,
      isLoading,
      messagesContainer,
      inputField,
      formatTime,
      createNewChat,
      switchSession,
      handleEnter,
      sendMessage
    }
  }
}
</script>

<style scoped>
#app {
  height: 100vh;
  overflow: hidden;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.chat-container {
  display: flex;
  height: 100%;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
}

/* 侧边栏样式 */
.sidebar {
  width: 280px;
  background: #f7f8fa;
  border-right: 1px solid #e5e7eb;
  display: flex;
  flex-direction: column;
}

.sidebar-header {
  padding: 20px;
  border-bottom: 1px solid #e5e7eb;
}

.new-chat-btn {
  width: 100%;
  padding: 12px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s ease;
}

.new-chat-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

.icon {
  width: 20px;
  height: 20px;
}

.chat-history {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
}

.history-title {
  font-size: 12px;
  color: #6b7280;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 15px;
}

.chat-session {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 12px;
  margin-bottom: 8px;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.chat-session:hover {
  background: #e5e7eb;
}

.chat-session.active {
  background: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.session-icon {
  width: 20px;
  height: 20px;
  color: #6b7280;
  flex-shrink: 0;
  margin-top: 2px;
}

.session-info {
  flex: 1;
  min-width: 0;
}

.session-title {
  font-size: 14px;
  font-weight: 500;
  color: #1f2937;
  margin-bottom: 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.session-preview {
  font-size: 12px;
  color: #6b7280;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 主聊天区域 */
.chat-main {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: white;
}

.chat-header {
  padding: 20px 30px;
  border-bottom: 1px solid #e5e7eb;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.05) 0%, rgba(118, 75, 162, 0.05) 100%);
}

.app-title {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 24px;
  font-weight: 700;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin: 0;
}

.title-icon {
  width: 28px;
  height: 28px;
  stroke: #667eea;
}

.header-subtitle {
  font-size: 14px;
  color: #6b7280;
  margin-top: 5px;
  margin-left: 38px;
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 30px;
  scroll-behavior: smooth;
}

/* 欢迎消息 */
.welcome-message {
  text-align: center;
  padding: 60px 20px;
}

.welcome-icon {
  font-size: 64px;
  margin-bottom: 20px;
}

.welcome-message h2 {
  font-size: 28px;
  color: #1f2937;
  margin-bottom: 10px;
}

.welcome-message p {
  color: #6b7280;
  margin-bottom: 30px;
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
  max-width: 600px;
  margin: 0 auto;
}

.feature-card {
  padding: 20px;
  background: #f7f8fa;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  transition: all 0.3s ease;
}

.feature-card:hover {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  transform: translateY(-2px);
}

.feature-icon {
  font-size: 32px;
}

.feature-card span:last-child {
  font-size: 14px;
  color: #4b5563;
  font-weight: 500;
}

/* 消息样式 */
.message-wrapper {
  margin-bottom: 20px;
}

.message {
  display: flex;
  gap: 12px;
  max-width: 70%;
}

.message.user {
  margin-left: auto;
  flex-direction: row-reverse;
}

.message-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  flex-shrink: 0;
}

.message.user .message-avatar {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.message.assistant .message-avatar {
  background: #f3f4f6;
}

.message-content {
  flex: 1;
}

.message-text {
  padding: 12px 16px;
  border-radius: 12px;
  font-size: 14px;
  line-height: 1.6;
  word-wrap: break-word;
}

.message.user .message-text {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-bottom-right-radius: 4px;
}

.message.assistant .message-text {
  background: #f3f4f6;
  color: #1f2937;
  border-bottom-left-radius: 4px;
}

.message-time {
  font-size: 11px;
  color: #9ca3af;
  margin-top: 4px;
  padding: 0 4px;
}

.message.user .message-time {
  text-align: right;
}

/* 打字动画 */
.typing-indicator {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 12px 16px;
}

.typing-indicator span {
  width: 8px;
  height: 8px;
  background: #9ca3af;
  border-radius: 50%;
  animation: typing 1.4s infinite;
}

.typing-indicator span:nth-child(2) {
  animation-delay: 0.2s;
}

.typing-indicator span:nth-child(3) {
  animation-delay: 0.4s;
}

@keyframes typing {
  0%, 60%, 100% {
    transform: translateY(0);
    opacity: 0.5;
  }
  30% {
    transform: translateY(-10px);
    opacity: 1;
  }
}

/* 输入区域 */
.chat-input-container {
  padding: 20px 30px;
  border-top: 1px solid #e5e7eb;
  background: #fafbfc;
}

.input-wrapper {
  display: flex;
  gap: 12px;
  align-items: flex-end;
}

.chat-input {
  flex: 1;
  padding: 12px 16px;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  font-size: 14px;
  resize: none;
  outline: none;
  transition: all 0.3s ease;
  font-family: inherit;
  line-height: 1.5;
  max-height: 120px;
}

.chat-input:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.send-button {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.send-button:hover:not(:disabled) {
  transform: scale(1.05);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

.send-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.send-icon {
  width: 20px;
  height: 20px;
  transform: rotate(45deg);
}

.input-hint {
  font-size: 12px;
  color: #9ca3af;
  margin-top: 8px;
  text-align: center;
}

/* 滚动条样式 */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
}

::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}
</style>
