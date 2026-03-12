# คู่มือการตั้งค่า Vite + React

## ขั้นตอนการติดตั้งและใช้งาน

### 0. สร้างไฟล์ที่จำเป็นก่อน npm install

ก่อนรัน `npm install` ต้องให้ไฟล์ที่จำเป็นครบถ้วน:

#### ไฟล์การตั้งค่า

**`package.json`** - ระบุ dependencies และ scripts:

```json
{
  "name": "test-ci-cd",
  "private": true,
  "version": "0.0.1",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.0.0",
    "vite": "^4.3.0"
  }
}
```

**`vite.config.js`** - การตั้งค่า Vite:

```javascript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  base: "/test-ci-cd/",
});
```

#### ไฟল์ HTML

**`index.html`** - แม่แบบ HTML:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>CI/CD Test</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

#### ไฟล์ต้นฉบับ React

**`src/main.jsx`** - Entry point:

```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

**`src/App.jsx`** - Main component:

```javascript
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <h1>Hello CI/CD</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
      </div>
    </>
  );
}

export default App;
```

**`src/App.css`** - App styles
**`src/index.css`** - Global styles

#### File .gitignore

```
node_modules
dist
dist-ssr
*.local
```

✅ **หลังจากสร้างไฟล์ทั้งหมดแล้ว ค่อยไปยัง step ถัดไป**

### 1. ติดตั้ง Dependencies

```bash
npm install
```

หรือถ้าใช้ yarn:

```bash
yarn install
```

### 2. รันเซิร์ฟเวอร์พัฒนา (Development Server)

```bash
npm run dev
```

จากนั้นเข้าไปที่ `http://localhost:5173` ในเบราว์เซอร์

### 3. Build สำหรับ Production

```bash
npm run build
```

ไฟล์ที่สร้างจะอยู่ในโฟลเดอร์ `dist/`

### 4. Preview Production Build

```bash
npm run preview
```

### 5. สร้าง GitHub Actions Workflow (สำหรับ Auto Deploy)

สร้างไฟล์ `.github/workflows/deploy.yml` เพื่อให้ GitHub Actions ทำการ build & deploy อัตโนมัติ:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main", "master"]
  pull_request:
    branches: ["main", "master"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: "18"
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

### 6. Deploy ไป GitHub Pages

1. **Commit และ Push เปลี่ยนแปลง:**

```bash
git add .
git commit -m "Setup Vite React with GitHub Pages deployment"
git push origin main
```

2. **GitHub Actions จะ trigger อัตโนมัติ:**
   - ไปที่ GitHub Repository → **Actions** tab เพื่อดูการ build
   - GitHub Actions จะ build project
   - Deploy ไปยัง GitHub Pages อัตโนมัติ

3. **ตั้งค่า GitHub Pages:**
   - ไปที่ **Settings** → **Pages**
   - ตรวจสอบว่า Source เป็น "Deploy from a branch"
   - Branch ควรเป็น `gh-pages` (auto สร้างจาก workflow)
   - Folder ควรเป็น `/ (root)`

4. **ตรวจสอบผลลัพธ์:**
   - รอ 1-2 นาทีให้ GitHub deploy เสร็จ
   - เข้าไปที่ `https://username.github.io/test-ci-cd/`
   - ควรเห็น "Hello CI/CD" แสดงขึ้นมา

---

## โครงสร้างโปรเจกต์

```
test-ci-cd/
├── src/
│   ├── main.jsx          # React Entry Point
│   ├── App.jsx           # React Component หลัก
│   ├── App.css           # Styles for App
│   └── index.css         # Global Styles
├── index.html            # HTML Template
├── package.json          # Dependencies & Scripts
├── vite.config.js        # Vite Configuration
├── .gitignore            # Git Ignore Rules
├── STEPS.md              # Guide นี้
└── README.md             # Project Description
```

---

## ไฟล์สำคัญ

### `vite.config.js`

- `base: '/test-ci-cd/'` - Base path สำหรับ GitHub Pages

### `package.json`

- `"dev"` - Start development server
- `"build"` - Build for production
- `"preview"` - Preview production build

### `src/main.jsx`

- จุดเริ่มต้นของแอปพลิเคชัน React

### `src/App.jsx`

- Main React component ที่แสดง "Hello CI/CD"

---

## Environment Setup

### ต้องติดตั้ง:

- **Node.js** (recommended v16+)
- **Git**
- **npm** หรือ **yarn**

### ตรวจสอบการติดตั้ง:

```bash
node --version
npm --version
git --version
```

---

## เคล็ดลับและการแก้ไขปัญหา

### ถ้า dev server ไม่เริ่มที่พอร์ต 5173

ลอง kill process ก่อนหน้า:

```bash
# Windows
netstat -ano | findstr :5173

# macOS/Linux
lsof -i :5173
```

### ถ้า build ล้มเหลว

ลองลบ `node_modules` และ `dist` แล้วลองใหม่:

```bash
rm -rf node_modules dist
npm install
npm run build
```

### ถ้า GitHub Pages ไม่อัปเดต

1. ตรวจสอบ GitHub Actions workflow ในแท็บ Actions
2. ตรวจสอบว่า branch ที่ deploy ถูกต้อง (main หรือ master)
3. ลองล้าง browser cache (Ctrl+Shift+Delete)

---

## สำหรับการพัฒนาต่อไป

### เพิ่ม React Router:

```bash
npm install react-router-dom
```

### เพิ่ม Component Libraries:

```bash
npm install axios
npm install zustand  # State management
```

### เปิดใช้งาน ESLint:

```bash
npm install --save-dev eslint eslint-plugin-react
```

---

## แหล่งอ้างอิง

- [Vite Documentation](https://vitejs.dev/)
- [React Documentation](https://react.dev/)
- [GitHub Pages](https://pages.github.com/)
