# MIDEA Kosovo – Customer & Services Platform

> A full production mobile app (iOS & Android) + web admin panel serving the MIDEA product network across Kosovo and Albania. Customers register products, activate warranties, and rate installers. Installers manage jobs and track points. Admins control everything from a dedicated web dashboard.

---

## What It Is

This is not a simple management tool — it's a multi-role platform with three separate app experiences built on a shared Firebase backend:

- **Customer App** (React Native / Expo) — for end users who purchased MIDEA products
- **Installer App** (React Native / Expo) — a separate experience for certified MIDEA installers
- **Admin Dashboard** (React + Vite, web) — for MIDEA staff to manage the entire operation

All three share the same Firebase project (Firestore, Auth, Cloud Functions, Storage) with role-based access enforced at the Firestore security rules level.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Mobile (iOS & Android) | React Native 0.81, Expo SDK 54 |
| Web Admin Panel | React 19, Vite 8 |
| Backend / Database | Firebase Firestore |
| Auth | Firebase Authentication |
| File Storage | Firebase Storage |
| Cloud Functions | Firebase Functions (Node.js) |
| Push Notifications | Expo Notifications + Cloud Functions → Expo Push API |
| Offline Support | AsyncStorage queue with auto-sync on reconnect |
| Build & Deploy | EAS Build (Expo) |
| Testing | Jest + jest-expo, @testing-library/react-native |

---

## Customer App

### Warranty Registration — Multi-Step Wizard

The core customer flow is a 9–10 step animated wizard (step count adapts to the selected product type):

| Step | What Happens |
|---|---|
| Personal info | Name, phone, email, city picker covering all Kosovo municipalities |
| Product type | Visual card selector: AC, Boiler, Water Dispenser, Other |
| AC subtype | (AC only) Residential, Multisplit, Cassette, Floor, Ducted, Fan Coil |
| Installation photos | In-app framed camera with a positioning overlay — separate shots of indoor and outdoor unit, with good/bad photo guide examples shown inline |
| Warranty document | Photo of the paper warranty card + purchase invoice together |
| Serial number | Barcode / QR scanner (supports EAN-13, EAN-8, Code128, QR, DataMatrix, PDF417, and more) with fallback to manual entry |
| Installer selection | Live search through verified installer accounts by name or city; supports selecting multiple installers |
| Installer rating | 5-point emoji satisfaction scale + binary questions (explained product usage / left area clean) + free-text comment |
| Referrals | Add 3 contacts to enter the rewards draw; skippable — warranty activates either way |
| Confirmation | Celebration animation, full registration summary |

Each step has animated transitions, a live progress bar, and an offline warning on the final step if connectivity is lost.

### Offline Support

Warranty submissions are fully offline-capable:
- If the user submits without internet, the record is saved locally to AsyncStorage
- When connectivity returns, a background sync hook automatically retries and uploads pending records to Firestore
- The UI shows a clear offline warning bar on the final submit step

### Rewards & Gamification

After warranty activation, customers can win rewards from a weighted probability draw:

| Reward | Type | Probability Weight |
|---|---|---|
| MIDEA Gift Set | Physical gift | 10 |
| 10% Discount Code | Unique single-use code | 35 |
| Free Service Visit | Service reward | 25 |
| Free Technical Check | Service reward | 30 |

Discount codes are generated as unique single-use codes — they become active only after the warranty is approved by an admin, and are marked used when redeemed in store.

### Other Customer Screens

- **My Products** — list of registered MIDEA devices with warranty status
- **Support** — FAQ, service request, warranty policy, help articles
- **Profile** — account settings, registered devices, rewards panel, ratings history

---

## Installer App

Installers get a completely separate in-app experience after login:

- **Home** — dashboard with job summary and stats
- **Jobs** — active and completed job list with real-time status updates
- **Points** — points earned per completed and rated job, rewards tracker
- **Profile** — installer profile, city, company, ratings received from customers

---

## Admin Dashboard (Web)

Built as a separate Vite/React app deployed to Firebase Hosting. Fully separate from the mobile app bundle.

### Dashboard Views

| View | What Admins Can Do |
|---|---|
| **Customers** | View all registered customer accounts, search, filter by city |
| **Warranties** | Review warranty submissions, approve/reject, view photos, serial numbers |
| **Installers** | View all installer accounts, ratings, job history, detailed individual view |
| **Installer Detail** | Deep-dive on a single installer — full job history, ratings breakdown, contact info |
| **Discount Codes** | View all generated codes with status (pending / active / used), mark codes as used when redeemed |
| **Gifts** | Manage physical reward fulfillment |
| **Chats** | Customer support chat management |

---

## Push Notifications

Full push notification pipeline built on Expo:
- Permission requested on first login
- Expo push token saved to each user's Firestore profile
- Cloud Functions send targeted push notifications to individual devices
- Separate Android notification channel (`midea-default`) with vibration and LED config
- Handles foreground notifications, background taps, and killed-state deep linking

---

## Security

- **Firestore security rules** — role-scoped read/write enforced server-side; customers can only read their own records, installers their own jobs, admins get full access
- **Storage rules** — photo uploads scoped to authenticated users only
- **Firebase App Check** — configured to block unofficial clients
- **Admin auth** — admin web panel has its own protected auth flow, separate from the customer/installer auth

---

## Testing

Jest test suite covering:
- Warranty flow end-to-end (`warrantyFlow.e2e.test.js`)
- Discount code generation and validation (`discountCodes.test.js`)
- Warranty record structure (`warrantyRecords.test.js`)
- Input validation (`validation.test.js`)
- Rewards logic (`rewards.test.js`)
- Screen smoke tests (`screens.smoke.test.js`)
- Helper utilities (`helpers.test.js`)

Firestore security rules are tested separately against a local emulator via `scripts/testRules.mjs`.

---

## Language

The app is fully localized in **Albanian**, serving both Kosovo and Albania markets.

---

## Status

Built and in active deployment. iOS and Android builds managed via EAS. Codebase is private (client project).

---

*Built by [Dion Balaj](https://linkedin.com/in/dion-balaj-81522b256) · 2026*
