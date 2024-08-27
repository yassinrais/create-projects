# Mobile: Expo Router + Nativewind + React Query

```
npx create-expo-app@latest
```

```
npx expo install nativewind@^4.0.1 react-native-reanimated tailwindcss \
@tanstack/react-query @dev-plugins/react-query
```

```
npx pod-install
```

```
npm run reset-project
```

```
rm -rf ./app-example ./constants ./scripts
rm -rf ./components/**
rm -rf ./hooks/**
```
---
```
npx tailwindcss init
```

  `./tailwind.config.js`
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
	content: ['./(app|lib|components)/**/*.{js,jsx,ts,tsx}'],
	presets: [require('nativewind/preset')],
	theme: {
		extend: {},
	},
	plugins: [],
};
```
---
`./global.css`
```scss
@tailwind base;
@tailwind components;
@tailwind utilities;
```
---
`./babel.config.js`
```js
module.exports = function (api) {
	api.cache(true);
	return {
		presets: [
			['babel-preset-expo', { jsxImportSource: 'nativewind' }],
			'nativewind/babel',
		],
	};
};
```
---
```
npx expo customize metro.config.js
```
---
`./metro.config.js`
```js
const { getDefaultConfig } = require('expo/metro-config');
const { withNativeWind } = require('nativewind/metro');

const config = getDefaultConfig(__dirname);

module.exports = withNativeWind(config, { input: './global.css' });
```
---
`./app/_layout.tsx`
```ts
import { Slot } from 'expo-router';
import '../global.css';

export default Slot;
```
---
`./nativewind-env.d.ts`
```ts
/// <reference types="nativewind/types" />
```
---
`./app/index.tsx`
```tsx
import { Text, View } from 'react-native';

export default function Index() {
	return (
		<View className="flex-1 items-center justify-center bg-green-500/20">
			<Text>Background should be green.</Text>
		</View>
	);
}
```
