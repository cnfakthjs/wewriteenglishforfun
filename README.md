# 學測英文寫作追蹤本

給單一學生使用的學測（GSAT）英文非選擇題（中譯英＋英文作文）寫作弱點追蹤工具。

這份是**公開展示版**，用於分享連結給他人瀏覽，純顯示、不可在網頁上編輯。實際使用（新增弱點紀錄、批改新作文）請回到 Claude 對話中操作那份會寫入 `window.storage` 的版本。

## 檔案結構

```
index.html   ← 網頁本體，畫面與邏輯，不含任何學生資料
data.json    ← 所有資料（弱點紀錄、作品集）都在這裡
robots.txt   ← 禁止搜尋引擎爬取
README.md    ← 本文件
.gitignore
```

`index.html` 一載入就會 `fetch('./data.json')` 把資料抓進來渲染，兩個檔案必須放在同一層目錄。

## 以後怎麼新增一篇新作文（不用重寫 index.html）

1. 把學生的新作文原文傳給 Claude，請它批改
2. 請 Claude 幫忙把這篇的資料整理成 `data.json` 裡 `portfolio` 陣列的格式（`id`、`title`、`date`、`score`、`prompt`、`original`、`corrected`、`note`），Claude 會直接給你更新後的完整 `data.json`
3. 如果這篇有新的錯誤類型，也請 Claude 一併更新 `weak` 陣列裡的弱點紀錄
4. 把新的 `data.json` 上傳到 GitHub repo，**取代**舊的檔案（GitHub 網頁介面：進到 repo → 點 `data.json` → 右上角鉛筆圖示編輯，或用 Upload files 覆蓋上傳）
5. Commit 之後，Vercel 因為已經接好這個 GitHub repo，**會自動重新部署**，不需要重新跑一次 Vercel 的設定流程，網址也不會變

`index.html` 本身除非要改版面或功能，否則完全不用動。

## data.json 資料格式

```json
{
  "weak": [
    { "cat": "類別", "desc": "說明", "date": "YYYY-MM-DD", "count": 數字, "id": "w0" }
  ],
  "portfolio": [
    {
      "id": "唯一英文代號",
      "title": "作文標題",
      "date": "YYYY-MM-DD",
      "score": 分數（0-20）,
      "prompt": "題目說明",
      "original": "學生原文",
      "corrected": "批改後版本",
      "note": "備註"
    }
  ]
}
```

## 隱私防護

- `robots.txt` 禁止搜尋引擎爬取整站
- `index.html` 的 `<head>` 內含 `<meta name="robots" content="noindex, nofollow, noarchive">`，即使被爬蟲讀到也不會被索引
- 部署時 GitHub repo 名稱與 Vercel 專案名稱都應避免使用任何跟學生、補習班、英文寫作相關的可辨識字眼

**注意**：以上防護只能降低「被搜尋引擎或路人意外找到」的機率，**無法做到真正的存取限制**。網址本身只要外流出去，任何人都能打開查看。這份工具目前假設分享對象範圍有限，且作文內容本身不含姓名等可辨識個資。

## 技術限制

`window.storage` 是 Claude Artifact 環境專屬的 API，這份公開部署版**不使用它**，資料改由 `data.json` 提供，網頁上也移除了新增/刪除表單（純顯示，避免訪客誤以為按了新增會真的存檔）。

## 版本紀錄

- 2026-08-23：資料與畫面分離為 `data.json` + `index.html`，方便日後只更新資料檔即可上線新內容，不需重寫整份程式碼
