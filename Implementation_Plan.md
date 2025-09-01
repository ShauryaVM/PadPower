# Pad Power App – Developer Handoff Document

---

## A) One-liner + Goals

**Pad Power** is a lightweight, multilingual mobile/web app that:

- Teaches menstrual health with short videos/quizzes.
- Helps students find free pad resources or request kits.
- Provides teachers with ready-to-use workshops.
- Offers anonymous Q&A + crisis resource directory.
- Tracks anonymous impact metrics for reports.

---

## B) MVP Scope (Phase 1)

1. **Auth & Profiles**

   - Email/phone + OTP; guest mode allowed.
   - Roles: Student (default), Teacher/Facilitator, Admin.
   - Minimal profile: age range (13–17 / 18+), language, country/region, optional school/org.
2. **Learning**

   - 8–10 micro-modules (2–5 min each): anatomy basics, cycle 101, hygiene, pads vs. reusable, pain basics, confidence & stigma, talking to adults, myths vs. facts.
   - Each module = 1 short video/graphic story + 3–5 question quiz.
   - Progress tracking; certificate after completing all modules.
3. **Find Pads**

   - Map + list of “Pad Points” (schools, clinics, NGOs). Works offline with cached list.
   - Filter by distance/hours; tap to call/WhatsApp.
   - Request a kit form (name optional; contact method required) routed to local partner + saved to Admin dashboard.
4. **Workshop Mode (Teacher)**

   - Ready-to-run lesson plans (30/45/60 min).
   - Tap-through slides (offline after first download).
   - Attendance counter + quick pre/post 3-question survey.
5. **Anonymous Q&A + Resource Directory**

   - Students submit questions anonymously; AI/curated FAQ surfaces answers.
   - Emergency/helpline cards (by country), downloadable PDFs.
6. **Languages**

   - English + Spanish + Hindi (Kannada later).
   - All strings externalized for easy expansion.
7. **Offline-first & Low-Data**

   - Content package download; <50 MB target for initial bundle.
   - Works on Android Go–level devices; PWA for web.

---

## C) Detailed Specs

### 1. User Roles & Stories

**Student**

- Choose language on first launch.
- Complete modules and see progress.
- Find nearby pad points and request a kit privately.
- Ask questions anonymously and browse vetted answers.
- Download a certificate after finishing modules.

**Teacher/Facilitator**

- Access workshop slides offline.
- Mark attendance (# only) and run pre/post mini surveys.
- Export impact summary (CSV/PDF) for class.

**Admin**

- CRUD content (modules, questions, translations).
- Manage pad points and route kit requests.
- Review flagged Q&A and publish answers.
- View analytics dashboards (usage, completion, requests).

---

### 2. Information Architecture (Screens)

- **Onboarding**: language → age range → optional signup (skip allowed).
- **Home**: Continue Learning, Find Pads, Ask Anonymously, Workshops (if teacher), Resources.
- **Module Detail**: video/graphics → quiz → feedback → progress %.
- **Find Pads**: map/list → location detail (address, hours, contact) → directions/contact.
- **Request Kit**: form → success screen → status “received”.
- **Q&A**: search → FAQ chips → ask new (anonymous).
- **Workshops (Teacher)**: pick lesson length → slides → attendance → pre/post survey → export.
- **Profile**: language, role switch, downloads manager.
- **Admin Web**: content CMS, pad points CRUD, request queue, Q&A moderation, analytics.

---

### 3. Content Model (MVP)

- **Module**: `{id, title, summary, media_url, duration, locale[], version}`
- **QuizQuestion**: `{id, module_id, prompt, options[4], correct_index, explanation, locale}`
- **FAQ**: `{id, question, answer, tags[], locale, status}`
- **PadPoint**: `{id, name, type, lat, lng, address, hours, contact_phone, whatsapp, region_code, notes, verified}`
- **KitRequest**: `{id, created_at, user_id (nullable), region_code, contact_method, contact_value, notes, status}`
- **Workshop**: `{id, title, run_time, slides[], activities[], locale}`
- **SurveyResponse**: `{id, type: pre/post, workshop_id, answers, demographics}`

---

### 4. Data Privacy & Safety

- No medical records. Only age range + region stored.
- COPPA: under 13 requires supervised use or guest mode.
- Consent: opt-in analytics, clear privacy policy & deletion flow.
- PII minimization: alias-based requests, minimal contact required.
- Safety: helplines always visible, harm keyword triggers referrals.

---

### 5. Accessibility & Inclusivity

- WCAG 2.2 AA compliance: scalable text, captions, screen reader labels.
- Plain-language mode with simpler phrasing + visuals.
- Low-bandwidth fallback: compressed media, text/illustrations.
- Cultural sensitivity: localized visuals, inclusive language.

---

### 6. Tech Stack (Suggested)

- **Mobile**: React Native (Android first) or Flutter; PWA for web.
- **Backend**: Node.js (NestJS/Express) + Postgres.
- **Auth**: Firebase Auth or Cognito.
- **Storage/CDN**: Firebase Storage or S3 + CloudFront.
- **Maps**: Mapbox (offline) or Google Maps (cached).
- **Notifications**: Firebase Cloud Messaging / Twilio WhatsApp.
- **Analytics**: Firebase, Mixpanel optional.
- **Admin**: Next.js or Retool.
- **Localization**: i18n JSON + crowdin/loco workflow.

---

### 7. API Endpoints (Examples)

**Auth**

- `POST /auth/otp/request {phone_or_email}`
- `POST /auth/otp/verify {code} → {token, role}`
- `GET /me`

**Content**

- `GET /modules?lang=en`
- `GET /modules/:id?lang=en`
- `GET /modules/:id/quiz?lang=en`
- `POST /progress {module_id, status, score}`

**Pad Points**

- `GET /padpoints?region=&lat=&lng=&radius_km=`
- `POST /kit-requests {...}`

**Q&A**

- `GET /faq?lang=en&tag=`
- `POST /questions {text, lang}`
- `POST /admin/answers {...}`

**Workshops/Surveys**

- `GET /workshops?lang=en`
- `POST /workshops/:id/attendance {count}`
- `POST /surveys {...}`

**Admin**

- CRUD for modules, pad points, FAQ, translations.
- `GET /analytics/summary?from=&to=`

---

### 8. Analytics Events

- `app_open`
- `language_set`
- `module_started`
- `module_completed`
- `quiz_submitted {score}`
- `padpoint_viewed`
- `kit_request_submitted`
- `faq_viewed`
- `question_submitted`
- `workshop_started`
- `attendance_logged`
- `survey_submitted`
- `certificate_downloaded`

---

### 9. Notifications (Examples)

- Nudge: *“New myth-busters lesson is ready.”*
- Follow-up: *“Your pad kit request was received—partner will contact you soon.”*
- Teacher reminder: *“Don’t forget to export your class impact summary.”*

---

### 10. Security & Reliability

- TLS, JWT, encrypted at rest.
- Rate limiting on Q&A and requests.
- Moderation pipeline (keywords + admin review).
- Nightly backups, error logging.

---

### 11. Performance Targets

- App bundle <25 MB, content pack <50 MB.
- Cold start <2.5s on low-end Android.
- Offline read/quiz; map fallback to cached list.

---

### 12. Admin Dashboard (Web)

- Content CMS for modules/media/translations.
- Q&A moderation tools.
- Pad Points management (import, verify, toggle).
- Requests queue with assignment/export.
- Analytics: funnels, completion, workshop stats.

---

## D) What You’ll Hand the Dev Team (Assets + Copy)

### 1. Copy Blocks

- **App Store Short**: “Pad Power: simple lessons, real answers, and free pad resources—built for students, teachers, and communities.”
- **Onboarding**: Language picker, Age range, Consent agreement.
- **Home Tiles**: Learn, Find Pads, Ask, Workshops, Resources.
- **Kit Request Success**: “Got it! A partner will reach out soon. If urgent, see Resources.”
- **Empty State**: “No internet? No problem. Lessons saved. Try again later.”

---

### 2. Module List (MVP)

1. What is a period?
2. Your cycle, simply explained
3. Pads 101: how to choose & change
4. Hygiene & comfort at school
5. Myths vs facts
6. Cramps: what helps
7. Confidence & talking to adults
8. Sports & periods

*(Each: 2–5 min, 4 quiz questions, tip sheet.)*

---

### 3. Visual Style Guide

- Friendly, high-contrast colors.
- Body: min 14pt; Headers: 18–20pt; Touch targets ≥44px.
- Illustrations > stock photos; inclusive representation.

---

### 4. Localization Plan

- Start: EN, ES, HI. Queue: Kannada.
- Text stored in `/i18n/{lang}.json`.
- RTL support reserved for Phase 2.

---

### 5. Test Plan (MVP)

**Functional**

- Complete modules offline → sync on reconnect.
- Submit kit request via WhatsApp → admin receives <60s.
- Teacher runs workshop offline → data saved.

**Edge Cases**

- Duplicate kit requests in 24h → rate-limit message.
- Incomplete quiz → resume where left off.
- Location denied → fallback list.

**Accessibility**

- Screen reader labels.
- Captions on videos.
- AA contrast compliance.

**Acceptance Criteria**

- From fresh install to Module 1 ≤ 3 taps + <10 MB download.
- Find Pads returns ≥ 3 verified points in seed region.
- Q&A returns relevant answers for “cramps,” “sports.”

---

### 6. Deliverables Checklist

- Figma wireframes.
- EN/ES/HI i18n JSONs.
- 8 module media files, transcripts, quiz CSV.
- Seed data: 50 pad points, 20 FAQ entries.
- Privacy Policy + Terms (youth-friendly).
- App icons, splash screens, color tokens.
- Admin roles + first admin account.
- Analytics event map.

---

## 🎨 Color Scheme (Pad Power)

- **Primary (Pink):** #E75480
- **Secondary (Soft Rose):** #F8BBD0
- **Accent 1 (Lilac Purple):** #9C27B0
- **Accent 2 (Teal/Green):** #26A69A
- **Neutral Dark:** #333333
- **Neutral Light:** #FAFAFA

**Usage**

- Primary = buttons, highlights, headers.
- Secondary = backgrounds, cards.
- Accents = progress bars, callouts.
- Neutrals = text + backgrounds.

---

## About the Founder (for the Founder Page within the application)

**Shriya Jagadeesh** – Public health advocate, researcher, and student leader passionate about menstrual health.

- Led projects in India and the USA, reaching hundreds of young girls and women.
- Founded Pad Power as grassroots campaign → now global initiative with schools & nonprofits.
- Background in Genetics + Sociology (Pre-Dental Track) at NC State University.
- Mission: ensure every girl has access to pads, education, and confidence.

---
