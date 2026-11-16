# mybili

一个使用 Kotlin 编写的第三方哔哩哔哩客户端，主要用于浏览动态、收藏夹和稍后再看，并支持直接播放视频。

> 本项目为个人学习项目，与哔哩哔哩官方无关。

[![Build APK](https://github.com/Dy1an5/mybili/actions/workflows/build.yml/badge.svg)](https://github.com/Dy1an5/mybili/actions/workflows/build.yml)

## 功能

- **动态**：浏览关注用户的视频动态，支持下拉刷新、按关注分组筛选
- **关注分组**：在本地创建、重命名、删除分组，并把关注的 UP 主归类
- **收藏夹**：查看自己的收藏夹及收藏内容
- **稍后再看**：查看稍后再看列表
- **视频播放**：使用 Media3 ExoPlayer 播放视频
- **账号登录**：通过内置 WebView 完成 B 站登录并保存 Cookie

## 下载 APK

有两种方式获取安装包：

1. **Releases**：打上 `v*` 标签（例如 `v1.0`）后会自动创建 Release，并附带 APK。
2. **Actions Artifacts**：每次推送到 `main` 分支都会自动构建，在
   [Actions](https://github.com/Dy1an5/mybili/actions) 页面打开对应运行记录，
   下载 `mybili-release-apk` 或 `mybili-debug-apk`。

优先使用 **release** 版本。覆盖安装时如果提示签名不一致，先卸载旧版本再安装。

## 从源码构建

环境要求：

- JDK 17
- Android SDK Platform 35（以及 Build-Tools 34.0.0）

```bash
# 调试包
./gradlew assembleDebug

# 发布包
./gradlew assembleRelease
```

产物位置：

- `app/build/outputs/apk/debug/app-debug.apk`
- `app/build/outputs/apk/release/app-release.apk`

`local.properties` 中的 SDK 路径由 Android Studio 自动生成，不需要提交到仓库。

## CI 云端打包

工作流文件：`.github/workflows/build.yml`

- 触发条件：推送到 `main`、提交 Pull Request、推送 `v*` 标签、手动触发
- 运行环境：Ubuntu + JDK 17 + Android SDK
- 构建内容：`assembleDebug` 和 `assembleRelease`
- 产物：两个 APK 会作为 Artifacts 上传，标签构建还会自动发布到 Releases

### 配置固定签名（可选）

如果不配置签名，CI 会回退到 debug 签名，不同构建环境生成的签名可能不同，导致覆盖安装失败。要获得固定签名，在仓库的 **Settings → Secrets and variables → Actions** 中添加：

| Secret | 说明 |
| --- | --- |
| `KEYSTORE_BASE64` | 签名文件（JKS）经 Base64 编码后的内容 |
| `KEYSTORE_PASSWORD` | 签名文件密码 |
| `KEY_ALIAS` | 密钥别名 |
| `KEY_PASSWORD` | 密钥密码 |

生成 Base64 内容：

```bash
base64 -w0 mybili-release.jks
```

**请务必备份签名文件和密码**。签名一旦丢失，已安装的用户将无法覆盖升级。

## 项目结构

```text
.
├── .github/workflows/build.yml   # CI 云端打包
├── app/
│   ├── build.gradle              # 应用模块配置
│   └── src/main/
│       ├── java/com/mybili/      # Kotlin 源码
│       │   ├── data/             # 数据库、网络、仓库层
│       │   └── ui/               # Activity、Fragment、Adapter
│       └── res/                  # 布局、图标、字符串等资源
├── gradle/
│   └── libs.versions.toml        # 依赖版本目录
├── build.gradle
├── settings.gradle               # 项目名：mybili
└── gradlew                       # Gradle Wrapper
```

## 说明

- 仓库名、Gradle 项目名、应用显示名均为 `mybili`
- 应用包名（applicationId / namespace）为 `com.mybili`
- 本项目仅供学习交流，请遵守哔哩哔哩的用户协议，不要用于商业用途
- 登录 Cookie 仅保存在本机，请勿将包含账号信息的文件上传到公开仓库
