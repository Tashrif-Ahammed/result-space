# ResultSpace — Android App (Capacitor project, ready to build)

## সবচেয়ে সহজ উপায়: GitHub Actions দিয়ে অনলাইনে APK বানানো (Android Studio লাগবে না)

এই ফোল্ডারে `.github/workflows/build-apk.yml` নামে একটা ফাইল আছে, যেটা GitHub-এর
ফ্রি cloud build service ব্যবহার করে অটোমেটিক APK বানিয়ে দেয়। ধাপগুলো:

1. https://github.com -এ ফ্রি অ্যাকাউন্ট বানাও (না থাকলে)।
2. GitHub-এ একটা নতুন **repository** বানাও (public বা private — দুটোতেই ফ্রি
   Actions minutes পাওয়া যায়)। নাম যা খুশি দাও, যেমন `resultspace-app`।
3. এই পুরো ফোল্ডারের কনটেন্ট সেই repo-তে push/upload করো। GitHub website
   থেকেও করা যায় (কোনো git কমান্ড না জানলেও):
   - repo পেজে "Add file" → "Upload files" ক্লিক করো
   - এই ফোল্ডারের সব ফাইল/ফোল্ডার (www, android, .github, package.json,
     capacitor.config.json) ড্র্যাগ করে দাও
   - "Commit changes" ক্লিক করো
4. Commit হওয়ার সাথে সাথেই build শুরু হয়ে যাবে — repo-র উপরে **"Actions"**
   ট্যাবে গিয়ে দেখতে পাবে (২-৫ মিনিট লাগে সাধারণত)।
5. Build সবুজ টিক (✓) দেখালে, ওই run-এর পেজে নিচে "Artifacts" সেকশনে
   **resultspace-debug-apk** নামে একটা zip পাবে — ডাউনলোড করলে ভেতরে
   `app-debug.apk` থাকবে। এটাই তোমার ফোনে সরাসরি install করা যাবে।

HTML পরে পরিবর্তন করলে শুধু আবার `www/index.html` আপডেট করে GitHub-এ push/upload
করলেই নতুন build অটোমেটিক চলবে, নতুন APK পাবে।

> নোট: এটা "debug" APK — ব্যক্তিগত ব্যবহার/টেস্টিং-এর জন্য সম্পূর্ণ ঠিক আছে,
> সরাসরি install করে চালানো যায়। Play Store-এ পাবলিশ করতে চাইলে "signed release"
> APK/AAB লাগবে, যেটার জন্য নিচের Android Studio পদ্ধতি বা Actions-এ signing
> secrets যোগ করতে হবে (চাইলে সেটাও পরে সেটআপ করে দিতে পারি)।

---

## বিকল্প উপায়: নিজের কম্পিউটারে Android Studio দিয়ে

এই folder-টা একটা সম্পূর্ণ, রেডি Capacitor project। তোমার fix করা `www/index.html`
এবং পুরো `android/` native project ইতিমধ্যে জেনারেট করা আছে। শুধু নিচের ২টা ধাপ
নিজের কম্পিউটারে (Android Studio ইনস্টল করা মেশিনে) করলেই APK পেয়ে যাবে —
কারণ actual APK build করতে Android SDK লাগে, যা sandbox-এ নেই।

## যা লাগবে (একবারই ইনস্টল করতে হবে, সব ফ্রি)
- Node.js — https://nodejs.org (LTS)
- Android Studio — https://developer.android.com/studio

## ধাপ

1. এই পুরো ফোল্ডারটা কম্পিউটারে extract/copy করো।
2. টার্মিনাল/CMD দিয়ে এই ফোল্ডারে ঢুকে dependency ইনস্টল করো:
   ```
   npm install
   ```
3. Android platform sync করো (assets আপ-টু-ডেট রাখার জন্য, প্রথমবার optional
   কিন্তু চালিয়ে নেওয়া ভালো):
   ```
   npx cap copy android
   ```
4. Android Studio-তে প্রজেক্ট খুলে ফেলো:
   ```
   npx cap open android
   ```
   (এটা `android/` ফোল্ডারটাকে Android Studio-তে খুলে দেবে। প্রথমবার Gradle sync
   হতে কয়েক মিনিট লাগতে পারে — এই সময় Android Studio নিজে থেকেই দরকারি সব
   ডাউনলোড করে নেবে।)
5. Android Studio-তে উপরে থেকে:
   **Build → Build Bundle(s) / APK(s) → Build APK(s)**
   ক্লিক করো।
6. Build শেষ হলে নিচে ডান কোণায় "locate" লিংক আসবে, ওখানে ক্লিক করলে
   `android/app/build/outputs/apk/debug/app-debug.apk` ফাইলটা পাবে।
   এই `.apk` ফাইলটাই তোমার ফোনে সরাসরি ইনস্টল করা যাবে — কোনো address bar,
   ট্যাব, বা browser UI ছাড়া, একদম native app-এর মতো চলবে।

## HTML আপডেট করলে

`www/index.html` ফাইলটা পরে পরিবর্তন করলে শুধু আবার চালাও:
```
npx cap copy android
```
তারপর আবার Build APK(s) করলেই নতুন ভার্সন পেয়ে যাবে।

## Play Store-এ পাবলিশ করতে চাইলে

- Build → Generate Signed Bundle/APK দিয়ে একটা keystore বানিয়ে **signed AAB**
  বানাতে হবে (এটা একবারই করার জিনিস, keystore ফাইলটা সাবধানে রেখে দিও —
  হারালে আর update দিতে পারবে না)।
- Google Play Console-এ one-time $25 developer registration fee লাগবে।
- সরাসরি ফোনে sideload করে ব্যবহার করতে চাইলে এসবের কিছুই লাগবে না — উপরের
  debug APK-ই যথেষ্ট।

## App ID / Name পরিবর্তন

`capacitor.config.json`-এ `appId` ("com.tashrif.resultspace") ও `appName`
("ResultSpace") চাইলে বদলে নিতে পারো — বদলানোর পর আবার
`npx cap copy android` চালিয়ে নিও।

## আইকন/স্প্ল্যাশ স্ক্রিন কাস্টমাইজ (ঐচ্ছিক)

```
npm install @capacitor/assets --save-dev
```
তারপর `resources/icon.png` (1024x1024) ও `resources/splash.png` রেখে:
```
npx capacitor-assets generate
```
