# project-documentation.md — ResultSpace

## ১. উদ্দেশ্য ও স্কোপ

**প্রজেক্ট:** ResultSpace — একটি Capacitor 7 ভিত্তিক Android অ্যাপ যা শিক্ষার্থীদের
ফলাফল (result) দেখা ও ট্র্যাক করা, Google Sign-In এবং ফাইল শেয়ার করার সুবিধা দেয়।

**এই আপডেটের উদ্দেশ্য:** workspace-এ থাকা `result-space-main.zip` আর্কাইভের **সব ফাইল**
GitHub রিপো `Tashrif-Ahammed/result-space`-এ `main` ব্রাঞ্চে commit ও push করা।

**স্কোপের বাইরে:** বিল্ড/সিগনিং কনফিগারেশন পরিবর্তন, Firebase কনসোল সেটআপ,
Play Store রিলিজ — এগুলো এই কাজে করা হয়নি।

## ২. ফাইল স্ট্রাকচার

```text
result-space/
  .github/workflows/       build-apk.yml (APK বিল্ড), deploy-pages.yml (লাইভ সাইট ডিপ্লয়)
  www/                     অ্যাপের ওয়েব কন্টেন্ট — index.html, sw.js, offline.html
  android/                 Capacitor করে জেনারেট করা native Android প্রজেক্ট
  resources/               আইকন ও স্প্ল্যাশ সোর্স ইমেজ (1024x1024)
  data/                    ডেটা উৎস ও ডেটা-ডেরাইভড ফাইল
  package.json             npm ডিপেন্ডেন্সি ও স্ক্রিপ্ট
  capacitor.config.json    Capacitor কনফিগ (appId, server, plugin সেটিংস)
  project-documentation.md এই ফাইল
```

## ৩. প্রযুক্তি ও নির্ভরতা

| বিষয় | মান |
| --- | --- |
| App ID | `com.tashrif.resultspace` |
| App Name | `ResultSpace` |
| Framework | Capacitor 7 (`@capacitor/core`, `/android`, `/cli`) |
| Plugins | `@capacitor/filesystem`, `@capacitor/share`, `@capacitor/splash-screen`, `@ironcode/capacitor-google-auth` |
| Auth | Google Sign-In (`scopes: profile, email`) |
| Build | Gradle wrapper, JDK 21, Node.js 20 (CI) |
| APK pipeline | GitHub Actions → `resultspace-debug-apk` artifact |
| Web hosting | GitHub Pages → `www/` ফোল্ডার |

## ৪. ডেটা উৎস

- সোর্স: `C:\Users\lenovo\OneDrive\Documents\Default Project\result-space-main.zip`
- আর্কাইভে 98টি ফাইল, root ফোল্ডার `result-space-main/`।
- প্রতিটি ফাইলের path/size/SHA-256 তালিকা: [`data/file-inventory.tsv`](data/file-inventory.tsv)
- বিস্তারিত: [`data/README.md`](data/README.md)

## ৫. এই আপডেটে যা বদলেছে

রিমোটে আগে থেকেই প্রজেক্টের একটি সংস্করণ ছিল (HEAD = `4dd34ed`)। রিপোজিটরি
history না হারিয়ে zip-এর ফাইলগুলো ওই base-এর উপর overlay করা হয়েছে, তাই
পরিবর্তনগুলো শুধুমাত্র প্রকৃত পার্থক্য হিসেবে commit হয়েছে।

**কন্টেন্ট পরিবর্তন (4টি ফাইল):**

| ফাইল | পরিবর্তন |
| --- | --- |
| `www/index.html` | +853 / −… — অটো-আপডেট সার্ভিস ওয়ার্কার রেজিস্ট্রেশন, লাইভ-সাইট লোডিং, UI পরিবর্তন |
| `android/app/src/main/assets/public/index.html` | `www/index.html`-এর Capacitor কপি — দুটো hash-এ সম্পূর্ণ সমান (sync যাচাই করা) |
| `README.md` | +১৭ লাইন — "অটো-আপডেট" সেকশন ও একবারের সেটআপ নির্দেশনা |
| `.github/workflows/build-apk.yml` | +১২ লাইন — `www/**` ও `README.md` push-এ APK বিল্ড এড়িয়ে যাওয়া, এবং `capacitor.config.json`-এ Pages URL সেট করা |

**নতুন ফাইল (3টি):**

| ফাইল | কাজ |
| --- | --- |
| `www/sw.js` | Network-first service worker — অনলাইনে নতুন ফাইল, অফলাইনে ক্যাশ |
| `www/offline.html` | Capacitor `server.errorPath` — লোড ব্যর্থ হলে দেখানো পেজ |
| `.github/workflows/deploy-pages.yml` | `www/**` push হলে GitHub Pages-এ ডিপ্লয় (Pages → Source: GitHub Actions দরকার) |

## ৬. ডিজাইন ডিসিশন ও অনুমান

1. **Clone + overlay, fresh init নয়** — রিমোটে ইতিমধ্যে কমিট ছিল। `git init` করলে
   push করতে হলে force-push লাগত, যা history মুছে ফেলত। তাই remote clone করে
   zip-এর ফাইল ওপরে বসানো হয়েছে; ফলে `4dd34ed`-এর ইতিহাস অক্ষত আছে।
2. **Line ending** — `core.autocrlf=true` এবং কোনো `.gitattributes` নেই। ফাইলগুলো
   index-এ LF থাকে, working copy-তে LF, তাই এই আপডেটে কোনো CRLF/LF churn হয়নি
   (zip-এর ফাইলগুলো remote-এর হুকেই হুবহু মিলেছে)।
3. **`build-apk.yml`-এ `paths-ignore`** — `www/**` push-এ আর APK বিল্ড হবে না,
   কারণ APK এখন GitHub Pages থেকে লোড করে; HTML বদলালে শুধু Pages ডিপ্লয় লাগে।
4. **`google-services.json`** রিপোতে আগে থেকেই ট্র্যাক করা, তাই এই আপডেটে
   কোনো নতুন secret যোগ হয়নি। কোনো ফাইলে API key/PAT/token লেখা হয়নি (যাচাই করা)।

## ৭. ব্যবহৃত কমান্ড

```powershell
# 1. remote clone (base = 4dd34ed)
git clone https://github.com/Tashrif-Ahammed/result-space result-space

# 2. zip extract ও overlay
Expand-Archive -LiteralPath ..\result-space-main.zip -DestinationPath <temp> -Force
robocopy <temp>\result-space-main <project> /E

# 3. commit ও push
git add -A
git commit -m "<see below>"
git push origin main
```

## ৮. ভেরিফিকেশন

| পরীক্ষা | ফলাফল |
| --- | --- |
| `www/index.html` ↔ `android/app/src/main/assets/public/index.html` | SHA-256 সমান — Capacitor sync যাচাই সফল |
| zip-এর ৯৮টি ফাইল রিপোতে আছে | `git ls-files` দিয়ে যাচাই (ফলাফল নিচে) |
| uncommitted/untracked ফাইল অবশিষ্ট | `git status --porcelain` খালি |
| `deploy-pages.yml` encoding | বৈধ UTF-8 (em-dash = `E2 80 94`) |
| push | `git ls-remote origin` দিয়ে remote HEAD যাচাই |

## ৯. পরবর্তী ধাপ (অসম্পন্ন)

- [ ] GitHub repo → **Settings → Pages → Source: GitHub Actions** সেলেক্ট করতে হবে,
      নইলে `deploy-pages.yml` ফেইল করবে।
- [ ] Firebase Console → Authentication → **Authorized domains**-এ
      `<username>.github.io` যোগ করতে হবে, নইলে Google Sign-In ব্যর্থ হবে।
- [ ] Actions থেকে **Build APK** একবার চালিয়ে নতুন (Pages-লিংকড) APK ইনস্টল করতে হবে।
- [ ] Play Store রিলিজের জন্য signed AAB + keystore এখনো বানানো হয়নি।

## ১০. জানা সমস্যা

- `www/index.html` একটি বড় এক-ফাইল অ্যাপ (~৩ MB); বিল্ড টাইমে lint/format করা হয় না।
- `.gitattributes` নেই, তাই Windows ও Linux ডেভেলপারদের মধ্যে line-ending
  সমস্যা হতে পারে।
- `build-apk.yml` এখন APK-কে GitHub Pages URL-এর দিকে পয়েন্ট করে — অফলাইনে
  শুধু `offline.html` + ক্যাশ চলবে, তাই Pages সেটআপ না হওয়া পর্যন্ত APK কার্যকর নয়।
