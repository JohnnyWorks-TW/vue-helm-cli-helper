# helm command line helper

<img src="https://raw.githubusercontent.com/JohnnyWorks-TW/vue-helm-cli-helper/master/screenshot/screenshot_en.png" />

## Introduction

Helm Chart Helper is a small tool that helps you quickly generate Helm Chart related commands. It can help you quickly understand and familiarize yourself with Helm related commands, thereby speeding up your development and deployment efficiency.

## Note for macOS

Default macOS security settings prevent unsigned applications from being run. If you encounter an error message when running the application, you can try the following steps to allow the application to run:

```bash
sudo xattr -r -d com.apple.quarantine /Applications/helm-cli-helper.app
```

## Technology

- Vue 3
- Vite
- Electron

## Project setup

```bash
yarn install
```

### Compiles and hot-reloads for development

```bash
yarb run start
```

### Compiles and minifies for production

```bash
yarn run make
```

### Lints and fixes files

```bash
yarn run lint
```

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).
