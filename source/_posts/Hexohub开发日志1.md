---
title: Hexohub开发日志1
date: 2025-08-25 17:10:46
tags:
  - 技术
  - 前端
  - 后端
  - hexo
  - blog
  - Hexo
  - 开发日志
cover: 0.jpg
background: url(0.jpg)
---
{% asset_img 0.jpg  %}
# 关于
{% note info modern %}
[HexoHub](https://github.com/forever218/HexoHub)是款个人开发的项目（桌面应用程序），旨在提供一个一体化的hexo集成可视化面板，优化hexo使用体验。
{% endnote %}
&nbsp; &nbsp; &nbsp; 截至撰文，[HexoHub](https://github.com/forever218/HexoHub)已更新到[v2.2.0](https://github.com/forever218/HexoHub/releases/tag/v2.2.0)。近几个版本的更新主要集中在界面的调整和性能的优化，包括但不限于：  
1. 加入背景自定义，面板透明度可调  
2. 优化顶部窗口栏，使程序界面看起来**无边框**化   
3. 优化日志记录，日志记录更加全面清晰  
4. 优化总体界面布局   
5. 优化程序性能，解决了当程序关闭后，hexohub进程依然存在的问题   
6. （下文将提到）优化应用大小，原本`850MB`缩小至`290MB`

{% asset_img 1.jpg  %}
{% asset_img 2.jpg  %}
{% asset_img 3.jpg  %}
{% asset_img 4.jpg  %}

# Electron应用构建思考
&nbsp; &nbsp; &nbsp; 我的package.json是这样的： 
```json
{
  "name": "nextjs_tailwind_shadcn_ts",
  "version": "2.1.1",
  "description": "HexoHub Desktop Application",
  "author": "Lisiran",
  "private": true,
  "main": "public/electron.js",
  "homepage": "./",
  "scripts": {
    "dev": "next dev",
    "dev:socket": "nodemon --exec \"npx tsx server.ts\" --watch server.ts --watch src --ext ts,tsx,js,jsx 2>&1 | tee dev.log",
    "build": "next build",
    "start": "NODE_ENV=production tsx server.ts 2>&1 | tee server.log",
    "lint": "next lint",
    "db:push": "prisma db push",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:reset": "prisma migrate reset",
    "electron": "npm run build && electron .",
    "electron-dev": "concurrently \"npm run dev\" \"wait-on http://localhost:3000 && electron .\"",
    "package": "electron-builder --dir",
    "make": "electron-builder",
    "publish": "electron-builder --publish"
  },
  "dependencies": {
    "@dnd-kit/core": "^6.3.1",
    "@dnd-kit/sortable": "^10.0.0",
    "@dnd-kit/utilities": "^3.2.2",
    "@hookform/resolvers": "^5.1.1",
    "@mdxeditor/editor": "^3.39.1",
    "@prisma/client": "^6.11.1",
    "@radix-ui/react-accordion": "^1.2.11",
    "@radix-ui/react-alert-dialog": "^1.1.14",
    "@radix-ui/react-aspect-ratio": "^1.1.7",
    "@radix-ui/react-avatar": "^1.1.10",
    "@radix-ui/react-checkbox": "^1.3.2",
    "@radix-ui/react-collapsible": "^1.1.11",
    "@radix-ui/react-context-menu": "^2.2.15",
    "@radix-ui/react-dialog": "^1.1.14",
    "@radix-ui/react-dropdown-menu": "^2.1.15",
    "@radix-ui/react-hover-card": "^1.1.14",
    "@radix-ui/react-label": "^2.1.7",
    "@radix-ui/react-menubar": "^1.1.15",
    "@radix-ui/react-navigation-menu": "^1.2.13",
    "@radix-ui/react-popover": "^1.1.14",
    "@radix-ui/react-progress": "^1.1.7",
    "@radix-ui/react-radio-group": "^1.3.7",
    "@radix-ui/react-scroll-area": "^1.2.9",
    "@radix-ui/react-select": "^2.2.5",
    "@radix-ui/react-separator": "^1.1.7",
    "@radix-ui/react-slider": "^1.3.5",
    "@radix-ui/react-slot": "^1.2.3",
    "@radix-ui/react-switch": "^1.2.5",
    "@radix-ui/react-tabs": "^1.1.12",
    "@radix-ui/react-toast": "^1.2.14",
    "@radix-ui/react-toggle": "^1.1.9",
    "@radix-ui/react-toggle-group": "^1.1.10",
    "@radix-ui/react-tooltip": "^1.2.7",
    "@reactuses/core": "^6.0.5",
    "@tanstack/react-query": "^5.82.0",
    "@tanstack/react-table": "^8.21.3",
    "axios": "^1.10.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "cmdk": "^1.1.1",
    "date-fns": "^4.1.0",
    "embla-carousel-react": "^8.6.0",
    "framer-motion": "^12.23.2",
    "input-otp": "^1.4.2",
    "lucide-react": "^0.525.0",
    "next": "^15.3.5",
    "next-auth": "^4.24.11",
    "next-intl": "^4.3.4",
    "next-themes": "^0.4.6",
    "prisma": "^6.11.1",
    "react": "^19.0.0",
    "react-day-picker": "^9.8.0",
    "react-dom": "^19.0.0",
    "react-hook-form": "^7.60.0",
    "react-markdown": "^10.1.0",
    "react-resizable-panels": "^3.0.3",
    "react-syntax-highlighter": "^15.6.1",
    "recharts": "^3.1.2",
    "remark-breaks": "^4.0.0",
    "remark-gfm": "^4.0.1",
    "sharp": "^0.34.3",
    "socket.io": "^4.8.1",
    "socket.io-client": "^4.8.1",
    "sonner": "^2.0.6",
    "tailwind-merge": "^3.3.1",
    "tailwindcss-animate": "^1.0.7",
    "tslib": "^2.8.1",
    "tsx": "^4.20.3",
    "uuid": "^11.1.0",
    "vaul": "^1.1.2",
    "z-ai-web-dev-sdk": "^0.0.10",
    "zod": "^4.0.2",
    "zustand": "^5.0.6"
  },
  "devDependencies": {
    "@electron-forge/cli": "^7.5.0",
    "@electron-forge/maker-deb": "^7.5.0",
    "@electron-forge/maker-rpm": "^7.5.0",
    "@electron-forge/maker-squirrel": "^7.5.0",
    "@electron-forge/maker-zip": "^7.5.0",
    "@electron-forge/plugin-auto-unpack-natives": "^7.5.0",
    "@eslint/eslintrc": "^3",
    "@tailwindcss/postcss": "^4",
    "@types/node": "^20",
    "@types/react": "^19",
    "@types/react-dom": "^19",
    "concurrently": "^9.2.0",
    "cross-env": "^10.0.0",
    "electron": "37.2.6",
    "electron-builder": "^26.0.12",
    "eslint": "^9",
    "eslint-config-next": "15.3.5",
    "nodemon": "^3.1.10",
    "tailwindcss": "^4",
    "tw-animate-css": "^1.3.5",
    "typescript": "^5",
    "wait-on": "^8.0.4"
  },
  "build": {
    "appId": "com.hexo.desktop",
    "productName": "HexoHub",
    "directories": {
      "output": "dist"
    },
    "files": [
      "out/**/*",
      "public/electron.js",
      "node_modules/**/*"
    ],
    "mac": {
      "category": "public.app-category.productivity",
      "target": [
        {
          "target": "dmg",
          "arch": [
            "x64",
            "arm64"
          ]
        }
      ]
    },
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": [
            "x64"
          ]
        }
      ],
      "icon": "public/icon.ico"
    },
    "linux": {
      "target": [
        {
          "target": "AppImage",
          "arch": [
            "x64"
          ]
        }
      ],
      "icon": "public/icon.png"
    },
    "nsis": {
      "oneClick": false,
      "allowToChangeInstallationDirectory": true
    }
  }
}

```
&nbsp; &nbsp; &nbsp; 完成了界面的设计和功能的搭建后，执行`cnpm run electron`在开发环境下预览，执行`cnpm run build`生成静态发布文件，执行`cnpm run make`构建exe可执行文件（发行版）。
&nbsp; &nbsp; &nbsp; 构建之后，发现安装所需大小为851MB，这么大！尽管我不是专业开发者，但能感觉到这个大小并不正常。当时是第一次构建成功（在此之前，有个全局css样式引入问题我一直解决不了，困扰了相当久——两个星期左右，导致一直构建失败），兴奋着来没管那么多，直接发布了第一个版本，后继的开发也主要在功能和界面的改进上，把这个大小问题暂时放在一边。
&nbsp; &nbsp; &nbsp; 后来功能完善的差不多，碰巧[R1cky](https://blog.ryq.me/)也在这时候提了个与占用空间有关的[issue](https://github.com/forever218/HexoHub/issues/1)：

{% asset_img 5.jpg  %} 
于是重新研究起这个问题。 
首先查看安装后的目录： 
{% asset_img 6.jpg  %}  
可以发现，应用本身、chromium v8浏览器内核、nodejs这些基本的electron应用组件占294MB，在850MB中占比并不大，真正大的是里面的asar文件，体积高达500MB：
{% asset_img 7.jpg %}
{% note info modern %}
**什么是ASAR文件？**
.asar 文件是 Electron 应用特有的一个存档格式，它把应用的源代码（HTML, CSS, JavaScript, 资源文件等）打包成一个单一文件，目的是为了组织和保护代码，防止用户轻易查看和修改，是 Electron 用来封装和分发应用程序源代码的标准容器文件。可以使用以下命令来查看、提取asar文件内容：
```bash
# 安装 asar 工具
npm install -g asar

# 查看 asar 文件内容
asar list app.asar

# 提取 asar 文件到指定目录
asar extract app.asar ./extracted-folder
```
{% endnote %}
查看asar文件发现，里面有相当多的npm包，绝大部分都是package.json里`dependencies`的。当时认为，这是因为我在build字段填入了"node_modules/**/*"导致的：
```json
  "build": {
    "appId": "com.hexo.desktop",
    "productName": "HexoHub",
    "directories": {
      "output": "dist"
    },
    "files": [
      "out/**/*",
      "public/electron.js",
      "node_modules/**/*"  //这里
    ],
```
那接下来的工作就是要优化dependencies，问了问AI，将几个体积比较大的依赖移动到了下面的`devDependencies`中（特别是next，这个玩意相当大）。这些包只在我开发的时候有用，在生产环境并无作用，执行构建时，构建器不会把开发依赖进行打包。下面是我优化后的package.json：
```json
{
  "name": "nextjs_tailwind_shadcn_ts",
  "version": "2.2.0",
  "description": "HexoHub Desktop Application",
  "author": "Lisiran",
  "private": true,
  "main": "public/electron.js",
  "homepage": "./",
  "scripts": {
    "dev": "next dev",
    "dev:socket": "nodemon --exec \"npx tsx server.ts\" --watch server.ts --watch src --ext ts,tsx,js,jsx 2>&1 | tee dev.log",
    "build": "next build",
    "start": "NODE_ENV=production tsx server.ts 2>&1 | tee server.log",
    "lint": "next lint",
    "db:push": "prisma db push",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:reset": "prisma migrate reset",
    "electron": "npm run build && electron .",
    "electron-dev": "concurrently \"npm run dev\" \"wait-on http://localhost:3000 && electron .\"",
    "package": "electron-builder --dir",
    "make": "electron-builder",
    "publish": "electron-builder --publish"
  },
  "dependencies": {
    "@dnd-kit/core": "^6.3.1",
    "@dnd-kit/sortable": "^10.0.0",
    "@dnd-kit/utilities": "^3.2.2",
    "@hookform/resolvers": "^5.1.1",
    "@mdxeditor/editor": "^3.39.1",
    "@radix-ui/react-accordion": "^1.2.11",
    "@radix-ui/react-alert-dialog": "^1.1.14",
    "@radix-ui/react-aspect-ratio": "^1.1.7",
    "@radix-ui/react-avatar": "^1.1.10",
    "@radix-ui/react-checkbox": "^1.3.2",
    "@radix-ui/react-collapsible": "^1.1.11",
    "@radix-ui/react-context-menu": "^2.2.15",
    "@radix-ui/react-dialog": "^1.1.14",
    "@radix-ui/react-dropdown-menu": "^2.1.15",
    "@radix-ui/react-hover-card": "^1.1.14",
    "@radix-ui/react-label": "^2.1.7",
    "@radix-ui/react-menubar": "^1.1.15",
    "@radix-ui/react-navigation-menu": "^1.2.13",
    "@radix-ui/react-popover": "^1.1.14",
    "@radix-ui/react-progress": "^1.1.7",
    "@radix-ui/react-radio-group": "^1.3.7",
    "@radix-ui/react-scroll-area": "^1.2.9",
    "@radix-ui/react-select": "^2.2.5",
    "@radix-ui/react-separator": "^1.1.7",
    "@radix-ui/react-slider": "^1.3.5",
    "@radix-ui/react-slot": "^1.2.3",
    "@radix-ui/react-switch": "^1.2.5",
    "@radix-ui/react-tabs": "^1.1.12",
    "@radix-ui/react-toast": "^1.2.14",
    "@radix-ui/react-toggle": "^1.1.9",
    "@radix-ui/react-toggle-group": "^1.1.10",
    "@radix-ui/react-tooltip": "^1.2.7",
    "@reactuses/core": "^6.0.5",
    "@tanstack/react-query": "^5.82.0",
    "@tanstack/react-table": "^8.21.3",
    "axios": "^1.10.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "cmdk": "^1.1.1",
    "date-fns": "^4.1.0",
    "embla-carousel-react": "^8.6.0",
    "framer-motion": "^12.23.2",
    "input-otp": "^1.4.2",
    "lucide-react": "^0.525.0",
    "react": "^19.0.0",
    "react-day-picker": "^9.8.0",
    "react-dom": "^19.0.0",
    "react-hook-form": "^7.60.0",
    "react-markdown": "^10.1.0",
    "react-resizable-panels": "^3.0.3",
    "react-syntax-highlighter": "^15.6.1",
    "recharts": "^3.1.2",
    "remark-breaks": "^4.0.0",
    "remark-gfm": "^4.0.1",
    "socket.io": "^4.8.1",
    "socket.io-client": "^4.8.1",
    "sonner": "^2.0.6",
    "tailwind-merge": "^3.3.1",
    "uuid": "^11.1.0",
    "vaul": "^1.1.2",
    "zod": "^4.0.2",
    "zustand": "^5.0.6"
  },
  "devDependencies": {
    "@electron-forge/cli": "^7.5.0",
    "@electron-forge/maker-deb": "^7.5.0",
    "@electron-forge/maker-rpm": "^7.5.0",
    "@electron-forge/maker-squirrel": "^7.5.0",
    "@electron-forge/maker-zip": "^7.5.0",
    "@electron-forge/plugin-auto-unpack-natives": "^7.5.0",
    "@eslint/eslintrc": "^3",
    "@tailwindcss/postcss": "^4",
    "@types/node": "^20",
    "@types/react": "^19",
    "@types/react-dom": "^19",
    "concurrently": "^9.2.0",
    "cross-env": "^10.0.0",
    "electron": "37.2.6",
    "electron-builder": "^26.0.12",
    "eslint": "^9",
    "eslint-config-next": "15.3.5",
    "nodemon": "^3.1.10",
    "tailwindcss": "^4",
    "tw-animate-css": "^1.3.5",
    "typescript": "^5",
    "tslib": "^2.8.1",
    "@prisma/client": "^6.11.1",
    "tailwindcss-animate": "^1.0.7",
    "tsx": "^4.20.3",
    "sharp": "^0.34.3",
    "wait-on": "^8.0.4",
    "next": "^15.3.5",
    "next-auth": "^4.24.11",
    "next-intl": "^4.3.4",
    "next-themes": "^0.4.6",
    "prisma": "^6.11.1"
  },
  "build": {
    "appId": "com.hexo.desktop",
    "productName": "HexoHub",
    "directories": {
      "output": "dist"
    },
    "files": [
      "out/**/*",
      "public/electron.js",
      "node_modules/**/*"
    ],
    "mac": {
      "category": "public.app-category.productivity",
      "target": [
        {
          "target": "dmg",
          "arch": [
            "x64",
            "arm64"
          ]
        }
      ]
    },
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": [
            "x64"
          ]
        }
      ],
      "icon": "public/icon.ico"
    },
    "linux": {
      "target": [
        {
          "target": "AppImage",
          "arch": [
            "x64"
          ]
        }
      ],
      "icon": "public/icon.png"
    },
    "nsis": {
      "oneClick": false,
      "allowToChangeInstallationDirectory": true
    }
  }
}

```
相比较初版，将以下这些依赖移动到了开发环境中：
```json
  "next": "^15.3.5",           //  Electron 不需要 Next.js 服务端
  "next-auth": "^4.24.11",     //  同上，属于服务端框架
  "next-intl": "^4.3.4",       //  Next.js 相关的国际化库
  "next-themes": "^0.4.6",     //  虽然叫 themes，但它是 Next.js 的
  "prisma": "^6.11.1",         //  Prisma CLI
  "sharp": "^0.34.3",          // 
  "tsx": "^4.20.3",            //  TypeScript 运行/开发工具
  "tailwindcss-animate": "^1.0.7", 
  "@types/*": "...",           // 所有以 @types/ 开头的包
  "typescript": "..."          // TypeScript 编译器
```

&nbsp; &nbsp; &nbsp; 然后重新进行构建，构建后的安装总体积下降到了500MB，感觉还是不理想。继续问AI，也是一直让我优化依赖，但是后面无论我如何修改生产依赖，如何在"files"字段中对打包文件加以限制（例如使用"!**/node_modules/*"来明确声明排除某些包），体积始终在500MB浮动。
## 转折
&nbsp; &nbsp; &nbsp; 折腾了很久没进展，突然间想到，electron的本质不就是把网页封装成可执行文件吗，所有功能是否已经在`run build`出的静态文件里实现了？如果是这样的话，那为什么还需要这些依赖呢？下面是执行`build`之后产生的静态文件，我认为electron已经将所有功能都封装进那些js文件里了。
```cmd
E:\BLOGTEST\14\HEXOHUB\OUT
│  404.html
│  electron.js
│  favicon.ico
│  icon.ico
│  icon.svg
│  index.html
│  index.txt
│  installer.nsh
│  logo.svg
│  robots.txt
│
├─404
│      index.html
│
└─_next
    ├─static
    │  ├─chunks
    │  │  │  182.89dbb621169a942c.js
    │  │  │  297-7e341dc6da4426c6.js
    │  │  │  308-bc370ace8bbf8006.js
    │  │  │  32e18ead-c6c3328902f2d616.js
    │  │  │  574-2ac505a5b5cd359f.js
    │  │  │  899.b5874afa5657b38d.js
    │  │  │  framework-91a47d5f4e0be608.js
    │  │  │  main-82bc7e31ff7e6e7d.js
    │  │  │  main-app-4ee3c8fd37d90de2.js
    │  │  │  polyfills-42372ed130431b0a.js
    │  │  │  webpack-15e4c3f711e908a0.js
    │  │  │
    │  │  ├─app
    │  │  │  │  error-b428f88d713c9f9c.js
    │  │  │  │  layout-67408bd94e231152.js
    │  │  │  │  not-found-0a2f44452989ced2.js
    │  │  │  │  page-3368876e03233bc2.js
    │  │  │  │
    │  │  │  └─_not-found
    │  │  │          page-744bdc24d9cbb20a.js
    │  │  │
    │  │  └─pages
    │  │          _app-e9fa8edca221553c.js
    │  │          _error-d5d88bbdcf8eb51a.js
    │  │
    │  ├─css
    │  │      516c5aaeed6130a1.css
    │  │
    │  └─ygsDCHyoURBbca-29m7VB
    │          _buildManifest.js
    │          _ssgManifest.js
    │
    └─ygsDCHyoURBbca-29m7VB
```
&nbsp; &nbsp; &nbsp; 真的是豁然开朗的感觉，于是直接去package.json里，把dependencies的内容全部删了重新构建，得到了开头提到的294MB的安装体积：
{% asset_img 4.jpg  %}
&nbsp; &nbsp; &nbsp; 这下舒服了，果然在某些方面，**AI的全力以赴，不如人类的灵机一动**。后来，找DS问了具体的原理：
> 这主要得益于现代前端构建工具（如 Vite、Webpack）和 electron-builder 的高度智能化。前端代码已被“打包内联” (Bundling)：在你运行 npm run build（或类似的构建命令）时，你的构建工具（Vite/Webpack）已经完成了最关键的工作：它将你所有的前端依赖（React, Radix, 各种组件库）和你自己写的代码，全部打包、压缩、Tree-shaken 后，生成了一些静态文件（通常是 index.html, assets/index-xxxxxx.js, assets/index-xxxxxx.css）。这些生成的 .js 和 .css 文件已经包含了所有运行时代码。原始的 node_modules 文件夹在运行时不再被需要。electron-builder 打包的是“构建结果”，而非源代码：在你的 electron-builder 配置中，files 字段很可能指向的是构建输出目录（例如 dist/ 或 build/）。electron-builder 的工作只是：把这个已经包含所有依赖的 dist/ 文件夹、你的主进程代码（main.js）和 Electron 运行时捆绑在一起。它并不需要原始的 node_modules 来执行你的渲染进程代码，因为它只需要最终的构建产物。依赖已被“烘焙”进最终文件：
你可以做一个实验：用文本编辑器打开 dist/assets/index-xxxxxx.js 这个文件。你会看到一大片被压缩的代码，在里面搜索 react、zustand 等关键词，你一定能找到它们。它们已经不再是独立的包，而是被“打碎”后融合进了一个或几个大的 JavaScript 文件中。

&nbsp; &nbsp; &nbsp; 这也证实了我一开始的猜想。当然了，这样的“临时修改”肯定是非正规手段，也是被DS狠狠教育了一番：

{% asset_img 8.jpg  %}
{% asset_img 9.jpg  %}

> 请立即停止“删除-恢复”的想法。
你通过实验证明了一件事：你的前端构建流程是正确的，最终的安装包体积与 dependencies 字段的内容无关。
你现在需要做的唯一工作就是：
坐下来。
仔细审核你的 package.json。
使用 npm install --save-dev 命令将所有开发工具（如 electron, electron-builder, vite, typescript）正确地移动到 devDependencies 中。
删除真正无用的依赖（如单机应用中的 socket.io）

&nbsp; &nbsp; &nbsp; 行吧，过几天再来研究怎么“正确的”删除依赖。前天游泳的时候，跟一哥们在水里泡着聊天聊久了，没运动开，身子也没热，给我冻感冒了，这几天发烧头晕的不行{% inlineimage 10.jpg, height=90px %}
