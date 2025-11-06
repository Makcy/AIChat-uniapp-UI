# AIChat / 智能助手（uni-app）

一个轻量的聊天界面项目，基于 uni-app 构建，包含自定义导航栏、对话历史抽屈、消息面板与输入栏。已针对移动端体验进行了多项细节优化。

## 功能概览
- 自定义导航栏（`navigationStyle: custom`），左侧“☰”按钮切换历史抽屉，标题居中。
- 欢迎面板：当 `messages.length === 0` 时显示欢迎内容；有消息后显示消息列表。
- 消息列表：
  - 用户消息右侧显示，使用圆角矩形气泡包裹；助手消息左侧显示为文本。
  - 相邻两条消息之间自动添加间距（`16rpx`）。
  - 面板溢出时纵向滚动，滚动条位于屏幕最右侧。
- 输入栏：
  - 多行 `textarea`，Enter 插入换行，不直接发送。
  - 文本框最大高度（`240rpx`），超出后内部滚动。
  - 发送按钮状态：空内容或发送中为浅色并禁用，有内容时恢复为深绿（`#00561E`）。
  - 发送按钮图标使用静态资源 `/static/arrow.svg`。
- 底部提示文案（footer-note）置于输入栏下方。

## 目录结构
```
AIChat/
├── App.vue
├── index.html
├── main.js
├── manifest.json
├── pages.json
├── pages/
│   └── index/
│       └── index.vue
├── static/
│   └── arrow.svg
├── uni.promisify.adaptor.js
└── uni.scss
```

## 运行与预览
本项目为 uni-app 工程，需通过 HBuilderX 或相关编译工具运行。

- 使用 HBuilderX（推荐）
  1. 安装最新 HBuilderX。
  2. 打开本项目目录。
  3. 运行到内置浏览器（H5）或运行到微信开发者工具（小程序）。

- 注意事项
  - 直接用本地静态服务器访问 `index.html` 会报错（例如 `Failed to resolve module specifier "vue"`），因为 uni-app 需要编译打包。
  - 若目标平台为微信小程序，`svg` 图片可能不被原生支持。可将 `static/arrow.svg` 转换为 `png` 并替换引用。

## 关键文件与配置
- `pages/index/index.vue`
  - 模板：自定义导航栏、欢迎面板、消息列表、输入栏与发送按钮。
  - 脚本：`messages` 管理消息；`sendMessage()` 将输入内容追加到消息列表，并清空输入框。
  - 样式：
    - 内容面板作为滚动容器（滚动条在屏幕右侧）。
    - 用户消息气泡、间距与对齐规则。
    - 输入栏为纵向布局，底部提示文案位于输入区域下方。
- `pages.json`
  - 设定首页使用自定义导航栏：`"navigationStyle": "custom"`。
  - 标题为“智能助手”（如需在系统导航上显示标题）。
- `static/arrow.svg`
  - 发送按钮的图标资源。可按需替换。

## 常见问题
- 浏览器直接打开无法运行？
  - 使用 HBuilderX 或 uni-app CLI 编译后运行，避免 `vue` 模块解析错误。
- 微信小程序端图标不显示？
  - 小程序端可能不支持 `svg`，将图标转为 `png` 并引用。
- 导航栏高度与胶囊对齐问题？
  - 已通过 `statusBarHeight` 与 `getMenuButtonBoundingClientRect()`（若可用）推算导航内容高度，以尽量贴合胶囊按钮；不同设备可能略有差异，可按需调整计算逻辑。

## 免责声明
- 页面中的提示“内容由 AI 生成，仅供参考”位于输入栏下方，可按需修改或移除。

如需我为某个平台（H5/微信小程序）进一步优化样式或交互，请告知目标平台与期望效果。