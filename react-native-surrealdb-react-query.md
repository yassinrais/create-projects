# React Native + Expo Router + Surrealdb + React Query

```
npm i surrealdb.js tiny-invariant \
@react-native-community/netinfo @tanstack/react-query @dev-plugins/react-query

npm i -D @sebastianwessel/surql-gen
```
---
`./.env`
```
EXPO_PUBLIC_SURREALDB_DATABASE=example-db
EXPO_PUBLIC_SURREALDB_NAMESPACE=example-ns
EXPO_PUBLIC_SURREALDB_URL=http://localhost:8000/rpc
```
---
`./surql-gen.json`
```json
{
	"username": "root",
	"password": "root",
	"ns": "example-db",
	"db": "example-ns",
	"outputFolder": "./api/generated",
	"generateClient": false,
	"lib": "surrealdb.js"
}
```
---
add this script to your 
`./package.json`
```json
{
  "scripts": {
    "db:gen": "rm -rf api/generated/ && npx surql-gen --import=ts"
  }
}
```
---
`./api/index.ts`
```ts
export * from './generated/schema/index';

import invariant from 'tiny-invariant';
import { Surreal } from 'surrealdb.js';

let client: Surreal | undefined;

const url = process.env.EXPO_PUBLIC_SURREALDB_URL!;
const database = process.env.EXPO_PUBLIC_SURREALDB_DATABASE!;
export const namespace = process.env.EXPO_PUBLIC_SURREALDB_NAMESPACE!;

export function getSurrealdbClient() {
	if (client) {
		return client;
	}

	invariant(url, `Surrealdb url was not provided`);
	invariant(database, `Surrealdb database was not provided`);
	invariant(namespace, `Surrealdb namespace was not provided`);

	client = new Surreal();
	client.connect(url, {
		namespace,
		database,
	});

	return client;
}
```
---
`./hooks/useSurrealdb.ts`
```tsx
import { useMemo } from 'react';
import { getSurrealdbClient } from '../api/index';

function useSurrealdb() {
	return useMemo(getSurrealdbClient, []);
}

export default useSurrealdb;
```
---
`./app/_layout.tsx`
```tsx
// import '../global.css';
import {
	onlineManager,
	QueryClient,
	QueryClientProvider,
} from '@tanstack/react-query';
import { SplashScreen, Stack } from 'expo-router';
import NetInfo from '@react-native-community/netinfo';

SplashScreen.preventAutoHideAsync();

onlineManager.setEventListener((setOnline) => {
	return NetInfo.addEventListener((state) => {
		setOnline(!!state.isConnected);
	});
});

const queryClient = new QueryClient({
	defaultOptions: {
		queries: {
			staleTime: Infinity,
			refetchOnWindowFocus: 'always',
		},
	},
});


export default function RootLayout() {
	return (
		<QueryClientProvider client={queryClient}>
			<Layout />
		</QueryClientProvider>
	);
}

function Layout() {
	return (
		<Stack />
	);
}
```
