# 🏥 NutriScan Clinical

> **Clinical hospital diet monitoring system for Malaysian warded patients**

Built for the **UM National Deep Tech Challenge** · Team APEX · Powered by UM Patent PI 2021004897

---

##  What Is This?

NutriScan Clinical is a full-stack clinical web application that helps hospital dietitians monitor what warded patients actually eat — in real time — using a meal photo logging workflow with simulated nutrition analysis.

> **⚠️ This is a hackathon prototype.** The nutrition analysis engine is simulated — no real AI or computer vision is active. Any uploaded photo returns pre-defined nutritional values to demonstrate the clinical workflow. Real AI integration (OpenAI GPT-4o Vision) is architected in the codebase and is the next development milestone.

**The Problem:** A hospital dietitian manages 20–40 warded patients daily. Tracking each patient's actual food intake is done manually — paper logs, patient recall, food charts. By the time poor intake is noticed, the patient may already be nutritionally compromised.

**Our Solution:** Nurses snap a photo before and after each meal. The system calculates macronutrients consumed and surfaces low-intake alerts on the dietitian's real-time dashboard. Zero manual logging. Actual intake tracked — not just what was served.

---

## ✨ Key Features

### 👩‍⚕️ Nurse Portal
- View assigned ward patients and their dietary profiles
- Upload before-meal and after-meal tray photos
- System returns simulated nutritional breakdown (calories, carbs, protein, fat) per meal
- Bilingual food item labels — English and Bahasa Malaysia

### 📊 Dietitian Dashboard
- Real-time overview of all patients' nutritional intake across the ward
- Automated alerts for low intake (< 50% target) and critical intake (< 25% target)
- Per-patient intake history with interactive charts (Recharts)
- Log clinical interventions directly on patient profiles
- Export nutritional reports as PDF (jsPDF)

### 🔧 Admin Panel
- Manage patients, nurses, and dietitians
- Configure ward assignments and diet types (Diabetic, Renal, Low Sodium, Post-Surgery, Regular)
- Maintain the Malaysian food database with nutritional values per 100g
- Generate ward-level reports

### 🔐 Role-Based Access Control
- Three distinct roles: `NURSE`, `DIETITIAN`, `ADMIN`
- Route-level middleware protection via NextAuth.js
- Session-based authentication with bcrypt password hashing

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 14 (App Router), React 18, TypeScript |
| **Styling** | Tailwind CSS |
| **Auth** | NextAuth.js v4 |
| **Database ORM** | Prisma 5 |
| **Database** | PostgreSQL |
| **AI / Vision** | OpenAI GPT-4o Vision API *(architecture ready, not yet integrated)* |
| **Charts** | Recharts |
| **PDF Export** | jsPDF + jsPDF-AutoTable |
| **Runtime** | Node.js |

---

## 🗄️ Database Schema

The system models a real clinical nutrition workflow:

```
User (NURSE | DIETITIAN | ADMIN)
Patient (ward, bed, dietType, kcalTarget)
MealLog (breakfast | lunch | dinner per patient per day)
  ├── MealPhoto (BEFORE | AFTER)
  ├── MealFoodItem (food items + portion in grams — simulated in prototype)
  └── NutritionResult (kcalActual, carbs, protein, fat, percentageEaten)
Alert (LOW_INTAKE | CRITICAL_INTAKE | MISSED_MEAL)
Intervention (dietitian notes per patient)
FoodItem (Malaysian food database — bilingual, nutrient values per 100g)
```

---

##  Getting Started

### Prerequisites
- Node.js 18+
- PostgreSQL database
- OpenAI API key *(optional — only needed when activating real AI integration)*

### Installation

```bash
# Clone the repo
git clone https://github.com/yourusername/nutriscan-clinical.git
cd nutriscan-clinical

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
```

Configure your `.env` file:

```env
DATABASE_URL="postgresql://username:password@localhost:5432/nutriscan"
NEXTAUTH_SECRET="your-secret-key"
NEXTAUTH_URL="http://localhost:3000"
OPENAI_API_KEY="sk-..."
```

### Database Setup

```bash
# Run migrations
npx prisma migrate dev

# Seed with sample data (patients, users, food items)
npx prisma db seed
```

### Run Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

### Default Login Credentials (Seeded)

| Role | Email | Password |
|---|---|---|
| Admin | admin@nutriscan.my | password123 |
| Dietitian | dietitian@nutriscan.my | password123 |
| Nurse | nurse@nutriscan.my | password123 |

---

## 📁 Project Structure

```
src/
├── app/
│   ├── admin/          # Admin panel pages
│   ├── dietitian/      # Dietitian dashboard pages
│   ├── nurse/          # Nurse portal pages
│   ├── login/          # Auth pages
│   └── api/            # REST API routes (Next.js Route Handlers)
│       ├── analyze-food/       # AI vision endpoint
│       ├── meal-logs/          # Meal logging CRUD
│       ├── patients/           # Patient management
│       ├── alerts/             # Alert system
│       ├── food-items/         # Food database
│       └── admin/              # Admin-only routes
├── components/
│   ├── admin/          # Admin UI components
│   ├── dietitian/      # Dashboard components (charts, alerts, tables)
│   ├── nurse/          # Meal photo uploader, food analysis result
│   └── ui/             # Shared UI primitives
├── lib/                # Auth config, Prisma client, OpenAI config
├── middleware.ts        # Route protection by role
└── types/              # TypeScript type definitions
```

---

##  Food Analysis Pipeline (Prototype)

```
Nurse uploads any meal photo
        ↓
POST /api/analyze-food
        ↓
Image saved to /public/uploads/
        ↓
Mock Nutrition Engine
  → Returns simulated Malaysian food data (Nasi Lemak baseline)
  → Scales macronutrients based on filename keywords
    (e.g. "after", "50", "leftover" → 50% portion values)
        ↓
NutritionResult saved to database
        ↓
Alert triggered if intake < threshold
        ↓
Dietitian dashboard updated in real time
```

> **⚠️ Prototype Disclaimer:** This is a hackathon prototype. The food analysis engine is simulated — any uploaded photo returns pre-defined nutritional values scaled by portion keywords. **No real AI or computer vision is active in this build.**
>
> The full OpenAI GPT-4o Vision integration is architected and preserved as commented code in `/api/analyze-food/route.ts`, ready to be activated with a valid `OPENAI_API_KEY`. The prototype was built to validate the clinical workflow and UI — AI integration is the next development milestone.

---

##  Context & Background

This project was built as part of the **UM National Deep Tech Challenge**, a 2 Days 1 Night hackathon focused on commercialising university intellectual property.

**The IP:** We built NutriScan Clinical on top of **MyDietCam** (UM Patent PI 2021004897) — an AI food recognition system developed by Universiti Malaya's AI, Nutrition, and Behavioural Science team. MyDietCam was trained on Malaysian food datasets and provides the food recognition engine that NutriScan Clinical commercialises for the clinical hospital setting.

**The Gap:** MyDietCam was designed for healthy adults tracking personal diet. No existing product adapts this technology for warded hospital patients and clinical dietitian workflows — that's the gap NutriScan fills.

---



##  License

This project was developed for hackathon purposes. The underlying food recognition IP belongs to Universiti Malaya (Patent PI 2021004897). Please contact [UMCIE](mailto:umcie@um.edu.my) for licensing inquiries.

---

> *"We are not building another food diary. We are building the clinical nutrition intelligence layer for Malaysian hospitals."*
