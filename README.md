StoreBounty Starter App

The StoreBounty Starter App is a Flutter/Dart reference application and testbed for developers who want to build a mobile storefront for any StoreBounty store with minimal effort.

Instead of rebuilding product listing, cart, checkout, authentication, and theming from scratch, this starter app wires together StoreBounty’s published Flutter packages and a simple JSON config file so you can:

Point the app at any StoreBounty store (via storebounty.json).

Pull live data from StoreBounty APIs.

Use prebuilt screens and flows from StoreBounty packages.

Focus almost entirely on branding, theming, and UX, not plumbing.

High-Level Architecture

At a high level, the app is built around two ideas:

Config-Driven Store Selection

At runtime, the app loads config/storebounty.json (bundled as an asset). 
GitHub

That file contains the identifiers and tokens for the target StoreBounty store (e.g. STORE_ID, X_ACCESS_TOKEN, environment, API base, etc.).

The storebounty_init package reads this JSON and exposes strongly-typed access to the store’s data and configuration in your Flutter code. 
Dart packages

Composable StoreBounty Packages

The starter app depends on a set of StoreBounty Flutter packages (see pubspec.yaml): 
GitHub

storebounty_init – core initialization & data access (store details, payment gateways, etc.). 
Dart packages

storebounty_cart_manager – cart storage and price/total calculations. 
Dart packages
+1

storebounty_onboarding – intro / onboarding screens that present what the store offers. 
Dart packages
+1

storebounty_themes – theming, layout, and prebuilt storefront UI components. 
Dart packages
+1

storebounty_auth – authentication for customers (login, access control). 
Dart packages

storebounty_checkout – unified checkout flow (delivery details, payments, order confirmation). 
Dart packages
+1

The starter app wires these together into a working storefront so you can just:

Drop in your storebounty.json.

Choose / tweak a theme.

Ship a branded mobile storefront.

Repository Layout

From the repo root: 
GitHub

.
├── android/           # Android platform files
├── ios/               # iOS platform files
├── macos/             # macOS runner
├── linux/             # Linux runner
├── windows/           # Windows runner
├── web/               # Web support (if enabled)
├── assets/
│   ├── images/        # Storefront images (logos, banners, etc.)
│   └── icons/         # Custom icons
├── config/
│   └── storebounty.json   # Store configuration (YOU customize this)
├── lib/
│   └── ...            # Flutter app source (entrypoint, widget tree, etc.)
├── test/              # Flutter tests
├── pubspec.yaml       # Dependencies & asset configuration
├── analysis_options.yaml
└── README.md


Key pieces for you as a StoreBounty integrator:

config/storebounty.json – the file that binds this app to a specific store.

assets/images & assets/icons – where you skin the app visually.

lib/ – where you:

Adjust routes & navigation.

Wrap StoreBounty components with your own widgets.

Add extra screens (blog, help center, etc.).

Requirements
1. Tooling

Flutter SDK: Dart SDK constraint is >=2.19.2 <3.0.0, which corresponds to Flutter 3.x in practice. 
GitHub

A working Flutter environment for:

Android (Android Studio / emulator or physical device)

iOS (Xcode + simulator or device)

Optionally web & desktop (if you care about those targets).

2. StoreBounty Account & Store Config

You need a StoreBounty Business Account and at least one store. 
admin.storebounty.com
+1

From the StoreBounty Admin Portal, you generate/download your storebounty.json file, which contains credentials and store metadata required by storebounty_init. 
Dart packages

Quick Start (TL;DR)

Clone the repo:

git clone https://github.com/storebounty/storebounty-starter-app.git
cd storebounty-starter-app


Drop in your store config:

Place your storebounty.json inside the config/ folder (replace any placeholder file).

Confirm that config/ is listed as an asset in pubspec.yaml (it already is in this repo). 
GitHub

Install dependencies:

flutter pub get


Run the app:

# Android
flutter run -d android

# iOS
flutter run -d ios

# or just
flutter run


Start theming:

Replace images/icons under assets/images and assets/icons.

Adjust colors/typography via storebounty_themes and your own widgets.

At this point, you have a fully working StoreBounty mobile storefront pulling live data from your store through StoreBounty APIs, using the official packages.

Configuration: config/storebounty.json
What storebounty.json Represents

The storebounty.json file is the bridge between your Flutter app and StoreBounty:

It tells storebounty_init which store to talk to.

It includes the access token(s) needed to call StoreBounty APIs on behalf of that store.

It can contain environment info (e.g., production vs sandbox) and other store-level metadata used by StoreBounty packages. 
Dart packages

You normally do not hand-craft this file – you download it from the StoreBounty Admin Portal, which guarantees the correct structure and keys. 
Dart packages
+1

Typical Fields (Illustrative)

Exact field names come from the portal and may evolve, but conceptually you should expect entries such as:

{
  "store_id": "YOUR_STORE_ID",
  "x_access_token": "YOUR_X_ACCESS_TOKEN",
  "environment": "production",
  "api_base_url": "https://api.storebounty.com",
  "store_name": "Your Brand Name",
  "theme": "default_teal"
}


Key points:

store_id: Identifies which store’s catalog, customers, and orders to work with.

x_access_token: API access token for authenticated calls from the mobile app.

environment: Lets you point the app to staging/sandbox if needed.

api_base_url: Optional override when talking to non-default endpoints.

If the structure from the Admin Portal differs from the example above, always treat the downloaded file as the source of truth.

Asset Wiring

The pubspec.yaml already declares config/ as an asset folder: 
GitHub

flutter:
  uses-material-design: true
  assets:
    - config/
    - assets/images/
    - assets/icons/


This lets storebounty_init load config/storebounty.json at runtime without extra work on your side.

Initialization Flow

The canonical pattern recommended by storebounty_init is: 
Dart packages

import 'package:flutter/material.dart';
import 'package:storebounty_init/storebountyinit.dart';

void main() {
  // Initialize StoreBounty core (reads storebounty.json, sets up data)
  StoreBountyInit.trigger();

  // Run your Flutter app
  runApp(MyApp());
}


StoreBountyInit.trigger():

Loads storebounty.json from config/.

Hydrates core models such as store data, payment gateways, etc.

Makes this data available via StoreBountyCore helpers (for example StoreBountyCore.getStore()). 
Dart packages

If you change the app structure, keep this invariant: trigger StoreBounty initialisation once before runApp(...).

StoreBounty Packages in This Starter

This section spells out what each imported StoreBounty package does and how the starter app is meant to leverage it.

storebounty_init

Purpose: Shared core utilities, configuration loading (storebounty.json), and access to store data. 
Dart packages

You use it to:

Bootstrap the app (StoreBountyInit.trigger()).

Retrieve the current store and other configuration via helper APIs (e.g. StoreBountyCore.getStore()).

storebounty_themes

Purpose: Provides prebuilt themes and UI components for StoreBounty mobile apps. It controls the global look & feel (colors, fonts, layout) and wires together flows from other packages. 
Dart packages

Example usage (from package docs):

import 'package:storebounty_themes/entry/entrypoint.dart';

class AppHome extends StatelessWidget {
  const AppHome({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return StorebountyTheme(
      goToCheckout: () {
        // 1. Use storebounty_auth to confirm user is logged in
        // 2. Navigate to storebounty_checkout screen
      },
    );
  }
}


Inside this starter app, your root widget will typically be a StorebountyTheme (or something that composes it), and you can:

Pick a predefined theme.

Override colors/fonts/icons.

Plug your checkout navigation into goToCheckout. 
Dart packages
+1

storebounty_onboarding

Purpose: Introductory/onboarding screens explaining what the store offers. 
Dart packages
+1

How the starter uses it conceptually:

First-time users see an onboarding carousel (hero images, short text).

After completion, they land in the themed storefront UI.

What you can customize:

Images and iconography via assets/images/ and assets/icons/.

Copy (e.g. value propositions, taglines) either through config or by wrapping the onboarding widget in your own.

storebounty_cart_manager

Purpose: Handles all cart operations and aggregates totals. 
Dart packages
+1

Capabilities:

Add/remove/update cart items.

Compute total amounts/prices for the cart.

Persist cart state using shared_preferences.

Why it matters here:

The starter app delegates cart logic to this package so you don’t re-implement cart state management.

UI components from storebounty_themes and storebounty_onboarding rely on it for cart data.

storebounty_auth

Purpose: Unified authentication layer for StoreBounty mobile apps; integrates tightly with storebounty_init. 
Dart packages

Typical workflow:

Before checkout or accessing restricted views, check if user is logged in.

If not, redirect to auth screens provided by this package.

Used in combination with:

storebounty_themes and storebounty_checkout, where the goToCheckout callback normally:

Validates login via storebounty_auth.

Navigates to the checkout flow in storebounty_checkout. 
Dart packages
+1

storebounty_checkout

Purpose: Standardized checkout UX for StoreBounty ecosystem – payment and delivery details collection. 
Dart packages
+1

Responsibilities:

Capture delivery details and shipping method.

Integrate with StoreBounty payment options.

Finalize orders and surface confirmation to the user.

Why use it instead of building your own:

Avoid duplicating checkout logic across multiple storefront apps.

Inherit any improvements/fixes from the core StoreBounty team by simply bumping package versions.

Theming & Branding

The starter app is deliberately thin on custom UI logic and leans heavily on storebounty_themes. 
Dart packages
+1

What You Can Change Quickly

Colors & Typography

Use the theme options in storebounty_themes to:

Change primary/secondary colors.

Adjust typography (font families and weights).

Customize icon style if provided by the theme.

Images & Icons

Replace or add files under:

assets/images/
assets/icons/


Update any image references you add in your own widgets.

Layout & UX Details

Wrap or extend the widgets from storebounty_themes:

Inject your own app bars, footer widgets, or floating action buttons.

Add additional pages (blog, help center, etc.) and link them from the main navigation.

Creating Your Own “Theme”

You have two broad options:

Extend Existing Themes

Keep using StorebountyTheme as your root widget.

Override configuration properties or pass custom builders where available.

Useful when you like the default structure but want brand-specific cosmetics.

Create a Custom Wrapper

Create your own top-level widget that:

Calls StoreBountyInit.trigger() before building the app.

Uses storebounty_init and storebounty_cart_manager directly for data.

Composes only specific pieces of storebounty_themes that you care about.

Best for highly customized experiences while still reusing StoreBounty logic.

Typical App Flow

Conceptually, once properly configured, the starter app behaves as follows:

Bootstrap

main() calls StoreBountyInit.trigger() which:

Loads and parses config/storebounty.json.

Sets up store data, allowed payment gateways, etc. 
Dart packages

Onboarding (first run)

storebounty_onboarding shows intro screens about the store’s offerings. 
Dart packages
+1

Themed Storefront

Root widget (often StorebountyTheme) renders:

Product listings.

Categories.

Cart icon with item count.

Navigation scaffolding.

Cart & Checkout

All cart actions funnel through storebounty_cart_manager. 
Dart packages
+1

On checkout:

storebounty_auth confirms user is logged in.

User is forwarded to storebounty_checkout which captures delivery/payment details and places the order. 
Dart packages
+1

Subsequent Launches

Onboarding is typically skipped based on stored flags.

Users land directly on the themed storefront, with cart state restored from storage if storebounty_cart_manager was configured that way.

Developing & Extending the Starter App
Running in Development

Standard Flutter flow:

# Install dependencies
flutter pub get

# Run on default device
flutter run

# Run on specific device
flutter devices         # list available
flutter run -d <device_id>


Use flutter run -d chrome or desktop devices if you want to validate web/desktop as well (subject to package support).

Where to Put Your Code

New screens – under lib/ in the standard Flutter way (e.g. lib/screens/, lib/widgets/).

Navigation – if you add your own routes, mount them around or inside the StoreBounty entry widgets.

Business-specific logic – encapsulate in separate Dart files or packages so the starter remains a clean shell.

Recommended Extension Patterns

Custom Routes Around StoreBounty UI

Wrap the main StoreBounty entry widget with your own MaterialApp navigator where you define additional named routes.

Hooks for Marketing Flows

Insert deep links or cross-links to marketing content, loyalty programs, or referral flows.

Feature Flags

Introduce a small config layer (e.g. local JSON or remote flags) to toggle certain UI features without modifying the StoreBounty packages.

Troubleshooting
1. App builds but shows empty or broken data

Likely causes:

storebounty.json is missing or malformed.

The file is not in config/ or not packaged as an asset.

Checklist:

Verify config/storebounty.json exists in the repo.

Ensure flutter: → assets: includes - config/ in pubspec.yaml. 
GitHub

Confirm you copied the file from the StoreBounty Admin Portal and did not edit required fields. 
Dart packages
+1

2. App fails on StoreBountyInit.trigger()

Likely causes:

Runtime error while parsing storebounty.json.

Network connectivity issues on first data fetch.

Suggestions:

Check your logs for JSON parsing errors.

Run on a device/emulator with internet access.

Confirm that api_base_url (if present) points to a reachable endpoint.

3. Checkout not working

Likely causes:

goToCheckout callback not properly wired.

storebounty_auth not invoked before storebounty_checkout. 
Dart packages
+1

Suggestions:

Ensure your StorebountyTheme goToCheckout callback:

Checks login status via storebounty_auth.

Navigates to a screen that uses storebounty_checkout.

Updating StoreBounty Packages

All StoreBounty packages are listed in pubspec.yaml with pinned versions: 
GitHub
+1

dependencies:
  storebounty_init: ^0.0.2
  storebounty_cart_manager: ^0.0.4
  storebounty_onboarding: ^0.0.2
  storebounty_themes: ^0.0.4
  storebounty_auth: ^0.0.2
  storebounty_checkout: ^0.0.5


To update:

flutter pub upgrade --major-versions


Then test:

Initialization (no breaking changes in StoreBountyInit.trigger()).

Onboarding flow.

Cart & checkout end-to-end.

If a particular package introduces a breaking change, refer to its pub.dev changelog.

Contributing

If you’d like to:

Add example screens showing advanced theming.

Improve error handling and logging.

Add sample unit/widget tests.

…then you can:

Fork the repo.

Create a feature branch.

Open a PR with:

A clear description of the change.

Screenshots (for UI changes).

Notes about any new configuration options.
