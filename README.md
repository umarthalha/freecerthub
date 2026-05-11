# freecerthub
Free online learning platform 
# 🎓 Free Cert Hub

> 100% Free · AI-Powered · Certified Learning Platform

A complete online learning platform with non-skippable video lessons, AI-generated quizzes, descriptive answer grading, cheat detection, and verified certificates.

---

## 🗂️ All Pages

| File | Page | Description |
|---|---|---|
| `index.html` | Homepage | Landing page with features & courses |
| `login.html` | Login / Signup | Authentication page |
| `courses.html` | Course Listing | Browse all 500+ courses |
| `lesson.html` | Video Lesson | Non-skippable YouTube player + tab detection |
| `quiz.html` | Per-Video Quiz | MCQ + Descriptive, AI graded |
| `exam.html` | Final Exam | Full final exam with AI grading |
| `dashboard.html` | Student Dashboard | Progress, scores, certificates |
| `generator.html` | Question Generator | Unlimited AI question papers |
| `certificate.html` | Certificate | Download PDF certificate |
| `tutor.html` | AI Tutor | Real-time Claude AI chat |
| `404.html` | Not Found | GitHub Pages 404 page |

---

## 🚀 Deploy to GitHub Pages (Free)

1. Create a new GitHub repo (e.g. `freecerthub`)
2. Upload **all HTML files** to the repo
3. Go to **Settings → Pages**
4. Set source to **Deploy from branch → main → / (root)**
5. Your site goes live at: `https://yourusername.github.io/freecerthub`

---

## 🤖 Connect Real Claude AI

The quiz, exam, generator, and tutor pages use the **Anthropic Claude API**.

To make them work:
1. Go to [console.anthropic.com](https://console.anthropic.com)
2. Create an account → Get your API key
3. The API calls are already written in each file — they work automatically

> ⚠️ For production: move API calls to a backend (Vercel/Netlify serverless function) to hide your API key.

---

## 🗄️ Connect Supabase (Free Database)

For real login, score saving, and progress tracking:

### Step 1 — Create Supabase Project
1. Go to [supabase.com](https://supabase.com) → New Project (free)
2. Note your **Project URL** and **Anon Key**

### Step 2 — Create Tables

Run this SQL in Supabase SQL Editor:

```sql
-- Users table (auto-created by Supabase Auth)

-- Scores table
CREATE TABLE scores (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users(id),
  course_name text,
  lesson_num integer,
  quiz_type text, -- 'quiz' or 'final_exam'
  mcq_score integer,
  desc_score integer,
  total_score integer,
  passed boolean,
  created_at timestamp DEFAULT now()
);

-- Progress table
CREATE TABLE progress (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users(id),
  course_name text,
  lessons_completed integer DEFAULT 0,
  total_lessons integer,
  last_lesson integer DEFAULT 1,
  updated_at timestamp DEFAULT now()
);

-- Certificates table
CREATE TABLE certificates (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users(id),
  course_name text,
  final_score integer,
  cert_id text,
  issued_at timestamp DEFAULT now()
);
```

### Step 3 — Add Supabase to Your Pages

Add this to the `<head>` of every HTML file:

```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script>
  const supabase = window.supabase.createClient(
    'YOUR_SUPABASE_URL',
    'YOUR_SUPABASE_ANON_KEY'
  );
</script>
```

### Step 4 — Login (in login.html)

Replace the `handleLogin()` and `handleSignup()` functions:

```javascript
// SIGN UP
async function handleSignup() {
  const { data, error } = await supabase.auth.signUp({
    email: document.getElementById('signup-email').value,
    password: document.getElementById('signup-pass').value,
    options: { data: { full_name: document.getElementById('signup-name').value } }
  });
  if (error) alert(error.message);
  else window.location.href = 'dashboard.html';
}

// LOGIN
async function handleLogin() {
  const { data, error } = await supabase.auth.signInWithPassword({
    email: document.getElementById('login-email').value,
    password: document.getElementById('login-pass').value
  });
  if (error) alert(error.message);
  else window.location.href = 'dashboard.html';
}

// GOOGLE LOGIN
async function handleGoogle() {
  await supabase.auth.signInWithOAuth({ provider: 'google' });
}
```

### Step 5 — Save Scores (in quiz.html and exam.html)

After grading, call this:

```javascript
async function saveScore(courseName, lessonNum, mcqScore, descScore, passed) {
  const { data: { user } } = await supabase.auth.getUser();
  if (!user) return;
  await supabase.from('scores').insert({
    user_id: user.id,
    course_name: courseName,
    lesson_num: lessonNum,
    quiz_type: 'quiz',
    mcq_score: mcqScore,
    desc_score: descScore,
    total_score: mcqScore + descScore,
    passed: passed
  });
}
```

---

## 🛡️ Security Notes

- Never expose your Claude API key in frontend code for production
- Use **Vercel Serverless Functions** or **Netlify Functions** as a proxy
- Enable **Row Level Security (RLS)** in Supabase so users only see their own data

---

## 📱 Tech Stack

| Layer | Tool | Cost |
|---|---|---|
| Hosting | GitHub Pages | FREE |
| AI (Quiz/Exam/Tutor) | Anthropic Claude API | FREE trial |
| Database + Auth | Supabase | FREE tier |
| Videos | YouTube Embed | FREE |
| Certificates | html2canvas + jsPDF | FREE |

---

Made with ❤️ — 100% Free · AI-Powered Education for Everyone
