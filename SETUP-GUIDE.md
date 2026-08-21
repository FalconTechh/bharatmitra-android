# BharatMitra — Android App Setup Guide (v2: Background Location + Push Notifications)

## Ye Files Kya Hain

- `www/index.html` — tumhari website (background location + OneSignal push ab isme integrate ho chuka hai)
- `package.json` — Capacitor + Background Location + OneSignal plugin list
- `capacitor.config.json` — app ka naam, ID, settings
- `codemagic.yaml` — CodeMagic ko batata hai app kaise build karna hai
- `.gitignore` — build files ko GitHub pe upload hone se rokta hai

**Note:** `android/` folder abhi nahi hai — CodeMagic pehli build ke time khud generate karega.

---

## STEP 1 — OneSignal Account Banao (Push Notification Ke Liye — 100% Free)

1. **onesignal.com** pe jao → Sign up (free, koi card nahi chahiye)
2. **New App/Website** banao → naam do `BharatMitra`
3. Platform me **Google Android (FCM)** select karo
4. Ye tumhare **existing Firebase project** (`bharatmitra-ai`) se link karna hai:
   - Firebase Console → apna project (`bharatmitra-ai`) → ⚙️ Project Settings → **Cloud Messaging** tab
   - "Service accounts" section me **"Generate new private key"** dabao → ek `.json` file download hogi
   - Wahi `.json` file OneSignal ke setup screen me upload kar do
   - *(Ye step Blaze plan nahi maangta — Cloud Messaging hamesha free hai)*
5. Setup complete hone ke baad, OneSignal dashboard me **Settings → Keys & IDs** me jao
6. Wahan se **OneSignal App ID** copy karo

## STEP 2 — App ID Website Me Daalo

`www/index.html` file me ye line dhoondo (Ctrl+F se search karo):

```js
const ONESIGNAL_APP_ID = "PASTE_YOUR_ONESIGNAL_APP_ID_HERE";
```

Yahan `PASTE_YOUR_ONESIGNAL_APP_ID_HERE` ki jagah apna copied App ID paste karo.

## STEP 3 — App Icon aur Splash Screen (Design)

`assets/` folder me maine placeholder brand assets bana di hain (navy+gold "BM" mark, tumhare app ke colors ke sath match karta hai):
- `assets/icon.png` — app icon
- `assets/icon-foreground.png` + `assets/icon-background.png` — Android adaptive icon layers
- `assets/splash.png` / `assets/splash-dark.png` — splash screen

**Agar tumhara asli logo already designed hai** (jo `www/logo.png` me use ho raha hai), to bas usi logo ko `assets/icon.png` (1024×1024) aur `assets/splash.png` (2732×2732, center me logo) ki jagah rakh do — CodeMagic build ke time `npx @capacitor/assets generate` khud saare Android sizes (mdpi se xxxhdpi tak) generate kar dega. Kuch replace na karo to mera placeholder use ho jayega.

**Onboarding + Splash ab app me built-in hai:**
- App khulte hi ek animated boot splash dikhega (gold ring + logo)
- Pehli baar app open karne par 4-slide onboarding aayega (Family Safety, Scam Guardian, Business/Docs) — Skip bhi kar sakte hain
- Dusri baar se seedha login/home pe jayega (localStorage me flag save hota hai)

---

## STEP 4 — GitHub Pe Repo Banao Aur Upload Karo

1. GitHub.com pe **New Repository** → naam `bharatmitra-android` → **Public** rakho
2. Is folder ki saari files (`www/` folder samet) us repo me upload karo

## STEP 5 — CodeMagic Se Build Karo

1. **codemagic.io** pe GitHub se login karo (free plan — 500 min/month)
2. **Add application** → `bharatmitra-android` repo select karo
3. **"Start new build"** → workflow `android-build` choose karo → 10-15 min me APK ready
4. **Artifacts** section se `.apk` download karke phone pe install karo

---

## ⚠️ Zaroori Baatein

1. **Permissions:** App install ke baad Android khud puchega:
   - Location → **"Allow all the time"** manually choose karna padega (background tracking ke liye zaroori, Android ka rule hai)
   - Notifications → **"Allow"** dabao (push ke liye zaroori, Android 13+)

2. **Live Location Sharing** ab background me bhi chalti rahegi — jab tak user Family Safety Hub → My Location toggle **ON** rakhta hai, phone screen band hone par bhi location update hoti rahegi (chhoti si permanent notification dikhegi, ye Android ka rule hai, isse app close nahi hoti).

3. **Push Notifications:** Abhi jo notifications Firestore me save hote hain (jaise "Aapki baari aa gayi"), wo sirf app khule hone par dikhte hain. Real push (app band hone par bhi) bhejne ke liye OneSignal ka REST API call karna padega — **iske liye alag se ek chhota secure step chahiye** (API key ko public GitHub repo me daalna surakshit nahi hai). Agla step: ek free Cloudflare Worker banake use karna jo key ko chhupaye rakhe.

4. **Play Store Pe Daalne Ke Liye:**
   - Google Play Console pe $25 one-time fee (Google ka rule)
   - APK ki jagah AAB chahiye — `codemagic.yaml` me `bundleRelease` use karna hoga
   - App signing key generate karni hogi (CodeMagic kar sakta hai)

5. **Website Update Karni Ho To:**
   - `www/index.html` replace karo GitHub pe → CodeMagic pe "Start new build" → naya APK ban jayega

---

## Agla Kaam (Baad Me)

- [x] Background location — done (native plugin integrate ho chuka)
- [x] Push notification client setup — done (OneSignal register/permission ho raha hai)
- [ ] OneSignal App ID daalna (Step 2 upar)
- [ ] Automated push sending (event pe khud-ba-khud notification bhejna) — secure relay ke saath
- [ ] App icon aur splash screen add karna
- [ ] Release build (AAB) config karna Play Store ke liye
