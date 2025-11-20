Ajoyib, tushundim. Siz xohlaysizki, hujjat **avval loyihaning umumiy tuzilishi va strukturasini** ko‘rsatib, keyin **har bir fayl va dasturchi vazifalari**, va **til tanlaganda foydalanuvchiga qanday xabar chiqishi**ni ham aniq namuna bilan kiritaylik. Mana to‘liq, rasmiy va tartibli variant:

---

# 🧠 MedRemind AI — Telegram Bot Loyiha Taqriznomasi

## 1. Loyiha Maqsadi

**MedRemind AI** foydalanuvchilarga:

* dori-darmonlarni o‘z vaqtida ichishni eslatish,
* AI orqali dorilar haqida batafsil ma’lumot berish,
* dori kurslarini yaratish va boshqarish imkonini beradi.

Bot foydalanuvchi retsepti asosida quyidagilarni boshqaradi:

* Dorining nomi
* Necha mahal ichilishi
* Qaysi vaqtda ichilishi
* Ovqatdan oldin yoki keyin ichilishi

Bundan tashqari, foydalanuvchi AI orqali dorining tarkibi, ishlatilish sohasi, doza, ichish tartibi, yon ta’sirlari va simptom bo‘yicha mos dorilar tavsiyasini oladi.

**Asosiy imkoniyatlar:**

* Dorilar haqida ma’lumot olish
* Dori kursi yaratish va boshqarish
* Eslatma yuborish va “Ichdim” logini saqlash
* Kunlik va oylik statistikalarni ko‘rish

---

## 2. Loyihaning Strukturasining Tavsifi

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

**Izoh:**

* `bot.py` — Telegram botni ishga tushirish
* `config.py` — konfiguratsiya (token, DB, API key)
* `scheduler.py` — eslatma tizimi
* `ai_client.py` — AI integratsiyasi va caching
* `dbx/` — ma’lumotlar bazasi modullari
* `handlers/` — foydalanuvchi xabarlarini qayta ishlash
* `utils/` — yordamchi funksiyalar va tugmalar

---

## 3. Til Tanlash UI va Namuna Xabarlar

Foydalanuvchi `/start` tugmasini bosganda 4 ta til tanlash tugmasi chiqadi:

| Emoji | Til               | Namuna xabar                                                                                                                                                                                                                                                                                                                                   |
| ----- | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🇺🇿  | O‘zbekcha (Lotin) | Assalomu aleykum! 👋
                            Men sizga dori-darmonlarni o'z vaqtida ichishni eslatib turish, dorilar haqida AI orqali ma'lumot berish va kurslarni boshqarishga yordam beraman.
                            Asosiy funksiyalar:
                                - Dorilar haqida ma’lumot olish
                                - Dori kursi yaratish
                                - Eslatma yuborish va ichim logini saqlash
                                - Kunlik va oylik statistikalarni ko‘rish |
| 🇺🇿  | O‘zbekcha (Krill) | Ассалому алейкум! 👋
                            Мен сизга дори-дармонларни ўз вақтда ичишингизни эслатиб туриш, дорилар ҳақида AI орқали маълумот бериш ва курсларни бошқаришда ёрдам бераман.
                            Асосий функциялар:
                                - Дорилар ҳақида маълумот олиш
                                - Дори курси яратиш
                                - Эслатма юбориш ва ичим логини сақлаш
                                - Кунлик ва ойлик статистикаларни кўриш               |
| 🇷🇺  | Русский           | Здравствуйте! 👋
                            Я помогу вам вовремя принимать лекарства, предоставлять информацию о лекарствах через AI и управлять курсами.
                            Основные функции:
                                - Получение информации о лекарствах
                                - Создание курса приема
                                - Отправка напоминаний и запись логов
                                - Просмотр ежедневной и ежемесячной статистики                                      |
| 🇬🇧  | English           | Hello! 👋
                            I help you take your medications on time, provide drug information via AI, and manage courses.
                            Main functions: 
                                - Get drug information
                                - Create medication courses
                                - Send reminders and log intake
                                - View daily and monthly  statistics                                                                                         |

---

## 4. Har Bir Faylning Vazifalari va Dasturchi Mas’uliyati

### 4.1 Root Fayllar

| Fayl             | Vazifasi                                     | Dasturchi vazifasi                                                      |
| ---------------- | -------------------------------------------- | ----------------------------------------------------------------------- |
| bot.py           | Botni ishga tushirish va handler’larni ulash | Dispatcher yaratish, startup/shutdown sozlash, logging                  |
| config.py        | Konfiguratsiya ma’lumotlarini saqlash        | Bot token, OpenAI API key, DB ulanishini sozlash                        |
| scheduler.py     | Eslatma job’larini boshqarish                | Job yaratish, yangilash, o‘chirish, “Ichdim” logikasi                   |
| ai_client.py     | AI so‘rovlarini yuborish va javob olish      | OpenAI API integratsiyasi, caching, so‘rovlarni qayta ishlash           |
| requirements.txt | Kutubxonalar ro‘yxati                        | Aiogram, asyncpg, apscheduler, openai va boshqa kutubxonalarni qo‘shish |

### 4.2 dbx/ — Ma’lumotlar Bazasi Modullari

| Fayl                | Vazifasi                                | Dasturchi vazifasi                                                                |
| ------------------- | --------------------------------------- | --------------------------------------------------------------------------------- |
| connection.py       | DB ulanishini boshqarish                | Asinxron ulanish yaratish, sessiyalarni boshqarish                                |
| schema.py           | DB jadvallarini yaratish                | CREATE TABLE skriptlarini yozish, bog‘lanishlarni belgilash                       |
| users.py            | Foydalanuvchi ma’lumotlarini boshqarish | Qo‘shish, o‘chirish, yangilash, Telegram ID, ism, vaqt zonasi saqlash             |
| medications.py      | Dorilar ma’lumotlarini saqlash          | Dori nomi, tarkibi, dozasi, ishlatilish sohasi va yon ta’sirlarini DB-ga kiritish |
| ai_cache.py         | AI javoblarini cache qilish             | So‘rov va javoblarni cache’da saqlash, tezkor javob olish                         |
| user_medications.py | Foydalanuvchi kurslarini boshqarish     | Kurs yaratish, yangilash, o‘chirish, vaqt va doza saqlash                         |
| intake_log.py       | “Ichdim” loglarini saqlash              | Foydalanuvchi dori ichgan vaqti va statusini yozish, statistikani yangilash       |

### 4.3 handlers/ — Telegram Komandalar

| Fayl          | Vazifasi                           | Dasturchi vazifasi                                                     |
| ------------- | ---------------------------------- | ---------------------------------------------------------------------- |
| start.py      | /start buyruqni boshqarish         | Til tanlash tugmalarini yaratish, boshlang‘ich xabar yuborish          |
| search_med.py | Dorilarni qidirish va AI tavsiyasi | Foydalanuvchi so‘rovini qabul qilish, AI yoki DB orqali javob yuborish |
| add_course.py | Dori kursini yaratish              | Kurs ma’lumotlarini DB-ga yozish, scheduler job’larini yaratish        |
| reminders.py  | Eslatma tugmalarini boshqarish     | “Ichdim” va “Keyinroq eslat” tugmalarini callback bilan ishlash        |
| help.py       | /help buyruqni boshqarish          | Botning vazifalari va yo‘riqnomani yuborish                            |

### 4.4 utils/ — Yordamchi Funksiyalar

| Fayl          | Vazifasi                                | Dasturchi vazifasi                                           |
| ------------- | --------------------------------------- | ------------------------------------------------------------ |
| validators.py | Foydalanuvchi ma’lumotlarini tekshirish | Dorilar nomi, dozasi, vaqt formatini validatsiya qilish      |
| formatter.py  | Xabarlarni chiroyli formatlash          | Markdown yoki HTML formatida xabar yaratish                  |
| keyboards.py  | Inline tugmalar yaratish                | Til tanlash, “Ichdim”, “Keyinroq eslat” tugmalarini yaratish |

### 4.5 Docker va Deployment

| Fayl               | Vazifasi                                | Dasturchi vazifasi                                                      |
| ------------------ | --------------------------------------- | ----------------------------------------------------------------------- |
| docker-compose.yml | Loyihani konteynerlarda ishga tushirish | Bot va PostgreSQL xizmatlarini bir vaqtda ishga tushirish               |
| Dockerfile         | Botni Docker konteyneriga joylashtirish | Python muhitini yaratish, dependencies o‘rnatish, botni ishga tushirish |
| README.md          | Loyihani tushuntirish                   | Loyihani tavsiflash, o‘rnatish va ishga tushirish ko‘rsatmalari         |

---

Shu tarzda hujjat **dasturchi uchun to‘liq yo‘riqnoma**, til tanlash xabarlari, fayllar vazifalari va botning asosiy imkoniyatlari bilan tayyor bo‘ladi.

---

Agar xohlasang, men **shu hujjatni professional PDF/Word formatda** diagrammalar bilan birga tayyorlab berishim mumkin.

Shuni qilaylikmi?


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
