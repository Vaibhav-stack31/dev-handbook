# Build and Deploy — Expo (EAS)

## Pre-build Checklist
- [ ] env variables set for target environment (`eas env:list production`)
- [ ] `app.json` version bumped
- [ ] committed and pushed to git

---

## Android

```bash
# Development build
eas build --profile development --platform android

# Preview (internal testing)
eas build --profile preview --platform android

# Production
eas build --profile production --platform android
```

---

## iOS

```bash
eas build --profile development --platform ios
eas build --profile preview --platform ios
eas build --profile production --platform ios
```

---

## Both platforms at once

```bash
eas build --profile production --platform all
```

---

## Submit to Store

```bash
# Android → Play Store
eas submit --platform android

# iOS → App Store
eas submit --platform ios
```

---

## Gotchas
- iOS builds require an Apple Developer account ($99/year) — even for preview
- Android builds have no account requirement until Play Store submission
- For quick iOS testing without an account, use Expo Go (limited to Expo SDK only)
- Preview build ≠ production build — always do a production build before final release
- Build runs on EAS servers — local machine doesn't need Android Studio / Xcode
- Check build logs at: https://expo.dev/accounts/[username]/projects/[project]/builds