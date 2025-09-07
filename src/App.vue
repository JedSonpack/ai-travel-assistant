<template>
  <div id="app">
    <div class="chat-container">
      <!-- 左侧导航栏组件 -->
      <Sidebar 
        :chat-sessions="chatSessions"
        :current-session-id="currentSessionId"
        @create-new-chat="createNewChat"
        @switch-session="switchSession"
      />

      <!-- 主聊天区域组件 -->
      <ChatMain 
        :messages="currentMessages"
        :is-loading="isLoading"
        @send-message="handleSendMessage"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import Sidebar from './components/Sidebar.vue'
import ChatMain from './components/ChatMain.vue'
import { mockChatSessions, generateMockResponse } from './utils/mockData'

// 状态管理
const chatSessions = ref(mockChatSessions)
const currentSessionId = ref(1)
const isLoading = ref(false)

// 计算属性：当前会话的消息
const currentMessages = computed(() => {
  const session = chatSessions.value.find(s => s.id === currentSessionId.value)
  return session ? session.messages : []
})

// 创建新对话
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

// 切换会话
const switchSession = (sessionId) => {
  currentSessionId.value = sessionId
}

// 处理发送消息
const handleSendMessage = async (messageText) => {
  const session = chatSessions.value.find(s => s.id === currentSessionId.value)
  if (!session) return

  // 添加用户消息
  const userMessage = {
    id: Date.now(),
    role: 'user',
    content: messageText,
    timestamp: Date.now()
  }
  session.messages.push(userMessage)
  session.lastMessage = messageText

  // 如果是新对话，更新标题
  if (session.messages.length === 1) {
    session.title = messageText.slice(0, 20) + (messageText.length > 20 ? '...' : '')
  }

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

/* 响应式设计 */
@media (max-width: 768px) {
  .chat-container {
    flex-direction: column;
  }
}
</style>
