# 腾讯新闻抓取脚本

依赖 `NewsCrawler` 仓库提供的 `TencentNewsCrawler`，通过腾讯官方 24 小时热闻接口（`sub_srv_id=24hours`）分批获取最新文章链接，再逐条抓取详情并输出 JSON。

## 快速开始

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python src/tencent_latest.py --limit 3 --output data/tencent_latest.json
```

命令参数：

- `--limit`：从 24 小时接口聚合的总条数，默认 20。
- `--sub-srv-id`：接口 `sub_srv_id`，默认 `24hours`。
- `--offset-start / --offset-end / --offset-step`：offset 遍历范围与步长，默认只抓第一页 `0`，可按需设置如 `--offset-end 160 --offset-step 20` 遍历多个分页。
- `--no-persist`：只返回数据，不在 `data/` 下保存逐条 JSON。
- `--output`：聚合结果写入文件（JSON 数组），为空则仅打印。
- `--api-endpoint`：后端提取接口地址，默认 `http://localhost:8000/api/extract`（参照 Swagger `/docs` 中的 `POST /api/extract`）。
- `--api-format`：调用后端 API 时的 `output_format`，可选 `json`/`markdown`。
- `--local-only`：跳过后端 API，直接使用脚本内置的 `TencentNewsCrawler`。

脚本默认按照后端 API 文档调用 `POST /api/extract`（参数包含 `url`、`platform=tencent`、`output_format`），后端成功返回时直接复用其结构化数据；若 API 不可用或返回失败，则自动回退至本地 `TencentNewsCrawler`。链接获取阶段仅使用 24 小时热闻 TRPC 接口，若该接口不可达会直接抛出异常，不再尝试 RSSHub/RSS 或本地示例。

