<div align="center">

<img src="assets/logo/logoo-app.png" alt="Gaza Shaheed logo" width="120" />

# Gaza Shaheed · شهداء غزة 

**A digital memorial that honors the martyrs of Gaza, documents their stories, and keeps their memory alive.**

[![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter&logoColor=white)](https://flutter.dev)
[![Dart](https://img.shields.io/badge/Dart-%5E3.6-0175C2?logo=dart&logoColor=white)](https://dart.dev)
[![Supabase](https://img.shields.io/badge/Backend-Supabase-3ECF8E?logo=supabase&logoColor=white)](https://supabase.com)
[![Firebase](https://img.shields.io/badge/Push-Firebase%20FCM-FFCA28?logo=firebase&logoColor=black)](https://firebase.google.com)
[![Platforms](https://img.shields.io/badge/Platforms-Android%20%7C%20Windows-informational)](#-tech-stack)
[![Version](https://img.shields.io/badge/version-1.0.5-green)](#-download-apk)

[Overview](#-project-overview) · [Features](#-features) · [Architecture](#-architecture) · [Download](#-download-apk) · [Contact](#-social-links)

</div>

---

## 📖 Project Overview

**Gaza Shaheed** is a bilingual (Arabic / English) mobile and desktop application built with **Flutter**. Its mission is not just to remember, but to make sure the names, faces, and human stories of Gaza's martyrs are never lost.

The platform brings together three kinds of content:

- **Martyr stories**: personal profiles with photos, age, city, job, date of martyrdom, and a written story, submitted by the community and reviewed by moderators.
- **Massacres**: documented events with dates, locations, casualty counts, and photo galleries.
- **Other stories**: curated stories grouped by category, plus historical events.

The project ships as **two experiences in one codebase**:

| Experience | Audience | Highlights |
|---|---|---|
| **Public app** | Visitors and families | Browse, search, favorite, share, submit a story, explore the map |
| **Admin dashboard** | Moderators and administrators | Review submissions, manage content, send notifications, manage admins and permissions |

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| **Framework** | Flutter (Dart `^3.6.0`), Material Design |
| **State management & routing** | [GetX](https://pub.dev/packages/get): controllers, bindings, named routes, translations |
| **Backend as a service** | [Supabase](https://supabase.com): Auth, PostgreSQL (with RLS), RPC functions, Storage |
| **Push notifications** | Firebase Core + Firebase Cloud Messaging + `flutter_local_notifications` |
| **Authentication** | Supabase Auth (email/password, OTP password recovery) + Google Sign-In |
| **Maps** | `flutter_map` + `latlong2` (OpenStreetMap tiles) |
| **Charts** | `fl_chart`, `syncfusion_flutter_sliders` |
| **Media** | `image_picker`, `cached_network_image`, `flutter_svg` |
| **Sharing & links** | `share_plus`, `url_launcher` |
| **Connectivity & storage** | `connectivity_plus`, `shared_preferences`, `path_provider` |
| **OTA updates** | `package_info_plus`, `http`, `open_filex` (self-hosted APK updates) |
| **Localization** | GetX `Translations` (English + Arabic, RTL-aware) |
| **Fonts** | Jali Arabic, Revalia, Salsa |
| **Tooling** | `flutter_launcher_icons`, `flutter_native_splash`, `flutter_lints` |

---

## 🏗️ Architecture

The app follows a **layered, controller-driven architecture** on top of GetX, with Supabase as the single source of truth.

```
┌──────────────────────────────────────────────────────────────┐
│                       Presentation Layer                     │
│   screens/User · screens/Admin · components (shared widgets) │
└──────────────────────────────┬───────────────────────────────┘
                               │  Obx / GetBuilder (reactive)
┌──────────────────────────────▼───────────────────────────────┐
│                        Controller Layer                      │
│   controllers/User · controllers/Admin  (GetxController)     │
│   Injected through bindings/ (UserBinding, AdminBinding)     │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────▼───────────────────────────────┐
│                          Data Layer                          │
│   models/ (fromJson / toJson) · Supabase client · FCM        │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────▼───────────────────────────────┐
│                           Backend                            │
│  Supabase Auth · Postgres + RLS + RPC · Storage buckets      │
│  Firebase Cloud Messaging (topics & per-user push)           │
└──────────────────────────────────────────────────────────────┘
```

### Key design decisions

- **Separate bindings for each experience.** `UserBinding` and `AdminBinding` register only the controllers each side needs, and most are kept alive (`permanent: true`) so data survives navigation.
- **Server-side logic in Postgres RPCs.** Heavier queries (dashboard stats, "On This Day", For You feed, admin exports, saving a massacre with its gallery) live in database functions, which keeps the client thin.
- **Row Level Security for access control.** The Supabase publishable key is safe to ship in the client because RLS enforces access server-side. Rate limits on story submissions (2 per 24h, 5 pending) are enforced by the database too.
- **Granular role-based permissions.** Admins get fine-grained permissions (`viewStories`, `approveStories`, `manageAdmins`, `sendNotifications`, and more) grouped by area and checked through a central `PermissionsController`.
- **Single source of truth for config.** `config/` holds colors, themes, limits, and Supabase setup. Social accounts are defined once and reused across the support, More, and About pages.
- **Resilient networking.** Request timeouts, a global network status banner, skeleton loaders, and orphaned-upload cleanup if a story submission fails.
- **OTA updates outside the Play Store.** The app checks an `app_releases` table by platform and build number, downloads the APK over HTTPS, and prompts the installer. Updates can be marked mandatory.

---

## ✨ Features

### 👥 For visitors and families

- 🏠 **Home feed** with *For You* and *Latest* sections, featured stories, and discovery sections
- 🔎 **Powerful search** with recent searches, suggestions, and filters by city, gender, job, age range (slider), and date of martyrdom
- ❤️ **Favorites** to save stories for later
- 📤 **Share** a story as formatted text with source credit and app link
- 📝 **Submit a story** with up to 3 photos (2 MB each), tracked through a pending → approved/rejected moderation flow
- 🗓️ **On This Day** to remember martyrs, massacres, and events tied to today's date, with an optional daily reminder
- 🕊️ **Massacres** section with details and image galleries
- 📚 **Other stories** organized by category
- 🗺️ **Interactive map of Gaza** with cities and areas
- 📊 **Statistics** screen with visual breakdowns
- 🚩 **Report content** that needs review
- 💬 **Contact and support tickets** (bug, suggestion, question, other) with in-app replies from the team
- 🔔 **Push and in-app notifications** with unread badges
- 🔐 **Authentication** by email/password or Google, OTP password recovery, and change password
- 👤 **Profile** page and account settings
- 🌍 **Arabic and English** with full RTL support
- 🌙 **Dark mode**
- ⬆️ **In-app update dialog** for new releases

### 🛡️ For administrators

- 📈 **Dashboard** with live stats and charts (visitors, story rates)
- ✅ **Moderation queue** to review, approve, or reject submitted stories
- ✍️ **Full content management** for stories, massacres, other stories, historical events, and story categories
- ⭐ **Home curation** to choose which stories are featured on the home screen
- 🏙️ **Cities and areas** management for search and the map
- 👥 **Users** list and detail views
- 🚩 **Reports** table and detail pages
- 📬 **Support inbox** to read and reply to user messages
- 📣 **Notification center** to send push notifications to users
- 🔑 **Admin management** to create admins and assign granular permissions
- 📤 **Data export** gated by permission
- 🖥️ **Desktop-friendly layout** with sidebar navigation (Windows)

---

## 📁 Folder Structure

```
gaza_martyer_app/
├── android/                    # Android platform project (manifest, splash, Firebase config)
├── ios/                        # iOS platform project
├── windows/                    # Windows desktop runner
├── assets/
│   ├── cover/                  # Cover images
│   ├── fonts/                  # Jali Arabic, Revalia, Salsa
│   ├── logo/                   # App icon, splash, social icons
│   └── svgs/                   # Vector assets
├── lib/
│   ├── main.dart               # Entry point, theming, routes, Firebase/Supabase init
│   ├── firebase_options.dart   # Generated Firebase options
│   ├── shared_pref_service.dart
│   ├── bindings/               # GetX bindings (user_binding, admin_binding)
│   ├── components/             # Reusable widgets (cards, dialogs, nav bar, skeletons, ...)
│   ├── config/                 # app_config, colors, themes, supabase_config
│   ├── controllers/
│   │   ├── Admin/              # Dashboard, permissions, moderation, notifications, ...
│   │   └── User/               # Auth, stories, search, filters, map, favorites, OTA, ...
│   ├── locale/                 # Translations (en / ar) and locale controller
│   ├── models/                 # Martyr, Massacre, OtherStory, Report, SupportTicket, ...
│   └── screens/
│       ├── Admin/              # Dashboard shell, pages, widgets
│       └── User/               # Home, Search, Favorite, Story, Submit, Map, Statistics, ...
├── analysis_options.yaml
└── pubspec.yaml
```

---

## 🚀 Getting Started

> **Prerequisites:** Flutter SDK (Dart `^3.6.0`), a Supabase project, and a Firebase project.

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/gaza_martyer_app.git
cd gaza_martyer_app

# 2. Install dependencies
flutter pub get

# 3. Configure your backend
#    - lib/config/supabase_config.dart  → your Supabase URL + publishable key
#    - lib/firebase_options.dart        → generate with `flutterfire configure`
#    - android/app/google-services.json → from your Firebase console

# 4. Run
flutter run
```

**Build release artifacts**

```bash
flutter build apk --release        # Android
flutter build windows --release    # Windows desktop
```

> ⚠️ Never commit a `service_role` / secret key. Only the publishable (anon) key belongs in client code. Consider adding `.idea/`, `.claude/`, and `.mcp.json` to your `.gitignore`.

---

## 🔮 Future Improvements

- [ ] 🍎 Publish the iOS build (Sign in with Apple is already stubbed in the UI strings)
- [ ] 📴 Offline mode with local caching of favorites and recently viewed stories
- [ ] 🔍 Full-text and phonetic Arabic name search (handling spelling variants)
- [ ] 🧪 Unit, widget, and integration test coverage, plus a CI pipeline (GitHub Actions)
- [ ] 🌐 More languages (French, Turkish, Spanish, ...)
- [ ] 🕯️ Family-verified profiles and memorial pages with a "light a candle" tribute
- [ ] 🖼️ Image compression before upload and lazy-loaded galleries
- [ ] 📱 Home-screen widget for "On This Day"
- [ ] 🌍 Web build of the public experience
- [ ] ♿ Accessibility audit (screen readers, font scaling, contrast)

Have an idea? [Open an issue](../../issues), contributions are welcome.

---

## 🖼️ Screenshots

> Add your screenshots to `docs/screenshots/` and update the paths below.

| Splash | Home | Story Details |
|:---:|:---:|:---:|
| ![Splash](docs/screenshots/splash.png) | ![Home](docs/screenshots/home.png) | ![Story](docs/screenshots/story.png) |

| Search & Filters | On This Day | Map of Gaza |
|:---:|:---:|:---:|
| ![Search](docs/screenshots/search.png) | ![On This Day](docs/screenshots/on_this_day.png) | ![Map](docs/screenshots/map.png) |

| Statistics | Submit a Story | Dark Mode |
|:---:|:---:|:---:|
| ![Statistics](docs/screenshots/statistics.png) | ![Submit](docs/screenshots/submit.png) | ![Dark](docs/screenshots/dark.png) |

**Admin dashboard**

| Dashboard | Moderation Queue | Permissions |
|:---:|:---:|:---:|
| ![Dashboard](docs/screenshots/admin_dashboard.png) | ![Requests](docs/screenshots/admin_requests.png) | ![Permissions](docs/screenshots/admin_permissions.png) |

---

## 📥 Download APK

<div align="center">

[![Download APK](https://img.shields.io/badge/Download-Latest%20APK-brightgreen?style=for-the-badge&logo=android&logoColor=white)](https://github.com/<your-username>/gaza_martyer_app/releases/latest)

**Current version:** `1.0.5` (build 6)

</div>

1. Open the [**Releases**](../../releases/latest) page and download the latest `.apk`.
2. On your phone, allow *Install unknown apps* for your browser or file manager.
3. Open the downloaded file and tap **Install**.

Already installed? The app checks for new versions on launch and can **update itself** from inside the app.

---

## 🌐 Social Links

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-a7med2002-181717?logo=github&logoColor=white)](https://github.com/<username>)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-ahmedmeqdad0-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ahmedmeqdad0)
[![Instagram](https://img.shields.io/badge/Instagram-ahmedmeqdad0-E4405F?logo=instagram&logoColor=white)](https://instagram.com/ahmedmeqdad0)
[![Telegram](https://img.shields.io/badge/Telegram-A7med_Meqdad-26A5E4?logo=telegram&logoColor=white)](https://t.me/A7med_Meqdad)
[![Email](https://img.shields.io/badge/Email-ahmd2002mqdad@gmail.com-D14836?logo=gmail&logoColor=white)](mailto:ahmd2002mqdad@gmail.com)

</div>

---

<div align="center">

*"They are not numbers. They are names, faces, and stories."*

Made with ❤️ and Flutter

</div>
