# 🚨 Hackathon Docs

## 📌 The Core Idea

The core idea is simple: your platform acts as a **middleman that listens for signals**.

**Pattern A** — UptimeRobot (zero client code needed): The client signs up on your platform, gets a webhook URL, pastes it into UptimeRobot's notification settings. Done. UptimeRobot pings their site from outside every 5 minutes. Their own backend doesn't need to do anything at all.

It does NOT actively check other websites.  
Instead, external tools send signals **to your system**.

---

## 🔗 Big Picture — Who Talks to Who

<img width="1440" height="1040" alt="image" src="https://github.com/user-attachments/assets/3e624877-825f-485d-abb9-b5782cf1ab5e" />


---

## 🔔 What is a Webhook?

A **webhook** is a way for one system to send real-time data to another system when an event happens.

Instead of constantly asking:
> “Did something happen?”

Your system gets notified instantly:
> “Something just happened.”

### 📊 Webhook Flow

<img width="1440" height="920" alt="image" src="https://github.com/user-attachments/assets/89e0e56a-3cfb-441f-b8f8-688dc9500b39" />


---

## ⚙️ Backend Webhook Processing

This is the **most important part** — what happens when your backend receives a webhook.

For your hackathon, you’re simulating this behavior.

### 📊 Backend Processing Flow

<img width="1440" height="1160" alt="image" src="https://github.com/user-attachments/assets/d8bbbb7b-d4ac-4b7b-8364-55bf32e3662e" />


---

And finally — here's the full picture of what a user (a company) actually does to onboard onto your platform:

<img width="1440" height="800" alt="image" src="https://github.com/user-attachments/assets/116ab7df-88fb-430a-a93a-ddee9e9f51cf" />

---

**Pattern B** — Client self-reports (tiny snippet on their side): If a client wants their own backend to report incidents (like an internal database crash that UptimeRobot can't see), they add one fetch() call to their error handler that hits your /api/incidents endpoint with their API key. That's all they add — literally 10 lines. You show this in your pitch as "advanced integration."

<img width="1440" height="960" alt="image" src="https://github.com/user-attachments/assets/9f6b6f69-fb8b-4921-8ea6-7f4587a73241" />


---

## 🧪 Alert Simulator (Demo Feature)

For the hackathon demo, you include an **Alert Simulator**.

### 💡 How it works:

- You create a button inside your app
- That button sends a **fake webhook request** to:

```bash
/api/webhooks/inbound/demo
