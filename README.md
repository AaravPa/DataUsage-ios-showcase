# Data Usage · iOS modernization showcase

Data Usage is a native iOS utility for monitoring cellular and Wi-Fi consumption across billing periods. The original product combines quota tracking, historical usage, custom counters, CSV/email export, StoreKit Pro upgrades, background refresh, and Today widgets.

This repository is the recruiter-friendly overview. The complete modernized source is maintained separately in the private [`AaravPa/DataUsage-private`](https://github.com/AaravPa/DataUsage-private) repository.

## Why this project is technically interesting

- Modernizes a roughly decade-old Objective-C/XIB application for the current Xcode toolchain without replacing the product architecture wholesale.
- Preserves the existing persistence and app-group boundaries used by the app and widget extensions.
- Replaces an incompatible vendored Core Plot archive with a focused native UIKit/Core Graphics chart view.
- Removes abandoned Crashlytics/Fabric and Flurry binaries while retaining a local `os_log`-backed logging boundary.
- Updates StoreKit payment creation to the product-backed API and removes hardcoded signing-profile assumptions from the project configuration.

## System snapshot

```text
App lifecycle / background refresh
              │
              ▼
Communications + DeviceInfo ──► AppData / SettingsManager
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
                 History        CustomCounter       Current UI
                    │                 │                 │
                    └──────────► shared app-group ◄─────┘
                                      │
                                      ▼
                              TodayWidget extensions
```

Primary implementation paths:

- App lifecycle/navigation: `Classes/iDataUsageAppDelegate.m`, `Classes/MainViewController.m`
- Usage/quota state: `AppData.m`, `SettingsManager.m`, `AppManager.m`
- History and chart: `History.m`, `HistoryChartViewController.m`, `NativeUsageChartView.m`
- Custom counters: `CustomCounter.m`, `CustomCounterManager.m`
- Pro entitlement: `InAppPurchaseManager.m`, `UpgradeViewController.m`
- Widgets/shared reads: `TodayWidget/`, `TodayWidgetPro/`

## Build signal

The `Data Usage` and `Data Usage Pro` schemes build successfully against the current local iOS simulator SDK with signing disabled:

```sh
xcodebuild \
  -project iDataUsage.xcodeproj \
  -scheme "Data Usage" \
  -sdk iphonesimulator \
  -configuration Debug \
  -derivedDataPath /tmp/datausage-ios-derived \
  CODE_SIGNING_ALLOWED=NO \
  CODE_SIGNING_REQUIRED=NO \
  build
```

The project has no XCTest target. A signed device is still required to validate cellular counters, background/location refresh, App Group provisioning, and live StoreKit behavior. See `docs/TESTING.md` in the private source repository for the full checklist.

## Modernization scope

The migration deliberately focuses on buildability, dependency health, and behavior preservation. It does not claim a full Swift/SwiftUI rewrite or a redesigned product UI. Remaining legacy UIKit/location/archive warnings are documented as follow-up work rather than hidden with compiler suppression.

## Links

- [Complete source repository](https://github.com/AaravPa/DataUsage-private)
- Architecture documentation — in the private source repository
- Behavior and invariants — in the private source repository
- Validation guide — in the private source repository
