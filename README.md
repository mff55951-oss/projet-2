# 🌟 Universal AI - Pro Version 🌟

> **A cutting-edge, privacy-first, client-side AI chat interface.** > **Developed by:** Md eyasin

![Universal AI Pro Interface](https://img.shields.io/badge/Version-2.0-blue?style=for-the-badge)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

---

## 📖 Table of Contents
1. [Project Overview](#-project-overview)
2. [Key Features](#-key-features)
3. [Technology Stack](#-technology-stack)
4. [Getting Started](#-getting-started)
5. [Configuration & API Setup](#-configuration--api-setup)
6. [Security & Privacy](#-security--privacy)
7. [Author](#-author)

---

## 🚀 Project Overview

**Universal AI - Pro Version** একটি অত্যন্ত অ্যাডভান্সড এবং রেসপনসিভ AI চ্যাট ইন্টারফেস। এটি ব্যবহারকারীকে সরাসরি **OpenRouter API**-এর মাধ্যমে পৃথিবীর সবথেকে শক্তিশালী AI মডেলগুলোর (যেমন: GPT-4, Gemini Pro, Claude 3, Llama 3) সাথে যুক্ত করে। 

এটি সম্পূর্ণ ব্রাউজারে রান করে, তাই কোনো সার্ভার-সাইড ট্র্যাকিং নেই। এটি স্পেশালি ইঞ্জিনিয়ারিং, প্রোগ্রামিং এবং সিস্টেম বিল্ডিং নিয়ে কাজ করা ইউজারের কথা মাথায় রেখে ডিজাইন করা হয়েছে।

---

## ✨ Key Features

### 🧠 Intelligent Chat & Context
* **Contextual Memory:** এআই আপনার আগের মেসেজগুলো মনে রাখতে পারে, ফলে কথা বলাটা অনেক ন্যাচারাল হয়।
* **Chat History:** আপনার সমস্ত চ্যাট অটোমেটিক ব্রাউজারের `localStorage`-এ সেভ থাকে। ব্রাউজার রিলোড দিলেও হিস্ট্রি মুছে যায় না।
* **Recent Chats Sidebar:** সাইডবারে আপনার গত চ্যাটগুলো দেখা যায় এবং যেকোনো সময় পুরনো চ্যাটে ফিরে যাওয়া যায়।

### 🎨 Premium UI/UX
* **Modern Aesthetic:** Tailwind CSS ব্যবহার করে গ্লাস-মরফিজম এবং ডার্ক মোড সাপোর্ট দেওয়া হয়েছে।
* **Markdown Support:** কোড ব্লক হাইলাইটিং এবং সুন্দর টেক্সট ফরম্যাটিং।
* **Mobile Ready:** মোবাইল এবং ডেস্কটপ সব ডিভাইসেই স্মুথ কাজ করে।

### 🛠️ Advanced Tools
* **Voice Input:** কিবোর্ডে টাইপ না করেই কথা বলে ইনপুট দেওয়ার সুবিধা।
* **Text-to-Speech:** এআই-এর উত্তরগুলো ব্রাউজার আপনাকে পড়ে শোনাবে।
* **Image Support:** সরাসরি ছবি আপলোড করে এআই-এর কাছ থেকে সাহায্য নেওয়া যায় (ভিশন সাপোর্ট মডেলের ক্ষেত্রে)।
* **Stop Generation:** বড় রেসপন্স জেনারেট হওয়ার সময় মাঝপথে থামিয়ে দেওয়ার সুবিধা।

### 🧹 Data Control
* **Nuclear Option (Clear All Data):** এক ক্লিকেই আপনার সমস্ত এপিআই কি, সেটিংস এবং হিস্ট্রি পার্মানেন্টলি ডিলিট করার সুবিধা।

---

## 🛠️ Technology Stack

* **Frontend:** HTML5, CSS3, JavaScript (ES6+)
* **Styling:** Tailwind CSS (via CDN)
* **Markdown:** Marked.js
* **Sanitization:** DOMPurify (XSS অ্যাটাক থেকে রক্ষার জন্য)
* **Icons:** Google Material Symbols
* **API Provider:** OpenRouter API

---

## 🏁 Getting Started

এই প্রজেক্টটি চালানো খুবই সহজ। কোনো নোড জেএস (Node.js) বা সার্ভার লাগবে না।

1.  আপনার কম্পিউটারে `index.html` নামে একটি ফাইল তৈরি করুন।
2.  কোডটি কপি করে ওই ফাইলে পেস্ট করুন।
3.  ফাইলটি যেকোনো ব্রাউজারে ওপেন করুন।

---

## ⚙️ Configuration & API Setup

এআই-এর সাথে কথা বলতে হলে আপনার একটি API Key লাগবে:
1.  [OpenRouter.ai](https://openrouter.ai/) এ যান এবং একটি অ্যাকাউন্ট তৈরি করে এপিআই কি জেনারেট করুন।
2.  আপনার অ্যাপের **Settings** আইকনে ক্লিক করুন।
3.  API Key টি পেস্ট করুন এবং আপনার পছন্দের মডেল (যেমন: `google/gemini-pro`) সিলেক্ট করুন।
4.  **Save Config** বাটনে ক্লিক করুন।

---

## 🔒 Security & Privacy

* আপনার API Key এবং চ্যাট ডেটা শুধু আপনার ডিভাইসেই সেভ থাকে।
* এপিআই কি লোকাল স্টোরেজে **Base64** এনকোডেড অবস্থায় থাকে।
* প্রজেক্টটি সম্পূর্ণ ক্লায়েন্ট-সাইড, তাই মাঝখানে কোনো থার্ড-পার্টি সার্ভার আপনার ডেটা দেখতে পারে না।

---

## 👨‍💻 Author

**Md eyasin** * 🎓 Student (Class 10)
* 📍 Gazipur, Bangladesh
* 🔧 Interests: Engineering, Inventions, Software Development

---

*Keep Exploring, Keep Creating!*
