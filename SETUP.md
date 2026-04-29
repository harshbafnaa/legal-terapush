# TeraPush Legal — Setup Guide

AI-powered legal due diligence platform for Indian startups.

---

## 🚀 Quick Deploy to Vercel

### 1. Clone & Deploy

```bash
git init
git add .
git commit -m "Initial commit"
# Push to GitHub, then import in Vercel
```

### 2. Environment Variables (Vercel Dashboard)

Set these in **Vercel → Settings → Environment Variables**:

| Variable | Value |
|----------|-------|
| `ANTHROPIC_API_KEY` | `sk-ant-...` (your Claude API key) |

### 3. Supabase Setup

Create a free project at [supabase.com](https://supabase.com) then run this SQL in the **SQL Editor**:

```sql
-- Profiles
CREATE TABLE profiles (
  id UUID REFERENCES auth.users PRIMARY KEY,
  full_name TEXT,
  company TEXT,
  role TEXT,
  phone TEXT,
  bio TEXT,
  email TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can read own profile" ON profiles FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can update own profile" ON profiles FOR ALL USING (auth.uid() = id);

-- Chat Sessions
CREATE TABLE chat_sessions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  title TEXT,
  assistant_type TEXT DEFAULT 'general',
  created_at TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE chat_sessions ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users own sessions" ON chat_sessions FOR ALL USING (auth.uid() = user_id);

-- Chat Messages
CREATE TABLE chat_messages (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  session_id UUID REFERENCES chat_sessions ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users NOT NULL,
  role TEXT CHECK (role IN ('user', 'assistant')),
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE chat_messages ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users own messages" ON chat_messages FOR ALL USING (auth.uid() = user_id);

-- Documents
CREATE TABLE documents (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  title TEXT NOT NULL,
  type TEXT NOT NULL,
  content TEXT,
  status TEXT DEFAULT 'Draft',
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users own documents" ON documents FOR ALL USING (auth.uid() = user_id);

-- DD Checklists
CREATE TABLE dd_checklists (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  deal_name TEXT,
  stage TEXT,
  role TEXT,
  state JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE dd_checklists ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users own checklists" ON dd_checklists FOR ALL USING (auth.uid() = user_id);
```

### 4. Add Supabase Keys to index.html

Open `index.html` and replace at the top of the `<script>` section:

```javascript
const SUPABASE_URL  = 'https://xxxxx.supabase.co';       // from Project Settings → API
const SUPABASE_KEY  = 'eyJhbGciOiJIUzI1NiIs...';          // anon/public key
```

---

## 📁 Project Structure

```
terapush-legal/
├── index.html          # Full SPA frontend
├── api/
│   └── claude.js       # Vercel serverless proxy for Claude API
├── vercel.json         # Routing config
└── SETUP.md            # This file
```

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🔐 Auth | Supabase email/password login, signup, logout |
| 💬 AI Chat | Multi-turn chat with session history saved to Supabase |
| 🤖 6 AI Assistants | Legal Counsel, DD Analyst, Cap Table, Compliance, Fundraise, Financial |
| 📄 Document Studio | 10 legal doc types — Term Sheet, SHA, SSA, NDA, ESOP, Convertible Note, and more |
| 🔍 Due Diligence | 50+ item interactive checklist across 6 DD categories |
| 📁 Document Vault | Upload and AI-analyze legal documents |
| 📊 Cap Table Builder | Model equity, founders, investors, ESOP pool |
| 👤 Profile | User profile management |

---

## 🇮🇳 Legal Documents Available

- Term Sheet (CCPS / CCD / Equity / SAFE)
- Shareholders Agreement (SHA)
- Share Subscription Agreement (SSA)
- Non-Disclosure Agreement (NDA)
- Board Resolution (Allotment, Appointment, ESOP)
- ESOP Policy
- Convertible Note Agreement
- Memorandum of Understanding (MoU)
- Founders' Agreement
- Employment Agreement

---

## 🎨 Brand & Design

- **Colors**: Navy `#0B1D3A` + Light Blue `#2563EB` / `#BAE0FF`
- **Fonts**: League Spartan (headings) + Poppins (body)
- **Theme**: Minimal, professional, mobile-responsive
