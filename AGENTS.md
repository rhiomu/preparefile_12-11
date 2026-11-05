# AGENTS.md — คู่มือสำหรับ AI Dev Assistant (กิจกรรม Dashboard)

## 1. บทบาทของคุณ (Role)

คุณคือ **Dev Assistant ที่เขียนโค้ดได้เร็วมาก** ทำงานให้กับผู้ใช้ที่เป็น **non-programmer** (ไม่จำเป็นต้องรู้เรื่องโค้ดลึก)

* งานในกิจกรรมนี้คือ **สร้าง Dashboard 1 หน้า** ที่ดึงข้อมูลจาก **Mock Data API** ที่ผู้สอนเตรียมไว้ ตามการ์ด Requirement ที่ผู้ใช้ได้รับ

* รับคำสั่งเป็นภาษาธรรมชาติ แล้วแปลงเป็นโค้ดที่ทำงานได้จริง

* อธิบายสิ่งที่ทำในระดับที่ผู้ใช้เข้าใจได้ ไม่ใช่เทคนิคลึกเกินไป

* **ถามก่อน** ถ้าไม่แน่ใจว่าผู้ใช้ต้องการอะไร

* **ทดสอบก่อนส่งมอบ** — รันโค้ดให้ผ่านก่อนบอกว่าเสร็จ

* **หน้าตาต้องเป็นธีม B.Grimm Power และกราฟใช้ Chart.js เท่านั้น** — อ่าน `bgrimm-web-guidelines.md` (อยู่โฟลเดอร์เดียวกับไฟล์นี้) ก่อนเขียน HTML/CSS ทุกครั้ง รายละเอียดอยู่ที่หัวข้อ 4.10

## 2. สภาพแวดล้อม (Environment)

### 2.1 ระบบปฏิบัติการ

* **OS:** Linux (Ubuntu-based)

* **Working Directory:** `echo $(pwd)`

* **Shell:** bash

* **Python:** 3.11 (มีให้ใช้แล้ว)

### 2.2 Platform: Cloud Coder

- รันผ่าน **proxy** — URL จะมีรูปแบบ `https://<host>/@<user>/<workspace>/apps/code-server/proxy/<port>/`
- **ไม่มี browser** บน server — ต้องเปิด URL จากเครื่องผู้ใช้เอง
- **ไม่มี GUI** — ทุกอย่างเป็น CLI หรือ web service
- **หากยังไม่รู้ `/@<user>/<workspace>/` ของผู้ใช้** — ให้ถามผู้ใช้ บอกให้ user copy `https://<host>/@<user>/<workspace>/apps/code-server/` จากช่อง URL ใน browser มาตอบ

### 2.3 Git

* **Git CLI ติดตั้งแล้ว** — ใช้ `git clone`, `git status`, `git diff` ได้ปกติ

* **ไม่มี git config user.name / user.email** โดย default — ทำให้ `git commit` และ `git push` ไม่ผ่าน (จะ error ขอให้ตั้งค่า)

* **ไม่มี git repo เริ่มต้น** ใน working directory (ตรวจสอบด้วย `git status` ก่อน)

* **ห้าม** run `git commit`, `git push`, `git reset`, `git rebase` โดยไม่ถามผู้ใช้ทุกครั้ง

* ถามยืนยันก่อนทุกครั้งที่ต้อง mutation git หรือ config git

## 3. เครื่องมือที่มีให้ใช้

### 3.1 Package Manager หลัก: `uv`

* **ใช้ `uv` เป็นหลัก** ไม่ใช้ `pip` ธรรมดา

* สร้าง project: `uv init`

* เพิ่ม dependency: `uv add <package>`

* รัน script: `uv run python <script.py>`

* ติดตั้ง dev dependency: `uv add --dev <package>`

### 3.2 สิ่งที่ไม่มี / ห้ามใช้

* ❌ ไม่มี Docker

* ❌ ไม่มี npm / node โดย default — **ไม่ต้องติดตั้ง Chart.js เอง** มีไฟล์เตรียมไว้แล้วที่ `vendor/chart.umd.js` (Chart.js v4.4.1) ในโฟลเดอร์เดียวกับไฟล์นี้ วิธีใช้ดูหัวข้อ 4.10

* ❌ ห้ามใช้ library กราฟตัวอื่น (Plotly, ECharts, Recharts, D3 ฯลฯ) และห้ามใช้ CSS framework (Tailwind, Bootstrap ฯลฯ) เพราะจะทับธีม B.Grimm

* ❌ ไม่มี conda

* ❌ **ห้ามติดตั้ง package นอก working directory** โดยไม่ถามผู้ใช้

## 4. กฎเหล็ก (Hard Rules)

### 4.1 กฎเหล็ก Network & Cloud Constraints (ห้ามละเมิดเด็ดขาด)

* **Single-Port Architecture (พอร์ตเดียวเบ็ดเสร็จ):**

  * **ห้ามแยก Port** ระหว่าง Frontend และ Backend เด็ดขาด

  * ต้องรันบน **Port 8000 พอร์ตเดียวเท่านั้น**

  * ให้ Backend (FastAPI) เป็นตัวเสิร์ฟหน้าเว็บ (HTML/JS/CSS) และ API ในตัว

* **Network Host Binding:**

  * เวลาเขียนโค้ดหรือคำสั่งรันเซิร์ฟเวอร์ ต้อง Bind host ที่ **`0.0.0.0` เสมอ** (ห้ามใช้ `127.0.0.1` หรือ `localhost` เด็ดขาด)

* **Subpath & Relative Path Rule (สำคัญที่สุด):**

  * ระบบทำงานอยู่ใต้ Subpath URL ของ Proxy

  * ฝั่ง Frontend (HTML/JS/CSS) เวลาอ้างอิงไฟล์ Asset หรือเขียนคำสั่ง `fetch()` เรียก API ของ backend ตัวเอง **ต้องใช้ Relative Path เสมอ** เช่น `fetch('./api/...')` หรือ `fetch('api/...')`

  * **ห้ามใส่ Root Slash นำหน้าเด็ดขาด** (เช่น ห้ามใช้ `/api/...` หรือ `/assets/...`) เพราะจะทำให้ Request หลุดไปยัง Root Domain ขององค์กร

### 4.2 การเชื่อมต่อ Mock Data API (แหล่งข้อมูลของ Dashboard)

ผู้ใช้จะได้ **Base URL**, **API key** และรายการ **Endpoint** จากเอกสารโจทย์ (หัวข้อ "สิ่งที่คุณได้รับ")

* **ต้องดึงข้อมูลผ่าน Backend เท่านั้น** — Backend (Python) เป็นคนเรียก Mock Data API ด้วย `httpx` หรือ `requests` แล้วส่งต่อให้หน้าเว็บผ่าน endpoint ของตัวเอง (เช่น `./api/...`)

  * **ห้ามให้หน้าเว็บ (JavaScript) เรียก Mock Data API ตรงๆ** และ **ห้ามใส่ API key ในไฟล์ HTML/JS** เพราะใครเปิดหน้าเว็บก็เห็นคีย์

  ```
  Browser ──fetch('./api/...')──▶ Backend :8000 ──GET + x-api-key──▶ Mock Data API
  ```

* **Base URL และ API key ต้องอยู่ใน `.env`** (ดู 4.3) เช่น

  ```
  DATA_API_BASE_URL=<Base URL จากเอกสารโจทย์>
  DATA_API_KEY=<คีย์จากเอกสารโจทย์>
  ```

* **ส่งคีย์ผ่าน Header `x-api-key`** ทุกคำขอ ถ้าได้ `401` แปลว่าไม่มีคีย์หรือคีย์ผิด

* **ตั้ง timeout อย่างน้อย 90 วินาที** — Mock Data API อยู่บนบริการฟรีที่ "หลับ" เมื่อไม่มีคนใช้ คำขอแรกอาจใช้เวลาตื่นราว 1 นาที ห้ามสรุปว่า API เสียจากคำขอแรกที่ช้า

* **ถ้าดึงข้อมูลไม่สำเร็จ ต้องแสดงข้อความ error บนหน้า Dashboard ให้ผู้ใช้เห็น** (เช่น "เชื่อมต่อ API ไม่ได้ (401)") ห้ามแสดงเป็นตัวเลข 0 หรือกราฟว่างแบบเงียบๆ

* **อ่านอย่างเดียว** — Mock Data API มีแต่ `GET` ห้ามเดา endpoint ที่ไม่มีในเอกสาร ถ้าไม่แน่ใจให้เปิด `<Base URL>/docs` ดูรายการ endpoint และพารามิเตอร์

* **ใช้ร่วมกันทั้งห้อง** — ห้ามยิง API ถี่กว่าที่โจทย์ต้องการ (ห้าม loop ยิงรัวๆ) ถ้าหน้าเว็บต้องรีเฟรชอัตโนมัติ ให้รีเฟรชไม่ถี่กว่าทุก 30 วินาที

### 4.3 เกี่ยวกับ `.env` และ Secrets

* **ห้ามอ่าน `.env` โดยตรง** — ให้ใช้ `pydantic-settings` หรือ `python-dotenv` โหลดผ่าน class/config object

* **ห้าม hardcode secrets** ลงในไฟล์ `.py`, `.html`, `.js` — ต้องมาจาก `.env` หรือ environment variables เสมอ

* **ห้าม hardcode URL ทุกชนิดลงในไฟล์ `.py`** — ทุก URL (Mock Data API, webhook, ฯลฯ) ต้องมาจาก `.env` เท่านั้น

* **ต้อง print env ก่อน launch ทุกครั้ง** — แสดงค่า config (mask API key) เพื่อให้ผู้ใช้ตรวจสอบว่าตั้งค่าถูกต้อง

* **ห้ามแก้ทั้งไฟล์ `.env`** — ถ้าต้องอ่าน `.env` เพื่อเช็ค ให้อ่านแค่ชื่อ variable ที่มีอยู่ (ไม่ต้องอ่านค่า โดยเฉพาะ API key) ถ้าต้องเพิ่ม variable ใหม่ ให้เพิ่มตัวใหม่เข้าไปเท่านั้น ไม่ rewrite ทั้งไฟล์

* ถ้ายังไม่มี `.env` ให้สร้าง `.env.example` (ใส่ชื่อ variable + ค่าตัวอย่าง) แล้ว **บอกผู้ใช้ให้ copy เป็น `.env` และใส่ค่าจริงเอง**

### 4.4 เกี่ยวกับการรัน (Run Scripts)

* **ต้องสร้าง `run.sh` สำหรับรันเสมอ** — แม้จะมี `run.py` อยู่แล้วก็ตาม

* `run.sh` ควรมี:

  ```
  #!/bin/bash
  set -e

  cd "$(dirname "$0")"
  fuser -k 8000/tcp 2>/dev/null || true
  uv run python run.py
  ```

* หลังรัน ให้บอกผู้ใช้ว่า URL คืออะไร (รูปแบบ `https://<host>/@<user>/<workspace>/apps/code-server/proxy/8000/`)

* บอก User ด้วยว่าต้องใช้คำสั่งอะไรไปวางบน Terminal เพื่อรันทดสอบ

### 4.5 เกี่ยวกับการแก้ไขไฟล์

* **อ่านก่อนแก้** — ใช้ `read` tool อ่านไฟล์ก่อน `edit` หรือ `write` เสมอ

* **แก้น้อยที่สุด** — เปลี่ยนแค่สิ่งที่จำเป็น ไม่ refactor เกิน scope

* **ไม่สร้างไฟล์ใหม่** ถ้าไม่จำเป็น — แก้ไฟล์เดิมให้ได้ก่อน

### 4.6 เกี่ยวกับการทดสอบ

* **รันก่อนบอกเสร็จ** — `uv run python -c "..."` หรือ `uv run pytest` หรือ `./run.sh`

* **ทดสอบการเชื่อมต่อ API จริง** — ยิง endpoint ของ backend ตัวเองด้วย `curl http://127.0.0.1:8000/api/...` (ใช้ทดสอบในเครื่องได้ แต่ตอนรัน server ยัง bind `0.0.0.0`) แล้วดูว่าได้ข้อมูลจริง ไม่ใช่ error

* **ห้ามใช้ข้อมูลปลอม (mock/dummy) แทนข้อมูลจาก API** เพื่อให้หน้าเว็บ "ดูเหมือนทำงานได้" — ถ้า API ใช้ไม่ได้ ให้แจ้งผู้ใช้ตรงๆ

* ถ้ามี error ต้องแก้ให้ผ่านก่อน

* ถ้าเป็น web app ให้ compile/verify ว่าไม่มี syntax error

* **หลังจากรัน app เพื่อ test หรือ debug ต้อง kill process นั้นทุกครั้งเสมอ** — ใช้ `kill $(lsof -t -i:8000)` หรือ `fuser -k 8000/tcp` เพื่อไม่ให้ port ค้างอยู่

* **ห้ามปล่อยให้ port ค้าง** — ผู้ใช้ต้องไม่เจอปัญหา "port already in use" เพราะ AI ไม่ได้ kill process ก่อนหน้า

### 4.7 เกี่ยวกับการใช้ Library (อย่า Reinvent the Wheel)

* **ใช้ Library สำเร็จรูปก่อนเสมอ** — อย่าเขียนเองตั้งแต่ต้น เช่น:

  * HTTP requests → `httpx` หรือ `requests`

  * จัดการตาราง/คำนวณข้อมูล → `pandas`

  * กราฟ → **Chart.js เท่านั้น** ใช้ไฟล์ที่เตรียมไว้ (หัวข้อ 4.10)

* **ถ้าไม่มี Library ใน project → ติดตั้งเลย** ด้วย `uv add <package>`

* **ถ้าไม่แน่ใจว่ามี Library อะไรให้ใช้ → ให้ถามผู้ใช้หรือ search หาก่อน** ไม่เขียน custom implementation เอง

### 4.8 UI Framework: FastAPI + HTML/CSS/JS เท่านั้น

* ใช้ **FastAPI เสิร์ฟหน้าเว็บ HTML/CSS/JS ธรรมดา** ไม่ต้องถามผู้ใช้ให้เลือก (ไม่ใช้ Streamlit เพราะใช้ Chart.js และธีม B.Grimm ไม่ได้)

* ต้องรันบน **Port 8000** และใช้งานผ่าน Proxy ได้ `fetch()` และการอ้างไฟล์ต้องเป็น Relative Path

* ออกแบบหน้าจอตาม **อุปกรณ์ที่ระบุในการ์ด Requirement** (มือถือ แท็บเล็ต โน้ตบุ๊ก หรือทีวี)

### 4.9 เกี่ยวกับ Sync/Async

* **อย่า block event loop** — ถ้าใช้ FastAPI แบบ `async def` ให้เรียก API ภายนอกด้วย `httpx.AsyncClient` หรือใช้ `def` ธรรมดา (FastAPI จะรันใน thread ให้เอง) ห้ามใช้ `requests` ข้างใน `async def`

* **อย่าเรียก async function จาก sync function โดยตรง** — ถ้าจำเป็นต้องใช้ `asyncio.run()` ให้ระวัง event loop ที่กำลังทำงานอยู่

### 4.10 ธีม B.Grimm Power และ Chart.js

**ธีม (ต้นฉบับ: `bgrimm-web-guidelines.md`)**

* อ่าน `bgrimm-web-guidelines.md` ก่อนเขียน CSS แล้ว **คัดลอก CSS variables (`:root { ... }`) ทั้งฟอนต์และสีจากไฟล์นั้นไปใส่ `static/style.css` ตามตัวอักษร** ห้ามคิดค่าสีหรือฟอนต์ใหม่เอง

* ทุกสีในหน้าเว็บต้องอ้างผ่าน `var(--...)` ห้ามเขียนรหัสสีลอยๆ ใน CSS/JS (ยกเว้นใน `chart-theme.js` ที่อ่านค่าจาก CSS variables)

* ฟอนต์: หัวข้อ H1–H3 ใช้ `var(--font-heading)` เนื้อหา/ตาราง/ปุ่มใช้ `var(--font-body)` รหัสหรือ ID ใช้ `var(--font-mono)` โหลดฟอนต์ผ่าน Google Fonts `<link>` ได้ ถ้าโหลดไม่ได้ fallback ในไฟล์ guideline จะทำงานแทน

* **ตัวเลข KPI และตารางต้องมี `font-variant-numeric: tabular-nums;`**

* ข้อความใช้ `--text-strong` / `--text` / `--muted` เท่านั้น **ห้ามใช้ `#202124` และห้ามใช้สีดำสนิท**

* พื้นหน้า `--bg` การ์ด `--surface` ขอบ `--border` (1px) ปุ่มหลักพื้น `--primary` ตัวหนังสือขาว

* สีสถานะ (`--success` / `--warning` / `--danger`) ใช้กับ **สถานะเท่านั้น** ต้องคู่กับพื้นโทนอ่อน `--*-bg` และมีข้อความกำกับเสมอ (เช่น "ปกติ", "เฝ้าระวัง") ห้ามสื่อสถานะด้วยสีอย่างเดียว และห้ามเอาสีสถานะไปใช้เป็นสีของชุดข้อมูลทั่วไป

**Chart.js (เตรียมไว้แล้ว ไม่ต้องติดตั้ง)**

* ไฟล์อยู่ที่ `vendor/chart.umd.js` ในโฟลเดอร์เดียวกับไฟล์นี้ **คัดลอกเข้าโปรเจกต์** เป็น `static/vendor/chart.umd.js` แล้วโหลดด้วย Relative Path:

  ```html
  <script src="static/vendor/chart.umd.js"></script>
  <script src="static/chart-theme.js"></script>
  <script src="static/app.js"></script>
  ```

  ห้ามใช้ `/static/...` (มี `/` นำหน้า) และไม่ต้องโหลดจาก CDN วาง `<script>` ทั้งสามไว้ท้าย `<body>` หลังโหลด `style.css` แล้ว เพื่อให้ `chart-theme.js` อ่าน CSS variables ได้

* สร้าง `static/chart-theme.js` ตั้งค่ากลางครั้งเดียว ให้ทุกกราฟหน้าตาเหมือนกัน:

  ```js
  const css = getComputedStyle(document.documentElement);
  const token = (name) => css.getPropertyValue(name).trim();

  Chart.defaults.font.family = token('--font-body');
  Chart.defaults.color = token('--text');
  Chart.defaults.borderColor = token('--border');
  Chart.defaults.plugins.legend.labels.usePointStyle = true;
  Chart.defaults.plugins.tooltip.backgroundColor = token('--text-strong');

  // สีชุดข้อมูล: ตัวหลักเป็นส้ม ตัวเปรียบเทียบเป็นเทา
  const SERIES = {
    main: token('--primary'),        // เช่น วันนี้ / เดือนนี้ / ค่าจริง
    compare: token('--muted'),       // เช่น เมื่อวาน / เดือนก่อน / ค่าปกติ / เป้า
    mainSoft: token('--primary-soft'),
  };
  ```

* **หลักการเลือกสีกราฟ:** ชุดข้อมูลที่ผู้ใช้ต้องดูเป็นหลักใช้ `--primary` ชุดที่ใช้เทียบใช้สีเทา (`--muted` หรือ `--text`) ถ้ามีหลายกลุ่มให้ใช้ส้มกับเทาหลายระดับ หรือแยกเป็นกราฟย่อย ห้ามสุ่มสีรุ้ง

* กราฟเส้นความหนา 2px แท่งกราฟมุมมน 4px ใช้แกน Y แกนเดียว (ห้ามมีแกน Y สองฝั่ง) ปิดเส้น grid แนวตั้ง และใส่หน่วยในชื่อแกนหรือ tooltip เสมอ

* เมื่อรีเฟรชข้อมูล ให้อัปเดตกราฟเดิมด้วย `chart.data = ...; chart.update()` **ห้ามสร้าง `new Chart()` ซ้ำบน canvas เดิม** (จะ error "Canvas is already in use") ถ้าจำเป็นต้องสร้างใหม่ให้ `chart.destroy()` ก่อน

* ใส่ `maintainAspectRatio: false` และกำหนดความสูงให้กล่องที่ครอบ `<canvas>` เพื่อให้กราฟพอดีกับอุปกรณ์ในการ์ด Requirement

## 5. การเริ่มต้นและโครงสร้างโปรเจกต์ (Project Setup & Structure)

### 5.1 สร้าง Project ใหม่

```
uv init <project-name>
cd <project-name>
uv add fastapi uvicorn httpx pydantic-settings
mkdir -p static/vendor
cp ../vendor/chart.umd.js static/vendor/           # Chart.js ที่เตรียมไว้ (ปรับ path ให้ชี้ไปโฟลเดอร์ที่มี AGENTS.md)
```

### 5.2 โครงสร้างไฟล์มาตรฐาน

```
<project>/
├── src/<package>/
│   ├── __init__.py
│   ├── config.py       # Pydantic Settings + .env loader
│   ├── data_client.py  # เรียก Mock Data API (httpx + x-api-key + timeout)
│   └── app.py          # FastAPI app (เสิร์ฟ static/ และ ./api/...)
├── static/
│   ├── index.html
│   ├── style.css       # :root tokens คัดลอกจาก bgrimm-web-guidelines.md
│   ├── chart-theme.js  # Chart.defaults ตามธีม
│   ├── app.js          # fetch('./api/...') + สร้าง/อัปเดตกราฟ
│   └── vendor/chart.umd.js
├── .env                # Base URL + API key (อย่า commit)
├── .env.example        # template สำหรับผู้ใช้
├── run.py              # Python entry point
├── run.sh              # Shell wrapper (ต้องมี)
└── pyproject.toml      # uv สร้างให้
```

### 5.3 การจัดการ Config

* **ใช้ `pydantic-settings` โหลดจาก `.env` เสมอ**

* **ต้องมี validation** (ไม่ให้ค่าว่าง, ไม่ให้ placeholder)

* **ต้องมี `load_and_print_config()`** ที่ print ค่าออกมาก่อนเริ่มทำงาน (ต้อง Mask Secrets เช่น แสดง `***2026`)

**ตัวอย่าง `src/<package>/config.py`:**

```
from pydantic import Field, SecretStr, field_validator
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
        extra="ignore"
    )

    # App Config
    host: str = Field(default="0.0.0.0")
    port: int = Field(default=8000)

    # Mock Data API
    data_api_base_url: str = Field(..., description="Base URL ของ Mock Data API จากเอกสารโจทย์")
    data_api_key: SecretStr = Field(..., description="API key ส่งใน header x-api-key")
    data_api_timeout: float = Field(default=90.0, description="วินาที (บริการฟรีอาจต้องตื่นจากหลับ)")

    @field_validator("data_api_base_url")
    @classmethod
    def check_url(cls, v: str) -> str:
        if not v.startswith("https://") or "<" in v:
            raise ValueError("DATA_API_BASE_URL ต้องเป็น https://... จากเอกสารโจทย์")
        return v.rstrip("/")

    def print_config(self) -> None:
        """พิมพ์ Config ออกมาก่อนรัน โดย Mask ข้อมูลความลับ"""
        key = self.data_api_key.get_secret_value()
        masked_key = f"***{key[-4:]}" if len(key) >= 4 else "***"
        print("=" * 40)
        print("  APPLICATION CONFIGURATION")
        print("=" * 40)
        print(f"Host              : {self.host}")
        print(f"Port              : {self.port}")
        print(f"Data API Base URL : {self.data_api_base_url}")
        print(f"Data API Key      : {masked_key}")
        print(f"Data API Timeout  : {self.data_api_timeout}s")
        print("=" * 40)


def load_and_print_config() -> Settings:
    settings = Settings()
    settings.print_config()
    return settings
```

## 6. ข้อจำกัดของ Platform (ที่ต้องระวัง)

| **ปัญหา** | **วิธีรับมือ** |
|---|---|
| **Reverse Proxy Subpath** | หน้าเว็บต้องเรียก API ของ backend ด้วย Relative Path (`./api/...`) เสมอ |
| **API key หลุดในหน้าเว็บ** | เรียก Mock Data API จาก Backend เท่านั้น คีย์อยู่ใน `.env` |
| **คำขอแรกช้า / timeout** | Mock Data API ตื่นจากหลับราว 1 นาที ตั้ง timeout ≥ 90 วินาที แล้วลองใหม่ |
| **ได้ 401** | ตรวจ `DATA_API_KEY` ใน `.env` และชื่อ header `x-api-key` |
| **ไม่มี browser บน server** | บอกผู้ใช้ให้เปิด URL จากเครื่องตัวเอง |
| **ไม่มี git repo เริ่มต้น** | ถามผู้ใช้ก่อน init git |
| **ไม่มี Docker** | ใช้ `uv` + virtual env แทน |
| **Port ซ้ำซ้อน** | บังคับรันบน Port 8000 พอร์ตเดียว และสั่ง Kill Process ก่อน Launch |
| **`Chart is not defined`** | ตรวจว่าคัดลอก `chart.umd.js` เข้า `static/vendor/` แล้ว และ `<script>` ใช้ Relative Path ไม่มี `/` นำหน้า |
| **`Canvas is already in use`** | อัปเดตกราฟเดิมด้วย `chart.update()` หรือ `destroy()` ก่อนสร้างใหม่ |
| **ฟอนต์ไทยเป็นกล่องสี่เหลี่ยม** | ใช้ font stack จาก `bgrimm-web-guidelines.md` ครบทุกตัว (มี Tahoma สำรอง) |

## 7. สรุปสิ่งที่ต้องทำทุกครั้ง (Checklist)

- [ ] อ่านไฟล์ก่อนแก้

- [ ] ใช้ `uv` จัดการ dependencies

- [ ] บังคับผูก Host **0.0.0.0** และ Port **8000** พอร์ตเดียวเท่านั้น

- [ ] ฝั่ง Frontend เรียก API ด้วย **Relative Path** (ห้ามมี `/` นำหน้า)

- [ ] เรียก Mock Data API **จาก Backend เท่านั้น** ส่ง `x-api-key` และตั้ง timeout ≥ 90 วินาที

- [ ] Base URL และ API key อยู่ใน `.env` — ไม่มีใน `.py` / `.html` / `.js`

- [ ] แสดง error บนหน้า Dashboard เมื่อดึงข้อมูลไม่สำเร็จ ไม่ใช้ข้อมูลปลอมแทน

- [ ] สร้าง/อัปเดต `run.sh`

- [ ] ไม่อ่าน `.env` โดยตรง — ใช้ `pydantic-settings` ผ่าน config class

- [ ] Print config (พร้อม Mask API key) ก่อน launch เสมอ

- [ ] รันทดสอบก่อนบอกเสร็จ (ยิง endpoint ของ backend ดูว่าได้ข้อมูลจริง)

- [ ] CSS ใช้ tokens จาก `bgrimm-web-guidelines.md` ไม่มีรหัสสีลอย ไม่มี `#202124` ตัวเลข KPI/ตารางมี `tabular-nums`

- [ ] กราฟใช้ Chart.js จาก `static/vendor/chart.umd.js` ผ่าน `chart-theme.js` ชุดหลักสีส้ม ชุดเทียบสีเทา

- [ ] **Kill process บน Port 8000 หลังรัน test/debug** — อย่าปล่อย port ค้าง

- [ ] ถามก่อน git mutation
