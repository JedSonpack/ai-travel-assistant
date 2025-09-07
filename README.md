# 🗺️ AI Travel Assistant - 智能旅游助手

<div align="center">
  <img src="https://img.shields.io/badge/Vue.js-3.x-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white" alt="Vue.js">
  <img src="https://img.shields.io/badge/Vite-5.x-646CFF?style=for-the-badge&logo=vite&logoColor=white" alt="Vite">
  <img src="https://img.shields.io/badge/Node.js-20.x-339933?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="License">
</div>

<div align="center">
  <h3>一个基于 Vue 3 + Vite 构建的智能旅游聊天助手应用</h3>
  <p>通过自然语言交互，为用户提供个性化的旅行规划建议</p>
</div>

## ✨ 特性

- 🎨 **现代化UI设计** - 采用渐变色主题，界面美观大方
- 💬 **智能对话系统** - 类似ChatGPT的聊天交互体验
- 📱 **响应式布局** - 完美适配各种屏幕尺寸
- 🚀 **高性能** - 基于Vite构建，启动速度快，热更新即时
- 🧩 **组件化架构** - 清晰的组件划分，易于维护和扩展
- 🎯 **Vue 3 Composition API** - 使用最新的 `<script setup>` 语法
- 🔄 **Mock数据系统** - 智能的模拟响应，无需后端即可体验

## 🖼️ 界面预览

### 主界面
- **左侧导航栏**：管理历史对话，快速切换会话
- **主聊天窗口**：清晰的消息展示，支持用户和AI助手的对话
- **底部输入框**：便捷的消息输入，支持快捷键发送

### 功能展示
- ✅ 新建对话
- ✅ 历史会话管理
- ✅ 智能路线规划
- ✅ 景点推荐
- ✅ 交通建议
- ✅ 住宿推荐
- ✅ 美食攻略
- ✅ 天气查询

## 🚀 快速开始

### 环境要求

- Node.js >= 20.18.0
- npm >= 10.8.0

### 安装步骤

1. **克隆项目**
```bash
git clone https://github.com/JedSonpack/ai-travel-assistant.git
cd ai-travel-assistant
```

2. **安装依赖**
```bash
npm install
```

3. **启动开发服务器**
```bash
npm run dev
```

4. **访问应用**
```
打开浏览器访问 http://localhost:5173
```

### 构建生产版本

```bash
npm run build
```

构建后的文件将生成在 `dist` 目录中。

### 预览生产版本

```bash
npm run preview
```

## 📁 项目结构

```
ai-travel-assistant/
├── public/                 # 静态资源
├── src/                   # 源代码
│   ├── assets/           # 图片等资源文件
│   ├── components/       # Vue组件
│   │   ├── Sidebar.vue  # 侧边栏组件
│   │   └── ChatMain.vue # 主聊天组件
│   ├── utils/           # 工具函数
│   │   └── mockData.js  # Mock数据和响应逻辑
│   ├── App.vue          # 根组件
│   ├── main.js          # 应用入口
│   └── style.css        # 全局样式
├── index.html            # HTML模板
├── vite.config.js        # Vite配置
├── package.json          # 项目配置
└── README.md            # 项目说明
```

## 🛠️ 技术栈

### 前端框架
- **Vue 3** - 渐进式JavaScript框架
- **Composition API** - 使用 `<script setup>` 语法
- **响应式系统** - 基于Proxy的响应式数据

### 构建工具
- **Vite** - 下一代前端构建工具
- **ESBuild** - 极速的JavaScript打包器

### 开发语言
- **JavaScript (ES6+)** - 现代JavaScript语法
- **HTML5** - 语义化标签
- **CSS3** - 现代CSS特性，包括Grid、Flexbox、动画等

## 💡 核心功能

### 1. 智能对话系统
- 基于关键词的智能响应匹配
- 多种场景的预设回复模板
- 模拟真实的网络延迟和打字效果

### 2. 会话管理
- 创建新对话
- 切换历史会话
- 自动保存对话内容
- 会话标题自动生成

### 3. Mock数据系统
```javascript
// 支持的查询类型
- 路线规划 (route)
- 交通方式 (transport)
- 住宿推荐 (accommodation)
- 景点推荐 (attraction)
- 美食推荐 (food)
- 旅行贴士 (tips)
- 天气查询 (weather)
```

### 4. UI/UX特性
- 流畅的动画效果
- 打字机效果
- 消息时间戳
- 自动滚动到最新消息
- 响应式设计

## 🔧 配置说明

### Vite配置
```javascript
// vite.config.js
export default {
  plugins: [vue()],
  server: {
    port: 5173,
    host: true
  }
}
```

### 自定义主题
主题颜色定义在CSS变量中，可以轻松修改：
```css
/* 主题色 */
--primary-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
--bg-color: #f7f8fa;
--text-color: #1f2937;
```

## 📝 开发指南

### 组件开发
所有组件使用Vue 3 Composition API的 `<script setup>` 语法：

```vue
<script setup>
import { ref, computed } from 'vue'

// Props定义
const props = defineProps({
  messages: Array
})

// Emits定义
const emit = defineEmits(['send-message'])

// 响应式数据
const inputMessage = ref('')
</script>
```

### 添加新的Mock响应
在 `src/utils/mockData.js` 中添加新的响应模板：

```javascript
const aiResponseTemplates = {
  yourCategory: [
    "响应模板1",
    "响应模板2"
  ]
}
```

### 状态管理
当前使用组件内部状态管理，如需扩展可集成Pinia：

```bash
npm install pinia
```

## 🚦 后续规划

- [ ] 集成真实AI API（OpenAI/文心一言等）
- [ ] 接入高德地图API实现路径可视化
- [ ] 添加用户认证系统
- [ ] 实现数据持久化存储
- [ ] 添加语音输入/输出功能
- [ ] 支持图片和文件上传
- [ ] 添加多语言支持
- [ ] 实现主题切换（深色模式）
- [ ] 添加TypeScript支持
- [ ] 单元测试和E2E测试

## 🤝 贡献指南

欢迎提交Issue和Pull Request！

1. Fork本仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个Pull Request

## 📄 开源协议

本项目采用 MIT 协议 - 查看 [LICENSE](LICENSE) 文件了解详情

## 👨‍💻 作者

- GitHub: [@JedSonpack](https://github.com/JedSonpack)

## 🙏 致谢

- [Vue.js](https://vuejs.org/) - 渐进式JavaScript框架
- [Vite](https://vitejs.dev/) - 下一代前端构建工具
- [高德地图API](https://lbs.amap.com/) - 地图服务提供商（计划集成）

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 [Issue](https://github.com/JedSonpack/ai-travel-assistant/issues)
- 发送邮件至：your.email@example.com

---

<div align="center">
  <p>如果这个项目对你有帮助，请给一个 ⭐️ Star！</p>
  <p>Made with ❤️ by AI Travel Assistant Team</p>
</div>
