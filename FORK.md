# Fork 说明（相对上游 lineagett/cf-workers-nav 的改动）

本 fork 只在上游基础上增加了自动同步 + 自动部署到 Cloudflare 的能力，
不改上游任何业务代码。

## 新增文件

- `wrangler.toml`：Worker 名 `cf-workers-nav`，入口 `workers.js`，
  绑定生产 KV 命名空间 `CARD_ORDER`。`compatibility_date` 取 `2024-01-01`
  （代码只用标准 Web API，无需 nodejs_compat）。
- `.github/workflows/sync-upstream.yml`：每天北京时间 03:00 自动合并上游
  `lineagett` 分支；有更新则自动 `wrangler deploy`；合并冲突时显式失败，
  需手动解决。也支持手动触发（手动触发一定会部署一次）。
- `.github/workflows/deploy.yml`：用户自己 push 到 `lineagett` 分支时自动部署；
  也支持手动触发。

## 需要的 GitHub Secrets（fork 的 Settings > Secrets and variables > Actions）

- `CLOUDFLARE_API_TOKEN`：Cloudflare 长期 Token（Edit Cloudflare Workers 模板），长期用不要删。
- `CLOUDFLARE_ACCOUNT_ID`：Cloudflare 账号 ID。

## 注意事项

1. `ADMIN_PASSWORD` / `JWT_SECRET` 已在 Cloudflare Worker 上以 Secret 形式配置，
   `wrangler deploy` 不会删除已有 Secret，不要把它们写进仓库。
2. 以后改代码直接 push 到本 fork 的 `lineagett` 分支（网页编辑或本地推送），
   推送即自动部署；不要再用 dashboard 手动粘贴代码（会被下一次自动部署覆盖）。
3. 如果上游新增了与本 fork 同名的文件导致合并冲突，Actions 会失败并提示手动解决。
4. 如需改默认分支名，同步修改两个 workflow 里的分支名。
