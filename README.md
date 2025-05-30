<h1 id="Zbecg">Pandu 文件存储解决方案</h1>
<h2 id="Ehnok">目录</h2>
+ [项目概述](#项目概述)
+ [技术栈](#技术栈)
+ [目录结构](#目录结构)
+ [核心功能](#核心功能)
+ [身份验证系统](#身份验证系统)
+ [文件管理系统](#文件管理系统)
+ [UI组件](#ui组件)
+ [常见问题](#常见问题)

<h2 id="m6gOa">项目概述</h2>
Pandu 是一个基于 Next.js 和 Appwrite 的文件存储解决方案，提供安全的文件上传、存储和共享功能。系统支持多种文件类型，包括图片、文档、视频和音频，并提供直观的用户界面进行管理。

<h3 id="MKPUZ">主要特性</h3>
+ 🔐 安全的邮箱+OTP身份验证
+ 📁 多种文件类型支持
+ 🔄 实时文件上传和预览
+ 📊 存储空间使用统计
+ 👥 文件共享功能

<h2 id="BNbKJ">技术栈</h2>
+ **前端框架**: [Next.js 15](https://nextjs.org/)
+ **后端服务**: [Appwrite](https://appwrite.io/)
+ **样式**: [Tailwind CSS](https://tailwindcss.com/)
+ **UI组件**: [Shadcn UI](https://ui.shadcn.com/)
+ **状态管理**: React 状态钩子
+ **表单处理**: React Hook Form + Zod
+ **文件上传**: React Dropzone

<h2 id="kf0bz">目录结构</h2>
```plain
/
├── app/                    # Next.js 应用目录
│   ├── (auth)/             # 认证相关页面
│   │   ├── sign-in/        # 登录页面
│   │   ├── sign-up/        # 注册页面
│   ├── (root)/             # 主应用页面(需登录)
│   │   ├── page.tsx        # 主页/仪表盘
│   │   ├── layout.tsx      # 主布局组件
├── components/             # 可复用组件
│   ├── ui/                 # UI基础组件
│   ├── FileUploader.tsx    # 文件上传组件 
│   ├── AuthForm.tsx        # 认证表单组件
│   ├── OTPModal.tsx        # OTP验证模态框
│   ├── Header.tsx          # 页面头部组件
│   ├── Sidebar.tsx         # 侧边栏组件
├── lib/                    # 工具库和服务
│   ├── actions/            # 服务器操作
│   │   ├── user.actions.ts # 用户相关操作
│   │   ├── file.actions.ts # 文件相关操作
│   ├── appwrite/           # Appwrite配置和客户端
│   ├── utils.ts            # 通用工具函数
├── types/                  # TypeScript类型定义
├── constants/              # 常量定义
├── hooks/                  # 自定义钩子
├── public/                 # 静态资源
```

<h2 id="fKaWK">核心功能</h2>
<h3 id="jUScN">身份验证系统</h3>
Pandu 采用邮箱+OTP(一次性密码)验证的安全登录方式，无需设置和记忆复杂密码。

<h4 id="BsDLi">身份验证流程</h4>
1. **用户注册/登录**：通过 `AuthForm.tsx` 组件，用户输入邮箱（及注册时的全名）
2. **OTP发送**：系统通过 `user.actions.ts` 中的 `sendEmailOTP` 函数发送一次性验证码
3. **验证确认**：用户在 `OTPModal.tsx` 组件中输入收到的验证码
4. **会话创建**：验证成功后，`verifySecret` 函数创建用户会话并设置 cookie

<h4 id="wbh2v">关键组件和文件</h4>
<h5 id="KRuwK">`AuthForm.tsx`</h5>
负责处理用户注册和登录表单，使用 React Hook Form 和 Zod 进行表单验证。

```tsx
// 用法示例
<AuthForm type="sign-in" /> // 登录表单
<AuthForm type="sign-up" /> // 注册表单
```

<h5 id="P784t">`OTPModal.tsx`</h5>
显示OTP输入界面，处理验证码提交和会话创建。

```tsx
// 用法示例
<OtpModal email="user@example.com" accountId="12345" />
```

<h5 id="iN0Ts">`lib/actions/user.actions.ts`</h5>
包含所有用户相关的服务器操作：

+ `createAccount`: 创建新用户账户
+ `signInUser`: 用户登录
+ `sendEmailOTP`: 发送OTP验证码
+ `verifySecret`: 验证OTP并创建会话
+ `getCurrentUser`: 获取当前登录用户
+ `signOutUser`: 用户登出

<h4 id="ZN6HH">数据模型</h4>
**users 集合**:

+ `fullName`: 用户全名
+ `email`: 用户邮箱(唯一)
+ `avatar`: 用户头像URL
+ `accountId`: Appwrite账户ID

<h3 id="wPdhO">文件管理系统</h3>
Pandu 的核心功能是文件管理系统，支持上传、查看、重命名、共享和删除文件。

<h4 id="i7Zqz">文件上传流程</h4>
1. **文件选择**: 通过 `FileUploader.tsx` 组件拖放或选择文件
2. **文件验证**: 检查文件大小是否超出限制
3. **存储上传**: 首先将文件上传到 Appwrite 存储桶
4. **数据库记录**: 创建文件元数据记录到数据库

<h4 id="nk79v">关键组件和文件</h4>
<h5 id="e0MYD">`FileUploader.tsx`</h5>
处理文件上传界面和逻辑，使用 React Dropzone 实现拖放功能。

```tsx
// 用法示例
<FileUploader ownerId="user123" accountId="account123" />
```

<h5 id="BGZhU">`lib/actions/file.actions.ts`</h5>
包含所有文件相关的服务器操作：

+ `uploadFile`: 上传文件到存储和数据库
+ `getFiles`: 获取用户的文件列表
+ `renameFile`: 重命名文件
+ `updateFileUsers`: 更新文件共享用户
+ `deleteFile`: 删除文件
+ `getTotalSpaceUsed`: 计算用户已使用空间

<h4 id="KDhwL">数据模型</h4>
**files 集合**:

+ `name`: 文件名称
+ `type`: 文件类型(document, image, video, audio, other)
+ `extension`: 文件扩展名
+ `url`: 文件访问URL
+ `size`: 文件大小(bytes)
+ `owner`: 文件所有者ID
+ `accountId`: 关联的账户ID
+ `users`: 可访问此文件的用户邮箱数组
+ `bucketFileld`: Appwrite存储桶ID

<h3 id="jkX1b">UI组件系统</h3>
Pandu 使用 Shadcn UI 和 Tailwind CSS 构建响应式用户界面。

<h4 id="kROj5">布局组件</h4>
<h5 id="kRQkv">`app/(root)/layout.tsx`</h5>
应用的主布局，包含侧边栏、头部和内容区域。

```tsx
<main className="flex h-screen">
  <Sidebar {...currentUser} />
  <section className="flex h-full flex-1 flex-col">
    <MobileNavigation {...currentUser} />
    <Header userId={currentUser.$id} accountId={currentUser.accountId} />
    <div className="main-content">{children}</div>
  </section>
</main>

```

<h5 id="cDEyn">`Sidebar.tsx`</h5>
桌面端导航侧边栏，显示用户信息和导航选项。

<h5 id="vVdHk">`MobileNavigation.tsx`</h5>
移动端导航菜单，在小屏幕设备上提供抽屉式导航。

<h5 id="WFBSP">`Header.tsx`</h5>
页面顶部组件，包含搜索栏、文件上传按钮和退出按钮。

<h4 id="l9v5T">功能组件</h4>
<h5 id="qO1Lq">`Search.tsx`</h5>
提供文件搜索功能，支持按名称搜索。

<h5 id="jNsGj">`FileCard.tsx`</h5>
显示单个文件的卡片组件，包含文件预览、名称和操作选项。

<h5 id="ZuklZ">`Thumbnail.tsx`</h5>
根据文件类型显示相应的缩略图或图标。

```tsx
// 用法示例
<Thumbnail type="document" extension="pdf" url="..." />
```

<h2 id="HPzHr">配置和环境变量</h2>
Pandu 使用 `.env.local` 文件存储所有Appwrite相关配置。必要的环境变量包括：

```plain
NEXT_PUBLIC_APPWRITE_ENDPOINT="https://cloud.appwrite.io/v1"
NEXT_PUBLIC_APPWRITE_PROJECT="your-project-id"
NEXT_PUBLIC_APPWRITE_DATABASE="your-database-id"
NEXT_PUBLIC_APPWRITE_USERS_COLLECTION="your-users-collection-id"
NEXT_PUBLIC_APPWRITE_FILES_COLLECTION="your-files-collection-id"
NEXT_PUBLIC_APPWRITE_BUCKET="your-bucket-id"
NEXT_APPWRITE_KEY="your-api-key"
```

<h2 id="VtObo">工具函数</h2>
`lib/utils.ts` 提供多个实用工具函数：

+ `parseStringify`: 处理服务器端返回数据的序列化
+ `constructFileUrl`: 构造文件访问URL
+ `getFileType`: 根据文件扩展名确定文件类型
+ `formatBytes`: 将字节大小转换为易读格式
+ `cn`: 合并条件类名的工具函数

<h2 id="ulsZz">常见开发问题和解决方案</h2>
<h3 id="otmrO">字段命名问题</h3>
在Appwrite数据库中，字段名区分大小写和拼写。常见错误：

+ `accountId` vs `accountld` (小写字母l vs 大写字母I)
+ `bucketField` vs `bucketFileld` (字母顺序不同)

确保代码中的字段名与数据库中定义的完全一致。

<h3 id="f25TV">文件上传失败</h3>
常见原因：

1. 文件大小超出限制 (默认50MB)
2. 缺少必要的数据库字段
3. Appwrite存储桶权限配置不正确

<h3 id="M4tr5">会话管理问题</h3>
确保cookie设置和读取正确：

```typescript
// 设置会话cookie
(await cookies()).set("appwrite-session", session.secret, {
  path: "/",
  httpOnly: true,
  sameSite: "strict",
  secure: true,
});

// 删除会话cookie
(await cookies()).delete("appwrite-session");
```

<h2 id="MjQvr">扩展和定制</h2>
<h3 id="VwkbY">添加新文件类型支持</h3>
1. 在 `constants/index.ts` 中更新 `FILE_TYPES` 常量
2. 在 `lib/utils.ts` 的 `getFileType` 函数中添加新扩展名的处理
3. 在 `Thumbnail.tsx` 中添加新类型的图标或预览处理

<h3 id="uaCHd">改进文件共享功能</h3>
当前系统支持通过邮箱分享文件。可以扩展为：

1. 添加权限级别(只读/编辑)
2. 生成分享链接
3. 设置访问过期时间

<h2 id="JLSRk">总结</h2>
Pandu 文件存储解决方案提供了一个完整的文件管理系统，结合了现代前端技术和Appwrite后端服务。系统架构清晰，组件化程度高，便于维护和扩展。

通过理解各个组件和服务的工作方式，新开发者可以快速上手并进行定制开发，满足不同的业务需求。

---

_需要查看更多代码示例或详细的API文档，请访问项目的GitHub仓库_[_https://github.com/zrea200/pandudemo_](https://github.com/zrea200/pandudemo)_。_

_在线体验 _[_https://pandudemo.vercel.app/sign-in_](https://pandudemo.vercel.app/sign-in)

