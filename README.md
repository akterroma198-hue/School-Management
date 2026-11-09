# School Management App — MVP Starter

Monorepo starter containing:
- `apps/backend`: NestJS + Prisma + PostgreSQL API
- `apps/mobile`: Flutter starter (Firebase email/password login, role dashboard, notices)
- `apps/admin`: React + Vite + TypeScript admin starter
- `infra`: Docker Compose for PostgreSQL

## Prerequisites
- Node.js 20+
- Flutter stable
- Docker Desktop
- Firebase project (for mobile/backend authentication)

## Start PostgreSQL
```bash
docker compose -f infra/docker-compose.yml up -d
```

## Start backend
```bash
cd apps/backend
cp .env.example .env
npm install
npx prisma generate
npx prisma migrate dev --name init
npm run start:dev
```
API base URL: `http://localhost:3000/api/v1`

Set `FIREBASE_SERVICE_ACCOUNT_PATH` to a Firebase Admin SDK service-account JSON file.
Set a strong `JWT_ACCESS_SECRET`. Never commit `.env` or service-account credentials.

## Start admin
```bash
cd apps/admin
npm install
npm run dev
```

## Start Flutter app
Configure Firebase for Android/iOS using FlutterFire CLI:
```bash
cd apps/mobile
flutter pub get
flutterfire configure
flutter run
```
Set the backend URL in `lib/core/config.dart`. For Android emulator use `http://10.0.2.2:3000/api/v1`; for iOS simulator use `http://localhost:3000/api/v1`.

## First user provisioning
This starter deliberately does not expose public self-registration or role selection.
Create Firebase users and provision matching rows in `users` using a trusted admin script or controlled admin workflow. The backend derives role and school membership from PostgreSQL, never from client input.

## MVP boundaries
This is a development starter, not a production-certified school information system. Before real student data or payments:
- add resource-level authorization for every student/parent/teacher query;
- add refresh-token rotation and session revocation;
- add school-scoped composite foreign keys to every tenant-owned table;
- add upload scanning and private signed object storage;
- add payment gateway server-side verification and reconciliation;
- add audit logs, backups, retention policies, monitoring, and automated tests;
- complete privacy/legal review for the jurisdictions where schools operate.

## API
- `POST /api/v1/auth/firebase`
- `GET /api/v1/auth/me`
- `GET /api/v1/notices`
- `POST /api/v1/notices` (admin/teacher)
- `POST /api/v1/notices/:id/publish` (admin)
- `GET /api/v1/attendance/student/:studentId` (role guarded; add ownership checks before production)
