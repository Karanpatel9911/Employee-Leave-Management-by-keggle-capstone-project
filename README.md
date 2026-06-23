# 🌌 Employee Leave Management System (AI-Powered)

A futuristic, space-themed Employee Leave Management web app built with **HTML, CSS, and JavaScript**, enhanced with **3 AI Agents** for smart leave validation, approval recommendation, and analytics.

🔗 **Live Demo:** https://karanpatel9911.github.io/Employee-Leave-Management-by-keggle-capstone-project/

---

## ❓ Problem

Manual leave management (paper forms, messages, spreadsheets) is slow, error-prone, and gives no useful feedback to employees or admins. There is no record of who approved/rejected what, no way to spot risky requests (overlaps, too-frequent leaves), and no overview of company-wide leave trends. Admins have to read every request manually with no support.

## 💡 Solution

This project solves it with a lightweight web app that adds **3 rule-based AI Agents** on top of a normal leave workflow:
- An agent **validates** every leave request and gives it a confidence score with reasons.
- A second agent **recommends** Approve / Review / Reject — but the **human Admin always makes the final call**.
- A third agent turns all leave data into **live analytics** (totals, approval rate, leave-type chart).

No paid AI APIs are used — all "intelligence" is transparent, explainable, rule-based JavaScript logic, so the project is 100% free to run and fully reproducible.

---

## 🏗️ Architecture

**How a leave request flows through the system:**

1. Employee submits the leave form
2. **Agent 1 (Leave Validation Agent)** checks duration, date overlap, reason-keyword match, and monthly frequency → outputs a confidence score (0-100) with explainable reasons
3. **Agent 2 (Approval Recommendation Agent)** takes that score + leave-type priority → outputs a suggestion: Approve / Review / Reject, with its own confidence and reasons
4. The leave is saved to LocalStorage with both agent results attached, and an Audit Trail entry is logged ("Submitted")
5. Admin Dashboard shows the AI suggestion (Human-in-the-Loop: AI suggests, Admin decides)
6. Admin clicks Approve / Reject → another Audit Trail entry is logged
7. **Agent 3 (Analytics & Reporting Agent)** recalculates stats and the chart every time data changes, and renders the Analytics panel + Global Audit Log panel

**Files:**
- `index.html` — page structure (Welcome / Employee / Admin views)
- `style.css` — space-themed design, animations, and AI-feature styling
- `script.js` — all logic: the 3 agents, form handling, rendering, LocalStorage persistence

---

## ✨ Features

### 🤖 AI Agents (Multi-Agent Workflow)
1. **Leave Validation Agent** — Checks duration, overlapping leaves, reason-type match, and request frequency.
2. **Approval Recommendation Agent** — Suggests Approve / Review / Reject with a confidence score.
3. **Analytics & Reporting Agent** — Shows total requests, approval rate, average AI confidence, and leave-type distribution chart.

### 🧠 Capstone Concepts Covered
- ✅ Confidence Score
- ✅ Explainable AI (reasons shown for every AI decision)
- ✅ Audit Trail (full action history per request + global log)
- ✅ Human-in-the-Loop (Admin always makes the final approve/reject decision)

---

## 🛠️ Tech Stack
- HTML5
- CSS3 (custom space-themed animations)
- Vanilla JavaScript
- Browser LocalStorage (data persistence)

---

## 🚀 How to Run Locally
1. Download/clone this repository
2. Open `index.html` in any browser
3. Login as **User** or **Admin** (use Quick Demo Login for Admin)

> Note: The "Quick Demo Login" auto-fills a sample demo username/password for convenience — it is not a real credential, API key, or secret.

---

## 👤 Roles
- **Employee:** Apply for leave, view AI insights & audit trail, cancel pending requests
- **Admin:** View AI recommendation, approve/reject requests, view analytics & global audit log

---

## 📌 Project Status
✅ Completed — Capstone Project Submission
