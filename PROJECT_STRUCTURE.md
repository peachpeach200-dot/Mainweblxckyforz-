# Project Structure

```
bio-link-appwrite/
├─ app/
│  ├─ admin/page.tsx
│  ├─ login/page.tsx
│  ├─ globals.css
│  ├─ layout.tsx
│  └─ page.tsx
├─ components/
│  ├─ AdminDashboard.tsx
│  ├─ LoginForm.tsx
│  └─ PublicProfile.tsx
├─ lib/
│  ├─ appwrite.ts
│  └─ types.ts
├─ public/
│  └─ avatar-placeholder.svg
├─ .env.example
├─ package.json
└─ README.md
```

## Appwrite

- Auth: Email/Password
- Database: `profile` + `links` collections
- Storage: `bio-assets` bucket
- Realtime: profile/links document channels
