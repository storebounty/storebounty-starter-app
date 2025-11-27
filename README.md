# StoreBounty Starter App

A **Flutter/Dart starter** and **testbed** for building mobile storefronts for any StoreBounty store — with live data, prebuilt flows, and theming handled for you.

---

## What is this?

The **StoreBounty Starter App** is a reference app that:

* Reads a simple config file (`config/storebounty.json`) to know **which StoreBounty store** to talk to.
* Uses published **StoreBounty Flutter packages** for:

  * Initialization & configuration
  * Onboarding screens
  * Themed storefront UI
  * Cart management
  * Authentication
  * Checkout
* Lets you focus on **branding, theming, and UX**, instead of wiring APIs and business logic yourself.

If you want a “just skin it and ship” mobile app for an existing StoreBounty store, this is the starting point.

---

## Who is this for?

This starter is ideal if you are:

* A **Flutter developer** who wants to ship a StoreBounty‑powered storefront quickly.
* An **agency / dev shop** building multiple white‑label apps for different StoreBounty stores.
* A **StoreBounty merchant** with an in‑house dev who just wants a branded app, not a new backend.

---

## How it works (high‑level)

1. At startup, the app loads **`config/storebounty.json`** (bundled as an asset).
2. That JSON file contains at least:

   * `store_id`
   * `x_access_token`
   * Other store‑level details and environment hints.
3. StoreBounty’s init package reads this config and configures:

   * Which store to call
   * Which environment to hit (sandbox / production, etc.).
4. The app then composes **StoreBounty UI + logic packages** (themes, onboarding, cart, auth, checkout).
5. You customize:

   * Assets (logos, hero images, icons)
   * Theme and layout
   * Any extra screens you want to bolt on.

Result: a fully working, **live** storefront app with minimal glue code.

---

## Project structure

At a glance:

```text
.
├── android/           # Android runner
├── ios/               # iOS runner
├── macos/             # macOS runner
├── linux/             # Linux runner
├── windows/           # Windows runner
├── web/               # Web support (if enabled)
├── assets/
│   ├── images/        # Logos, banners, product imagery, hero art
│   └── icons/         # App icons and custom icon assets
├── config/
│   └── storebounty.json   # StoreBounty config (you provide this)
├── lib/               # Flutter application code
│   ├── main.dart      # Entry point
│   └── ...            # Screens, widgets, navigation, etc.
├── test/              # Flutter tests
├── pubspec.yaml       # Dependencies & asset declarations
├── analysis_options.yaml
└── README.md
```

The **two most important things** you’ll touch:

* `config/storebounty.json` → binds this app to a specific StoreBounty store.
* `lib/` and `assets/` → where you implement your branding and custom UX.

---

## StoreBounty packages used

The starter app is designed to sit on top of several StoreBounty Flutter packages (names/versions may vary over time):

| Package                    | Responsibility (conceptually)                                         |
| -------------------------- | --------------------------------------------------------------------- |
| `storebounty_init`         | Loads `storebounty.json`, exposes store config & common data helpers. |
| `storebounty_onboarding`   | Intro / onboarding screens about the store and its offerings.         |
| `storebounty_themes`       | Prebuilt themes & storefront UI: lists, product cards, layouts, etc.  |
| `storebounty_cart_manager` | Cart storage & mutations, totals calculation.                         |
| `storebounty_auth`         | Authentication flows and helpers for customer login.                  |
| `storebounty_checkout`     | Checkout flow: delivery details, payment, order confirmation.         |

You don’t have to wire these from scratch — the starter app already does the heavy lifting.
Your main job is to **configure**, **compose**, and **skin**.

---

## Requirements

* **Flutter SDK**: any recent 3.x release that supports Dart ≥ 2.19.
* A working Flutter setup for:

  * Android (emulator or device)
  * iOS (Xcode + simulator or device)
* A **StoreBounty business account** and at least one store with API access.

---

## Quickstart

### 1. Clone the repo

```bash
git clone https://github.com/storebounty/storebounty-starter-app.git
cd storebounty-starter-app
```

### 2. Drop in your `storebounty.json`

Inside the `config/` directory, place your store’s configuration file:

```text
config/
└── storebounty.json
```

You typically get this from the StoreBounty admin portal / developer section.
Replace any placeholder file in that folder.

Tip: avoid committing live production secrets to a public repo. For open‑sourcing your fork, consider a `.env` strategy or keep that file out of version control.

### 3. Install dependencies

```bash
flutter pub get
```

### 4. Run the app

```bash
# default connected device
flutter run

# explicitly target Android / iOS if needed
flutter run -d android
flutter run -d ios
```

If `storebounty.json` is valid and your store is correctly configured, you should now see **live data from your StoreBounty store** in the app.

---

## Configuration: `config/storebounty.json`

This file is the **contract** between your app and StoreBounty.

A simplified conceptual shape (your real file may have more/different fields):

```json
{
  "store_id": "YOUR_STORE_ID",
  "x_access_token": "YOUR_X_ACCESS_TOKEN",
  "environment": "production",
  "api_base_url": "https://api.storebounty.com",
  "store_name": "Your Store Name",
  "theme": "default_teal"
}
```

Key ideas:

* `store_id` – which store to pull catalog, prices, orders, etc. from.
* `x_access_token` – access token for StoreBounty APIs for this app.
* `environment` – choose between `sandbox`, `staging`, `production`, etc., depending on how your backend is set up.
* `api_base_url` – override if you’re pointing at a non‑default endpoint.
* Other fields – can be used by themes, onboarding, or custom logic.

Rule of thumb: treat the file downloaded from StoreBounty as the **source of truth**. If your downloaded file differs from the snippet above, follow that structure, not this example.

---

## Application flow (runtime overview)

Think of the app lifecycle like this:

1. **Bootstrap**

   * `main()` triggers the StoreBounty init flow, which:

     * Loads `config/storebounty.json` as an asset.
     * Parses it into internal models.
     * Prepares store metadata and endpoints.

2. **Onboarding (first run)**

   * `storebounty_onboarding` shows an intro: what the store does, what users can expect.
   * You can customize copy and imagery to match the brand.

3. **Themed storefront**

   * The root of the app is a StoreBounty theme/widget (from `storebounty_themes`).
   * It renders:

     * Product listings and categories.
     * Access to product details.
     * Cart entry points.
   * You can wrap this with your own `MaterialApp` and navigation.

4. **Cart**

   * Cart logic is delegated to `storebounty_cart_manager`:

     * Add / remove / update line items.
     * Maintain a persistent cart.
     * Compute totals, discounts, etc.

5. **Authentication**

   * When a flow requires a logged‑in customer, the app relies on `storebounty_auth`:

     * Sign‑in screen(s).
     * Session management.
     * Checks at critical points (for example, before checkout).

6. **Checkout**

   * `storebounty_checkout` orchestrates:

     * Address and delivery details.
     * Payment integration.
     * Order creation and confirmation screen.

7. **Subsequent launches**

   * Onboarding typically gets skipped after first run.
   * Cart and login state can be restored, depending on how you configure persistence.

---

## Theming and branding

The point of this starter is: **you don’t rewrite core flows; you reskin them.**

### 1. Update assets

Replace the placeholders in:

* `assets/images/`
* `assets/icons/`

with your brand’s:

* logos
* splash / hero art
* product category/icons illustrations
* empty state graphics

Update any references in your widget tree if you rename anything.

### 2. Customize themes

Depending on how you structure it, your main widget will usually be a theme entry widget from the StoreBounty themes package.

Typical things to tweak:

* Primary / secondary colors
* App bar styling
* Typography (font families, weights)
* Button radius, spacing, etc.

You can either:

* **Extend** the default theme (quickest), or
* **Wrap** the StoreBounty widgets in your own design system for full control.

### 3. Navigation and extra screens

You can—and should—add your own screens around the core storefront:

* About / brand story
* Help and FAQ
* Contact / support
* Blog, content, etc.

Generally you will:

1. Keep a standard `MaterialApp` with a route table.
2. Mount the StoreBounty root view as one of the routes.
3. Add your extra routes around it.

---

## Development workflow

A typical workflow for a merchant or client‑specific app:

1. Create a new store in StoreBounty for that brand.
2. Generate `storebounty.json` from the StoreBounty portal.
3. Fork or clone this starter app.
4. Drop `storebounty.json` into `config/`.
5. Run and verify that live products and categories appear.
6. Skin and customize:

   * Colors, fonts, imagery.
   * Navigation and additional screens.
7. Add platform‑specific polish:

   * App icon and splash screens.
   * Store listing assets (screenshots, descriptions).
8. Ship:

   * Android: Play Store / internal distribution.
   * iOS: App Store / TestFlight.

Then repeat for the next store if you are doing white‑label work.

---

## Troubleshooting

### App builds but shows no data

Check:

* Is `config/storebounty.json` present?
* Is `config/` registered as an asset in `pubspec.yaml`?
* Is the file valid JSON?
* Are `store_id` and `x_access_token` correct and active?

### Crash or error very early in startup

Typical causes:

* JSON parse error in `storebounty.json`.
* Asset not found (typo in filename or missing `config/` entry in `pubspec.yaml`).
* No network connectivity when calling StoreBounty APIs.

### Checkout doesn’t proceed

Likely issues:

* User is not authenticated and the authentication flow is not wired correctly.
* Your checkout navigation is not properly pointing into the `storebounty_checkout` flow.
* API credentials or payment gateway configuration is incomplete in StoreBounty.

---

## Contributing

If you’d like to improve this starter app:

1. Fork the repo.
2. Create a feature branch.
3. Make small, focused changes with clear commit messages.
4. Open a pull request describing:

   * What changed.
   * Why it’s needed.
   * Any breaking changes or migration notes.

Ideas that are always welcome:

* Extra example themes.
* Better error and empty states.
* Sample integrations (push notifications, analytics, etc.).

---

## License

See the `LICENSE` file in this repository (if present) or the license terms for the individual StoreBounty packages on pub.dev.
