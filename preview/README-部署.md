# 中交 AI 中心能力图谱 · 部署说明

版本：v1.6.10　｜　类型：纯静态站点（HTML + 图片）　｜　依赖：无

## 1. 这个包是什么

- 一个静态页面：`index.html` + `assets/img/covers/` 图片，**不需要 Node、Python 或任何构建步骤**；
- 图片是外置文件，通过 `./assets/img/covers/...` 相对路径引用，**必须和 index.html 一起部署**，只传 HTML 会全部破图；
- 本包**不包含二期数字人模块**（数字人侧栏、收起悬浮窗、人设/提示词配置都不在里面），只含一期能力图谱。

## 2. 目录清单

```
cap-atlas-v1.6.10/
├── index.html          页面入口（约 217 KB）
├── assets/img/covers/  能力截图 82 个文件（jpg，按能力 id 命名，如 p24-1.jpg）
├── README-部署.md      本文件
├── CHANGELOG.md        版本与变更
├── VERSION             版本号与构建时间
└── sha256.txt          逐文件校验值
```

## 3. 部署前准备

1. 备份线上现有目录（例如 `/product/`），至少保留一份可回滚的完整副本；
2. 确认部署目录**可读**，且不允许目录列表暴露（可选）；
3. 若站点有 CDN，发布后需要刷新 CDN 缓存。

## 4. 部署步骤（推荐 B）

### 方式 A：直接覆盖
1. 把包内所有文件上传到 `/product/`，保持 `assets/` 相对目录不变；
2. 确认 `index.html` 与 `assets/` 同级。

### 方式 B：新目录 + 切换（可秒级回滚，推荐）
1. 新建 `/product-v1.6.10/`，上传整包内容；
2. 在 Nginx 里把 `/product/` 的 root/alias 指向新目录，`nginx -s reload`；
3. 验证通过后，保留旧目录至少一周。

## 5. Nginx 参考配置

```nginx
location /product/ {
    alias /data/www/cap-atlas-v1.6.10/;
    index index.html;
    try_files $uri $uri/ /product/index.html;

    # HTML 不缓存，保证发版即时生效
    location = /product/index.html {
        add_header Cache-Control "no-cache, must-revalidate";
    }
    # 图片长缓存（文件名带能力 id 与序号，发版加新文件即可）
    location /product/assets/ {
        expires 30d;
        add_header Cache-Control "public, max-age=2592000";
    }
}
```

## 6. 部署后自检清单

- [ ] 打开 `/product/`，页面正常渲染，无白屏；
- [ ] 控制台 Network 里图片全部 200，没有 404；
- [ ] 任选“商机罗盘”“本体中心”，展开后左侧出现图片和 1/3、2/3 页码，可左右切换；
- [ ] 点击图片能放大，Esc 可关闭；
- [ ] “训练中心”按钮显示“打开训推平台”，新窗口打开；
- [ ] “模型中心”和“Token统一大市场”按钮都显示“打开模型中心”；
- [ ] 场景应用页能看到 7 个场景卡 + 2 个综合能力卡，综合能力里“定价策略”为“项目定制”；
- [ ] 手机/窄屏（<900px）下图片与文字上下排列，不横向溢出。

## 7. 回滚

- 方式 B：把 Nginx 指回旧目录，reload 即可；
- 方式 A：用备份目录整体覆盖回来；
- 回滚不需要动数据库或后端，纯静态文件。

## 8. 常见问题

| 现象 | 原因 | 处理 |
|---|---|---|
| 图片全破 | 只传了 index.html，或 `assets/` 路径变了 | 保持 `index.html` 与 `assets/` 同级 |
| 发版后仍是旧页面 | 浏览器/CDN 缓存 | 强制刷新，或按第 5 节设置 HTML 不缓存 |
| 部分图片 404 | 文件名大小写不一致（Linux 区分大小写） | 按 `sha256.txt` 比对文件名 |
| 页面样式错乱 | 部署在子路径但访问路径不一致 | 本包全部相对路径，需整目录一起访问 |

## 9. 本次更新内容

见同目录 `CHANGELOG.md`，摘要：新增 30+ 产品截图；入口按钮去掉“官网 / 试用入口”和“↗”；训练中心改“打开训推平台”；模型中心与 Token 改“打开模型中心”；“系统智能体 / PageAgent”统一为“交融浏览器助理”；综合能力定价改为“项目定制”。

## 10. 联系

- 页面内容与文案：产品侧（陈曦 / 各产品负责人）
- 构建与打包：DSH 工作区 `cap-atlas` 仓库，标签 v1.6.10
