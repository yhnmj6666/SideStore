# SideStore

> SideStore is an *untethered, community driven* alternative app store for non-jailbroken iOS devices 

[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://makeapullrequest.com)
[![Nightly SideStore build](https://github.com/SideStore/SideStore/actions/workflows/nightly.yml/badge.svg)](https://github.com/SideStore/SideStore/actions/workflows/nightly.yml)
[![.github/workflows/beta.yml](https://github.com/SideStore/SideStore/actions/workflows/beta.yml/badge.svg)](https://github.com/SideStore/SideStore/actions/workflows/beta.yml)
[![Discord](https://img.shields.io/discord/949183273383395328?label=Discord)](https://dis.sidestore.io)

![Alt](https://repobeats.axiom.co/api/embed/3a329ce95955690b9a9366f8d5598626a847d96c.svg "Repobeats analytics image")

SideStore is an iOS application that allows you to sideload apps onto your iOS device with just your Apple ID. SideStore resigns apps with your personal development certificate, and then uses a [specially designed VPN](https://github.com/jkcoxson/em_proxy) in order to trick iOS into installing them. SideStore will periodically "refresh" your apps in the background, to keep their normal 7-day development period from expiring.

SideStore's goal is to provide an untethered sideloading experience. It's a community driven fork of [AltStore](https://github.com/rileytestut/AltStore), and has already implemented some of the community's most-requested features.

(Contributions are welcome! 🙂)

## Requirements
- Xcode 15
- iOS 14+
- Rustup (`brew install rustup`)

Why iOS 14? Targeting such a recent version of iOS allows us to accelerate development, especially since not many developers have older devices to test on. This is corrobated by the fact that SwiftUI support is much better, allowing us to transistion to a more modern UI codebase.
## Project Overview

### SideStore
SideStore is a just regular, sandboxed iOS application. The AltStore app target contains the vast majority of SideStore's functionality, including all the logic for downloading and updating apps through SideStore. SideStore makes heavy use of standard iOS frameworks and technologies most iOS developers are familiar with.

### EM Proxy
[EM Proxy](https://github.com/jkcoxson/em_proxy) powers the defining feature of SideStore: untethered app installation. By leveraging a custom-built App Store app with additional entitlements ([LocalDevVPN](https://github.com/jkcoxson/LocalDevVPN)) to create the VPN tunnel for us, it allows SideStore to take advantage of [Jitterbug](https://github.com/osy/Jitterbug)'s loopback method without requiring a paid developer account.

### Minimuxer
[Minimuxer](https://github.com/jkcoxson/minimuxer) is a lockdown muxer that can run inside iOS’s sandbox. It replicates Apple’s usbmuxd protocol on macOS to “discover” devices to interface with LocalDevVPN on-device.

### Roxas
[Roxas](https://github.com/rileytestut/roxas) is Riley Testut's internal framework from AltStore used across many of their iOS projects, developed to simplify a variety of common tasks used in iOS development.

We're hoping to eventually eliminate our dependency on it, as it increases the amount of unnecessary Objective-C in the project.

## Contributing/Compilation Instructions

Please see [CONTRIBUTING.md](./CONTRIBUTING.md)

## Personal enterprise-signing build

This build also recognizes the five existing App Groups named
`group.7b9b30a5664c4b76.1` through `group.7b9b30a5664c4b76.5`. The normal
SideStore group takes priority. Otherwise, the lowest-numbered group present in
the executable's signed entitlements is selected. Entitlement ordering does not
change that priority, and an unrecognized group still produces the existing
missing-container error.

When signing in Feather, enable **Merge Entitlements** to include the selected
profile's App Groups in the signature. The provisioning profile must authorize
the group, and the final signed app must include it under
`com.apple.security.application-groups`. Included widgets must be signed with
the same selected group to share data. This code does not modify provisioning
profiles, grant additional entitlements, or use a private database fallback.
Other apps authorized for the same group can access its shared data.

On a personal fork, run the **Nightly SideStore Build** workflow on the patched
branch and download its `SideStore-*.ipa` artifact. This build does not require
uploading a certificate or provisioning profile to GitHub. Release publishing
is restricted to the upstream repository; a personal fork only uploads build
artifacts.

This is a personal-use compatibility change, not enterprise-certificate support
for SideStore's signing pipeline. SideStore still provisions apps with the
authenticated Apple account. Refreshing SideStore itself can change its signing
identity and App Group, and installing an unmodified update removes this
compatibility change. Data is not migrated between groups. Backup helpers
signed by a different team cannot access the enterprise group's container.
Container access and shared widgets require on-device testing after signing.

## Licensing

This project is licensed under the **AGPLv3 license**.
