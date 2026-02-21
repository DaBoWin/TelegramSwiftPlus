# Telegram macOS 安装说明

## 📦 安装步骤

### 方法 1：右键打开（推荐）
1. 下载 `Telegram.app` 或 `Telegram-macOS.dmg`
2. 将 APP 拖到"应用程序"文件夹
3. **右键点击** Telegram.app
4. 选择"打开"
5. 在弹出的对话框中点击"打开"按钮
6. 以后就可以正常双击打开了

### 方法 2：命令行（快速）
```bash
# 下载后执行
xattr -cr /Applications/Telegram.app

# 然后就可以正常打开了
open /Applications/Telegram.app
```

### 方法 3：系统设置
1. 尝试打开 APP（会失败）
2. 打开"系统偏好设置" → "安全性与隐私"
3. 在"通用"标签下，点击"仍要打开"按钮

## ⚠️ 为什么会这样？

这个版本未经过 Apple 签名和公证，macOS 的 Gatekeeper 会阻止运行。
但这**不是病毒**，只是安全检查机制。

## ✅ 安全说明

- 源代码开源：https://github.com/YOUR_USERNAME/TelegramSwift
- 基于官方 Telegram 源码修改
- 仅修改了账号数量限制（3个→30个）
- 可以自行查看代码和构建日志

## 🔧 如果还是打不开

尝试完全移除隔离属性：
```bash
sudo xattr -rd com.apple.quarantine /Applications/Telegram.app
```

## 📞 需要帮助？

如有问题请联系：[你的联系方式]
