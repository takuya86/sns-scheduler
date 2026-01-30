# SNS Scheduler

TikTok / Instagram 投稿のSlack通知を自動送信するスケジューラー。

## 仕組み

```
cron-job.org → GitHub Actions → Slack通知
                    ↓
            obsidian-sns-data (PRIVATE)
              - schedule.md
              - videos/*/captions.md
```

## セットアップ

### 1. GitHub Secrets を設定

| Secret | 説明 |
|--------|------|
| `DATA_REPO_PAT` | obsidian-sns-data にアクセスするPAT |
| `SLACK_WEBHOOK_URL` | Slack Incoming Webhook URL |

### 2. cron-job.org でトリガー設定

**TikTok（12:00 JST = 03:00 UTC）**
```
URL: https://api.github.com/repos/takuya86/sns-scheduler/actions/workflows/notify.yml/dispatches
Method: POST
Headers:
  Authorization: token <GITHUB_PAT>
  Accept: application/vnd.github.v3+json
Body: {"ref":"main","inputs":{"platform":"tiktok"}}
```

**Instagram（19:00 JST = 10:00 UTC）**
```
URL: https://api.github.com/repos/takuya86/sns-scheduler/actions/workflows/notify.yml/dispatches
Method: POST
Headers:
  Authorization: token <GITHUB_PAT>
  Accept: application/vnd.github.v3+json
Body: {"ref":"main","inputs":{"platform":"instagram"}}
```

## 手動実行

```bash
gh workflow run notify.yml -f platform=tiktok
gh workflow run notify.yml -f platform=instagram
```
