# 建站筆記 — jack-chinho-yao.github.io

**日期：** 2026-07-08
**目標：** 用 Vite + Tailwind 做 DevSecOps About Me 單頁，GitHub Actions 部署到 GitHub Pages。

---

## 前置知識

- **使用者主站（root 網域）規則**：repo 名字必須剛好等於「GitHub 帳號名 + `.github.io`」。
  - 帳號 `jack-chinho-yao` → repo 必須叫 `jack-chinho-yao.github.io`
  - 網址才會是乾淨的 `https://jack-chinho-yao.github.io`
  - 名字不匹配的話會被當成 project 站，網址會多一段 `/repo-name/` 尾巴
- **vanilla** = 純 JavaScript、不帶框架（相對於 react / vue / svelte 範本）。
  vanilla ≠ 沒有 build，我們一樣有 Vite 打包 + Tailwind。

## 環境

- npm `10.9.8`（對應 Node 22 級，Vite 需要 Node 18+）

---

## 步驟 1 — 建立 Vite 專案（vanilla 範本）

```bash
npm create vite@latest . -- --template vanilla
```

指令拆解：
- `npm create vite@latest` — 用最新版 Vite 建立工具
- `.` — 建在**目前資料夾**（跟 git repo 同一層，不開新資料夾）
- `-- --template vanilla` — 用 vanilla 範本；`--` 把後面參數轉交給 vite

互動提示的選法：
- `Current directory is not empty` → 選 **`Ignore files and continue`**（別選 Remove，會刪東西）

結果：
- 產生 `index.html`、`src/`（`main.js`、`style.css`、`counter.js`、`assets/`）、`public/`
- 自動產生 `.gitignore`（已忽略 `node_modules`、`dist`）
- `package.json` 內含 script：`dev` / `build` / `preview`
- Vite 版本 `^8.1.1`
- `npm run dev` 會開本機開發伺服器 `http://localhost:5173`（看預設範本頁）
- 範本的 `counter.js`、`assets/`、demo 版 `index.html` 之後會清掉換成自己的

## 步驟 2 — 安裝 Tailwind CSS v4 + Vite 外掛

```bash
npm install tailwindcss @tailwindcss/vite
```

指令拆解：
- `tailwindcss` — Tailwind 本體（v4）
- `@tailwindcss/vite` — Tailwind 官方 Vite 外掛
- **Tailwind v4 新做法**：不再需要 `tailwind.config.js` / `postcss.config.js`，
  只要掛這個外掛 + 在 CSS 寫一行 `@import "tailwindcss"` 即可，設定大幅簡化
- 兩者屬 build 用途，會歸到 `devDependencies`

---

## 步驟 3 — 建立 `vite.config.ts`（掛 Tailwind 外掛）

放在**專案根目錄**（跟 `package.json`、`index.html` 同層）。`.ts` 或 `.js` 都可，Vite 原生支援。

```ts
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [tailwindcss()],
})
```

重點：
- `vite.config` = Vite 的操作手冊，在這裡開關功能(外掛)、調參數
- `plugins: [tailwindcss()]` 一定要有，Vite 才看得懂 `@import "tailwindcss"` 跟 `@theme`
- 使用者主站 → 不用設 `base`（預設 `/`）。project 站才要設 `base: '/repo/'`
- 每個專案 config 內容不同是正常的：需要什麼就寫什麼

## 步驟 4 — 清範本 + 設定 `src/style.css`

```bash
rm src/counter.js
rm -rf src/assets
```

`src/style.css` 換成：`@import "tailwindcss";` + `@theme { ... }`（配色 + 字型）。

Tailwind v4 命名慣例（重點）：
- `@theme` 裡定義 `--<命名空間>-<名字>`，Tailwind 自動生出對應 class
- `--color-accent` → `bg-accent` / `text-accent` / `border-accent`
- `--font-mono` → `font-mono`
- 前綴（`bg-` `text-` `border-`）對應 CSS 屬性；想改什麼屬性就用哪個前綴
- 建議裝 VS Code 外掛「Tailwind CSS IntelliSense」→ 自動補全、顯示色塊
- 自訂字型(JetBrains Mono/Inter)光在 CSS 寫名字不夠，要在 `index.html` 用 Google Fonts `<link>` 載入

## 步驟 5 — 寫 `index.html` + 精簡 `main.js` + 游標動畫

做法選擇：**內容直接寫在 `index.html`**（語意化、對 SEO/無障礙好），
`main.js` 只留 `import './style.css'`，閃爍游標用**純 CSS 動畫**（不用 JS）。

- `index.html`：`<head>` 加 Google Fonts；`<body>` 六個區塊
  （Hero / About / Skills / Certifications / Projects / Contact）+ footer，
  用 `[佔位字]` 之後自己填
- `src/main.js`：只留 `import './style.css'`
- `src/style.css`：加 `@keyframes blink` + `.cursor { animation: blink 1s step-end infinite }`

資料流：`index.html` →(script)→ `main.js` →(import)→ `style.css`(Tailwind)

驗證：`npm run dev` → http://localhost:5173 應看到黑底綠字終端風、閃爍游標、各區塊。
dev server 開著時改檔存檔會即時更新(HMR)。

---

## 待辦（接下來的步驟）

- [ ] 步驟 6：填入真實資料（取代 `[佔位字]`）
- [ ] 步驟 7：`.github/workflows/deploy.yml` + GitHub Pages 設定（Source 選 GitHub Actions）
- [ ] 步驟 8：`git push` 後確認自動部署，網站上線 `https://jack-chinho-yao.github.io`
