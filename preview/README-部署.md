# 部署说明

## 目录
- `index.html`：页面（引用 ./assets 相对路径）
- `assets/img/covers/`：能力图片，可单独替换
- `VERSION` / `CHANGELOG.md`：版本与变更

## 部署步骤
1. 备份线上 `/product/` 目录；
2. 上传整个包内容到 `/product/`（保持 `assets/` 相对路径不变）；
3. `index.html` 设置 no-cache，`assets/` 设置长缓存；
4. 打开页面核对：能力详情左侧图片、AI阅读轮播、场景应用综合能力卡。

## 回滚
保留上一版目录，发现问题原样切回即可。

## 图片更新
只替换 `assets/img/covers/` 下对应文件，不需要重新发布 HTML。
