# Supabase → Firebase Migration Guide

## 1. Firebase setup
1. Enable **Authentication > Email/Password**.
2. Create Firestore database.
3. Add the app web config already embedded in `root`.

## 2. Collections used
- `users` (role profiles)
- `cabanas` (per-cabana session records)
- `servers` (server list)
- `settings` (global prices + manager/shared state)
- `sessions` (daily/session tracking)
- `late_arrivals` (late-joiner tracking)

## 3. Export from Supabase
From browser console in the app:

```js
await exportSupabaseData('https://<project>.supabase.co', '<anon-or-service-key>');
```

This downloads `supabase_export_YYYY-MM-DD.json`.

## 4. Import into Firebase
1. Sign in as a **Manager**.
2. Load JSON in console and import:

```js
const payload = /* parsed exported json */;
await importFirebaseMigration(payload);
```

## 5. Validate
- Confirm user roles in `users`.
- Confirm sessions load in Manager/Kitchen/Headcount views.
- Confirm late arrivals appear in `late_arrivals`.
