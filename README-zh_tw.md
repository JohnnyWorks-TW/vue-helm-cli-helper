# Helm Chart 小助手

<img src="https://raw.githubusercontent.com/JohnnyWorks-TW/vue-helm-cli-helper/master/screenshot/screenshot_zh.png" />

## 介紹

Helm Chart 小助手是一個幫您快速產生 Helm Chart 相關指令的小工具，它可以幫助你快速瞭解並熟悉 Helm 相關指令，進而加快您的開發佈建效率。

## macOS 注意事項

macOS 預設安全設定會阻止未簽署的應用程式執行，如果您在執行應用程式時遇到錯誤訊息，您可以嘗試以下步驟允許應用程式執行：

```bash
sudo xattr -r -d com.apple.quarantine /Applications/helm-cli-helper.app
```

## 使用技術

- Vue 3
- Vite
- Electron

### 專案建置

```bash
yarn install
```

### 開發時編譯與即時載入

```bash
yarn run start
```

### 編譯與打包

```bash
yarn run make
```

### Lints and fixes files

```bash
yarn run lint
```

### 自訂配置

See [Configuration Reference](https://cli.vuejs.org/config/).