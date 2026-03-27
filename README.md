# 🔐 Enigma Simulator

Modern web-based simulator of the Enigma cipher machine built with Nuxt + Vue + Tailwind.

---

## ✨ Features

- Realistic **3-rotor Enigma logic** (I, II, III)
- **Reflectors B / C**
- Configurable:
    - Rotor order (no duplicates)
    - Ring settings (Ringstellung)
    - Start positions (Grundstellung)
- Real-time **rotor stepping (double-step included)**
- **Randomize** button
- Clean modern UI with rotor drums

---

## 🧠 How it works

Each character goes through:

1. Right → Left rotors (forward)
2. Reflector
3. Left → Right rotors (backward)

After each key press:
- Right rotor always steps
- Middle rotor steps on notch (double-step behavior)
- Left rotor steps conditionally

---

## ⚙️ Tech Stack

- **Nuxt 3**
- **Vue 3 (Composition API)**
- **TailwindCSS**

---

## 🚀 Run locally

```bash
npm install
npm run dev