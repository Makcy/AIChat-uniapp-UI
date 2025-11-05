<template>
  <view class="page">
    <!-- 左上角对话历史入口按钮 -->
    <view class="history-btn" @tap="toggleHistory">☰</view>
    <view class="welcome">
      <view class="intro">
        <text class="intro-title">我是 DeepSeek，很高兴见到你！</text>
        <text class="intro-sub">我可以帮你写代码、读文件、写作各种创意内容，请把你的任务交给我吧~</text>
      </view>
    </view>

    <view class="footer-note">内容由 AI 生成，仅供参考</view>

    <view class="input-bar">
      <input
        class="input"
        v-model="inputValue"
        :disabled="isSending"
        placeholder="给 DeepSeek 发送消息"
        confirm-type="send"
        @confirm="sendMessage"
      />
      <view class="send-btn" :class="{ disabled: isSending }" @tap="sendMessage">
        <text v-if="!isSending" class="send-icon">➤</text>
        <view v-else class="spinner"></view>
      </view>
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
      conversations: [
        { title: '对话 1' },
        { title: '对话 2' },
        { title: '对话 3' }
      ]
    }
  },
  methods: {
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
        this.inputValue = ''
        this.isSending = false
      }, 800)
    }
  }
}
</script>

<style>
.page {
  min-height: 100vh;
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
}

.history-btn {
  position: fixed;
  left: 20rpx;
  top: calc(env(safe-area-inset-top) + 20rpx);
  width: 64rpx;
  height: 64rpx;
  line-height: 64rpx;
  text-align: center;
  border-radius: 50%;
  background-color: #eaf9ef;
  color: #4cd964;
  border: 2rpx solid #4cd964;
  box-shadow: 0 6rpx 16rpx rgba(0,0,0,0.08);
}

.welcome {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40rpx 40rpx 220rpx;
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

.input-bar {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  padding: 16rpx 24rpx calc(16rpx + env(safe-area-inset-bottom));
  display: flex;
  align-items: center;
  background-color: #fff;
  box-shadow: 0 -8rpx 24rpx rgba(0,0,0,0.06);
}

.input {
  flex: 1;
  height: 72rpx;
  background-color: #f5f5f7;
  border-radius: 16rpx;
  padding: 0 24rpx;
  font-size: 28rpx;
  border: 2rpx solid #eee;
}

.send-btn {
  margin-left: 16rpx;
  width: 72rpx;
  height: 72rpx;
  border-radius: 50%;
  background-color: #4cd964;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
}
.send-btn.disabled { opacity: 0.7; }
.send-icon { font-size: 40rpx; }
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
  position: fixed;
  left: 0;
  right: 0;
  bottom: calc(160rpx + env(safe-area-inset-bottom));
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
}
.drawer-list { height: 100%; }
.drawer-item {
  padding: 24rpx;
  border-bottom: 2rpx solid #f0f0f0;
}
</style>
