# বাংলা অনুবাদ · [English](README.md)

> **টেমপ্লেট পরিবারের অংশ** — এটি নিচের দুটির সাথে একসাথে ব্যবহার করুন:
> [`consumer-hardware-products-template`](https://github.com/anurag-panda-dev/consumer-hardware-products-template) (শুধু হার্ডওয়্যার) এবং
> [`full-stack-consumer-hardware-products-template`](https://github.com/anurag-panda-dev/full-stack-consumer-hardware-products-template) (হার্ডওয়্যার + সফটওয়্যার)।

<h1 align="center">বিগ সফটওয়্যার প্রোডাক্ট টেমপ্লেট</h1>
<p align="center"><i>Big Software Product Template</i></p>

<p align="center"><b>প্রতিটি ফোল্ডার নিজেকে ব্যাখ্যা করে — মানুষের কাছেও, AI এজেন্টের কাছেও।</b><br>
২৬৮টি <code>purpose.txt</code> ম্যাপ · lint দিয়ে প্রযোজ্য ইমপোর্ট সীমানা · ADR/RFC সিদ্ধান্ত শৃঙ্খলা</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue?style=flat-square" alt="MIT License" /></a>
  <a href="https://github.com/anurag-panda-dev/big-software-product-template/generate"><img src="https://img.shields.io/badge/Use%20this%20template-grey?style=flat-square&logo=github&logoColor=white" alt="Use this template" /></a>
  <a href="https://github.com/anurag-panda-dev/big-software-product-template"><img src="https://img.shields.io/github/repo-size/anurag-panda-dev/big-software-product-template?style=flat-square" alt="Repo size" /></a>
</p>

<p align="center">
  <img src="docs/assets/repo-hero.png" alt="GitHub-এ big-software-product-template রিপোজিটরির বিন্যাস" width="100%">
</p>

```text
apps/            ডিপ্লয়েবল — রুট, প্রোভাইডার, ওয়্যারিং
categories/      ডোমেইন ভার্টিক্যাল ── ফিচার স্লাইস {ui, logic, data, api}
crosscutting/    শেয়ারড-লেয়ার প্রোডাক্ট
kernel/          ডিজাইন-সিস্টেম · কন্ট্রাক্ট · অথ · অফলাইন · i18n · এনটাইটেলমেন্ট
platform/        api · পেমেন্ট · নোটিফিকেশন · জবস ── উপর থেকে কখনো ইমপোর্ট হয় না
datasets/ content/ business/ legal/ docs/ rfcs/ labs/ infra/ sandbox/
```

**বড় আকারের বহু-প্রোডাক্ট সফটওয়্যার সিস্টেম** তৈরির জন্য একটি ইকোসিস্টেম-প্রথম মনোরিপো টেমপ্লেট — যেখানে সাজানো হয় *ডোমেইন* অনুযায়ী (প্রোডাক্টটা আসলে কী), প্রায়োগিক ধরন অনুযায়ী নয় (apps/packages-এর স্যুপ)।

স্ট্রাকচার ও পেছনের যুক্তি: [`docs/decisions/EXAMPLE-ADR-0001-Bongo-Tathya.md`](docs/decisions/EXAMPLE-ADR-0001-Bongo-Tathya.md) দেখুন — এই লেআউটে তৈরি একটি প্রকৃত প্রজেক্টের লেখা ADR।

## অভিজ্ঞতালব্ধ নিয়ম

| যদি এটি হয়… | এটি যাবে… |
|---|---|
| ডিপ্লয়েবল (URL/বাইনারি আছে) | `apps/` |
| কোনো প্রোডাক্টের ডোমেইন লজিক | `categories/<category>/<product>/features/` |
| সবকিছুর জন্য পুনর্ব্যবহারযোগ্য | `kernel/` |
| একটি ক্রস-কাটিং প্রোডাক্ট/লেয়ার | `crosscutting/` |
| ব্যাকএন্ড / UI কখনো ইমপোর্ট করে না | `platform/` |
| ডেটা (কাঁচা → সম্পাদিত → যাচাইকৃত) | `datasets/` |
| সম্পাদনীয় কনটেন্ট | `content/` |
| ব্যবসা ও পরিচালন | `business/` |
| কোড-সংলগ্ন আইনি বিষয় | `legal/` |
| জ্ঞান / সিদ্ধান্ত | `docs/` (গৃহীত) · `rfcs/` (অমীমাংসিত) |
| স্ক্র্যাচ / প্রোটোটাইপ | `sandbox/` |

## স্ট্রাকচার

```text
apps/           ওয়েব · অ্যাডমিন · pwa · মার্কেটিং · মোবাইল (RN ⇄ Flutter, স্ট্যাক-নিউট্রাল)
categories/     প্রতি ক্যাটাগরিতে একটি ফোল্ডার → প্রতি প্রোডাক্টে একটি → ফিচার স্লাইস
crosscutting/   শেয়ারড-লেয়ার প্রোডাক্ট (categories-এর সমপর্যায়ে)
kernel/         ডিজাইন-সিস্টেম · কন্ট্রাক্ট · অথ · অফলাইন · i18n · এনটাইটেলমেন্ট · টেলিমেট্রি · কনফিগ
platform/       api (স্ট্যাক-নিউট্রাল) · পেমেন্ট · নোটিফিকেশন · ভয়েস · সার্চ · জবস · আইডেন্টিটি · ডেটাবেস · অবজারভেবিলিটি
tests/          e2e · কন্ট্রাক্ট · ভিজ্যুয়াল  (ফিচার স্লাইস নিজেই পরীক্ষা করে)
datasets/       কাঁচা → সম্পাদিত → স্কিমা → সোর্স → পাইপলাইন → যাচাইকরণ
content/        স্টাইল-গাইড · টেমপ্লেট · ওয়ার্কফ্লো · লাইব্রেরি · লোকেল · seo · মডারেশন
business/       প্রোডাক্ট · মার্কেট-রিসার্চ · মার্কেটিং · মূল্যনির্ধারণ · মনিটাইজেশন · ফিনান্স · মেট্রিক্স · টিম · সাপোর্ট
legal/          কমপ্লায়েন্স · লাইসেন্স · প্রাইভেসি · টেমপ্লেট
docs/           আর্কিটেকচার · প্রোডাক্ট · স্ট্র্যাটেজি · সিদ্ধান্ত (+ নতুন ADR-এর জন্য TEMPLATE.md)
rfcs/           অমীমাংসিত আর্কিটেকচার প্রস্তাব
labs/           গবেষণা (NLP, ভয়েস, অফলাইন)
infra/          vercel · cloudflare · docker · মনিটরিং
security/       থ্রেট মডেল, নিরাপত্তা নীতি
tooling/        স্ক্রিপ্ট · কোডজেন
sandbox/        প্রোটোটাইপ (প্রোডাকশন কোড কখনো ইমপোর্ট করে না)
.ai/ + AGENTS.md  AI এজেন্টের জন্য মেশিন-রিডেবল কনভেনশন
```

**প্রতিটি ফোল্ডারে একটি `purpose.txt` থাকে** — কোনটা ওখানে বসবে এবং কী ফাইল/সাবফোল্ডার থাকা উচিত তা বর্ণনা করে। ফোল্ডার বদলালে সেগুলোও হালনাগাদ রাখুন — এগুলোই মানচিত্র।

## ইমপোর্ট সীমানা (lint দিয়ে প্রযোজ্য — দেখুন PACKAGE.md)

1. `apps/*` → `categories/*`, `kernel/*`, `crosscutting/*`
2. `categories/*` → `kernel/*`, `crosscutting/*`, নিজের `_category/` — **অন্য কোনো ক্যাটাগরি নয়**
3. ফিচার → শুধু পাশাপাশি ফিচারগুলোর public `index.ts`
4. `crosscutting/*` → শুধু `kernel/*`
5. `platform/*` → তার উপরে থেকে কখনো ইমপোর্ট হয় না (যোগাযোগ হয় `openapi/` + `kernel/contracts` দিয়ে)
6. প্রোডাক্ট কখনো পেমেন্ট গেটওয়ে ছোঁয় না — এগুলো পড়ে `kernel/entitlements`
7. `business/`, `legal/`, `docs/` → শুধু মানুষ/এজেন্টের জন্য, কোড কখনো ইমপোর্ট করে না
8. `logic/` কখনো React, HTTP, বা ডেটাবেস ক্লায়েন্ট ইমপোর্ট করে না

## এই টেমপ্লেট ব্যবহার করা

1. GitHub-এ **এই টেমপ্লেটটি ব্যবহার করুন** (অথবা `gh repo create my-project --template anurag-panda-dev/big-software-product-template`)
2. **নাম বদলান** — প্রজেক্টের নাম সার্চ-রিপ্লেস করুন; রুট `README.md`, `purpose.txt`, `AGENTS.md` হালনাগাদ করুন
3. **উদাহরণ ডোমেইনটি বাদ দিন** — `categories/` একটি উদাহরণ ডোমেইন নিয়ে আসে (একটি প্রকৃত প্রজেক্ট থেকে ৮টি ক্যাটাগরি / ২৭টি প্রোডাক্ট) যাতে প্যাটার্নটা বোঝা যায়। সেটি মুছে ফেলুন এবং নিজের ক্যাটাগরি/প্রোডাক্ট বানান, লেআউট ঠিক রেখে:
   `categories/<category>/<product>/{product.md, routes/, features/<feature>/{ui,logic,data,api,__tests__}}`
4. **রুট ওয়ার্কস্পেস পূরণ করুন** — `package.json`, `pnpm-workspace.yaml`, `turbo.json`, `tsconfig.base.json` (রুট `purpose.txt`-এ তালিকাভুক্ত; টেমপ্লেটটি স্ট্যাক-ফ্লেক্সিবল থাকায় এগুলো দেওয়া হয় না)
5. **প্রথম সিদ্ধান্তটি লিখে রাখুন** — `docs/decisions/TEMPLATE.md` কপি করে → `ADR-0001-…`; বড় সিদ্ধান্ত আগে `rfcs/` দিয়ে যায়
6. **খালি স্ক্যাফোল্ডিং আগে থেকে বানাবেন না** — আসল কাজ এলে ফোল্ডার পূরণ হয় (এটি স্ট্রাকচারের একটি সুস্পষ্ট নিয়ম)

## কনভেনশন

- **সিদ্ধান্ত:** `docs/decisions/ADR-NNNN-Title.md` (গৃহীত, কখনো নীরবে পুনর্লিখিত হয় না) · `rfcs/NNNN-title.md` (অমীমাংসিত)
- **purpose.txt ফরম্যাট:** `PURPOSE:` → `SHOULD CONTAIN:` → `SEE:`
- **বাংলা/RTL/অন্যান্য লোকেল:** `kernel/i18n` প্রাথমিক লোকেলটিকে ফিচার নয়, সাবস্ট্রেট হিসেবে দেখে — শুধু ইংরেজি প্রজেক্টেও ফরম্যাটিং ও মেসেজ ক্যাটালগ ওখানেই থাকে
- **স্ট্যাক-নিউট্রাল হটস্পট:** `platform/api` (Express ⇄ FastAPI) এবং `apps/mobile` (RN ⇄ Flutter) RFC-এর মাধ্যমে সিদ্ধান্ত পিছিয়ে দেয় — পরে ঠিক করুন, ব্যবহারকারীর উপর শূন্য প্রভাব
