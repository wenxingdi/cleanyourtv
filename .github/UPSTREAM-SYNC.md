# 上游自动同步说明

本仓库是 **[Gathin23/cleanyourtv](https://github.com/Gathin23/cleanyourtv)** 的镜像，
由 GitHub Actions 每天自动检查上游是否有新提交，有则同步过来。

- 上游：`https://github.com/Gathin23/cleanyourtv.git`
- 同步分支：`main`、`maintainer-bot-live`
- 同步模式：`merge`（保留本仓库改动并与上游合并）
- 触发时间：每天 UTC 02:17，即北京时间 10:17
- 手动触发：`Actions` → `Sync Upstream` → `Run workflow`

## 这个仓库的特殊之处

上游自带 `.github`（3 个 workflow：`ci.yml` / `addons.yml` / `maintainer-bot.yml` + issue 模板），
全部保留。定时任务只从默认分支 `main` 读取工作流文件，所以 `.github/` 必须注入到每个同步分支。

## 改配置

所有行为由 `.github/upstream-sync.json` 决定：

```json
{
  "upstream": "https://github.com/Gathin23/cleanyourtv.git",
  "branches": ["main", "maintainer-bot-live"],
  "primary": "main",
  "mode": "merge",
  "syncTags": true
}
```

- `branches`：要跟随上游同步的分支列表。
- `mode`：`merge` 合并保留改动；`mirror` 每次硬覆盖为上游最新。
- `syncTags`：是否同步 tag。

## 已知限制

1. **仓库连续 60 天没有任何提交活动时，GitHub 会自动停用定时任务。**
   进 `Actions` → 选 `Sync Upstream` → 点 `Enable workflow` 即可恢复。
2. 本仓库是 **Public**，Actions 分钟数免费不限量。
3. 若上游做了 force-push 重写历史，`merge` 模式可能报冲突而不是静默覆盖。
   届时把 `mode` 改成 `mirror` 再手动跑一次即可对齐。

## 本地手动同步

```bash
git fetch upstream --prune --tags --force
git checkout main && git merge upstream/main
git checkout maintainer-bot-live && git merge upstream/maintainer-bot-live
git push origin --all --tags
```
