# Steam 状态监控

用于在群聊中监控 Steam 好友状态、订阅游戏资讯/价格，并将结果以文本或图片卡片形式推送。

## 功能概览

- 绑定 Steam 账号到群成员，自动轮询状态变化并推送
- 订阅游戏后自动推送公告、特卖、降价、免费等变化
- 支持 `/steam 价格` 查询一年历史价格（需 IsThereAnyDeal API Key）
- 支持商店链接识别并生成预览图
- 支持 `/steam 自检` 快速排查依赖与配置状态

---

## 配置方法

在 AstrBot 插件管理中打开本插件配置页，按下列项填写。

### 必填（建议）

1. `steam_web_api_key`
	 - 用途：好友状态、游戏基础信息拉取
	 - 获取：<https://steamcommunity.com/dev/apikey>

2. `steamgriddb_api_key`
	 - 用途：游戏封面获取（图片卡片更完整）
	 - 获取：<https://www.steamgriddb.com/profile/preferences/api>

3. `isthereanydeal_api_key`
	 - 用途：`/steam 价格` 与特卖低价查询
	 - 获取：<https://isthereanydeal.com/apps/my/>

### 可选配置

- `poll_interval_sec`
	- 轮询间隔（秒），默认 `60`
	- 建议 `>= 30`，过小可能触发接口限流

- `http_proxy`
	- HTTP/HTTPS 代理地址（可选）
	- 示例：`http://127.0.0.1:7890`
	- 留空则不使用代理

- `verbose_poll_log`
	- 是否输出详细轮询日志，默认 `false`

- `llm_provider_id`
	- 结束游戏点评使用的 Provider（留空使用当前默认 Provider）

- `llm_comment_prompt`
	- 点评 Prompt 模板
	- 可用变量：`{display_name}`、`{game_name}`、`{duration_text}`

- `llm_comment_timeout_sec`
	- 点评请求超时秒数，默认 `15`
	- 范围 `3~60`，模型响应慢时可适当调高

- `llm_comment_max_attempts`
	- 点评总尝试次数（含首次请求），默认 `2`
	- 范围 `1~5`

- `llm_comment_concurrency`
	- 点评并发上限，默认 `1`
	- 范围 `1~5`，并发过高可能触发模型限流

---

## 指令说明

所有指令均以 `/steam` 开头。

### 账号绑定

- `/steam 绑定 [好友码/64位id/好友链接/资料链接] [可选:qq]`
	- 别名：`bind` `addid` `add`
	- 说明：将 Steam 账号绑定到当前群成员；可附带 QQ 指定绑定对象

- `/steam 解绑 [steamid64|all]`
	- 别名：`unbind`
	- 说明：解绑当前用户绑定关系；`all` 表示解绑当前群内自己全部绑定

### 订阅与价格

- `/steam 订阅 [游戏链接/游戏id/游戏名称]`
	- 别名：`subscribe` `sub`
	- 说明：订阅指定游戏资讯与价格变化推送

- `/steam 价格 [appid|游戏链接|游戏名称]`
	- 别名：`price` `price1y`
	- 说明：查询一年历史价格并生成价格图（需配置 `isthereanydeal_api_key`）

### 查询与运维

- `/steam 列表`
	- 别名：`list` `ls`
	- 说明：查看当前群绑定与订阅概览

- `/steam 我`
	- 别名：`me` `my`
	- 说明：查看自己的绑定与订阅信息

- `/steam 自检`
	- 别名：`check` `diag`
	- 说明：检查依赖、字体、渲染环境与 Key 配置状态

- `/steam 帮助`
	- 别名：`help` `h`

### 测试指令（调试）

- `/steam 状态测试`
	- 别名：`status` `statustest`

- `/steam 订阅测试`
	- 别名：`subtest` `testsub`

---

## 快速开始

1. 在插件配置中先填好 `steam_web_api_key`（至少）
2. 群内执行：`/steam 绑定 你的Steam好友码`
3. 可选执行：`/steam 订阅 https://store.steampowered.com/app/570/`
4. 验证环境：`/steam 自检`

---

## 常见问题

- `未配置 Steam Web API Key`
	- 说明 `steam_web_api_key` 未填写或未保存，保存后重试

- `/steam 价格` 提示未配置 ITAD Key
	- 需填写 `isthereanydeal_api_key`

- 图片未生成或渲染失败
	- 先执行 `/steam 自检`，检查 `playwright`、`fonts_dir`、`logo_svg` 状态