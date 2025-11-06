<template>
  <view class="page">
    <!-- 自定义导航栏 -->
    <view class="navbar" :style="{ paddingTop: statusBarHeight + 'px', height: navTotalHeight + 'px'}">
      <view class="nav-row" :style="{ height: navContentHeight + 'px' }">
        <view class="nav-left" @tap="toggleHistory">☰</view>
        <view class="nav-title">智能助手</view>
        <view class="nav-right"></view>
      </view>
    </view>
    <view class="content-panel">
      <view v-if="messages.length === 0" class="welcome">
        <view class="intro">
          <text class="intro-title">我是智能助手，很高兴见到你！</text>
          <text class="intro-sub">我可以帮你写代码、读文件、写作各种创意内容，请把你的任务交给我吧~</text>
        </view>
      </view>
      <scroll-view v-else class="message-list" scroll-y="true">
        <view
          v-for="(m, i) in messages"
          :key="i"
          :class="['message-item', m.role === 'user' ? 'right' : 'left']"
        >
          <view v-if="m.role === 'user'" class="message-bubble user">{{ m.content }}</view>
          <text v-else class="message-text">{{ m.content }}</text>
        </view>
      </scroll-view>
    </view>

    <view class="input-bar">
      <view class="input-row">
        <textarea
          class="input textarea"
          v-model="inputValue"
          :disabled="isSending"
          placeholder="给 智能助手 发送消息"
          auto-height
          cursor-spacing="8"
          maxlength="-1"
          :show-confirm-bar="false"
        />
        <view class="send-btn" :class="{ disabled: isSending || !(inputValue || '').trim().length }" @tap="sendMessage">
          <image v-if="!isSending" class="send-icon-svg" src="/static/arrow.svg" mode="widthFix" />
          <view v-else class="spinner"></view>
        </view>
      </view>
      <view class="footer-note">内容由 AI 生成，仅供参考</view>
    </view>

    <!-- 左侧滑出对话历史抽屉 -->
    <view v-if="showHistory" class="drawer-mask" @tap="toggleHistory"></view>
    <view :class="['drawer', { 'drawer-open': showHistory }]">
      <view class="drawer-header">对话历史</view>
      <scroll-view class="drawer-list" scroll-y="true">
        <view v-for="(c, idx) in conversations" :key="idx" class="drawer-item">{{ c.title }}</view>
      </scroll-view>
    </view>
  </view>
  
</template>

<script>
export default {
  data() {
    return {
      inputValue: '',
      isSending: false,
      showHistory: false,
      statusBarHeight: 0,
      navContentHeight: 40,
      navTotalHeight: 60,
      messages: [],
      conversations: [
        { title: '对话 1' },
        { title: '对话 2' },
        { title: '对话 3' }
      ]
    }
  },
  onLoad() {
    this.initNavBar()
  },
  methods: {
    initNavBar() {
      const sys = uni.getSystemInfoSync()
      const sbh = sys.statusBarHeight || 0
      let contentH = 44
      let rect = null
      try {
        rect = typeof uni.getMenuButtonBoundingClientRect === 'function'
          ? uni.getMenuButtonBoundingClientRect()
          : null
      } catch (e) { rect = null }
      if (rect && rect.height && rect.top) {
        contentH = rect.height + (rect.top - sbh) * 2
      }
      // 小幅缩小高度以贴近示例胶囊视觉
      contentH = Math.max(36, Math.round(contentH - 6))
      this.statusBarHeight = sbh
      this.navContentHeight = contentH
      this.navTotalHeight = sbh + contentH
    },
    toggleHistory() {
      this.showHistory = !this.showHistory
    },
    sendMessage() {
      if (this.isSending) return
      const text = (this.inputValue || '').trim()
      if (!text) return
      this.isSending = true
      // 这里只处理UI交互，实际发送逻辑可后续接入
      uni.showToast({ title: '发送中...', icon: 'loading', duration: 800 })
      setTimeout(() => {
        uni.showToast({ title: '已发送', icon: 'success', duration: 800 })
        this.messages.push({ role: 'user', content: text, time: Date.now() })
        this.inputValue = ''
        this.isSending = false
      }, 800)
    }
  }
}
</script>

<style>
.page {
  height: 100vh;
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
}

/* 自定义导航栏 */
.navbar {
  width: 100%;
  display: block;
  background-color: #fff;
}
.nav-row { display: flex; align-items: center; }
.nav-left, .nav-right, .nav-title { height: 100%; display: flex; align-items: center; }
.nav-left { width: 140rpx; color: #111; font-size: 40rpx; padding-left: 24rpx;}
.nav-right { width: 140rpx; }
.nav-title { flex: 1; text-align: center; justify-content: center; font-size: 32rpx; color: #111; }

.welcome {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40rpx 40rpx 220rpx;
  box-sizing: border-box;
}

.logo {
  width: 200rpx;
  height: 200rpx;
  margin-top: 0;
  margin-bottom: 40rpx;
}

.intro { width: 100%; }
.intro-title {
  display: block;
  font-size: 36rpx;
  color: #111;
  margin-bottom: 20rpx;
}
.intro-sub {
  display: block;
  font-size: 28rpx;
  color: #666;
}

.message-list {
  flex: 1;
  padding: 24rpx 24rpx 220rpx;
  box-sizing: border-box;
}
.message-item {
  display: flex;
  margin-bottom: 16rpx;
}
.message-item.left { justify-content: flex-start; }
.message-item.right { justify-content: flex-end; }
.message-bubble {
  max-width: 80%;
  line-height: 40rpx;
  word-break: break-word;
}
.message-bubble.user {
  background-color: rgba(0, 86, 30, 0.10);
  border-radius: 16rpx;
  padding: 12rpx 16rpx;
  color: #111;
  box-sizing: border-box;
}
.message-role {
  font-size: 24rpx;
  color: #999;
  margin-right: 8rpx;
}
.message-text {
  font-size: 28rpx;
  color: #111;
  max-width: 80%;
  line-height: 40rpx;
  word-break: break-word;
}
.content-panel {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.input-bar {
  padding: 16rpx 24rpx calc(16rpx + env(safe-area-inset-bottom));
  display: flex;
  flex-direction: column;
  align-items: stretch;
  background-color: #fff;
  box-shadow: 0 -8rpx 24rpx rgba(0,0,0,0.06);
  box-sizing: border-box;
}
.input-row { display: flex; align-items: center; }

.input {
  flex: 1;
  background-color: #f5f5f7;
  border-radius: 16rpx;
  padding: 0 24rpx;
  font-size: 28rpx;
  border: 2rpx solid #eee;
  box-sizing: border-box;
}

.textarea {
  min-height: 72rpx;
  line-height: 40rpx;
  padding-top: 12rpx;
  padding-bottom: 12rpx;
  max-height: 240rpx;
  overflow-y: auto;
  box-sizing: border-box;
}

.send-btn {
  margin-left: 16rpx;
  width: 72rpx;
  height: 72rpx;
  border-radius: 50%;
  background-color: #00561E;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
}
.send-btn.disabled { 
  background-color: rgba(0, 86, 30, 0.35);
  pointer-events: none;
}
.send-icon { font-size: 40rpx; }
.send-icon-svg { width: 60rpx; height: 60rpx; }
.spinner {
  width: 32rpx;
  height: 32rpx;
  border: 4rpx solid rgba(255,255,255,0.7);
  border-top-color: #ffffff;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
@keyframes spin { from { transform: rotate(0); } to { transform: rotate(360deg); } }

.footer-note {
  margin-top: 8rpx;
  text-align: center;
  color: #999;
  font-size: 24rpx;
}

/* 抽屉样式 */
.drawer {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  width: 560rpx;
  background-color: #fff;
  box-shadow: 12rpx 0 24rpx rgba(0,0,0,0.08);
  transform: translateX(-100%);
  transition: transform 200ms ease;
  padding-top: calc(env(safe-area-inset-top) + 20rpx);
  box-sizing: border-box;
}
.drawer-open { transform: translateX(0); }
.drawer-mask {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background-color: rgba(0,0,0,0.25);
}
.drawer-header {
  font-size: 32rpx;
  padding: 24rpx;
  color: #111;
  box-sizing: border-box;
}
.drawer-list { height: 100%; }
.drawer-item {
  padding: 24rpx;
  border-bottom: 2rpx solid #f0f0f0;
  box-sizing: border-box;
}
</style>
