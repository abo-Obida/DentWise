<h1 align="center">🦷 DentWise — Dental Clinic Booking Platform</h1>

<p align="center">
  A modern dental clinic platform where patients book appointments, manage their care,
  and chat with an AI voice assistant — with a full admin dashboard for doctors and schedules.
</p>

<p align="center">
  <img alt="Status" src="https://img.shields.io/badge/status-demo%20%2F%20PoC-orange" />
  <img alt="Not for production" src="https://img.shields.io/badge/production-not%20ready-red" />
  <img alt="Next.js" src="https://img.shields.io/badge/Next.js-15-black" />
  <img alt="React" src="https://img.shields.io/badge/React-19-149eca" />
  <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-5-3178c6" />
  <img alt="License" src="https://img.shields.io/badge/license-MIT-green" />
</p>

---

> ### ⚠️ Demo / Proof of Concept
> This is a **portfolio / showcase project** built to demonstrate a full-stack Next.js
> application. **It is not production-ready** and is **not intended for real clinical use**.
> It contains **no real patient data** — any names, doctors, or appointments are fictional
> and for demonstration only. Do not enter real personal or medical information.

---

![DentWise Screenshot](/public/screenshot-for-readme.png)

<!-- [Screenshot / GIF here] — replace the image above with your own screenshot or a short demo GIF -->

## ✨ Features

- 🏠 **Modern landing page** with hero, pricing, and "how it works" sections
- 🔐 **Authentication** via Clerk (Google, GitHub, Email & Password + 6-digit email verification)
- 📅 **3-step appointment booking** — pick a doctor → choose service & time → confirm
- ⏱️ **Time-slot conflict handling** — already-booked slots are filtered out
- 📩 **Email confirmations** for bookings via Resend
- 📊 **Admin dashboard** — manage doctors and view all appointments
- 🧑‍⚕️ **Doctor management** — add / edit doctors, specialities, and availability
- 🗣️ **AI voice agent** powered by Vapi (gated behind paid plans)
- 💳 **Subscription plans** (Free + paid tiers) via Clerk Billing
- 📈 **Patient dashboard** — appointment stats and dental-care overview
- 🎨 **UI** built with Tailwind CSS v4 + shadcn/ui + Radix primitives

## 🛠️ Tech Stack

| Layer | Technologies |
| --- | --- |
| **Frontend** | Next.js 15 (App Router), React 19, TypeScript, Tailwind CSS v4, shadcn/ui, Radix UI, Lucide icons, TanStack Query, React Hook Form + Zod |
| **Backend** | Next.js Server Actions & Route Handlers, Prisma ORM |
| **Database** | PostgreSQL (e.g. Neon) |
| **Auth & Billing** | Clerk (authentication + subscription plans) |
| **AI Voice** | Vapi Web SDK |
| **Email** | Resend + React Email |
| **Tooling** | Biome (lint/format), Turbopack |

## 🚀 Installation & Usage

### Prerequisites
- Node.js 18+ and npm
- A PostgreSQL database (local, or a free tier on [Neon](https://neon.tech))
- Accounts for [Clerk](https://clerk.com), [Vapi](https://vapi.ai), and [Resend](https://resend.com) (free tiers work)

### Steps

```bash
# 1. Install dependencies
npm install

# 2. Configure environment variables
#    Copy the example file and fill in your own keys
cp .env.example .env

# 3. Generate the Prisma client and push the schema to your database
npx prisma generate
npx prisma db push

# 4. Start the development server
npm run dev
```

Then open **http://localhost:3000**.

To access the **admin dashboard** (`/admin`), sign in with the email you set in `ADMIN_EMAIL`.

### Environment variables

See [`.env.example`](.env.example) for the full list. Required keys:

| Variable | Purpose |
| --- | --- |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` / `CLERK_SECRET_KEY` | Clerk authentication |
| `DATABASE_URL` | PostgreSQL connection string |
| `NEXT_PUBLIC_VAPI_ASSISTANT_ID` / `NEXT_PUBLIC_VAPI_API_KEY` | Vapi voice agent |
| `ADMIN_EMAIL` | Email allowed into `/admin` |
| `RESEND_API_KEY` | Transactional emails |
| `NEXT_PUBLIC_APP_URL` | Public base URL |

## 🧩 How It Works

**Authentication & user sync.** Clerk handles sign-in. On first load a `UserSync` component
calls a `syncUser` server action that mirrors the Clerk user into the local PostgreSQL `User`
table (via Prisma), so appointments can be linked to a database record.

**Booking flow (3 steps).**
1. **Select a doctor** — the app fetches active doctors from the database.
2. **Select service & time** — for the chosen doctor and date, already-booked slots are
   removed so patients can only pick free times.
3. **Confirm** — a `bookAppointment` server action creates a `CONFIRMED` appointment linked to
   the patient and doctor, then a Route Handler (`/api/send-appointment-email`) sends a
   confirmation email through Resend.

**Roles & permissions.**
- **Patient** — the default role: books appointments and sees their own dashboard/stats.
- **Admin** — a single user whose email matches `ADMIN_EMAIL`; the `/admin` route redirects
  everyone else. Admins manage doctors and view all appointments.
- **Pro/Basic plan users** — the `/voice` AI assistant is gated by Clerk plan checks
  (`ai_basic` / `ai_pro`).

**Data model (Prisma).** Three core models — `User`, `Doctor`, and `Appointment`
(with `Gender` and `AppointmentStatus` enums). An appointment references one user and one
doctor, cascading on delete.

## ⚠️ Limitations / Known Issues

Because this is a demo project, several things are intentionally simplified or incomplete:

- **Single-admin model** — admin access is a plain string match against one `ADMIN_EMAIL`;
  there is no real role system.
- **Server-action authorization gaps** — some actions (e.g. listing all appointments,
  creating/updating doctors) do not re-check that the caller is an admin. Fine for a demo,
  **not safe for production**.
- **Env var typo** — the committed `.env` used `NEXT_PUBLIC_VAIP_API_KEY`, but the code reads
  `NEXT_PUBLIC_VAPI_API_KEY` (`src/lib/vapi.ts`). Use the correct name from `.env.example`,
  or the voice client initializes without a key.
- **Minor UI typo** — `src/app/pro/page.tsx` has a `bg-primar/10` class (should be
  `bg-primary/10`).
- **No seed data** — the database starts empty; add doctors from the admin dashboard.
- **No automated tests** and limited input validation.
- **Payments/plans** rely on Clerk Billing configuration that must be set up separately.

## 📄 License

Released under the [MIT License](LICENSE). Free to use, modify, and learn from.

---

<p align="center"><em>Built as a portfolio demo — not affiliated with any real dental practice.</em></p>
