# GitHub Actions 构建说明

## 自动构建配置

已创建 `.github/workflows/build.yml` 文件，支持以下功能：

### 触发条件
- ✅ 推送到 `main` 或 `master` 分支
- ✅ Pull Request 到 `main` 或 `master` 分支  
- ✅ 手动触发（workflow_dispatch）
- ✅ 创建 Tag 时自动发布 Release

### 构建流程

1. **检出代码**：包含所有子模块
2. **设置 Xcode**：使用最新稳定版
3. **安装依赖**：cmake, ninja, openssl 等
4. **配置框架**：运行 configure_frameworks.sh
5. **构建应用**：使用 xcodebuild（无签名）
6. **创建 DMG**：打包为安装包
7. **上传产物**：保存 30 天
8. **发布 Release**：Tag 推送时自动创建

## 使用方法

### 1. 推送代码到 GitHub

```bash
# 添加远程仓库（如果还没有）
git remote add origin https://github.com/YOUR_USERNAME/TelegramSwift.git

# 提交修改
git add .
git commit -m "支持30个账号登录"

# 推送到 GitHub
git push origin main
```

### 2. 手动触发构建

1. 进入 GitHub 仓库页面
2. 点击 `Actions` 标签
3. 选择 `Build Telegram macOS` 工作流
4. 点击 `Run workflow` 按钮
5. 选择分支并点击 `Run workflow`

### 3. 下载构建产物

构建完成后：
1. 进入 `Actions` 标签
2. 点击对应的构建任务
3. 在 `Artifacts` 部分下载 `Telegram-macOS`
4. 解压后即可使用

### 4. 创建 Release 版本

```bash
# 创建并推送 tag
git tag v1.0.0-30accounts
git push origin v1.0.0-30accounts
```

这会自动：
- 触发构建
- 创建 GitHub Release
- 上传 DMG 安装包

## 注意事项

### ⚠️ 代码签名

当前配置**禁用了代码签名**（适合个人使用）：
```yaml
CODE_SIGN_IDENTITY=""
CODE_SIGNING_REQUIRED=NO
CODE_SIGNING_ALLOWED=NO
```

如果需要签名版本：
1. 添加 Apple Developer 证书到 GitHub Secrets
2. 修改构建配置启用签名
3. 参考：[Xcode Cloud Signing](https://developer.apple.com/documentation/xcode/configuring-your-xcode-cloud-workflow-to-use-a-different-code-signing-certificate)

### 📦 构建时间

- 首次构建：约 30-45 分钟（需要编译所有依赖）
- 后续构建：约 15-20 分钟（有缓存）

### 💾 存储空间

- 每次构建约占用 2-3 GB
- 产物保存 30 天后自动删除
- 可在工作流中修改 `retention-days`

### 🔧 自定义配置

编辑 `.github/workflows/build.yml` 可以：
- 修改触发条件
- 调整构建配置
- 更改产物保存时间
- 添加测试步骤

## 常见问题

### Q: 构建失败怎么办？

1. 查看 Actions 日志找到错误信息
2. 常见问题：
   - 子模块未正确初始化
   - 依赖安装失败
   - Xcode 版本不兼容
   - 框架配置错误

### Q: 如何加速构建？

可以添加缓存：
```yaml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: |
      ~/Library/Caches/Homebrew
      ./build
    key: ${{ runner.os }}-build-${{ hashFiles('**/Podfile.lock') }}
```

### Q: 构建的 APP 能直接运行吗？

- ✅ 可以在自己的 Mac 上运行
- ⚠️ 分发给他人需要签名和公证
- ⚠️ 首次运行可能需要在"系统偏好设置 → 安全性与隐私"中允许

### Q: 如何修改 API ID？

1. 获取自己的 [Telegram API ID](https://core.telegram.org/api/obtaining_api_id)
2. 编辑 `Telegram-Mac/Config.swift`
3. 替换 `apiId` 和 `apiHash`
4. 推送代码重新构建

## 高级配置

### 添加构建通知

在工作流末尾添加：
```yaml
- name: Notify on success
  if: success()
  run: |
    curl -X POST -H 'Content-type: application/json' \
    --data '{"text":"✅ Telegram macOS 构建成功！"}' \
    YOUR_WEBHOOK_URL
```

### 多版本构建

可以创建矩阵构建不同配置：
```yaml
strategy:
  matrix:
    configuration: [Debug, Release]
    xcode: ['14.3', '15.0']
```

### 自动测试

添加测试步骤：
```yaml
- name: Run tests
  run: |
    xcodebuild test \
      -workspace Telegram-Mac.xcworkspace \
      -scheme Telegram-Mac \
      -destination 'platform=macOS'
```

## 相关链接

- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [Xcode Build Settings](https://developer.apple.com/documentation/xcode/build-settings-reference)
- [Telegram API](https://core.telegram.org/api)
