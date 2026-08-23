# BharatMitra — Android App Setup Guide (v4: Hardware Back Button + Global Design Upgrade)

## Is Baar Kya Fix Hua (changelog)

- ✅ **Mobile ka hardware back button** — ab app ke andar screen-by-screen back jaata hai (jaise koi bhi real app), home pe do baar back dabane se hi app close hoga
- ✅ **Har form/input field** (Business Manager, Queue, sab jagah) — premium shadow + focus glow, ab website ke plain box jaisa nahi lagta
- ✅ **Har subpage ka header + back button** — sabhi 10+ pages (Documents, Scam Guardian, Family Safety, Business Manager, etc.) me ek jaisa premium bada header
- ✅ **Tab pills aur stat cards** — poori app me consistent shadow-based premium look
- ✅ Profile page redesign, header fix, icon navy background (pichli baari)

**Translation note:** Abhi login + home screen ke labels translate hote hain. Baaki deeper pages (Business Manager, Family Safety, Scam Guardian, etc.) abhi Hinglish me fixed hain — inko sabko translate karna ek bada alag kaam hai (sainkdo strings, 7 languages), agli baari step-by-step karenge agar chaho.

---

## Ye Files Kya Hain

- `www/index.html` — tumhari website (background location + push + Google Sign-In fix + link fix ab isme hai)
- `www/terms.html` — naya, pehle missing tha
- `package.json` — saare Capacitor plugins ki list
- `capacitor.config.json` — app ka naam, ID, settings
- `codemagic.yaml` — CodeMagic build steps
- `debug.keystore` — fixed debug signing key (Google Sign-In ke liye zaroori, neeche explain hai)
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

## STEP 3 — Google Sign-In Poori Tarah Chalu Karo

✅ **Update:** Iss zip me ab tumhara **asli `google-services.json`** already hai — sahi Firebase project (`bharatmitra-ai`) se, jisme tumhara real data hai. Ab bas ye confirm kar lo:

1. Firebase Console → **Authentication → Sign-in method** tab me jaake **Google** provider **Enabled** hai check karo (agar nahi hai, Enable kar do)
2. Fixed debug keystore (`debug.keystore`) already zip me hai, jiska SHA-1 (`DC:3A:6C:2E:...`) tumne Firebase me register kar diya hai

Bas — CodeMagic build khud sab sahi jagah copy kar dega.

## STEP 4 — App Icon aur Splash Screen (Design)

`assets/` folder me tumhare **asli logo** se bane assets hain:
- `assets/icon.png` — app icon (navy background, logo bada)
- `assets/icon-foreground.png` + `assets/icon-background.png` — Android adaptive icon layers
- `assets/splash.png` / `assets/splash-dark.png` — splash screen (white card pe pura logo)

Agar future me logo change karna ho, isi `assets/icon.png` (1024×1024) aur `assets/splash.png` (2732×2732) ko replace kar dena — CodeMagic build ke time `npx @capacitor/assets generate` khud saare Android sizes generate kar dega.

**Onboarding + Boot Splash app me built-in hai:**
- App khulte hi ek animated boot splash dikhega (gold ring + logo)
- Pehli baar app open karne par 4-slide onboarding aayega — Skip bhi kar sakte hain

---

## STEP 5 — GitHub Pe Repo Banao Aur Upload Karo

1. GitHub.com pe **New Repository** → naam `bharatmitra-android` → **Public** rakho
2. Is folder ki saari files upload karo — structure bilkul aisa hona chahiye:
```
package.json
capacitor.config.json
codemagic.yaml
debug.keystore
google-services.json   ← tumne Step 3 me add ki
SETUP-GUIDE.md
www/
  index.html
  logo.png
  terms.html
  privacy-policy.html
assets/
  icon.png, icon-background.png, icon-foreground.png, splash.png, splash-dark.png
```

## STEP 6 — CodeMagic Se Build Karo

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

- [x] Background location — done
- [x] Push notification client setup — done
- [x] Broken links (Terms, Privacy, WhatsApp share, Maps) — fixed
- [x] Google Sign-In native fix — code done, tumhe Step 3 complete karni hai
- [x] App icon navy background — done
- [ ] Poori app ka page-by-page redesign (Profile, Business Manager, Queue) — abhi baaki hai, agli baari continue karenge
- [ ] Har page/feature ka poora language translation (abhi sirf login + home ke labels translated hain, baaki screens Hinglish me hardcoded hain)
- [ ] Automated push sending (event pe khud-ba-khud notification) — secure relay ke saath
- [ ] Release build (AAB) config karna Play Store ke liye
