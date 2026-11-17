# سامانه مدیریت مجریان ذی‌صلاح ساختمان

سامانه‌ای برای مدیریت اطلاعات مجریان ذی‌صلاح، پروژه‌های ساختمانی، ضوابط و فرآیند بررسی و تخصیص کار.

## پشته فناوری

- **Backend:** ASP.NET Core 11، C#، Entity Framework Core 11
- **Frontend:** React، TypeScript، Vite، Tailwind CSS
- **Database:** Microsoft SQL Server
- **Architecture:** Modular Monolith / Clean Architecture

## ساختار پروژه

```text
.
├── docs/                 # مستندات
├── src/
│   ├── backend/          # Domain, Application, Infrastructure, Api
│   └── frontend/         # React application
├── tests/                # آزمون‌ها
└── scripts/              # اسکریپت‌های کمکی
```

## پیش‌نیازها

.NET SDK 11، Node.js 24+، SQL Server و Git.

## اجرای محیط توسعه

**بک‌اند** (از ریشه پروژه):

```powershell
dotnet restore
dotnet run --project src/backend/ExecutorManagementSystem.Api
```

**فرانت‌اند** (در ترمینال جداگانه):

```powershell
cd src/frontend
npm install
npm run dev
```

پیش از اجرای بک‌اند، اتصال پایگاه داده را در تنظیمات محلی برنامه پیکربندی کنید. اطلاعات محرمانه را در مخزن ثبت نکنید.

> وضعیت: در حال توسعه؛ قابلیت‌ها مطابق مستندات و تصمیمات تأییدشده به‌تدریج پیاده‌سازی می‌شوند.
