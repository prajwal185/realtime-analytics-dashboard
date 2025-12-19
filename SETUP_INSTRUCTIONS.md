# Complete Setup Instructions

## Quick Setup (5 minutes)

This is the **fastest way** to get the project running. Follow these exact steps:

### Step 1: Clone Repository
```bash
git clone https://github.com/prajwal185/realtime-analytics-dashboard.git
cd realtime-analytics-dashboard
```

### Step 2: Setup Backend
```bash
cd server
npm install
npm run start:dev
```

You'll see:
```
🚀 Analytics Server running on http://localhost:4000
📊 WebSocket namespace: ws://localhost:4000/analytics
```

### Step 3: Setup Frontend (NEW TERMINAL)
```bash
cd client
npm install
npm run dev
```

You'll see:
```
▲ Next.js 14.x.x
- Local: http://localhost:3000
```

**Open browser**: http://localhost:3000

You're done! The dashboard is now live with real-time data.

---

## Detailed Setup

### Prerequisites
- **Node.js 18+** (LTS): https://nodejs.org
- **npm 9+** (comes with Node.js)
- **Git** (optional, for cloning)

### Verify Installation
```bash
node --version    # Should be v18+
npm --version     # Should be 9+
```

### Project Structure After Setup
```
realtime-analytics-dashboard/
├── server/
│   ├── src/
│   │   ├── kpi/
│   │   │   ├── kpi.gateway.ts          # WebSocket handler
│   │   │   ├── kpi.module.ts
│   │   │   ├── services/
│   │   │   │   └── kpi.engine.ts       # Analytics engine
│   │   │   ├── dto/
│   │   │   │   └── create-transaction.dto.ts
│   │   │   └── entities/
│   │   │       └── transaction.entity.ts
│   │   ├── app.module.ts
│   │   └── main.ts                    # Server entry point
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env                           # Created after setup
│   └── .env.example
├── client/
│   ├── src/
│   │   ├── app/
│   │   │   ├── page.tsx              # Main dashboard
│   │   │   ├── layout.tsx
│   │   │   └── globals.css
│   │   ├── components/
│   │   │   ├── Dashboard.tsx         # Main component
│   │   │   ├── KpiCard.tsx           # KPI display
│   │   │   ├── Charts.tsx            # Recharts visualizations
│   │   │   └── TransactionTable.tsx  # Anomaly table
│   │   ├── hooks/
│   │   │   └── useSocket.ts          # Socket.IO hook
│   │   └── lib/
│   │       └── socket.ts             # Socket initialization
│   ├── package.json
│   ├── tsconfig.json
│   └── next.config.js
├── README.md
├── .gitignore
└── SETUP_INSTRUCTIONS.md
```

---

## Generating All Project Files Locally

If you want to create this project from scratch locally (without cloning), follow this process:

### Using the Provided Files

1. **Create project folder**
```bash
mkdir realtime-analytics-dashboard
cd realtime-analytics-dashboard
```

2. **Initialize git (optional)**
```bash
git init
```

3. **Copy all files from GitHub to your local directory**
   - Download ZIP from GitHub, OR
   - Use `git clone` (recommended)

4. **Run setup**
```bash
cd server && npm install && cd ..
cd client && npm install && cd ..
```

5. **Start services**
```bash
# Terminal 1
cd server && npm run start:dev

# Terminal 2
cd client && npm run dev
```

---

## Creating Backend Files Manually

If you're creating files manually, here's the complete backend structure:

### server/package.json
```json
{
  "name": "realtime-analytics-server",
  "version": "1.0.0",
  "scripts": {
    "start": "nest start",
    "start:dev": "nest start --watch",
    "build": "nest build"
  },
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/platform-express": "^10.0.0",
    "@nestjs/websockets": "^10.0.0",
    "@nestjs/platform-socket.io": "^10.0.0",
    "class-validator": "^0.14.0",
    "class-transformer": "^0.5.1",
    "reflect-metadata": "^0.1.13",
    "rxjs": "^7.8.1",
    "socket.io": "^4.7.2",
    "uuid": "^9.0.0"
  },
  "devDependencies": {
    "@nestjs/cli": "^10.0.1",
    "typescript": "^5.1.3",
    "ts-node": "^10.9.1",
    "@types/node": "^20.3.1"
  }
}
```

### server/src/main.ts
```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors({
    origin: ['http://localhost:3000', 'http://localhost:3001'],
    credentials: true,
  });
  await app.listen(4000);
  console.log('🚀 Analytics Server running on http://localhost:4000');
  console.log('📊 WebSocket namespace: ws://localhost:4000/analytics');
}

bootstrap();
```

### server/src/app.module.ts
```typescript
import { Module } from '@nestjs/common';
import { KpiModule } from './kpi/kpi.module';

@Module({
  imports: [KpiModule],
})
export class AppModule {}
```

*See full code files in the GitHub repository*

---

## Creating Frontend Files Manually

### client/package.json
```json
{
  "name": "realtime-analytics-client",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "socket.io-client": "^4.7.2",
    "recharts": "^2.10.0"
  }
}
```

---

## Testing the Setup

### Option 1: Via Browser
1. Open http://localhost:3000
2. You should see the dashboard with real-time KPI cards
3. Charts will update every 2 seconds

### Option 2: Via WebSocket (Test Tool)

Use **Postman** or **WebSocket King** to test:

**Connect to**: `ws://localhost:4000/analytics`

**Send event**:
```json
{
  "content": "addTransaction",
  "payload": {
    "amount": 250.50,
    "region": "US-WEST",
    "segment": "premium",
    "status": "completed"
  }
}
```

**Expected response**:
```json
{
  "content": "kpiUpdate",
  "payload": {
    "timestamp": 1703000000000,
    "totalRevenue": 15432.50,
    "txCountLastMinute": 45,
    "avgTicketSize": 343.17,
    "anomalyCount": 2,
    "regionBreakdown": {...}
  }
}
```

---

## Troubleshooting

### Port Already in Use
```bash
# Backend (default 4000)
lsof -i :4000
kill -9 <PID>

# Frontend (default 3000)
lsof -i :3000
kill -9 <PID>
```

### Module Not Found
```bash
# Clear and reinstall
rm -rf node_modules package-lock.json
npm install
```

### TypeScript Errors
```bash
# Check TypeScript version
npm ls typescript

# Rebuild
npm run build
```

---

## Deployment

### Deploy Backend to Heroku
```bash
cd server
heroku login
heroku create your-app-name
heroku config:set NODE_ENV=production
git push heroku main
```

### Deploy Frontend to Vercel
```bash
cd client
npm install -g vercel
vercel
# Update WebSocket URL in environment if needed
```

---

## Next Steps After Setup

1. **Explore the code**: Read through the backend and frontend source files
2. **Add features**: Extend KPI calculations, add persistence (MongoDB/PostgreSQL)
3. **Deploy**: Push to production using above guides
4. **Share**: Add to portfolio with link to GitHub

---

## Support

For issues or questions:
1. Check README.md for architecture details
2. Review NestJS docs: https://docs.nestjs.com
3. Review Socket.IO docs: https://socket.io/docs
4. Check Next.js docs: https://nextjs.org/docs

