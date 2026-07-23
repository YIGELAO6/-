# 📒 笔记本 - Android App

一个纯离线、零权限收集的安卓笔记应用。链接自动识别可点击，毛玻璃 UI。

## 技术架构

| 层 | 技术 |
|---|---|
| 界面 | WebView 加载本地 HTML（零网络请求） |
| 逻辑 | 原生 Java（MainActivity + NativeBridge） |
| 存储 | WebView localStorage + 文件系统导出/导入 |
| 构建 | Gradle + GitHub Actions 自动编译 |

## 快速开始（获取 APK）

### 方式一：GitHub Actions 自动构建（推荐，零环境）

1. 把这个项目推到你的 GitHub 仓库
2. 进入仓库 → **Actions** 标签页
3. 点 **"Build APK"** → 点 **Run workflow**
4. 等待 3-5 分钟，构建完成后下载 Artifact
5. 得到 `Notebook-Release-APK` 和 `Notebook-Debug-APK` 两个产物

### 方式二：本地 Android Studio 构建

1. 安装 [Android Studio](https://developer.android.com/studio)
2. 打开本项目（选择 `android-app` 目录）
3. 等待 Gradle 同步完成
4. **Build** → **Build Bundle(s) / APK(s)** → **Build APK(s)**
5. 在 `app/build/outputs/apk/release/` 找到 APK

### 方式三：命令行构建

```bash
# 需要 JDK 17 + Android SDK
cd android-app
./gradlew assembleRelease
# APK 在 app/build/outputs/apk/release/
```

## 项目结构

```
android-app/
├── .github/workflows/build-apk.yml   # CI/CD 自动打包
├── app/
│   ├── build.gradle                  # 模块构建配置
│   ├── proguard-rules.pro            # 混淆规则
│   └── src/main/
│       ├── AndroidManifest.xml       # 权限 & 组件声明
│       ├── java/com/notebook/app/
│       │   ├── MainActivity.java     # WebView 主界面
│       │   ├── NotebookApp.java      # Application 初始化
│       │   └── NativeBridge.java     # JS ↔ Java 桥接
│       ├── assets/
│       │   └── notebook.html         # 核心应用（HTML+CSS+JS）
│       ├── res/
│       │   ├── layout/activity_main.xml
│       │   ├── drawable/ic_launcher_*.xml
│       │   ├── mipmap-anydpi-v26/ic_launcher*.xml
│       │   ├── values/themes.xml
│       │   └── xml/file_paths.xml
│       └── res/xml/backup_rules.xml
├── build.gradle                      # 根构建配置
├── settings.gradle
├── gradle/wrapper/gradle-wrapper.properties
├── gradlew / gradlew.bat
└── README.md
```

## 功能

- ✅ 纯离线，无需联网
- ✅ 链接自动识别为可点击超链接
- ✅ 毛玻璃半透明 UI
- ✅ 导出/导入笔记（JSON）
- ✅ 搜索笔记
- ✅ 自动保存
- ✅ 支持 Android 10 ~ 17

## 权限说明

| 权限 | 用途 |
|---|---|
| INTERNET | WebView 加载（实际不联网，仅链接跳转用） |
| WRITE_EXTERNAL_STORAGE | Android 10-11 导出文件到 Download 目录 |
| MANAGE_EXTERNAL_STORAGE | Android 11 文件管理 |
| READ_MEDIA_* | Android 13+ 读取导入文件 |

## 修改网页内容

所有界面逻辑在 `app/src/main/assets/notebook.html` 中。修改后重新构建即可。

## 签名发布

如需上架应用商店，需生成签名密钥：

```bash
keytool -genkey -v -keystore notebook-release.keystore \
  -alias notebook -keyalg RSA -keysize 2048 -validity 10000
```

然后在 GitHub Secrets 中设置：
- `KEYSTORE_FILE` - 上传 keystore 文件
- `KEYSTORE_PASSWORD`
- `KEY_ALIAS`
- `KEY_PASSWORD`

Release 构建会自动签名。
