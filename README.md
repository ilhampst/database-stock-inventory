# 🏠 Telegram Home Inventory Bot (Google Sheets Backend)

Bot Telegram berbasis **Google Apps Script** untuk mencatat, memantau,
dan mengelola stok rumah menggunakan **Google Sheets sebagai backend database**.

<a id="bahasa-indonesia"></a>
<a id="english"></a>

[🇮🇩 Bahasa Indonesia](#bahasa-indonesia) | [🇬🇧 English](#english)

---

## 📑 Table of Contents

### Bahasa Indonesia
- [Deskripsi](#deskripsi)
- [Arsitektur Sistem](#arsitektur-sistem)
- [Fitur Utama](#fitur-utama)
- [Struktur Google Sheet](#struktur-google-sheet)
- [Daftar Command](#daftar-command)
- [Setup & Deployment](#setup--deployment)
- [Keamanan](#keamanan)
- [Roadmap](#roadmap)

### English
- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Core Features](#core-features)
- [Google Sheet Schema](#google-sheet-schema)
- [Commands](#commands)
- [Deployment](#deployment)
- [Security Notes](#security-notes)
- [License](#license)

---

## Bahasa Indonesia

<a id="deskripsi"></a>
### Deskripsi

Telegram Home Inventory Bot adalah bot Telegram berbasis **Google Apps Script**
yang digunakan untuk mencatat, memantau, dan mengelola stok rumah
(makanan, kebutuhan rumah tangga, dll) dengan **Google Sheets sebagai database utama**.

Bot ini dirancang untuk:
- input cepat via chat Telegram
- database tetap terstruktur dan mudah diaudit
- tanpa server tambahan (serverless)

---

<a id="arsitektur-sistem"></a>
### Arsitektur Sistem

```

Telegram User
│
▼
Telegram Bot API (Webhook)
│
▼
Google Apps Script (Web App)
│
▼
Google Sheets (Inventory Database)

```

Prinsip desain:
- Telegram bertindak sebagai **UI**
- Seluruh business logic berada di **Apps Script**
- Google Sheets adalah **single source of truth**

---

<a id="fitur-utama"></a>
### Fitur Utama

**Manajemen Stok**
- Tambah item baru atau update stok otomatis (`/add`)
- Kurangi stok berdasarkan `ITEM_ID` (`/use`)
- Satuan fleksibel (pcs, kg, liter, dll)

**Pencarian & Filter**
- Lihat semua stok (`/stock`)
- Filter kategori (FD, OT, dll)
- Search berbasis string (case-insensitive)
- Opsi tampilkan `ITEM_ID`

**Monitoring**
- `/stock low` → stok di bawah batas aman
- `/stock expire` → item mendekati / melewati tanggal kadaluarsa
- Threshold expire dapat dikustomisasi

**Help & UX**
- `/help` dengan inline keyboard
- Help spesifik per command (`/help add`, `/help low`, dll)
- Kombinasi klik (UI) dan command (power user)

**(Opsional) Notifikasi Harian**
- Stok LOW
- Item mendekati EXPIRE
- Dikirim otomatis ke admin chat

---

<a id="struktur-google-sheet"></a>
### Struktur Google Sheet

Sheet **WAJIB** sesuai konfigurasi (`inventory` secara default).

| Kolom | Field | Deskripsi |
|------|------|----------|
| A | item_id | ID unik item |
| B | nama | Nama item |
| C | kategori | FD, OT, dll |
| D | lokasi | Lokasi penyimpanan |
| E | qty | Jumlah stok |
| F | satuan | pcs, kg, liter |
| G | min_qty | Batas aman stok |
| H | expire | Tanggal expire (YYYY-MM-DD) |
| I | (opsional) | Bebas |

Definisi stok LOW:
```

qty < min_qty

```

---

<a id="daftar-command"></a>
### Daftar Command

**Tambah / Update Stok**
```

/add <Nama> <qty> <satuan> loc:<Lokasi> cat:<Kategori> min:<min_qty> exp:<YYYY-MM-DD>

```

Contoh:
```

/add Mie_Goreng 3 pcs loc:Box_transparan cat:FD min:2

```

**Kurangi Stok**
```

/use <ITEM_ID> <qty>

```

**Lihat Stok**
```

/stock
/stock FD
/stock mie
/stock id
/stock FD mie id

```

**Stok LOW**
```

/stock low
/stock low FD mie id

```

**Stok EXPIRE**
```

/stock expire
/stock expire 3

```

**Bantuan**
```

/help
/help add
/help stock
/help low
/help expire

````

<a id="setup--deployment"></a>
### Setup & Deployment

1. Buat bot Telegram menggunakan `@BotFather`
2. Buat project **Google Apps Script**
3. Paste seluruh source code bot
4. Konfigurasi:
   
   const TOKEN = "BOT_TOKEN";
   const SHEET_NAME = "inventory";
   const ADMIN_CHAT_ID = 123456789;

5. Deploy sebagai **Web App**

   * Access: Anyone
6. Set webhook:

   ```
   https://api.telegram.org/bot<BOT_TOKEN>/setWebhook?url=<WEB_APP_URL>
   ```

---

<a id="keamanan"></a>

### Keamanan

* Jangan commit `BOT_TOKEN` ke repository publik
* Gunakan placeholder di repo
* Revoke token jika terjadi kebocoran

---

<a id="roadmap"></a>

### Roadmap

* Status otomatis (OK / LOW / EXP) di sheet
* Notifikasi hanya saat status berubah
* Shopping list otomatis
* Multi-database (rumah / kantor)

---

## English

<a id="overview"></a>

### Overview

Telegram Home Inventory Bot is a **Telegram bot built on Google Apps Script**
to manage and monitor home inventory using **Google Sheets as the backend database**.

The system is designed to be chat-first, serverless, and easy to audit.

---

<a id="system-architecture"></a>

### System Architecture

```
Telegram User
    │
    ▼
Telegram Bot API (Webhook)
    │
    ▼
Google Apps Script (Web App)
    │
    ▼
Google Sheets (Inventory Database)
```

---

<a id="core-features"></a>

### Core Features

* Add or update stock (`/add`)
* Consume stock using `ITEM_ID` (`/use`)
* Flexible units
* Search and filter inventory
* Low stock and expiration monitoring
* Inline help with command-specific documentation
* Optional daily notifications

---

<a id="google-sheet-schema"></a>

### Google Sheet Schema

| Column | Field    | Description      |
| ------ | -------- | ---------------- |
| A      | item_id  | Unique item ID   |
| B      | name     | Item name        |
| C      | category | FD, OT, etc      |
| D      | location | Storage location |
| E      | qty      | Quantity         |
| F      | unit     | pcs, kg, liter   |
| G      | min_qty  | Safe minimum     |
| H      | expire   | Expiry date      |
| I      | optional | Free             |

LOW definition:

```
qty < min_qty
```

---

<a id="commands"></a>

### Commands

```
/add
/use
/stock
/stock low
/stock expire
/help
```

Commands support filtering, search, and combinations.

---

<a id="deployment"></a>

### Deployment

1. Create a Telegram bot via `@BotFather`
2. Configure Google Apps Script
3. Deploy as Web App
4. Set Telegram webhook

---

<a id="security-notes"></a>

### Security Notes

* Never commit bot tokens to public repositories
* Rotate token if compromised

---

<a id="license"></a>

### License

MIT / Personal Use
Feel free to fork and modify.

---

Happy inventory tracking 📦
