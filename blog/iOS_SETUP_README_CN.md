---
slug: flutter-ios-github-actions
title: 使用 GitHub Actions 进行 Flutter iOS 应用自动化构建发布
authors: [wilinz]
tags: [flutter, ios, github-actions, 自动化]
date: 2025-08-31
---

本文档说明如何配置 GitHub Actions 进行 Flutter iOS 应用自动化发布。

<!-- truncate -->

## 必需的 Secrets

iOS 发布工作流需要以下 GitHub Secrets：

- `IOS_CERTIFICATES_P12_BASE64`
- `IOS_CERTIFICATES_PASSWORD`
- `APPSTORE_CONNECT_API_KEY_ID`
- `APPSTORE_CONNECT_API_ISSUER_ID`
- `APPSTORE_CONNECT_API_KEY`

## 1. iOS 开发和分发证书

### 如果本地已经存在两个证书

先打开钥匙串看看两个证书是否都存在：

1. 在 Mac 上打开 command + 空格**钥匙串访问**
2. 在 `默认钥匙串` -> `登录` 下找到你的 iOS 开发证书和分发证书 
3. 按command键同时选择 Development 证书和 Distribution 证书，前者用于构建 xxx.app，后者用于打包 ipa，这里我们需要同时选中并导出为一个 p12 文件
4. 右键点击证书并选择**导出**
5. 选择**个人信息交换 (.p12)** 格式
6. 设置一个强密码（这将是您的 `IOS_CERTIFICATES_PASSWORD`）
7. 保存 `.p12` 文件
8. ![image-20250901032706798](/assets/image-20250901032706798.png)

### 如果需要创建新证书

#### 第一步：创建证书签名请求 (CSR)
1. 在 Mac 上打开**钥匙串访问**

2. 选择**钥匙串访问 > 证书助理 > 从证书颁发机构请求证书**

3. **电子邮件 **输入您的邮箱地址，建议填你的 AppStore 开发者账号

4. **通用名称 **这里的“通用名称”通常填你的姓名或公司名称，主要是为了标识证书的所有者，并不会影响证书的使用。

   常见情况：

   - **个人开发者账号**：建议填写你的 **英文全名**（比如 `Li Ming`），和 Apple ID 上保持一致更清晰。
   - **公司开发者账号**：建议填写公司英文名称。
   - **团队内部调试**：其实填什么都行，甚至写 `iOS Developer` 也可以，只是方便你自己区分。

   ⚠️ 注意：

   - 不要写中文（Apple 不推荐），最好用拼音或英文。
   - 这个字段只是标识用，不会影响证书能否正常使用。

5. 选择**存储到磁盘**和**让我指定密钥对信息**

6. 保存 `.certSigningRequest` 文件

   ![image-20250901034011706](/assets/image-20250901034011706.png)

#### 第二步：创建 iOS 开发证书
1. 前往 [Apple 开发者门户](https://developer.apple.com/account/resources/certificates)
2. 点击 **+** 创建新证书
3. 选择 **iOS App Development**
4. 上传您的 `.certSigningRequest` 文件
5. 下载证书（`.cer` 文件）

#### 第三步: 创建 iOS 分发证书

1. 点击 **+** 创建新证书
2. 选择 **iOS Distribution (App Store and Ad Hoc)**
3. 上传您的 `.certSigningRequest` 文件
4. 下载证书（`.cer` 文件）
5. ![image-20250901034831033](/assets/image-20250901034831033.png)

#### 第三步：导出证书为 P12 格式
1. 分别双击下载的两个 `.cer` 文件将其安装到钥匙串访问中
2. 在钥匙串访问中，在**我的证书**下找到您的证书
3. 按住 command 同时点击两个证书并右键选择**导出**
4. 选择**个人信息交换 (.p12)** 格式
5. 设置密码（这将是您的 `IOS_CERTIFICATES_PASSWORD`）
6. 保存 `.p12` 文件

### 第四步：转换为 Base64

在你的路径打开控制台

```bash
base64 -i *.p12 | pbcopy
```

- `IOS_CERTIFICATES_P12_BASE64`：上面生成的 base64 编码字符串
- `IOS_CERTIFICATES_PASSWORD`：导出 P12 文件时设置的密码

## 2. 创建描述文件

### 步骤 1：创建 App Store 描述文件

1. 访问 [Apple Developer Portal > Profiles](https://developer.apple.com/account/resources/profiles)
2. 点击 **+** 创建一个新的配置文件
3. 在分发选项下选择 **App Store**
4. 选择你的 App ID
5. 选择之前创建的分发证书
6. 输入配置文件名称并生成

![image-20250901040723135](/assets/image-20250901040723135.png)

![image-20250901040937865](/assets/image-20250901040937865.png)

![image-20250901041005455](/assets/image-20250901041005455.png)

## 3. App Store Connect API 密钥

### 第一步：创建 API 密钥
1. 前往 [App Store Connect > 用户和访问 > 集成](https://appstoreconnect.apple.com/access/integrations/api)
2. 点击 **+** 创建新密钥
3. 为您的密钥输入名称
4. 选择**管理员**访问权限
5. 点击**生成**
6. 立即下载 `.p8` 文件（只能下载一次）

### 第二步：获取密钥信息
在 App Store Connect 密钥页面，记录：
- **密钥 ID**：10 字符的密钥标识符
- **签发者 ID (Issuer ID)**：页面顶部的 UUID

![image-20250901035322784](/assets/image-20250901035322784.png)

### 第三步：将 P8 转换为 文本
```bash
awk '{printf "%s\n", $0}' ./AuthKey_*.p8 | pbcopy
```

- `APPSTORE_CONNECT_API_KEY_ID`：密钥 ID（10 个字符）
- `APPSTORE_CONNECT_API_ISSUER_ID`：签发者 ID（UUID）
- `APPSTORE_CONNECT_API_KEY` P8 文件文本

## 4. 创建 ios/ExportOptions.plist 文件

在 Flutter 项目中创建 `ios/ExportOptions.plist` 文件并提交

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>method</key>
	<string>app-store</string>
	<key>teamID</key>
	<string>替换为你的teamID</string>
	<key>provisioningProfiles</key>
	<dict>
		<key>替换为你的包名 com.xxx.xxx </key>
		<string>替换成你的描述文件名</string>
	</dict>
	<key>signingStyle</key>
	<string>manual</string>
	<key>uploadBitcode</key>
	<false/>
	<key>uploadSymbols</key>
	<true/>
	<key>compileBitcode</key>
	<false/>
</dict>
</plist>
```

**从开发者门户获取**

1. 前往 [Apple 开发者门户 > 成员资格](https://developer.apple.com/account/#/membership)

2. 找到您的**团队 ID**（10 字符标识符）

   ![image-20250901035945837](/assets/image-20250901035945837.png)

3. 描述文件名就是刚才创建的那个 [https://developer.apple.com/account/resources/profiles/list](https://developer.apple.com/account/resources/profiles/list)![image-20250901040257149](/assets/image-20250901040257149.png)

## 5. Xcode 配置文件设置 iOS CI 打包
### 1. 修改 Xcode 项目配置

打开配置文件 `ios/Runner.xcodeproj/project.pbxproj` 搜索替换下面内容
将签名方式从自动改为手动，并为 iOS 设备指定特定的签名配置：

```shell
CODE_SIGN_STYLE = Manual;  // 改为手动签名
CODE_SIGN_IDENTITY = "iPhone Developer";
DEVELOPMENT_TEAM = your_teamID;  // 指定 teamID，即开发者网站的10位开发者ID
PROVISIONING_PROFILE_SPECIFIER = your_provisioning_profile_name ;  // 指定 provisioning profile, 即刚刚创建的profile 名
```



## 6. 添加 Secrets 到 GitHub

1. 前往您的 GitHub 仓库

2. 导航到**设置 > Secrets and variables > Actions**

3. 点击**New repository secret**

4. 使用上面列出的确切名称添加每个 secret

   ```shell
   # IOS_CERTIFICATES_P12_BASE64
   # IOS_CERTIFICATES_PASSWORD
   # APPSTORE_CONNECT_API_KEY_ID
   # APPSTORE_CONNECT_API_ISSUER_ID
   # APPSTORE_CONNECT_API_KEY
   ```

## 安全注意事项

- 永远不要将证书、私钥或 API 密钥提交到仓库中
- 将所有敏感数据存储为 GitHub Secrets
- P12 密码应该强壮且唯一
- 保持您的证书和描述文件为最新状态

## 故障排除

### 常见问题：
1. **证书过期**：每年更新您的分发证书
2. **描述文件过期**：每年更新您的描述文件
3. **错误的团队 ID**：确保团队 ID 与您的 Apple 开发者账户匹配
4. **API 密钥权限**：确保您的 API 密钥具有应用管理员或管理员权限
5. **Base64 编码**：确保 base64 字符串中没有额外的空格或换行符

## 测试工作流

`.github/workflows/release_ios_test.yaml`

```yaml
name: Release iOS
# IOS_CERTIFICATES_P12_BASE64
# IOS_CERTIFICATES_PASSWORD
# APPSTORE_CONNECT_API_KEY_ID
# APPSTORE_CONNECT_API_ISSUER_ID
# APPSTORE_CONNECT_API_KEY

on:
  workflow_dispatch:
  push:
    tags:
      - '*'

jobs:
  build_ios:
    runs-on: macos-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Extract Version from pubspec.yaml
        id: extract_version
        run: |
          version=$(grep '^version: ' pubspec.yaml | sed 's/version: //' | cut -d '+' -f 1)
          echo "PUBSPEC_VERSION=$version" >> $GITHUB_ENV
          
      - name: Import Apple Codesign Certificates
        uses: apple-actions/import-codesign-certs@v5
        with:
          p12-file-base64: "${{ secrets.IOS_CERTIFICATES_P12_BASE64 }}"
          p12-password: "${{ secrets.IOS_CERTIFICATES_PASSWORD }}"

      - name: Download Provisioning Profiles
        uses: apple-actions/download-provisioning-profiles@v4
        with:
          bundle-id: 'com.example.myapp' # 替换成你的包名
          profile-type: 'IOS_APP_STORE'
          issuer-id: ${{ secrets.APPSTORE_CONNECT_API_ISSUER_ID }}
          api-key-id: ${{ secrets.APPSTORE_CONNECT_API_KEY_ID }}
          api-private-key: ${{ secrets.APPSTORE_CONNECT_API_KEY }}

      - name: Set up Flutter
        uses: subosito/flutter-action@v2
        with:
          channel: 'stable'
          cache: true
          cache-key: 'flutter-:os:-:channel:-:version:-:arch:-:hash:' 
          cache-path: '${{ runner.tool_cache }}/flutter/:channel:-:version:-:arch:' 
          architecture: arm64 # optional, x64 or arm64
          flutter-version: 3.35.0
      - run: flutter --version

      - name: Cache Flutter dependencies
        uses: actions/cache@v4
        with:
          path: |
            ~/.pub-cache
          key: ${{ runner.os }}-pub-${{ hashFiles('**/pubspec.yaml') }}
          restore-keys: |
            ${{ runner.os }}-pub-

      - name: Cache iOS build
        uses: actions/cache@v4
        with:
          path: |
            ios/build
            ios/Pods
          key: ${{ runner.os }}-ios-build-${{ hashFiles('ios/Podfile.lock', 'ios/**/*.pbxproj') }}
          restore-keys: |
            ${{ runner.os }}-ios-build-

      - name: Build IPA with distribution profile
        run: |
          flutter build ipa --export-options-plist=ios/ExportOptions.plist

      - name: Get app info
        id: app-info
        run: |
          version=$(grep '^version: ' pubspec.yaml | sed 's/version: //' | cut -d '+' -f 1)
          echo "version=$version" >> $GITHUB_OUTPUT
          
      - name: Rename and prepare IPA for artifacts
        run: |
          mkdir -p build/ios/my-release
          cp build/ios/ipa/*.ipa build/ios/my-release/myapp-ios-v${{ steps.app-info.outputs.version }}.ipa

#      # 可选上传到 appstoreconnect
#      - name: Upload app to TestFlight
#        uses: apple-actions/upload-testflight-build@v3
#        with:
#          app-path: build/ios/my-release/guethub-ios-v${{ steps.app-info.outputs.version }}.ipa
#          issuer-id: ${{ vars.APPSTORE_ISSUER_ID }}
#          api-key-id: ${{ vars.APPSTORE_API_KEY_ID }}
#          api-private-key: ${{ secrets.APPSTORE_API_PRIVATE_KEY }}

      - name: Upload IPA to artifact
        uses: actions/upload-artifact@v4
        with:
          name: myapp-ios-v${{ steps.app-info.outputs.version }}.ipa
          path: build/ios/my-release/myapp-ios-v${{ steps.app-info.outputs.version }}.ipa
```