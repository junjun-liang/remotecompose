# Remote Compose 演示项目

[AndroidX Remote Compose](https://developer.android.com/jetpack/androidx/releases/compose-remote) 的演示项目 —— 面向 Android 的服务端驱动 UI，通过 Jetpack Compose 进行原生渲染。

**在线编辑器：** [Web Editor](https://armcha.github.io/remotecompose/)

https://github.com/user-attachments/assets/e6ac7586-1d85-4c68-a751-ca8ffb71a59f

## 架构

```
 创建端（JVM 服务端）                   播放端（Android 应用）
┌────────────────────────┐            ┌────────────────────────┐
│  Web 编辑器            │            │                        │
│  编辑 JSON 配置        │            │  RemoteComposePlayer   │
│         │              │            │  渲染二进制文档        │
│         ▼              │            │         ▲              │
│  GitHub Action 运行    │            │  直接下载 ByteArray    │
│  JVM 转换器            │   二进制   │                        │
│         │              │── .rc ────>│                        │
│         ▼              │   文件     │  无创建端依赖！        │
│  RemoteComposeWriter   │            │  无 JSON 解析！        │
│  （纯 JVM）            │            │  无 @RestrictTo API！  │
└────────────────────────┘            └────────────────────────┘
```

Android 应用是一个**纯播放器** —— 它下载预构建的二进制 Remote Compose 文档，并通过 `RemoteComposePlayer` 进行渲染。所有文档创建都在 JVM 服务端完成。

## 工作原理

### 1. 在 Web 编辑器中编辑 UI

[Web 编辑器](https://armcha.github.io/remotecompose/) 提供拖拽式界面来构建 UI 布局，支持：
- **文本**、**按钮**、**间距**、**分割线**、**卡片**、**行** 元素
- 可配置属性：颜色、字体大小、内边距、圆角、边框
- 点击动作：导航和宿主应用事件
- 多屏幕支持（首页、详情、报价、报价详情）
- 在手机框架中实时预览 Compose/Wasm 效果

### 2. 部署

点击 **Deploy** 将 JSON 配置推送到 GitHub。GitHub Action 会自动：
1. 运行 JVM 转换器（`server/`）
2. 使用 `RemoteComposeWriter` 生成二进制 `.rc` 文档
3. 将 `.rc` 文件提交回仓库

Web 编辑器会显示 Action 的实时进度和计时器。

### 3. Android 应用渲染

Android 应用下载二进制 `.rc` 文件，并使用 `RemoteComposePlayer`（基于 View 的播放器）进行渲染。没有创建端代码，没有 JSON 解析 —— 只需二进制字节输入，原生 UI 输出。

## 关键技术

| 组件 | 技术 |
|---|---|
| Android 播放器 | `RemoteComposePlayer`（来自 `remote-player-view`） |
| 二进制格式 | Remote Compose 有线格式（二进制操作） |
| JVM 转换器 | `RemoteComposeWriter`（来自 `remote-creation-core` + `remote-creation-jvm`） |
| Web 预览 | Kotlin/Wasm + Compose Multiplatform |
| Web 编辑器 | 原生 HTML/CSS/JS |
| CI/CD | GitHub Actions |
| 托管 | GitHub Pages |

## 相关资源

- [remotecompose-android](https://github.com/armcha/remotecompose-android) —— Android 播放器应用（纯播放器，无创建端代码）
- [Remote Compose 文档](https://github.com/androidx/androidx/tree/androidx-main/compose/remote/Documentation)
- [Remote Compose 版本发布](https://developer.android.com/jetpack/androidx/releases/compose-remote)
