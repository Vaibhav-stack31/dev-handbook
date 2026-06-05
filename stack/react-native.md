# React Native (Expo)

## What I use it for

To build mobile apps with React Native and Expo Router, camera, offline sync, etc.

## Setup / Boilerplate

To skip manual setup and use NativeWind (initializes a new Expo project with NativeWind v5, Expo SDK 54, and Tailwind CSS.)

```bash
npx rn-new@next --nativewind
```

---

## To setup React Native Expo manually with NativeWind

### Create Expo App

Scaffold a new Expo project using the latest template:

```bash
npx create-expo-app@latest <app_name>
```

### NativeWind v5 Setup

NativeWind v5 brings Tailwind CSS to React Native.

> Note: NativeWind v5 is currently in preview. Verify compatibility before using it in production.

#### Step 1 — Install NativeWind and peer dependencies

```bash
npx expo install nativewind@preview react-native-css react-native-reanimated react-native-safe-area-context
```

#### Step 2 — Install Tailwind CSS dev dependencies

```bash
npx expo install --dev tailwindcss @tailwindcss/postcss postcss
```

#### Step 3 — Create `postcss.config.mjs`

Create this file at the project root:

```js
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

#### Step 4 — Create `global.css`

Create this file at the project root:

```css
@import "tailwindcss/theme.css" layer(theme);
@import "tailwindcss/preflight.css" layer(base);
@import "tailwindcss/utilities.css";

@import "nativewind/theme";
```

#### Step 5 — Configure Metro (`metro.config.js`)

Scaffold the config file if it does not already exist:

```bash
npx expo customize metro.config.js
```

Update `metro.config.js` with:

```js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativewind } = require("nativewind/metro");

/** @type {import('expo/metro-config').MetroConfig} */
const config = getDefaultConfig(__dirname);

module.exports = withNativewind(config);
```

#### Step 6 — Pin `lightningcss` version

Add or merge the following into `package.json`:

```json
{
  "overrides": {
    "lightningcss": "1.30.1"
  }
}
```

#### Step 7 — Generate TypeScript declarations

Start the dev server once:

```bash
npx expo start --clear
```

This automatically generates `nativewind-env.d.ts`, enabling TypeScript support for Tailwind class names.

### Setup Complete

You can now use Tailwind utility classes directly in React Native components via the `className` prop.
