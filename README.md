# medTgBot

# **MedRemind AI — AI Asosidagi Dori Eslatma Telegram Bot (Full Project)**

## 📌 **1. Loyihaning umumiy maqsadi**

**MedRemind AI** — foydalanuvchilar uchun dori-darmonlarni **o‘z vaqtida ichishni eslatib turuvchi**, AI yordamida **dori haqida batafsil tibbiy ma’lumot beruvchi**, kurslarni boshqaruvchi **zamonaviy Telegram bot loyihasi**.

Loyiha hech qanday web-framework (**Django**, **FastAPI**) ishlatmaydi. Faqat:

* **aiogram** — Telegram bot uchun
* **PostgreSQL** — ma’lumotlar bazasi
* **apscheduler** — rejalashtirilgan eslatmalar
* **OpenAI API** — dori haqida AI orqali izoh yaratish

Bot quyidagi funksiyalarni bajaradi:

* Foydalanuvchi retsepi asosida dorilarni necha mahal ichishi, qaysi vaqtda ichishi, ovqatdan oldin/yoki keyin ichilishi haqidagi kurs maʼlumotlarini qabul qiladi.
* Vaqti kelganda **eslatma yuboradi**.
* Foydalanuvchi "**Ichdim**" tugmasini bosmaguncha **har 5 daqiqada takror eslatma yuboradi**.
* "**Dori haqida**" tugmasi orqali foydalanuvchi dorining nomini yozadi, AI esa:

  * Tarkibi
  * Qaysi kasalliklarda ishlatilishi
  * Qanday tartibda ichilishi
  * Dozalash bo‘yicha yo‘riqnoma
  * Yon ta’sirlari
    haqidagi ma'lumotlarni beradi.
* Agar foydalanuvchi: “**Mening boshim og‘riyapti, qaysi dorini tavsiya qilasan?**” desa — AI unga mos dorilarni tavsiya qiladi.

---

## 🏗 **2. Loyiha arxitekturasi**

Loyiha 4 ta asosiy komponentga bo‘linadi:

### **1) Telegram Bot (aiogram)**

* Foydalanuvchi bilan muloqot
* Dorilar haqida AI javoblari
* Dori kursi yaratish
* Eslatma yuborish
* “Ichdim” tugmasi orqali log yuritish
* Statistikalar

### **2) PostgreSQL Database Layer**

Alohida `db/` package ichida joylashgan:

* Ma’lumotlar bazasiga ulanish
* SQL jadvallar yaratish
* CRUD funksiyalar (insert/select/update/delete)
* Kesh (AI javoblarini saqlash)

### **3) Scheduler (apscheduler)**

* Har bir dori vaqti uchun job yaratadi
* Vaqti kelganda eslatma yuboradi
* “Ichdim” bosilmasa 5 daqiqada bir eslatib turadi

### **4) OpenAI AI Module**

* Dorilar haqida AI asosida izoh yaratish
* Kasallik simptomlariga mos dorilar tavsiya qilish
* Keshdan foydalanish (tez ishlash uchun)

---

## 🗂 **3. Papka strukturasining to‘liq ko‘rinishi**

```
medremind_ai/
│── bot.py
│── config.py
│── scheduler.py
│── ai_client.py
│── requirements.txt
│
│── db/
│   ├── database.py
│   ├── models.py
│   └── queries.py
│
│── handlers/
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
```

---

## 🗄 **4. PostgreSQL Ma’lumotlar Bazasi Modeli**

### **1) users**

| Ustun       | Tavsif              |
| ----------- | ------------------- |
| id          | PK                  |
| telegram_id | Foydalanuvchi TG ID |
| name        | Ismi                |
| timezone    | UTC+?               |
| created_at  | Yaralgan vaqt       |

### **2) medications**

AI tomonidan ishlatiladigan dori maʼlumotlari

### **3) ai_drug_info_cache**

AI javoblarini saqlash (tezlashtiradi)

### **4) user_medications**

Foydalanuvchining kurslari

* Necha mahal
* Qaysi vaqtlar
* Kurs boshlanish/oxiri
* Ovqatdan oldin/keyin

### **5) intake_log**

“**Ichdim**” tugmasi bosilganini qayd qilish

---

## ⚙ **5. Asosiy funksiyalar**

### ✅ **1. AI orqali dori haqida maʼlumot olish**

Foydalanuvchi dorining nomini yuboradi:
`"Analgin"`

Bot AI orqali to‘liq izoh beradi:

* Tarkibi
* Ishlatilish sohasi
* Dozasi
* Qanday tartibda ichiladi
* Yon ta’sirlari

---

### ✅ **2. Dori kursi yaratish**

Foydalanuvchi:

* Dorining nomini
* Necha mahal ichishini
* Qaysi vaqtlarda ichishini
* Ovqatdan oldin yoki keyin
* Kurs necha kun davom etishini

kiritadi.

---

### ✅ **3. Eslatmalar (Scheduler)**

* Vaqti kelganda eslatma yuboriladi
* "Ichdim" bosilmasa **har 5 daqiqada qayta eslatadi**
* "Ichdim" bosilganda job to‘xtatiladi va log yoziladi

---

### ✅ **4. AI orqali simptom bo‘yicha dori tavsiyasi**

Misol:
Foydalanuvchi yozadi:

> "Mening boshim og‘riyapti, nima tavsiya qilasan?"

AI simptomni tahlil qilib mos dorilarni tavsiya qiladi.

---

## 🤖 **6. AI Funksiyasi uchun prompt**

```
Quyidagi dori haqida oddiy va tushunarli tibbiy izoh tayyorla:
- Tarkibi
- Qaysi kasalliklarda qo‘llanadi
- Qanday tartibda ichiladi
- Dozalash bo‘yicha yo‘riqnoma
- Yon ta’sirlari

Dori nomi: {drug_name}
```

Simptom bo‘yicha so‘rov uchun:

```
Foydalanuvchining quyidagi shikoyatini tahlil qilib, dori tavsiyasi ber:
"{symptom_text}"
```

---

## ⏰ **7. Scheduler Ishlash Jarayoni**

1. Foydalanuvchi kurs yaratadi
2. DB-ga yoziladi
3. Har bir vaqt uchun job yaratiladi
4. Vaqti kelganda eslatma yuboriladi
5. "Ichdim" bosilmasa — 5 daqiqadan so‘ng qayta yuboriladi
6. "Ichdim" bosilsa — log yoziladi va job to‘xtaydi

---

## 🧪 **8. Test Ssenariylari**

1. /start bosish
2. Dori qidirish (AI javobi chiqadi)
3. Dori kursi yaratish
4. Scheduler eslatma yuborishi
5. “Ichdim” tugmasi bosish
6. Statistikalar to‘g‘ri chiqishi
7. AI orqali simptom bo‘yicha dori tavsiyasi

---

## 🚀 **9. Kelajakdagi Kengaytirish Rejasi (Roadmap)**

* Dorixonalar bilan integratsiya
* Oila a’zolarini kuzatish tizimi
* Tibbiy kartochka (Medical Card) moduli
* API orqali klinikalar bilan integratsiya
* Dori QR kod skaneri

---

Agar xohlasangiz:
📌 **README ga rasmli diagrammalar** (UML, Architecture Map, ER Diagram) qo‘shib beraman.
📌 Yoki **Docker compose + .env + launch script** tayyorlab beraman.

Qanday davom ettiramiz?
