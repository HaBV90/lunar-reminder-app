# Slack Integration Setup Guide

## Mục đích

Tích hợp Slack để:
- Claude tự động report status khi: bắt đầu task, hoàn thành, gặp blocker
- User reply Slack → feedback cho Claude để fix bug hoặc tiếp tục task
- CI/CD notify khi build pass/fail, deploy thành công

---

## Step 1: Tạo Slack App

1. Truy cập https://api.slack.com/apps
2. Click **"Create New App"** → **"From scratch"**
3. Điền thông tin:
   - **App Name:** `Lunar Reminder Bot`
   - **Workspace:** Chọn workspace của bạn
4. Click **"Create App"**

---

## Step 2: Enable Incoming Webhooks

1. Trong app settings, chọn **"Incoming Webhooks"** (sidebar trái)
2. Toggle **"Activate Incoming Webhooks"** → **ON**
3. Scroll xuống, click **"Add New Webhook to Workspace"**
4. Chọn channel: `#lunar-reminder-dev` (hoặc tạo channel mới)
5. Click **"Allow"**
6. Copy **Webhook URL** (dạng: `https://hooks.slack.com/services/T.../B.../xxx`)

> **Lưu ý:** Giữ bí mật Webhook URL, không commit vào git!

---

## Step 3: Cấu hình trong project

### 3.1 Tạo file `.env.local` (root project)

```bash
# Slack Integration
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/T.../B.../xxx
SLACK_CHANNEL=#lunar-reminder-dev
SLACK_ENABLED=true
```

> **Quan trọng:** File `.env.local` đã được thêm vào `.gitignore`, không bị commit.

### 3.2 Cấu hình `.claude/integrations/slack.config.json`

```json
{
  "webhookUrl": "${SLACK_WEBHOOK_URL}",
  "channel": "${SLACK_CHANNEL}",
  "enabled": true,
  "notifications": {
    "taskStart": true,
    "taskComplete": true,
    "blocker": true,
    "commit": false,
    "prCreated": true,
    "ciStatus": true
  },
  "mentions": {
    "blocker": ["@Ha"],
    "prCreated": ["@Ha"]
  }
}
```

### 3.3 Thêm secret vào GitHub (cho CI/CD)

1. Vào repo GitHub → **Settings** → **Secrets and variables** → **Actions**
2. Click **"New repository secret"**
3. Thêm:
   - **Name:** `SLACK_WEBHOOK_URL`
   - **Value:** Paste webhook URL

---

## Step 4: Test Webhook

### Test từ terminal

```bash
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"🧪 Test message from Lunar Reminder App"}' \
  $SLACK_WEBHOOK_URL
```

### Test từ Claude

```
/report-status Testing Slack integration
```

---

## Message Templates

### Task Started
```
🚀 *Task Started*
`[TASK-ID]` Add login screen with form validation

_Branch:_ `feature/add-login-screen`
_Assignee:_ Claude
```

### Task Completed
```
✅ *Task Completed*
`[TASK-ID]` Add login screen with form validation

*Summary:*
- 5 commits
- 12 files changed
- +486 / -23 lines

_PR:_ #42
_Duration:_ 2h 15m
```

### Blocker
```
🚨 *Blocker Encountered*
`[TASK-ID]` Add login screen with form validation

*Issue:*
Cannot resolve peer dependency conflict between react-navigation and react-native-screens

*Attempted:*
- npm install --legacy-peer-deps
- Downgrade react-navigation to v5

*Need:* @Ha please advise

_Branch:_ `feature/add-login-screen`
```

### PR Created
```
📝 *Pull Request Ready*
`[TASK-ID]` Add login screen with form validation

*PR:* #42 - feat(mobile): add login screen with form validation
*Changes:* 12 files, +486 / -23 lines
*Tests:* ✅ All passing (24 tests)

_Waiting for review:_ @Ha
```

### CI Status
```
✅ *CI Passed*
Branch: `feature/add-login-screen`
Commit: `abc1234` - feat(mobile): add login screen

- Lint: ✅
- TypeCheck: ✅
- Tests: ✅ (156 passed)
- Build: ✅
```

```
❌ *CI Failed*
Branch: `feature/add-login-screen`
Commit: `abc1234` - feat(mobile): add login screen

- Lint: ✅
- TypeCheck: ❌ Error in LoginScreen.tsx:45
- Tests: Skipped
- Build: Skipped

_Details:_ [View Workflow Run](https://github.com/...)
```

---

## User Commands (Reply trong Slack)

Khi Claude gửi message, user có thể reply để điều khiển:

| Command | Mô tả | Ví dụ |
|---------|-------|-------|
| `continue` | Tiếp tục task tiếp theo | `continue` |
| `fix: [mô tả]` | Fix issue cụ thể | `fix: add missing import statement` |
| `pause` | Pause và đợi hướng dẫn | `pause` |
| `status` | Report current status | `status` |
| `review` | Chạy self-review code | `review` |
| `cancel` | Hủy task hiện tại | `cancel` |
| `retry` | Retry task đang fail | `retry` |

### Ví dụ flow

```
[Claude] 🚨 Blocker: Cannot resolve dependency X
[Ha] fix: try using version 2.0.0 instead
[Claude] 🔧 Attempting fix...
[Claude] ✅ Fixed! Continuing with task...
```

---

## Cấu hình nâng cao

### Thay đổi notification settings

Trong `.claude/integrations/slack.config.json`:

```json
{
  "notifications": {
    "taskStart": false,      // Tắt notify khi bắt đầu task
    "taskComplete": true,    // Bật notify khi xong task
    "blocker": true,         // Bật notify khi gặp blocker (quan trọng)
    "commit": false,         // Tắt notify mỗi commit (quá nhiều)
    "prCreated": true,       // Bật notify khi tạo PR
    "ciStatus": true         // Bật notify CI pass/fail
  }
}
```

### Custom mentions

```json
{
  "mentions": {
    "blocker": ["@Ha", "@dev-team"],
    "prCreated": ["@Ha"],
    "ciFailed": ["@Ha", "@dev-team"]
  }
}
```

### Scheduled summary (daily digest)

```json
{
  "scheduledReports": {
    "enabled": true,
    "time": "18:00",
    "timezone": "Asia/Ho_Chi_Minh",
    "content": ["tasksCompleted", "prsCreated", "blockers"]
  }
}
```

---

## Troubleshooting

### Webhook không hoạt động

1. **Kiểm tra URL:** Đảm bảo copy đúng webhook URL
2. **Kiểm tra channel:** Channel phải tồn tại và app có quyền post
3. **Kiểm tra enabled:** `SLACK_ENABLED=true` trong `.env.local`

### Không nhận được message

1. Kiểm tra `enabled: true` trong `slack.config.json`
2. Kiểm tra notification type có bật không
3. Xem logs: `.claude/logs/slack.log`

### CI notification không hoạt động

1. Kiểm tra secret `SLACK_WEBHOOK_URL` đã thêm vào GitHub
2. Kiểm tra workflow file có step notify
3. Xem GitHub Actions logs

### Rate limiting

Slack có rate limit 1 message/second. Nếu gửi quá nhanh:
- Batch messages lại
- Giảm notification types
- Tăng delay giữa các messages

---

## Files liên quan

| File | Mô tả |
|------|-------|
| `.env.local` | Webhook URL (không commit) |
| `.claude/integrations/slack.config.json` | Cấu hình notifications |
| `.claude/integrations/slack-reporter.ts` | Script gửi messages |
| `.claude/commands/report-status.md` | Skill `/report-status` |
| `.claude/commands/notify-blocker.md` | Skill `/notify-blocker` |
| `.github/workflows/ci.yml` | CI với Slack notification |

---

## Security Notes

1. **Không commit webhook URL** vào git
2. **Rotate webhook** nếu bị lộ (delete và tạo mới trong Slack app)
3. **Giới hạn channel** - chỉ post vào private channel nếu có thông tin nhạy cảm
4. **Audit logs** - Slack giữ logs tất cả messages

---

## Tham khảo

- [Slack Incoming Webhooks](https://api.slack.com/messaging/webhooks)
- [Slack Block Kit Builder](https://app.slack.com/block-kit-builder)
- [GitHub Actions Slack Action](https://github.com/8398a7/action-slack)
