# MEMORY.md - QQ 通道长期记忆

> 这份记忆从主 workspace 同步，供 QQ agent 使用。

---

## 老板信息

- **名字:** jam james
- **称呼:** 老板 (boss)
- **时区:** GMT+8 (北京时区)
- **工作组:** Telegram (-1003867881577)
- **QQ富媒体能力:** QQ支持图片/文件/语音/视频收发；发送图片使用 `<qqimg>`，文件用 `<qqfile>`，语音用 `<qqvoice>`，视频用 `<qqvideo>`；富媒体优先走标签能力
- **Heartbeat偏好:** 没有实质进展时不要发 HEARTBEAT_OK；默认静默，只有有实质新增再发

---

## OpenClaw 基础设施 / 通道架构

- **当前形态:** 云服务器主控 + 台式机节点协同 + 多通道接入
- **关键机器:**
  - 云服务器：`VM-0-10-opencloudos` (100.75.128.55)
  - 台式机：`XTZJ-20220626AI` (100.79.239.80)
  - 笔记本：`yy-thinkpad-x1-carbon-4th` (192.168.199.103 / 100.121.213.11)
- **关键通道:** Telegram / QQ Bot / 飞书
- **QQ 通道默认模型:** `minimax/MiniMax-M2.7`
- **当前版本:** 2026.4.2（笔记本）

---

## Young Y 海外内容推广体系

- **品牌名:** Young Y
- **主题:** 英语区内容推广 / 海外数字产品增长
- **当前主线平台:** X / Twitter / Medium / Pinterest
- **核心方向:** simpler Notion templates
- **主要产品类型:** budgeting、trading journals、content workflows
- **营销外链统一优先挂 Gumroad**

---

## 🔴 强制规则

### 新产品开发流程
1. 市场调研（Gumroad、Notion Marketplace 搜索同类产品）
2. 亮点可行性评估（AI 生成难易度）
3. 按难易度排序：先做简单的，后做复杂的

### 数据可信度规则
- Brave Ask 结果只能参考，不能作为最终决策依据
- 最终决策必须基于：一手数据 > 官方 API > 权威评测 > Brave Ask

### 交易操作同步规则（强制）
任何交易操作（买入/卖出/清仓），必须立刻执行三步：
1. 更新持仓数据库（调用 API）
2. 记录交易流水（写入 trades_YYYYMMDD.md）
3. 回复确认

### 投研时间规则
所有量化/股票相关时间一律使用北京时间（GMT+8）。

---

## 量化系统与投研团队

- **当前形态:** 两套独立投研团队并行
  - 笔记本团队：Tushare 直连 + 微信通道
  - 云服务器团队：量化系统 API + Telegram 通道
- **核心结构:** researcher / analyst / risk_officer / strategist / reporter / execution_officer
- **当前持仓:** 空仓（2026-03-17 全部清仓）
- ⚠️ 交易操作必须同步数据库 + 记录流水 + 回复确认

---

## 待办

- [ ] Reddit 养号（台式机浏览器+VPN）
- [ ] Git 同步配置（台式机 clone）
- [ ] Node 加固（计划任务重启策略）
- [ ] 把 OpenClaw 加入 360 白名单

---

## OpenClaw 语音控制

- 已落地链路：Telegram 语音 → OpenClaw audio → 本地 ASR (127.0.0.1:8765) → transcript
- 当前默认模型：`base`（CPU 推理）

---

*最后更新: 2026-04-05*

---

## 笔记本爬虫与浏览器自动化工具（2026-04-05 装完）

| 工具 | 版本 | 状态 |
|------|------|------|
| Playwright (npm) | 1.59.1 | ✅ |
| Chromium Headless Shell | 147.0.7727 | ✅ |
| Selenium | 4.41.0 | ✅ |
| httpx | 0.28.1 | ✅ |
| aiohttp | 3.13.5 | ✅ |
| scrapling | 0.4.4 | ✅ |
| crawl4ai | 0.8.6 | ✅ |
| yt-dlp | 2026.3.17 | ✅ |
| instaprocess | 4.15.1 | ✅ |
| LightPanda | 0.2.8 | ✅ |

---

## 搜索 API Keys（笔记本已配，存于 ~/.openclaw/openclaw.json，不在 git 中）

| 工具 | Key 存放位置 |
|------|-------------|
| Brave Search | `~/.openclaw/openclaw.json` → `plugins.entries.brave.config.webSearch.apiKey` |
| Tavily | `~/.openclaw/openclaw.json` → `plugins.entries.tavily.config.webSearch.apiKey` |
| Baidu Search | `~/.openclaw/openclaw.json` → `env.BAIDU_API_KEY` |

### 搜索调用方法（直接用，不要走 skill 脚本）

**Brave Search（英文/国际）：**
```python
import httpx
resp = httpx.get("https://api.search.brave.com/res/v1/web/search",
    params={"q": "关键词"},
    headers={"Accept": "application/json", "X-Subscription-Token": "<Brave Key>"},
    timeout=10)
results = resp.json().get('web', {}).get('results', [])
```

**Tavily Search（英文/综合）：**
```python
from tavily import TavilyClient
t = TavilyClient(api_key="<Tavily Key>")
r = t.search("关键词")
results = r.get('results', [])
```

**Baidu Search（中文/国内）：**
```python
import requests
api_key = '<Baidu Key>'
resp = requests.post('https://qianfan.baidubce.com/v2/ai_search/web_search',
    headers={'Authorization': f'Bearer {api_key}', 'X-Appbuilder-From': 'openclaw', 'Content-Type': 'application/json'},
    json={'messages': [{'content': '关键词', 'role': 'user'}],
          'resource_type_filter': [{'type': 'web', 'top_k': 10}],
          'search_source': 'baidu_search_v2'},
    timeout=10)
refs = resp.json().get('references', [])
```

### 其他工具调用

**LightPanda（JS渲染页面抓取）：**
```bash
~/.local/bin/lightpanda fetch --dump html --wait-ms 3000 https://example.com
```

**scrapling（反爬 + JS渲染）：**
```python
from scrapling import Scrapling
page = Scrapling(url="https://example.com")
content = page.content
```

**crawl4ai（JS渲染页面）：**
```python
import asyncio
from crawl4ai import AsyncWebCrawler
async def crawl(url):
    async with AsyncWebCrawler() as crawler:
        result = await crawler.arun(url=url)
        return result.markdown
```

**yt-dlp（视频/音频下载）：**
```bash
yt-dlp "URL"
```

**instaloader（Instagram 下载）：**
```bash
instaloader --dirname=./downloads profile_or_hashtag
```

---

*最后更新: 2026-04-05*
