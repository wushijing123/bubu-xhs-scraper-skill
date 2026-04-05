---
name: bubu-xhs-scraper-skill
description: 多平台社交媒体数据抓取工具，基于 TikHub API。支持小红书、抖音、TikTok、Bilibili、微博、YouTube、微信公众号、视频号、Twitter/X。触发词：「小红书」「抖音」「TikTok」「B站」「bilibili」「微博」「YouTube」「公众号」「视频号」「Twitter」「X平台」「抓取数据」「获取用户」「搜索」「评论」「笔记」「视频」。只要用户提到这些平台 + 数据需求，立即触发此 skill。
version: 1.4.0
metadata:
  openclaw:
    requires:
      env:
        - TIKHUB_API_KEY
      bins:
        - python3
    primaryEnv: TIKHUB_API_KEY
    emoji: 🔍
    homepage: https://github.com/wushijing123/bubu-xhs-scraper-skill
    setup:
      command: pip3 install httpx
  state_file: .state.json
---

# 多平台数据抓取 Skill（TikHub API）

## 概述

通过 TikHub API 直接 HTTP 调用抓取多平台数据。API key 存储在环境变量 `TIKHUB_API_KEY` 中，**无需用户提供**。

> **重要**：不要使用 `tikhub` Python SDK，它的端点已过时。直接用 `httpx` 调用 API。

## 支持平台一览

| 平台 | 用户信息 | 内容列表 | 内容详情 | 评论 | 搜索 | 热搜 |
|------|---------|---------|---------|------|------|------|
| 小红书 | ✅ | ✅ | ✅ | ✅ | ✅ | — |
| 抖音 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| TikTok | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Bilibili | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| 微博 | ✅ | ✅ | ✅ | — | ✅ | ✅ |
| 微信公众号 | — | ✅ | ✅ | ✅ | — | — |
| 微信视频号 | ✅ | — | ✅ | ✅ | ✅ | ✅ |
| Twitter / X | ✅ | ✅ | — | — | ✅ | — |
| YouTube | — | — | — | — | ✅ | — |

## 安装依赖

```bash
pip3 install httpx
```

## 标准代码模板

```python
import asyncio, os, httpx

BASE_URL = "https://api.tikhub.io"
HEADERS = {"Authorization": f"Bearer {os.environ.get('TIKHUB_API_KEY')}"}

async def api_get(endpoint: str, params: dict) -> dict:
    async with httpx.AsyncClient() as client:
        r = await client.get(f"{BASE_URL}{endpoint}", params=params, headers=HEADERS, timeout=30)
        r.raise_for_status()
        return r.json()
```

---

## 平台一：小红书（Xiaohongshu）

> **端点优先级**：`app_v2`（主力）→ `app/`（降级备用）。搜索用户优先用 `web/search_users`（实测最稳定）。

### App V2 端点（主力）

| 功能 | 端点 | 主要参数 |
|------|------|---------|
| 获取用户信息 | `GET /api/v1/xiaohongshu/app_v2/get_user_info` | `user_id` |
| 获取用户发布笔记 | `GET /api/v1/xiaohongshu/app_v2/get_user_posted_notes` | `user_id`, `cursor` |
| 获取用户收藏笔记 | `GET /api/v1/xiaohongshu/app_v2/get_user_faved_notes` | `user_id`, `cursor` |
| 获取图文笔记详情 | `GET /api/v1/xiaohongshu/app_v2/get_image_note_detail` | `note_id` |
| 获取视频笔记详情 | `GET /api/v1/xiaohongshu/app_v2/get_video_note_detail` | `note_id` |
| 获取混合笔记详情 | `GET /api/v1/xiaohongshu/app_v2/get_mixed_note_detail` | `note_id` 或 `share_text` |
| 获取笔记评论 | `GET /api/v1/xiaohongshu/app_v2/get_note_comments` | `note_id`, `cursor` |
| 获取评论子回复 | `GET /api/v1/xiaohongshu/app_v2/get_note_sub_comments` | `note_id`, `comment_id`, `cursor` |
| 搜索笔记 | `GET /api/v1/xiaohongshu/app_v2/search_notes` | `keyword`, `cursor` |
| 搜索用户⚠️ | `GET /api/v1/xiaohongshu/app_v2/search_users` | `keyword`, `page`, `search_id`（翻页用首次返回的search_id） |
| 搜索图片 | `GET /api/v1/xiaohongshu/app_v2/search_images` | `keyword`, `cursor` |
| 搜索商品 | `GET /api/v1/xiaohongshu/app_v2/search_products` | `keyword`, `cursor` |
| 搜索群组 | `GET /api/v1/xiaohongshu/app_v2/search_groups` | `keyword`, `cursor` |
| 获取话题信息 | `GET /api/v1/xiaohongshu/app_v2/get_topic_info` | `topic_id` |
| 获取话题 Feed | `GET /api/v1/xiaohongshu/app_v2/get_topic_feed` | `topic_id`, `cursor` |
| 获取商品详情 | `GET /api/v1/xiaohongshu/app_v2/get_product_detail` | `product_id` |
| 获取商品评测概览 | `GET /api/v1/xiaohongshu/app_v2/get_product_review_overview` | `product_id` |
| 获取商品评测列表 | `GET /api/v1/xiaohongshu/app_v2/get_product_reviews` | `product_id`, `cursor` |
| 获取商品推荐 | `GET /api/v1/xiaohongshu/app_v2/get_product_recommendations` | `product_id` |
| 创作者灵感 Feed | `GET /api/v1/xiaohongshu/app_v2/get_creator_inspiration_feed` | `cursor` |
| 创作者热门灵感 Feed | `GET /api/v1/xiaohongshu/app_v2/get_creator_hot_inspiration_feed` | `cursor` |

> ⚠️ `app_v2/search_users` 容易返回 400，**搜索用户请优先用 `web/search_users`**（见下方翻页示例）。

### App V1 端点（降级备用）

| 功能 | 端点 | 主要参数 |
|------|------|---------|
| 获取用户信息 | `GET /api/v1/xiaohongshu/app/get_user_info` | `user_id` |
| 获取用户笔记 | `GET /api/v1/xiaohongshu/app/get_user_notes` | `user_id`, `cursor` |
| 获取笔记详情 | `GET /api/v1/xiaohongshu/app/get_note_info` | `note_id` 或 `share_text` |
| 获取视频笔记详情 | `GET /api/v1/xiaohongshu/app/get_video_note_info` | `note_id` |
| 获取笔记评论 | `GET /api/v1/xiaohongshu/app/get_note_comments` | `note_id` |
| 获取评论子回复 | `GET /api/v1/xiaohongshu/app/get_sub_comments` | `note_id`, `comment_id` |
| 搜索笔记 | `GET /api/v1/xiaohongshu/app/search_notes` | `keyword`, `page` |
| 按话题获取笔记 | `GET /api/v1/xiaohongshu/app/get_notes_by_topic` | `page_id`(话题ID), `first_load_time`(毫秒时间戳), `sort`(hot/time/trend), `last_note_id`, `last_note_ct`, `cursor_score` |
| 通过分享链接解析用户ID | `GET /api/v1/xiaohongshu/app/get_user_id_and_xsec_token` | `share_link` |
| 解析笔记分享链接 | `GET /api/v1/xiaohongshu/app/extract_share_info` | `share_text` |

### Web V2 端点（粉丝/关注列表 + 热榜）

| 功能 | 端点 | 主要参数 |
|------|------|---------|
| 获取用户粉丝列表 | `GET /api/v1/xiaohongshu/web_v2/fetch_follower_list` | `user_id`, `cursor`（需目标用户未设私密） |
| 获取用户关注列表 | `GET /api/v1/xiaohongshu/web_v2/fetch_following_list` | `user_id`, `cursor` |
| 获取热榜 | `GET /api/v1/xiaohongshu/web_v2/fetch_hot_list` | 无参数 |
| 搜索笔记 | `GET /api/v1/xiaohongshu/web_v2/fetch_search_notes` | `keywords`, `page` |
| 搜索用户（备用） | `GET /api/v1/xiaohongshu/web_v2/fetch_search_users` | `keywords`, `page` |
| 获取笔记评论 | `GET /api/v1/xiaohongshu/web_v2/fetch_note_comments` | `note_id` |
| 获取子评论 | `GET /api/v1/xiaohongshu/web_v2/fetch_sub_comments` | `note_id`, `comment_id` |
| 获取笔记图片 | `GET /api/v1/xiaohongshu/web_v2/fetch_note_image` | `note_id` |
| 获取用户橱窗商品 | `GET /api/v1/xiaohongshu/web_v2/fetch_product_list` | `user_id` |
| 获取用户信息 | `GET /api/v1/xiaohongshu/web_v2/fetch_user_info` | `user_id` |
| 获取首页推荐 | `GET /api/v1/xiaohongshu/web_v2/fetch_home_notes` | `cursor` |

### 搜索用户并完整翻页（修复21条限制）

> **根因**：`web/search_users` 每页返回20条，响应里没有 `has_more` 字段，需靠返回数量判断是否继续翻页。`app_v2/search_users` 目前容易400，不可靠。

```python
import asyncio, os, httpx

BASE_URL = "https://api.tikhub.io"
HEADERS = {"Authorization": f"Bearer {os.environ.get('TIKHUB_API_KEY')}"}

async def api_get(endpoint: str, params: dict) -> dict:
    async with httpx.AsyncClient() as client:
        r = await client.get(f"{BASE_URL}{endpoint}", params=params, headers=HEADERS, timeout=30)
        r.raise_for_status()
        return r.json()

async def search_xhs_users(keyword: str, max_pages: int = 10) -> list:
    """搜索小红书用户，web/search_users page 翻页（每页20条，无 has_more，靠数量判断）"""
    all_users = []
    page_size = 20  # web 端点固定每页返回约20条

    for page in range(1, max_pages + 1):
        result = await api_get("/api/v1/xiaohongshu/web/search_users",
                               {"keyword": keyword, "page": page})
        inner = result.get("data", {}).get("data", {})
        users = inner.get("users", [])
        all_users.extend(users)
        # 返回数量不足一页说明已到末尾
        if len(users) < page_size:
            break
        await asyncio.sleep(0.5)

    return all_users

# 使用示例
users = asyncio.run(search_xhs_users("AI自媒体", max_pages=10))
print(f"共抓取 {len(users)} 个用户")
for u in users[:5]:
    print(u.get("name"), u.get("id"), u.get("sub_title"))
```
---

### 获取用户所有笔记（完整翻页）

> **主力**：`web/get_user_notes_v2`（稳定）。`app_v2/get_user_posted_notes` 频繁返回400，勿用。
>
> **分页游标位置（坑）**：游标不在响应顶层 `cursor`，而是在**每个 note 对象里的 `cursor` 字段**。翻页时取 `notes[-1]["cursor"]` 传入 `lastCursor`。
>
> **数据路径（坑）**：`data.data.data.notes`，不是 `data.data.notes`。
>
> **超时**：API 有概率 ReadTimeout，每次请求加 `timeout=60` + 重试2次。

```python
async def get_user_all_notes(user_id: str, max_pages: int = 50) -> list:
    \"\"\"获取用户全部发布笔记（web/get_user_notes_v2，稳定翻页）\"\"\"
    all_notes = []
    last_cursor = None
    page = 0

    async with httpx.AsyncClient() as client:
        while page < max_pages:
            params = {"user_id": user_id}
            if last_cursor:
                params["lastCursor"] = last_cursor

            for retry in range(3):
                try:
                    r = await client.get(
                        f"{BASE_URL}/api/v1/xiaohongshu/web/get_user_notes_v2",
                        params=params, headers=HEADERS, timeout=60
                    )
                    r.raise_for_status()
                    data = r.json()
                    break
                except (httpx.ReadTimeout, httpx.ConnectError) as e:
                    if retry == 2:
                        print(f"Page {page+1} 超时放弃，已抓 {len(all_notes)} 条")
                        return all_notes
                    await asyncio.sleep(2)

            # 数据路径: data.data.data.notes
            d = data.get("data", {})
            inner = d.get("data", {})
            notes = inner.get("notes", [])
            has_more = inner.get("has_more", False)
            # 游标在 note 对象里，不在顶层
            new_cursor = notes[-1].get("cursor") if notes else None

            all_notes.extend(notes)
            page += 1
            print(f"Page {page}: +{len(notes)} 条, has_more={has_more}")

            if not has_more or not new_cursor:
                break
            last_cursor = new_cursor
            await asyncio.sleep(0.5)

    return all_notes
```

> **点赞数说明**：`web/get_user_notes_v2` 返回列表不含 `liked_count`，需单独调 `get_note_info` 获取（$0.01/条）。

---

## 平台二：抖音（Douyin）

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/douyin/web/handler_user_profile` | `unique_id` 或 `sec_user_id` |
| 获取用户视频列表 | `GET /api/v1/douyin/web/fetch_user_post_videos` | `sec_user_id`, `max_cursor` |
| 获取单条视频 | `GET /api/v1/douyin/web/fetch_one_video` | `aweme_id` |
| 通过分享链接获取视频 | `GET /api/v1/douyin/web/fetch_one_video_by_share_url` | `share_url` |
| 获取视频评论 | `GET /api/v1/douyin/web/fetch_video_comments` | `aweme_id`, `cursor` |
| 搜索用户 | `GET /api/v1/douyin/web/fetch_user_search_result` | `keyword`, `cursor` |
| 搜索视频 | `GET /api/v1/douyin/web/fetch_video_search_result` | `keyword`, `cursor` |
| 热搜榜 | `GET /api/v1/douyin/web/fetch_hot_search_result` | 无 |

### 备用端点（App 版，Web 失败时用）

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/douyin/app/v3/handler_user_profile` | `sec_user_id` |
| 获取用户视频 | `GET /api/v1/douyin/app/v3/fetch_user_post_videos` | `sec_user_id`, `max_cursor` |
| 获取单条视频 | `GET /api/v1/douyin/app/v3/fetch_one_video` | `aweme_id` |
| 通过分享链接获取视频 | `GET /api/v1/douyin/app/v3/fetch_one_video_by_share_url` | `share_url` |
| 搜索视频 | `GET /api/v1/douyin/app/v3/fetch_video_search_result` | `keyword`, `cursor` |

### 响应要点

- `unique_id` = 抖音号（@xxx），`sec_user_id` = URL 中的长 ID
- 视频列表翻页用 `max_cursor`（不是 `cursor`）
- 从 URL 提取 `aweme_id`：`douyin.com/video/{aweme_id}`

### ID 提取

```
sec_user_id：https://www.douyin.com/user/{sec_user_id}
aweme_id：https://www.douyin.com/video/{aweme_id}
```

---

## 平台三：TikTok

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/tiktok/web/fetch_user_profile` | `uniqueId` |
| 获取用户视频 | `GET /api/v1/tiktok/web/fetch_user_post` | `secUid`, `cursor`, `count` |
| 获取视频详情 | `GET /api/v1/tiktok/web/fetch_post_detail` | `itemId` |
| 通过分享链接获取视频 | `GET /api/v1/tiktok/app/v3/fetch_one_video_by_share_url` | `share_url` |
| 获取视频评论 | `GET /api/v1/tiktok/web/fetch_post_comment` | `aweme_id`, `cursor` |
| 搜索用户 | `GET /api/v1/tiktok/web/fetch_search_user` | `keyword`, `cursor` |
| 搜索视频 | `GET /api/v1/tiktok/web/fetch_search_video` | `keyword`, `count`, `offset` |
| 热门话题 | `GET /api/v1/tiktok/web/fetch_trending_searchwords` | 无 |

### 备用端点（App 版，Web 失败时用）

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/tiktok/app/v3/handler_user_profile` | `sec_user_id` |
| 获取用户视频 | `GET /api/v1/tiktok/app/v3/fetch_user_post_videos` | `sec_user_id`, `max_cursor` |
| 获取单条视频 | `GET /api/v1/tiktok/app/v3/fetch_one_video` | `aweme_id` |
| 搜索用户 | `GET /api/v1/tiktok/app/v3/fetch_user_search_result` | `keyword`, `cursor` |
| 搜索视频 | `GET /api/v1/tiktok/app/v3/fetch_video_search_result` | `keyword`, `cursor` |

### 响应要点

- `uniqueId` = @用户名（不含@），`secUid` = URL 中的长 ID
- 先用 `uniqueId` 调用 `fetch_user_profile` 拿到 `secUid`，再翻页获取视频
- 视频列表翻页用 `cursor`

### ID 提取

```
uniqueId：https://www.tiktok.com/@{uniqueId}
itemId：https://www.tiktok.com/@xxx/video/{itemId}
```

---

## 平台四：Bilibili

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/bilibili/web/fetch_user_profile` | `uid` |
| 获取用户视频 | `GET /api/v1/bilibili/web/fetch_user_post_videos` | `uid`, `page`, `pagesize` |
| 获取视频详情 | `GET /api/v1/bilibili/web/fetch_one_video` | `bvid` 或 `aid` |
| 获取视频详情 v2 | `GET /api/v1/bilibili/web/fetch_video_detail` | `bvid` |
| 获取视频评论 | `GET /api/v1/bilibili/web/fetch_video_comments` | `bvid`, `page` |
| 综合搜索 | `GET /api/v1/bilibili/web/fetch_general_search` | `keyword`, `page` |
| 热搜 | `GET /api/v1/bilibili/web/fetch_hot_search` | 无 |

### 备用端点（App 版，Web 失败时用）

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户信息 | `GET /api/v1/bilibili/app/fetch_user_info` | `uid` |
| 获取用户视频 | `GET /api/v1/bilibili/app/fetch_user_videos` | `uid`, `page` |
| 获取视频详情 | `GET /api/v1/bilibili/app/fetch_one_video` | `bvid` |
| 获取视频评论 | `GET /api/v1/bilibili/app/fetch_video_comments` | `bvid`, `page` |
| 综合搜索 | `GET /api/v1/bilibili/app/fetch_search_all` | `keyword`, `page` |

### 响应要点

- `uid` = 数字用户 ID
- `bvid` = BV 号（如 `BV1xx411c7mD`），`aid` = AV 号（数字）
- 视频列表翻页用 `page`（从 1 开始），`pagesize` 默认 30

### ID 提取

```
uid：https://space.bilibili.com/{uid}
bvid：https://www.bilibili.com/video/{bvid}
```

---

## 平台五：微博（Weibo）

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户信息 | `GET /api/v1/weibo/app/fetch_user_info` | `uid` 或 `screen_name` |
| 获取用户微博 | `GET /api/v1/weibo/web/fetch_user_posts` | `uid`, `page` |
| 获取用户视频 | `GET /api/v1/weibo/web_v2/fetch_user_video_list` | `uid`, `page` |
| 搜索（综合） | `GET /api/v1/weibo/web/fetch_search` | `keyword`, `page` |
| 搜索用户 | `GET /api/v1/weibo/web_v2/fetch_user_search` | `keyword`, `page` |
| 搜索视频 | `GET /api/v1/weibo/web_v2/fetch_video_search` | `keyword`, `page` |
| 热搜榜 | `GET /api/v1/weibo/app/fetch_hot_search` | 无 |
| AI 热点搜索 | `GET /api/v1/weibo/web_v2/fetch_ai_search` | `keyword` |
| 高级搜索 | `GET /api/v1/weibo/web_v2/fetch_advanced_search` | `keyword`, `page` |

### 响应要点

- `uid` = 数字 ID，`screen_name` = 微博昵称
- 翻页用 `page`（从 1 开始）

### ID 提取

```
uid：https://weibo.com/u/{uid}
     https://weibo.com/{screen_name}（昵称直接用）
```

---

## 平台六：YouTube

> ⚠️ **目前仅支持搜索**，暂无频道详情或视频详情端点。

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 综合搜索 | `GET /api/v1/youtube/web_v2/get_general_search` | `keyword` |
| 搜索频道 | `GET /api/v1/youtube/web_v2/search_channels` | `keyword` |
| 搜索视频 | `GET /api/v1/youtube/web/search_video` | `keyword` |
| 搜索 Shorts | `GET /api/v1/youtube/web_v2/get_shorts_search` | `keyword` |
| 搜索建议词 | `GET /api/v1/youtube/web_v2/get_search_suggestions` | `keyword` |

---

## 平台七：微信公众号（WeChat MP）

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取文章详情（JSON） | `GET /api/v1/wechat_mp/web/fetch_mp_article_detail_json` | `url` |
| 获取文章详情（HTML） | `GET /api/v1/wechat_mp/web/fetch_mp_article_detail_html` | `url` |
| 获取公众号文章列表 | `GET /api/v1/wechat_mp/web/fetch_mp_article_list` | `url` 或 `biz` |
| 获取文章评论 | `GET /api/v1/wechat_mp/web/fetch_mp_article_comment_list` | `url` |
| 获取文章阅读数 | `GET /api/v1/wechat_mp/web/fetch_mp_article_read_count` | `url` |
| 获取相关文章 | `GET /api/v1/wechat_mp/web/fetch_mp_related_articles` | `url` |
| 获取文章广告 | `GET /api/v1/wechat_mp/web/fetch_mp_article_ad` | `url` |

### 响应要点

- 所有端点主要参数为文章 `url`（`mp.weixin.qq.com/s/...`）
- `biz` = 公众号唯一标识，从文章 URL 中提取：`__biz=MzI...`

---

## 平台八：微信视频号（WeChat Channels）

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 搜索创作者 | `GET /api/v1/wechat_channels/fetch_user_search` | `keyword` |
| 搜索创作者 v2 | `GET /api/v1/wechat_channels/fetch_user_search_v2` | `keyword` |
| 综合搜索 | `GET /api/v1/wechat_channels/fetch_default_search` | `keyword` |
| 最新内容搜索 | `GET /api/v1/wechat_channels/fetch_search_latest` | `keyword` |
| 普通内容搜索 | `GET /api/v1/wechat_channels/fetch_search_ordinary` | `keyword` |
| 获取视频详情 | `GET /api/v1/wechat_channels/fetch_video_detail` | `video_id` 或 `url` |
| 获取视频评论 | `GET /api/v1/wechat_channels/fetch_comments` | `video_id` |
| 主页内容 | `GET /api/v1/wechat_channels/fetch_home_page` | `username` |
| 热词 | `GET /api/v1/wechat_channels/fetch_hot_words` | 无 |
| 直播历史 | `GET /api/v1/wechat_channels/fetch_live_history` | `username` |

---

## 平台九：Twitter / X

### 端点列表

| 功能 | 端点 | 主要参数 |
|------|------|---------|\
| 获取用户主页 | `GET /api/v1/twitter/web/fetch_user_profile` | `screen_name` 或 `user_id` |
| 获取用户推文 | `GET /api/v1/twitter/web/fetch_user_post_tweet` | `user_id`, `cursor` |
| 搜索推文 | `GET /api/v1/twitter/web/fetch_search_timeline` | `keyword`, `cursor` |

### 响应要点

- `screen_name` = @用户名（不含@），`user_id` = 数字 ID
- 先用 `screen_name` 调 `fetch_user_profile` 获取 `user_id`，再翻页

### ID 提取

```
screen_name：https://twitter.com/{screen_name}
             https://x.com/{screen_name}
```

---

## 批量翻页通用模板

```python
async def fetch_all_pages(endpoint, base_params, cursor_key="cursor", data_key="list"):
    """通用翻页抓取"""
    all_items = []
    cursor = None
    while True:
        params = {**base_params}
        if cursor:
            params[cursor_key] = cursor
        result = await api_get(endpoint, params)
        inner = result.get("data", {}).get("data", {})
        items = inner.get(data_key, [])
        all_items.extend(items)
        has_more = inner.get("has_more", False)
        cursor = inner.get(cursor_key)
        if not has_more or not cursor:
            break
        await asyncio.sleep(0.5)
    return all_items
```

> ⚠️ cursor_key 差异：小红书/TikTok/Twitter 用 `cursor`，抖音用 `max_cursor`，Bilibili/微博用 `page`（数字递增）

---

## 常见问题

| 报错 | 可能原因 | 解决方法 |
|------|---------|---------|
| `400` | 端点需要额外参数或内容已删除 | 换分享链接方式，或换 v2/v3 备用端点 |
| `401` | API Key 未配置 | 检查 `TIKHUB_API_KEY` 环境变量 |
| `429` | 限流 | 每次请求间加 `await asyncio.sleep(0.5~1)` |
| 超时 | VPN/代理冲突 | 切换 VPN 状态，`api.tikhub.io` 可能需要直连 |

## API 基础信息

- **Base URL**: `https://api.tikhub.io`（国内备用：`https://api.tikhub.dev`）
- **鉴权**: `Authorization: Bearer {TIKHUB_API_KEY}`
- **定价**: 大多数小红书端点 $0.01/次（详见下方费用速查表）
- **缓存**: 成功请求缓存 24 小时，重复请求不额外计费
---

## 费用管理

### 查询余额与今日消耗

```python
import asyncio, os, httpx, json
from datetime import datetime

BASE_URL = "https://api.tikhub.io"
HEADERS = {"Authorization": f"Bearer {os.environ.get('TIKHUB_API_KEY')}"}

async def check_balance():
    """查询账户余额与今日消耗，并自动更新本地状态文件（需要 API Key 有 /api/v1/tikhub/user/ 权限）"""
    import json, os, datetime
    state_file = os.path.join(os.path.dirname(__file__), ".state.json")

    async with httpx.AsyncClient() as client:
        # 余额
        r1 = await client.get(f"{BASE_URL}/api/v1/tikhub/user/get_user_info",
                             headers=HEADERS, timeout=30)
        user_data = r1.json().get("user_data", {})
        balance = user_data.get("balance", 0)
        free_credit = user_data.get("free_credit", 0)
        # 今日消耗
        r2 = await client.get(f"{BASE_URL}/api/v1/tikhub/user/get_user_daily_usage",
                             headers=HEADERS, timeout=30)
        daily = r2.json().get("data", {})
        used_today = daily.get("usage", 0)
        requests_today = daily.get("total_request_per_day", 0)

        total = balance + free_credit
        print(f"账户余额：${balance:.3f}")
        print(f"免费额度：${free_credit:.3f}")
        print(f"合计可用：${total:.3f}")
        print(f"今日消耗：${used_today:.3f}（{requests_today} 次请求）")

        # 持久化到 .state.json
        state = {}
        if os.path.exists(state_file):
            with open(state_file) as f:
                state = json.load(f)
        state.update({
            "balance": balance,
            "free_credit": free_credit,
            "total_available": total,
            "last_check": datetime.datetime.now().isoformat(),
            "last_daily_usage": used_today,
            "last_daily_requests": requests_today
        })
        with open(state_file, "w") as f:
            json.dump(state, f, indent=2, ensure_ascii=False)
        print(f"状态已保存至 {state_file}")
        return total

balance = asyncio.run(check_balance())
```

### 小红书接口费用速查（来源：飞书费用表，2026-04-04 同步）

> 所有限速均为 10次/秒，24小时缓存（重复请求不重复计费）。

| 端点（省略 /api/v1/xiaohongshu/ 前缀） | 费用/次 | 免费额度 | 折扣 |
|----------------------------------------|---------|---------|------|
| `web/sign` | $0.001 | 是 | 是 |
| `web/get_note_info_v5` | $0.001 | 是 | 是 |
| `web/get_note_id_and_xsec_token` | $0.001 | 是 | 是 |
| `app/get_user_id_and_xsec_token` | $0.001 | 否 | 否 |
| `web/get_note_comments_v3` | $0.011 | 否 | 否 |
| `web/search_notes_v3` | $0.011 | 否 | 否 |
| `web/test` / `app/test` | $0 | — | — |
| **其余所有端点** | **$0.01** | 否 | 否 |

### 任务费用估算模板

规划批量任务时，先估算费用再执行：

```python
def estimate_cost(tasks: dict) -> None:
    """
    tasks 格式: {端点简称: 预计调用次数}
    示例: {"search_users": 10, "get_user_info": 50, "get_user_posted_notes": 200}
    """
    PRICES = {
        "web/sign": 0.001,
        "web/get_note_info_v5": 0.001,
        "web/get_note_id_and_xsec_token": 0.001,
        "app/get_user_id_and_xsec_token": 0.001,
        "web/get_note_comments_v3": 0.011,
        "web/search_notes_v3": 0.011,
    }
    DEFAULT_PRICE = 0.01

    total = 0
    print("费用估算：")
    for endpoint, count in tasks.items():
        price = PRICES.get(endpoint, DEFAULT_PRICE)
        cost = price * count
        total += cost
        print(f"  {endpoint} × {count} = ${cost:.4f}")
    print(f"合计：${total:.4f}（约 {total/0.01:.0f} 次标准调用等价）")

# 示例：搜索50页用户 + 获取每人信息 + 每人10条笔记
estimate_cost({
    "web/search_users": 50,
    "app_v2/get_user_info": 1000,
    "app_v2/get_user_posted_notes": 3000,
})
```

> **规则**：每次规划批量抓取任务时，必须先运行估算，确认用户知晓费用后再执行。

