# 🫓哥手扎 · A股每日复盘与交易计划

手机/浏览器可访问的 A 股复盘小工具：收盘复盘（热点题材、连板梯队+每层核心题材标签）、次日交易计划（同花顺/东方财富/淘股吧三源热榜前20 + 逐股备注）、炫酷分享图（自定义文字水印）、每日 Markdown 归档。

## 功能

| 页面 | 功能 |
|---|---|
| 复盘 | 情绪概览（涨停数/最高板/炸板）、热点题材词频+淘股吧题材榜、连板梯队金字塔（每层核心题材标签）、复盘笔记自动保存 |
| 热榜 | 三源前20：同花顺热榜（含概念/连板标签）/ 东财人气榜 / 淘股吧龙头聚合，一键「+计划」；单源失败自动降级可手动补 |
| 计划 | 类型徽章（打板/低吸/半路/埋伏/观望）、仓位/目标价、备注自动保存、排序 |
| 分享 | 所见即所得长卡片，勾选板块、文字水印（角落/平铺+透明度），导出 PNG（iOS 长按保存 / Web Share） |
| 历史 | 每日归档列表，回看、下载 MD |

## 技术栈

- 后端 Python 3.11+：FastAPI + httpx + SQLite + Jinja2 + qrcode
- 前端：Vue 3 + Vite + TypeScript + Pinia + TailwindCSS v3 + @zumer/snapdom

## 数据来源（公开接口，已实测）

| 数据 | 来源 |
|---|---|
| 连板梯队 | 东财涨停池 `push2ex.eastmoney.com/getTopicZTPool`（连板数/几天几板/封单/炸板） |
| 题材标签 | 同花顺涨停池 `reason_type` 涨停原因（按 `+` 拆词频 = 每层核心题材） |
| 东财热榜 | `emappdata.eastmoney.com/stockrank/getAllCurrentList` + 行情转换 |
| 淘股吧 | `tgb.cn/ticai/getTiCaiList` 题材淘金榜（龙头/中军/日内龙聚合为人气股榜） |
| 同花顺热榜 | `dq.10jqka.com.cn/fuyao/hot_list_data/out/hot_list/v1/stock`，参数 `list_type=normal&type=day`（含热度值/涨跌幅/概念标签/人气标签），已实测开放 |

所有数据落 SQLite，任何源失败不影响整体；梯队/热榜行均支持手动增删改兜底。

## 快速开始

```bash
# 1. 后端
cd backend
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
.venv/bin/uvicorn app.main:app --host 0.0.0.0 --port 8000

# 2. 前端（已提供 dist 可跳过；改动前端才需要）
cd frontend && npm install && npm run build

# 3. 访问
#   电脑: http://localhost:8000
#   手机: 同一 WiFi 扫 http://<局域网IP>:8000 （页面右上角 ◱ 有二维码）
```

开发模式（前端热更新）：`cd frontend && npm run dev`（5173 端口，已配代理到 8000）。

生产也可用 `bash scripts/start.sh` 一键构建+启动。

## 公网部署预留

- 设置环境变量 `API_TOKEN=xxx` 后，所有 `/api` 请求需带 `X-API-Token` 头（前端暂未加口令输入，公网使用建议配 nginx 做Basic Auth 或自行扩展登录页）
- Nginx 反代示例：

```nginx
server {
  listen 443 ssl;
  server_name fupan.example.com;
  location / {
    proxy_pass http://127.0.0.1:8000;
    proxy_set_header Host $host;
  }
}
```

## 目录

```
backend/app    FastAPI 入口、抓取器(eastmoney/ths/taoguba)、API、MD模板
frontend/src   Vue 视图(复盘/热榜/计划/分享/历史)、分享卡片、水印工具
data/          fupan.db、plans/*.md、exports/
```

> 仅供个人复盘研究，数据来自公开接口，不构成投资建议。
