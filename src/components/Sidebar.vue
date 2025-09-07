<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <button class="new-chat-btn" @click="$emit('create-new-chat')">
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
        @click="$emit('switch-session', session.id)"
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
</template>

<script setup>
// Props定义
defineProps({
  chatSessions: {
    type: Array,
    required: true
  },
  currentSessionId: {
    type: Number,
    required: true
  }
})

// Events定义
defineEmits(['create-new-chat', 'switch-session'])
</script>

<style scoped>
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
</style>
