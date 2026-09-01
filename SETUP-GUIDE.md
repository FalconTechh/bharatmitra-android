# BharatMitra — Android App Setup Guide (v6: New Branding + Play Store Ready)

## Is Baar Kya Fix Hua (changelog)

- ✅ **Naya App Icon aur Splash Screen** — tumhare bheje gaye premium gold/navy design se banaya, India Gate + animated loading bar ke saath
- ✅ **Search bar** — bada, bold, premium look
- ✅ **Profile photo ka blue border** — asli reason mila (avatar ke around blue border tha), gold kar diya
- ✅ **Privacy Policy** — Play Store ke Data Safety rules ke hisaab se update kiya (permissions, data sharing, ads, children's privacy sab declare kiya)
- ✅ **Play Store Release Build** — ek naya secure signing setup banaya taaki AAB file Play Store pe upload ki ja sake (neeche Step 4 me detail hai — **zaroori steps hain, dhyan se padhna**)

---

## Ye Files Kya Hain

- `www/index.html` — tumhari website (background location + push + Google Sign-In fix + link fix ab isme hai)
- `www/terms.html` — naya, pehle missing tha
- `package.json` — saare Capacitor plugins ki list
- `capacitor.config.json` — app ka naam, ID, settings
- `codemagic.yaml` — CodeMagic build steps (ab 2 workflows: testing ke liye aur Play Store ke liye)
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

## STEP 3 — Google Sign-In (Filhaal Rolled Back)

⚠️ Native Google Sign-In app crash kar raha tha, isliye abhi ke liye **hata diya gaya hai** (upar changelog dekho). Email/Password login pura kaam karta hai. `google-services.json` file zip me rehne di hai (useless nahi, future me phir se try karne ke kaam aayegi) lekin abhi build isse actively use nahi karta.

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

## STEP 4b — Search Engine Chalu Karo (Free — 100 search/din, uske baad auto browser fallback)

1. [programmablesearchengine.google.com](https://programmablesearchengine.google.com) pe jao, apne Google account se login karo
2. **"Add"** / **"Create a search engine"** dabao
3. "What to search" me select karo **"Search the entire web"**
4. Naam do (jaise "BharatMitra Search") → **Create**
5. Banne ke baad **"Search engine ID"** (cx) copy karo — Control Panel me milega
6. Ab [console.cloud.google.com](https://console.cloud.google.com) pe jao → apna project (`bharatmitra-ai`) select karo
7. **APIs & Services → Library** → search karo **"Custom Search API"** → **Enable** karo
8. **APIs & Services → Credentials → Create Credentials → API Key** → naya key generate hoga, copy karo

`www/index.html` me ye 2 lines dhoondo aur update karo:
```js
const GOOGLE_CSE_API_KEY = "PASTE_YOUR_GOOGLE_CSE_API_KEY_HERE";
const GOOGLE_CSE_ENGINE_ID = "PASTE_YOUR_SEARCH_ENGINE_ID_HERE";
```

Jab tak ye nahi karte, search bar tab bhi kaam karega — bas results app ke andar ki jagah seedha browser me Google search khulega (fully free, unlimited).

## STEP 4c — Mandi Bhav Ke Liye Free API Key

`www/index.html` me ye line dhoondo:
```js
const DATA_GOV_API_KEY = "PASTE_YOUR_DATA_GOV_API_KEY_HERE";
```
data.gov.in se free API key lene ka process pehle bataya gaya hai (Register → My Account → API Key).

## STEP 4d — Community Board Ke Liye Firestore Rules Update Karo

Firebase Console → `bharatmitra-ai` → Firestore Database → Rules — jo rules pehle diye the, unme neeche wala block bhi **add** karo (kisi purane block ko hataye bina):

```
match /communityPosts/{postId} {
  allow read: if request.auth != null;
  allow create: if request.auth != null && request.resource.data.userId == request.auth.uid;
  allow delete: if request.auth != null && resource.data.userId == request.auth.uid;
  allow update: if request.auth != null && resource.data.userId == request.auth.uid;
}
```
Iske bina Community Board pe post karna fail hoga ("Missing or insufficient permissions" error).

---



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

## STEP 4 — Play Store Ke Liye Release Build (Zaroori Steps)

### 4a. GitHub Pages Chalu Karo (Privacy Policy + Google Sign-In dono ke liye zaroori)

1. GitHub repo (`bharatmitra-android`) → **Settings** tab → left sidebar me **Pages**
2. "Build and deployment" me Source: **Deploy from a branch**
3. Branch: `main`, folder: **/ (root)** → **Save**
4. 1-2 minute wait karo, phir ye links live ho jayenge:
   - Privacy Policy: `https://falcontechh.github.io/bharatmitra-android/www/privacy-policy.html`
   - Terms: `https://falcontechh.github.io/bharatmitra-android/www/terms.html`
   - Google Sign-In bridge: `https://falcontechh.github.io/bharatmitra-android/www/oauth-redirect.html`

   *(Agar tumhara GitHub username ya repo naam alag hai, to `www/index.html` me `PUBLIC_APP_URL` line dhoondo aur sahi URL daalo)*

5. Privacy Policy wala live link Play Console ke "Privacy Policy URL" field me daalna hoga (Step 4f me)

### 4b. Google Cloud Console me redirect URI add karo (Google Sign-In ke liye zaroori)

Google ka rule hai ki "Web" type client custom app-scheme (`com.bharatmitra.ai://`) seedha allow nahi karta — isliye pehle ek HTTPS page (`oauth-redirect.html`, jo upar bana diya) pe redirect hota hai, wo hi app ko wapas kholta hai. Iske liye Google Cloud Console me registration karni hogi:

1. [console.cloud.google.com](https://console.cloud.google.com) pe jao, upar **project select karo: `bharatmitra-ai`**
2. **APIs & Services → Credentials**
3. "OAuth 2.0 Client IDs" list me **Web client (auto created by Google Service)** wale par click karo
4. **Authorized redirect URIs** me **Add URI** dabao, aur ye daalo:
   ```
   https://falcontechh.github.io/bharatmitra-android/www/oauth-redirect.html
   ```
5. **Save** dabao

### 4c. Release Signing Key (bahut zaroori — sambhal ke rakhna)

Maine tumhare liye ek **release.keystore** generate ki hai — ye tumhari **permanent app identity** hai. Agar ye kho gayi, to app ka **future update kabhi upload nahi kar paoge** (Play Store naya app samjhega). Isliye:

1. Maine ye file tumhe **is chat me alag se** bheji hai (`bharatmitra-release.keystore`) — ise **kahi surakshit jagah save karo** (Google Drive, email apne aap ko) — **GitHub pe kabhi mat daalna**, wo public hai
2. Keystore password: `BharatMitra@2026`, alias: `bharatmitra`, alias password: same — inko bhi kahi likh ke rakho

### 4d. CodeMagic me signing secrets add karo

1. CodeMagic → apna app → **Environment variables** (settings me)
2. Ek naya **group** banao naam: `release_signing`
3. Isme ye 4 variables add karo (har ek ko **"Secure"** checkbox tick karke):
   - `CM_KEYSTORE` — iski value ke liye maine ek `bharatmitra-release-keystore-base64.txt` file bheji hai, uska pura content copy-paste karo
   - `CM_KEYSTORE_PASSWORD` — `BharatMitra@2026`
   - `CM_KEY_ALIAS` — `bharatmitra`
   - `CM_KEY_PASSWORD` — `BharatMitra@2026`

### 4e. Release build chalao

1. CodeMagic pe **"Start new build"** → is baar workflow me `android-release` choose karo (`android-build` nahi)
2. Build complete hone par ek `.aab` file milegi — yahi file Play Console pe upload hogi (APK nahi, AAB chahiye)

### 4f. Play Console pe submit karo

1. [play.google.com/console](https://play.google.com/console) pe jao, $25 one-time fee de ke developer account banao (agar nahi hai)
2. New App banao → package name `com.bharatmitra.ai`
3. Store listing me app icon, screenshots, description bharo
4. **Privacy Policy URL** me Step 4a wala live link daalo
5. **Data Safety** section me batao: Location (optional, family sharing ke liye), Photos (documents/QR scan), Personal Info (naam/email) collect hota hai
6. Production/Internal Testing track me `.aab` file upload karo

---



- [x] Background location — done
- [x] Push notification client setup — done
- [x] Broken links (Terms, Privacy, WhatsApp share, Maps) — fixed
- [x] Google Sign-In native fix — code done, tumhe Step 3 complete karni hai
- [x] App icon navy background — done
- [ ] Poori app ka page-by-page redesign (Profile, Business Manager, Queue) — abhi baaki hai, agli baari continue karenge
- [ ] Har page/feature ka poora language translation (abhi sirf login + home ke labels translated hain, baaki screens Hinglish me hardcoded hain)
- [ ] Automated push sending (event pe khud-ba-khud notification) — secure relay ke saath
- [ ] Release build (AAB) config karna Play Store ke liye
