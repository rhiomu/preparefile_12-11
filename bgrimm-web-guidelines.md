# B.Grimm Power — Web & Dashboard Design Guidelines

คู่มือมาตรฐานฟอนต์ (Typography) และระบบสี (Color System) สำหรับงานพัฒนา Website และ Dashboard ของ B.Grimm Power โดย Digital Project Office (DPO)

---

## 1. Typography (ระบบฟอนต์สองภาษาและ Fallback)

### Font Stack & CSS Variables
```css
:root {
  /* Heading: ภาษาอังกฤษแสดงผลแบบ Serif พรีเมียม / ภาษาไทย Fallback เป็น Sarabun */
  --font-heading: 'Source Serif Pro', 'Times New Roman', 'Sarabun', serif;

  /* Body & UI: รองรับทั้งไทย-อังกฤษ เนียนตา พร้อม System Font สำรองกรณีออฟไลน์ */
  --font-body: 'Noto Sans Thai', Arial, Tahoma, sans-serif;

  /* Monospace: สำหรับ Code Block, API Logs หรือ ID อ้างอิงทางเทคนิค */
  --font-mono: 'JetBrains Mono', 'Fira Code', Consolas, monospace;
}
```

### แนวทางการนำไปใช้งาน (Usage Rules)
* **Heading (หัวข้อและพาดหัว):**
  * กำหนดใช้ `--font-heading` สำหรับ H1–H3 และ Hero Banners
  * ตัวอักษรภาษาอังกฤษจะดึงความพรีเมียมของ `Source Serif Pro` มาแสดงผลโดยอัตโนมัติ
  * ตัวอักษรภาษาไทยจะ Fallback ไปยัง `Sarabun` (มีหัว คลาสสิก แมตช์กับฟอนต์สไตล์ Serif)
* **Body & UI (เนื้อหา, ตาราง, ฟอร์ม, เมนู และปุ่มกด):**
  * กำหนดใช้ `--font-body` เป็นค่าเริ่มต้นหลักของหน้าเว็บ
  * มี `Noto Sans Thai` เป็นแกนหลัก และมี `Tahoma` สำรองในระดับ OS ป้องกันปัญหาสระลอยหรือฟอนต์กล่องสี่เหลี่ยม (Tofu)
* **ตัวเลขสถิติและตารางข้อมูล (KPIs & Data Tables):**
  * ต้องเพิ่มคุณสมบัติ `font-variant-numeric: tabular-nums;` เสมอ เพื่อให้ตัวเลขในตารางและตัวเลข Dashboard ตรงกันในแนวดิ่ง

---

## 2. Color Palette (ระบบสีทางการ)

### CSS Color Tokens
```css
:root {
  /* Brand & Accent */
  --primary: #FF6727;          /* Corporate Orange: สีส้มหลักของแบรนด์, ปุ่ม CTA, จุดโฟกัส */
  --primary-dark: #D94D16;     /* Dark Orange: สถานะ Hover, ลิงก์ข้อความขนาดเล็ก */
  --primary-soft: #FFF0E8;     /* Pale Wash: พื้นหลังการ์ดที่ Active, แบดจ์ไอคอน */
  --primary-border: #FFD4BF;   /* ขอบเน้นสำหรับ Highlighted Cards */

  /* Surface & Neutral */
  --bg: #F8F8FA;               /* พื้นหลังหน้า Dashboard / Canvas */
  --surface: #FFFFFF;          /* พื้นหลังการ์ดหลัก และ Container สีขาว */
  --surface-soft: #F2F2F2;     /* Card Background ทั่วไป, แถบหัวตาราง, Input Fill */
  --border: #ECECEF;           /* เส้นขอบการ์ด และเส้นแบ่งตาราง (Hairlines) */

  /* Text Hierarchy (Strictly NO #202124) */
  --text-strong: #404040;      /* Charcoal Grey: หัวข้อหลัก, ตัวเลข KPI, ข้อความเนื้อหาสำคัญ */
  --text: #63666F;             /* Soft Grey: คำอธิบายรอง, เนื้อหาในตาราง */
  --muted: #989AA5;            /* Muted Grey: วันที่อัปเดต, แคปชัน, หน่วยกำกับ */

  /* Functional Status (Apple System Standard) */
  --success: #34C759;          /* สำเร็จ / ปกติ / Online */
  --success-bg: #EAFBEF;
  --warning: #FFCC00;          /* เฝ้าระวัง / แจ้งเตือน / Pending */
  --warning-bg: #FFF9E6;
  --danger: #FF3B30;           /* วิกฤต / ผิดพลาด / Offline */
  --danger-bg: #FFEBEA;
}
```

### สรุปการจับคู่สีใช้งาน (Color Application Summary)

| วัตถุประสงค์ | รหัสสี (Token) | การแสดงผลและการจับคู่สี |
| :--- | :--- | :--- |
| **Primary Action** | `#FF6727` | ปุ่มกดหลัก (ตัวหนังสือขาว `#FFFFFF`), ไฮไลต์กราฟ |
| **Card Active / Selected** | `#FFF0E8` | พื้นหลังการ์ดที่ถูกเลือก กรอบใช้ `#FFD4BF` |
| **App Canvas / Page** | `#F8F8FA` | พื้นหลังหลักทั้งหน้า เพื่อขับให้การ์ดสีขาว `#FFFFFF` ลอยเด่น |
| **General Cards & Tables** | `#F2F2F2` & `#ECECEF` | การ์ดข้อมูลทั่วไป เส้นแบ่งใช้ Hairline บาง 1px |
| **High Contrast Text** | `#404040` | ให้อ่านง่าย คอนทราสต์ชัด สบายตากว่าสีดำสนิท |
| **Secondary Descriptions** | `#63666F` | ลดน้ำหนักสายตา ใช้กับคำอธิบายรอง |
| **Status Indicators** | เขียว / เหลือง / แดง | ใช้คู่กับพื้นหลังโทนอ่อน (`--*-bg`) เสมอ |
