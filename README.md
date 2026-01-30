# SNS Scheduler

SNS投稿のSlack通知を自動送信するスケジューラー。

## 対応プラットフォーム
- TikTok / Instagram（動画投稿）
- X / Twitter（事務note用テキスト投稿）

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
# TikTok/Instagram
gh workflow run notify.yml -f platform=tiktok
gh workflow run notify.yml -f platform=instagram

# 事務note X投稿
gh workflow run x-jimu-note.yml -f timing=morning
gh workflow run x-jimu-note.yml -f timing=noon
gh workflow run x-jimu-note.yml -f timing=evening
```

---

## 事務note X投稿スケジューラー

### GitHub Secrets 追加設定

| Secret | 説明 |
|--------|------|
| `SLACK_WEBHOOK_JIMU` | 事務note用 Slack Incoming Webhook URL |

### cron-job.org 設定

**朝予約通知（21:00 JST = 12:00 UTC）**
```
URL: https://api.github.com/repos/takuya86/sns-scheduler/actions/workflows/x-jimu-note.yml/dispatches
Method: POST
Headers:
  Authorization: token <GITHUB_PAT>
  Accept: application/vnd.github.v3+json
Body: {"ref":"main","inputs":{"timing":"morning"}}
Schedule: 0 12 * * 0-5 (UTC) ← 日〜金の翌朝分
```

**昼通知（11:00 JST = 02:00 UTC）- 日曜のみ**
```
URL: https://api.github.com/repos/takuya86/sns-scheduler/actions/workflows/x-jimu-note.yml/dispatches
Method: POST
Headers:
  Authorization: token <GITHUB_PAT>
  Accept: application/vnd.github.v3+json
Body: {"ref":"main","inputs":{"timing":"noon"}}
Schedule: 0 2 * * 0 (UTC) ← 日曜のみ
```

**夜通知（19:00 JST = 10:00 UTC）**
```
URL: https://api.github.com/repos/takuya86/sns-scheduler/actions/workflows/x-jimu-note.yml/dispatches
Method: POST
Headers:
  Authorization: token <GITHUB_PAT>
  Accept: application/vnd.github.v3+json
Body: {"ref":"main","inputs":{"timing":"evening"}}
Schedule: 0 10 * * 1-6 (UTC) ← 月〜土
```

### 投稿スケジュール

| 曜日 | 朝7:00 | 昼12:00 | 夜20:00 |
|------|--------|---------|---------|
| 月〜土 | 集客ポスト | - | 教育ポスト |
| 日 | - | フィルタポスト | - |

### データ管理

投稿内容は `obsidian-sns-data/jimu-note/` で管理:
- `schedule.md` - 投稿スケジュール
- `posts.md` - ポストストック
