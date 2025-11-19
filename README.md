# 🚀 Serverless, Database-Free URL Shortener Powered by Git Commits

I built a URL shortener with an unconventional architecture — it uses **no database**, **no traditional backend server**, and **no CI workflows**.  
Instead, it uses **GitHub itself as the datastore**, where each shortened URL is represented by a uniquely generated **Git commit hash**.

---

## 💡 Core Idea — Git as an Immutable, Versioned Database

Every time a user shortens a URL:

- A new **empty Git commit** is created in a GitHub repository  
- The **commit message stores the original long URL**  
- The **commit hash (first 7 chars)** becomes the short URL identifier  

Example:


Internally maps to commit `a5b4af0`, where the commit message contains the long URL.

This creates a **fully versioned, permanent, tamper-proof record** without any traditional database.

---

## ⚙️ System Architecture

### **Frontend**
- Built with **React + Vite**
- Deployed on **Vercel**
- Communicates with backend using serverless API endpoints (`/api/create`, `/api/[id]`)

---

### **Backend — Vercel Serverless APIs**

There is **no persistent server**. Everything runs as serverless functions.

---

### 1. **URL Shortening API — `/api/create`**

This endpoint:

1. Receives the long URL
2. Fetches the latest commit on `main` via GitHub REST API:
   - `/git/ref/heads/main`
   - `/git/commits/{sha}`
3. Creates a new **empty Git commit** using:
   - `POST /git/commits`  
     - The long URL becomes the **commit message**
4. Updates the branch pointer using:
   - `PATCH /git/refs/heads/main`
5. Returns:
   - The short hash (7 chars)
   - Full commit SHA

The operation is lightweight (~150–300ms) and requires **no file changes**.

---

### 2. **Redirect API — `/api/[id]`**

This endpoint:

- Takes a short hash (first 7 chars of a commit)
- Resolves the full commit SHA from GitHub
- Fetches the commit message (the original long URL)
- Redirects the user using a 301 redirect

No database — just GitHub API reads.

---

## 🏛️ Why This Architecture Works Well

- **Zero database overhead**
- **Zero DevOps** — GitHub as storage, Vercel as compute  
- Immutable, append-only Git commit log
- **Global redirect performance** via Vercel Edge Network  
- **Versioning is built-in**  
- **Extremely low-cost**, nearly free

---

## 🔌 Tech Stack

- **Frontend:** React, Vite, TailwindCSS  
- **Backend:** Vercel Serverless Functions (Node.js)  
- **Storage Layer:** GitHub REST API (`/git/*` endpoints)  
- **Auth:** GitHub PAT (`contents:write` scope)

---

## 📌 Summary

I engineered a stateless, database-free URL shortener by treating Git commits as storage objects.  
Each short URL corresponds to a Git commit whose message contains the long URL.  
The system uses React + Vercel serverless functions + GitHub’s low-level Git API to achieve a fast, scalable, and fully versioned solution with virtually zero backend maintenance.

---

## Demo
![alt text](./public/ss1.png)
