# 量潮媒体云命令行工具 qtcloud-media-cli 发布 v0.1.0

一条命令，把 Markdown 文章排好版，直接送进微信公众号草稿箱。

## 背景

写公众号的人写完稿还要耗大量时间调排版：字号、间距、中英文之间的空格、标题样式，一篇稿子写完，排版又去掉小半天。市面上排版工具不少，我们调研后的结论很直接——这件事值得做成自己的基础设施，因为新媒体矩阵的每一篇稿子都要过这一关。

qtcloud-media-cli 就是这个基础设施的第一块：排版引擎加发布通道，一条命令走完。

## 功能

两个命令，对应两种用法：

| 命令 | 做什么 |
|------|--------|
| `qtcloud-media render` | Markdown → 公众号草稿 HTML。只排版，不联网 |
| `qtcloud-media publish` | 排版 → 上传正文图片 → 封面入库 → 推进公众号草稿箱 |

`publish` 不是群发。文章停在草稿箱，到公众号后台确认后再手动群发——发布动作始终留在人手里。

## 使用方法

从 crates.io 安装：

```bash
cargo install qtcloud-media-cli

# 只排版
qtcloud-media render article.md --theme themes/default.toml -o article.html

# 排版并推进公众号草稿箱
export QTCLOUD_MEDIA_APP_ID=wx...
export QTCLOUD_MEDIA_APP_SECRET=...
qtcloud-media publish article.md --title "文章标题" --thumb ./cover.png
```

两个前置条件：公众号账号需要有 API 权限；服务器出口 IP 要加进公众平台后台的 IP 白名单。

源码托管在 GitHub：https://github.com/quanttide/qtcloud-media

## 排版

排版参数全部在一个 TOML 主题文件里：字号、行高、间距、标题、引用、代码、配色。换主题不碰正文一个字——这是我们的基本判断：排版归排版，内容归内容。

渲染产物是逐元素内联样式的 HTML，不含 `<style>` 块与 class。这不是随手实现的——微信草稿箱会清洗样式块，我们在真实公众号上实测过两种输出，混乱与规整的差别就在这里。

## 实测

v0.1.0 在真实公众号上完整跑通：一篇三千字的真实文章从 Markdown 进，到排好版的草稿出，全程零手调样式。中间踩到的坑（微信侧瞬时错误、图片上传的 MIME 声明）都已修复并写进测试。

## 下一步

接下来的重点是草稿质量。今后稿件大多由 AI 写、AI 发，中间难免出现混乱——引用失效、表述跑偏、该核对的细节没核对。发布前的最后一道关仍然在人，工具要做的是把这道关变得省力：草稿进箱之前，先给出一份可核对的评审结果。

欢迎通过 GitHub issue 提交反馈：https://github.com/quanttide/qtcloud-media/issues
