# 🧠 MedRemind AI — AI Asosidagi Dori Eslatma Telegram Bot

## 📌 1. Loyihaning Umumiy Maqsadi

**MedRemind AI** — foydalanuvchilarga dori-darmonlarni **o‘z vaqtida ichishni eslatadigan**, AI yordamida **dorilar haqida batafsil tibbiy maʼlumot beradigan**, hamda dori kurslarini boshqaradigan **zamonaviy Telegram bot loyihasi**.

Loyiha hech qanday web-framework (**Django**, **FastAPI**) ishlatmaydi. Faqat:

* **aiogram** — Telegram bot yaratish uchun
* **PostgreSQL** — maʼlumotlar bazasi uchun
* **apscheduler** — rejalashtirilgan eslatmalar uchun
* **OpenAI API** — AI asosidagi dori maʼlumotlarini yaratish uchun

### 🔹 Botning asosiy vazifalari

* Foydalanuvchidan **doktor tomonidan yozilgan retsept** asosida:

  * Dorining nomi
  * Necha mahal ichiladi
  * Qaysi vaqtlarda ichiladi
  * Ovqatdan oldin/ovqatdan keyin/inson ovqatlanayotganda ichish holati
    kabi maʼlumotlarni qabul qiladi.

* Belgilangan vaqt kelganda avtomatik tarzda **eslatma yuboradi**.

* Foydalanuvchi **“Ichdim”** tugmasini bosmaguncha bot **har 5 daqiqada** takroriy eslatma yuboradi.

* “**Dori haqida**” tugmasi bosilganda foydalanuvchi dorining nomini yuboradi, bot esa AI orqali quyidagi maʼlumotlarni taqdim etadi:

  * Tarkibi
  * Qaysi kasalliklarda ishlatilishi
  * Qanday tartibda ichilishi
  * Dozalash bo‘yicha yo‘riqnoma
  * Yon taʼsirlari

* Foydalanuvchi umumiy simptom bo‘yicha savol bersa:

  **Misol:** “Mening boshim og‘riyapti, qanday dorini maslahat berasan?”

  AI foydalanuvchi shikoyatini tahlil qilib, mos dorilarni tavsiya qiladi.

---

## 🏗 2. Loyiha Arxitekturasi

Loyiha quyidagi 4 ta asosiy moduldan tashkil topgan:

### **1) Telegram Bot (Aiogram)**

* Foydalanuvchi bilan muloqot
* Dori qidirish
* Kurs qo‘shish
* Eslatma yuborish
* “Ichdim” tugmasi boshqaruvi
* Statistika chiqarish

### **2) PostgreSQL Database Layer (dbx/)**

* SQL jadvallarni yaratish
* CRUD amallar
* Foydalanuvchining dorilari
* Dori AI kesh
* Log yozish

### **3) Scheduler (APScheduler)**

* Dori vaqtlari bo‘yicha job yaratadi
* Eslatma yuboradi
* “Ichdim” tugmasi bosilmaguncha 5 daqiqada takrorlaydi

### **4) OpenAI AI Module**

* Dorilar haqida AI asosida tibbiy izoh
* Simptom bo‘yicha mos dori tavsiyasi
* Keshga yozish (tez ishlashi uchun)

---

## 📂 3. To‘liq Papka Struktura

```
medremind_ai/
│── bot.py
│── config.py
│── scheduler.py
│── ai_client.py
│── requirements.txt
│
│── dbx/
│   ├── __init__.py
│   ├── connection.py
│   ├── schema.py
│   ├── users.py
│   ├── medications.py
│   ├── ai_cache.py
│   ├── user_medications.py
│   └── intake_log.py
│
│── handlers/
│   ├── __init__.py
│   ├── start.py
│   ├── search_med.py
│   ├── add_course.py
│   ├── reminders.py
│   └── help.py
│
│── utils/
│   ├── validators.py
│   ├── formatter.py
│   └── keyboards.py
│
│── docker-compose.yml
│── Dockerfile
│── README.md
```

---

## 📘 4. Har Bir Faylning Vazifasi

### 🔹 **bot.py**

Botni ishga tushirish, event loop, dispatcher va handlerlarni ulash.

### 🔹 **config.py**

Tokenlar, DB URL, OpenAI API Key, sozlamalar.

### 🔹 **scheduler.py**

APScheduler yordamida job yaratish, o‘chirish, takrorlash, eslatma yuborish.

### 🔹 **ai_client.py**

OpenAI API chaqiruvlari, dori AI maʼlumotlari va caching.

### **dbx/** — PostgreSQL moduli

| Fayl                | Vazifa                          |
| ------------------- | ------------------------------- |
| connection.py       | asyncpg yordamida DB ga ulanish |
| schema.py           | CREATE TABLE SQL buyruqlari     |
| users.py            | Foydalanuvchi CRUD              |
| medications.py      | Dorilar CRUD                    |
| ai_cache.py         | AI javoblarini kesh saqlash     |
| user_medications.py | Foydalanuvchi kurslari          |
| intake_log.py       | “Ichdim” loglari                |

### **handlers/** — Telegram komandalar

| Fayl          | Vazifasi                       |
| ------------- | ------------------------------ |
| start.py      | /start — ro‘yxatdan o‘tish     |
| search_med.py | Dori haqida AI orqali maʼlumot |
| add_course.py | Dori kursi yaratish            |
| reminders.py  | “Ichdim / O‘tkazdim” tugmalari |
| help.py       | /help                          |

### **utils/** — yordamchi modul

* validators.py — vaqt, sana, va kiruvchi maʼlumotlarni tekshirish
* formatter.py — xabarlarni chiroyli formatlash
* keyboards.py — Inline tugmalar

---

## 🧬 5. AI Promptlar

### 🔹 **Dori haqida AI izoh**

```
Quyidagi dori haqida oddiy va tushunarli tibbiy izoh tayyorla:
- Tarkibi
- Qaysi kasalliklarda qo‘llanadi
- Qanday tartibda ichiladi
- Dozalash bo‘yicha yo‘riqnoma
- Yon ta’sirlari

Dori nomi: {drug_name}
```

### 🔹 **Simptom bo‘yicha tavsiya**

```
Foydalanuvchining quyidagi shikoyatini tahlil qilib, dori tavsiyasi ber:
"{symptom_text}"
```

---

## ⏱ 6. Scheduler Ishlash Tartibi

1. Foydalanuvchi dori kursini yaratadi
2. Malumotlar DB ga yoziladi
3. Har bir vaqt uchun alohida scheduler job yaratiladi
4. Belgilangan vaqtda eslatma yuboriladi
5. “Ichdim” bosilmasa bot 5 daqiqadan so‘ng qayta yuboradi
6. “Ichdim” bosilganda job to‘xtatiladi va intake_log ga yoziladi

---

## 🔬 7. Test Ssenariylari

* /start orqali ro‘yxatdan o‘tish
* AI orqali dori qidirish
* Yangi dori kursi qo‘shish
* Scheduler orqali signal chiqishi
* "Ichdim" tugmasi bosilishi
* Statistikalarni tekshirish

---

## 🚀 8. Kelajakdagi Takomillashtirish

* Dorixonalar API integratsiyasi
* Oila aʼzolarini boshqarish
* Foydalanuvchi tibbiy kartochkasi
* Klinikalar bilan API integratsiya
* Dori QR kod skaneri qo‘llab-quvvatlash

---
