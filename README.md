# SMD Banking App

An Android application that helps users with common **pre-application banking tasks**: checking their eligibility for financial products and preparing the documents they need. It was built as the final project for the **Android Mobile App Development** course at Nanjing University of Information Science and Technology (NUIST).

> **Status:** academic prototype. Some parts (the Banker role, the "Ask AI" button and the backend workflows) are intentionally kept at UI level.

## Why this project

Many people cannot easily tell whether their profile matches the requirements of a loan or credit card, and they often do not know which documents to bring. This causes delays, repeated bank visits and a poor first experience. SMD Banking App gives users a clear, guided way to check eligibility first and to organise their documents.

## Features

- **Role-based entry:** separate User and Banker login (Banker is a prototype for future expansion such as reviewing applications).
- **Eligibility Check (4-step wizard):** profile, income and expenses, credit history, and loan requirements with consent.
- **Rule-based results:** each product is classified as **Approved**, **Conditional** or **Not Eligible**, shown on product cards with key terms and requirement checklists. The "Apply Now" button is enabled or disabled according to the status.
- **Document Intelligence:** upload a document with the Android file picker, or browse and search standard bank documents.
- **Profile and settings:** view the profile, account settings and privacy/security options.

## How eligibility is calculated

The rules run on the device, using the values collected in the wizard:

```
totalIncome = monthlyIncome + otherIncome
DTI         = (monthlyExpenses + existingLoansAmount / 12) / totalIncome
```

| Status | Rule |
|---|---|
| Approved | All constraints of the product are satisfied |
| Conditional | The credit score is close to the threshold (within a margin) |
| Not Eligible | Otherwise |

Example prototype thresholds:

| Product | Conditions |
|---|---|
| HBL Personal Loan | income ≥ 35k, credit score ≥ 650, existing loans ≤ 150k, age ≥ 21, DTI ≤ 0.45 |
| MCB Gold Credit Card | income ≥ 50k, credit score ≥ 600, existing loans ≤ 200k, age ≥ 21 |

## Tech stack

| Category | Technology |
|---|---|
| Language | Kotlin |
| UI | XML layouts, Material / Material3 |
| Async | Kotlin Coroutines |
| Local storage | Room 2.6.1 and a SQLite asset database |
| Networking | Retrofit 2.9.0, OkHttp, Moshi |
| Cloud services | Firebase Auth, Realtime Database, Firestore, FCM |
| Security utility | bcrypt (password hashing) |
| SDK | minSdk 24, targetSdk 36 |

## Architecture

The code is organised in layers:

- **UI layer:** activities for login, signup, dashboard, eligibility steps, results and documents.
- **Session / logic layer:** `EligibilitySession` keeps the multi-step data consistent, and the rule-based classifier produces the results.
- **Network layer:** `RetrofitProvider` and `EligibilityApiService`, with coroutine calls on `Dispatchers.IO` and safe `Result<...>` wrappers.
- **Persistence layer:** Room components and the SQLite asset database `smd_app.db`.
- **Cloud layer:** Firebase services prepared for identity, synchronisation and notifications.

| Function | Activity / class |
|---|---|
| Launcher | `MainActivity` |
| Role selection | `ChooseLoginActivity` |
| Login | `Login`, `Login2Activity` |
| Signup | `SignUp` |
| Dashboard | `dashboard` |
| Eligibility wizard | `eligibility_1` to `eligibility_4` |
| Results | `eligibility_results` |
| Documents | `doc_intel`, `doc_upload`, `BrowseDocsActivity` |

## API endpoints (prototype)

| Operation | Method | Endpoint |
|---|---|---|
| Get eligibility by user id | GET | `api/get_eligibility.php?userId=...` |
| Compatibility endpoint | GET | `api/eligibility/get_for_user?userId=...` |
| Save eligibility | POST | `api/eligibility/save` |
| Upload supporting document | Multipart POST | `api/eligibility/upload_supporting` |

## Getting started

1. Clone this repository.
2. Open the project in **Android Studio**.
3. Let Gradle sync, then connect a device or start an emulator (Android 7.0 / API 24 or higher).
4. Run the app.

Network features need a backend. The prototype points to a local HTTP server, so change the base URL in the Retrofit provider to your own server.

## Security notes

- The prototype allows cleartext (HTTP) traffic only for local testing. A production version must use **HTTPS**.
- The eligibility flow asks for explicit consent before credit-check-related processing.
- Passwords are stored as hashes in the prototype database.

## Known limitations and future work

- Replace the static product cards with a **RecyclerView + Adapter** so offers load dynamically.
- Connect eligibility decisions to a backend for centralised rule management.
- Enforce HTTPS and stricter network security rules.
- Build the "Ask AI" feature to give user-specific reasons and improvement suggestions.
- Complete the Banker workflow (review applications, verify documents).

## Team (Group 1)

| Member | Main responsibilities |
|---|---|
| Akbar Mubashar Ali (Leader) | Project coordination and integration, final APK packaging, UI consistency, group report, SQLite database support |
| Khattak Naid Khan | Eligibility workflow (steps 1 to 4), rule-based classification, results behaviour |
| Khan Musab | Document Intelligence UI, upload/browse flows, file picker integration |
| Shishir Md Shifat Hossain | Authentication screens, local database integration, session and persistence support |
| Shah Muhammad Daud | Banker login support, Firebase integration, testing and bug fixing, login interface support |

## Report

The full group report (design, architecture, problems and solutions, and reflections) is available in this repository as `APP_Group_Report.docx`.

---

**Course:** Android Mobile App Development
**Department:** School of Computer & Software
**Institution:** Nanjing University of Information Science and Technology (NUIST)
**Date:** January 2026
