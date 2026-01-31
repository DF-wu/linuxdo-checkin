# 多帳號配置指南

本指南說明如何為 linux.do 自動登入腳本配置多個帳號。

## 快速配置步驟

### 1. 設置 GitHub Secret

前往您的 GitHub 倉庫，依照以下步驟設置 Secret：
1. 進入 **Settings** > **Secrets and variables** > **Actions**
2. 點擊 **New repository secret**
3. 建立一個名為 `LINUXDO_ACCOUNTS` 的 Secret
4. 在 Value 欄位中，按照以下格式填入您的多個帳號資訊：

```
帳號1:密碼1,帳號2:密碼2,帳號3:密碼3
```

### 範例

假設您有三個帳號：
- 帳號 1：`user1@example.com`，密碼：`pass123`
- 帳號 2：`user2@example.com`，密碼：`pass456`
- 帳號 3：`user3@example.com`，密碼：`pass789`

那麼 `LINUXDO_ACCOUNTS` 的值應為：
```
user1@example.com:pass123,user2@example.com:pass456,user3@example.com:pass789
```

**注意事項**：
- 使用 **英文冒號** `:` 分隔帳號與密碼
- 使用 **英文逗號** `,` 分隔不同的帳號
- 帳號或密碼中不應包含冒號或逗號字符（如有特殊需求請聯繫維護者）

## 向下兼容說明

如果您不設置 `LINUXDO_ACCOUNTS`，工作流會自動回退到單帳號模式，使用原有的 `LINUXDO_USERNAME` 和 `LINUXDO_PASSWORD`（或 `USERNAME` 和 `PASSWORD`）。

這意味著您可以：
- 保留現有的單帳號配置不變
- 僅在需要多帳號時才設置 `LINUXDO_ACCOUNTS`

## 執行順序

當設置多帳號後，GitHub Actions 會：
1. 依序為每個帳號執行登入和簽到任務
2. 每個帳號獨立輸出日誌
3. 每個帳號執行完後發送獨立的通知（如已配置 Gotify/Server醬/wxpush）

## 效能優化

工作流已經過優化：
- ✅ 使用 `actions/cache` 快取 Python 依賴，減少安裝時間
- ✅ 在同一個 Job 中順序執行多個帳號，避免重複的虛擬機啟動開銷
- ✅ 預計每次執行節省約 30-60 秒的依賴安裝時間

## 故障排查

### 帳號密碼格式錯誤
如果看到類似 "DETAILS[0]" 或 "DETAILS[1]" 為空的錯誤，請檢查：
- 是否使用了英文標點符號
- 是否有多餘的空格
- 格式是否嚴格遵循 `user:pass,user:pass`

### 某個帳號登入失敗
- 工作流會繼續執行後續帳號，不會中斷
- 請檢查該帳號的密碼是否正確
- 查看 Actions 日誌中該帳號的具體錯誤訊息

## 進階配置

### 為不同帳號設置不同的通知
目前所有帳號共用同一組通知配置（`GOTIFY_TOKEN`、`SC3_PUSH_KEY` 等）。如果您需要為不同帳號設置不同的通知 Token，請提出 Issue 或 Pull Request。

### 自定義執行順序
帳號按照在 `LINUXDO_ACCOUNTS` 中的排列順序依次執行。如需調整順序，請修改 Secret 中的帳號排列。

## 安全提示

⚠️ **請勿在公開的 Issue 或 Pull Request 中暴露您的帳號密碼**  
⚠️ GitHub Secrets 是加密存儲的，但請仍然確保您的倉庫權限設置正確
