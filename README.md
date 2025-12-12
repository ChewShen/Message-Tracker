# 🥗 WhatsApp Calorie & Health Tracker (Django + Gemini)

A WhatsApp-based chatbot that allows users to track food intake by sending photos. It uses Google's Gemini 1.5 Flash / 2.0 Flash API to analyze food images for calorie estimation and logs them into a Django database. It also includes an automated email pill reminder system.

## 🛠 Tech Stack
* **Backend:** Python (Django 5.x)
* **AI/ML:** Google Gemini 1.5 Flash (via `google-generativeai`)
* **Messaging:** Twilio Sandbox for WhatsApp (Input) & Gmail SMTP (Output)
* **Tunneling:** Ngrok (for local development)
* **Database:** SQLite (Dev) / PostgreSQL (Prod)

---

## 📅 Development Roadmap & Progress

### Phase 1: The Foundation (Connectivity)
- [x] **1.1. Project Setup:** Install Django, create `caloriebot` project and `core` app.
- [x] **1.2. Environment Setup:** Install `twilio`, `python-dotenv`, `google-generativeai`.
- [x] **1.3. Basic View Logic:** Create a Django view (`views.py`) to handle incoming POST requests.
- [x] **1.4. The "Echo" Test:** Configure Twilio Sandbox to hit Ngrok URL. Send "Hi" and get a reply.
- [x] **1.5. Security:** Implement `@csrf_exempt` to allow Twilio webhooks.

### Phase 2: The "Eyes" (AI Integration)
- [x] **2.1. API Key Setup:** Get Google Gemini API Key and store it in `.env`.
- [x] **2.2. Image Handling:** Update Django view to detect if an incoming message contains media (`MediaUrl0`).
- [x] **2.3. Gemini Function:** Write a utility function `analyze_image(image_url)` that sends the photo to Gemini.
- [x] **2.4. JSON Parsing:** Ensure Gemini returns structured JSON (e.g., `{"food": "Burger", "calories": 500}`) and parse it in Python.
- [x] **2.5. Integration Test:** Send a food photo via WhatsApp and receive a text reply with the estimated calories.

### Phase 3: The "Memory" (Database & Logging)
- [x] **3.1. Database Design:** Create `models.py` with:
    - `Profile` (User phone number, daily goal).
    - `FoodLog` (Food name, calories, image path, timestamp).
- [x] **3.2. Migrations:** Run `makemigrations` and `migrate`.
- [x] **3.3. Saving Logic:** Update the View to save the Gemini result into `FoodLog`.
- [x] **3.4. "Daily Total" Logic:** Write a query to sum calories for the current day.
- [x] **3.5. Feedback Loop:** Reply to the user with "Saved! Your total today is X / 2000 kcal."

### Phase 4: The Assistant (Email Reminders)
- [x] **4.1. Setup:** Configure Gmail App Password and Django SMTP settings.
- [x] **4.2. Pill Model:** Create `PillReminder` model with `user_email` and `reminder_time`.
- [x] **4.3. The Alarm Script:** Create a management command (`check_pills.py`) to check time and send emails.
- [x] **4.4. Magic Links:** Implement a "Mark as Taken" URL (`/pills/taken/<id>`) that updates the database when clicked.
- [x] **4.5. Remote Access:** Configure the script to use the Ngrok public URL so links work from mobile devices.

### Phase 5: Polish & Refinement (Future Work)
- [ ] **5.1. Context Awareness:** Allow users to add captions (e.g., "I only ate half") and have Gemini adjust the math.
- [ ] **5.2. Error Handling:** What happens if the image is blurry? Add error messages.
- [ ] **5.3. Admin Panel:** Polish Django Admin to view user logs easily.

---

## 🤖 How to Use

Once your bot is running, interacting with it is simple. It supports both **Images** (for tracking) and **Text Commands** (for managing your data).

### 📸 1. Log Your Food
Simply send a photo of your meal to the WhatsApp number.
* **Action:** Upload an image (e.g., a plate of Nasi Lemak).
* **Result:** The bot will analyze the food, estimate calories, save it to your log, and show your remaining daily allowance.

### 💬 2. Text Commands
You can control your profile using these text keywords:

| Command | Example | Description |
| :--- | :--- | :--- |
| **Status** | `"Status"` | Shows your total calories consumed vs. your daily goal. |
| **Set Goal** | `"Set goal 1800"` | Updates your daily calorie target to the specified number (default is 2000). |
| **Reset** | `"Reset"` | **Clears today's logs only.** Useful if you made a mistake or want to start fresh for the day. History is preserved. |
| **Help** | `"Help"` | Shows a list of available commands. |

### 🧠 Features
* **Smart Daily Totals:** The bot automatically resets your "Consumed" count at midnight (based on server time).
* **Context Aware:** If you eat 5 meals, sending `"Status"` will sum up all of them for the current day.
* **Magic Link Reminders:** Receive an email when it's time to take medication. Click the link inside the email to instantly mark it as "Taken" in the database. 
