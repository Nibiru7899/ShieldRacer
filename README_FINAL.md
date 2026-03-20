# ShieldRacer 🛡️
### Parametric Income Insurance for Food Delivery Partners
**Guidewire DEVTrails 2026 — Phase 1 Submission**

> *"When the rain stops your deliveries, your income shouldn't stop too."*

---

## Table of Contents
1. [The Problem](#1-the-problem)
2. [Our Solution](#2-our-solution)
3. [Persona & Real-World Scenarios](#3-persona--real-world-scenarios)
4. [How the Application Works](#4-how-the-application-works)
5. [Weekly Premium Model](#5-weekly-premium-model)
6. [Parametric Triggers](#6-parametric-triggers)
7. [Platform Choice](#7-platform-choice)
8. [System Block Diagram](#8-system-block-diagram)
9. [AI/ML Architecture](#9-aiml-architecture)
10. [Adversarial Defense & Anti-Spoofing Strategy](#10-adversarial-defense--anti-spoofing-strategy)
11. [Tech Stack](#11-tech-stack)
12. [6-Week Development Plan](#12-6-week-development-plan)
13. [Coverage Exclusions](#13-coverage-exclusions)

---

## 1. The Problem

Food delivery partners on Zomato and Swiggy earn between ₹3,500–₹6,000 per week. They have no fixed salary, no employer benefits, and no safety net. When external disruptions happen — a heavy monsoon, a 44°C afternoon, a city-wide curfew, or a platform outage — their income drops to zero for hours or even days.

These workers lose **20–30% of their monthly earnings** to disruptions they have absolutely no control over. No existing insurance product addresses this. Traditional insurance requires medical bills, accident reports, or injury documentation. Parametric insurance requires none of that — it pays when a measurable external condition (rainfall > 35mm/hr) is verified by a third-party data source, automatically.

**ShieldRacer covers what no one else covers: the hours a delivery partner couldn't work because the world outside made it impossible.**

---

## 2. Our Solution

ShieldRacer is a mobile-first parametric income insurance platform built specifically for food delivery partners on Zomato and Swiggy.

**How it works in one paragraph:** A worker subscribes for ₹30–₹80/week via UPI AutoPay. They tap "Start Shift" before working. ShieldRacer monitors 5 real-time data feeds in the background. When a disruption threshold is crossed — rainfall exceeding 35mm/hr, temperature above 42°C, AQI crossing 300, platform going offline for 45+ minutes, or a verified city curfew — a claim is automatically created. A fraud check runs silently using 6 behavioral signals from the worker's phone. If the Trust Score clears, a UPI payout of the worker's estimated lost income hits their phone within 10 minutes. The worker never fills a form. The worker never calls anyone.

**What we strictly do not cover:** Health expenses, life insurance, accident compensation, vehicle repair, or any loss not tied to a verified parametric event.

---

## 3. Persona & Real-World Scenarios

### Target Persona

| Attribute | Detail |
|-----------|--------|
| Platform | Zomato or Swiggy food delivery partner |
| Cities | Tier-1 and Tier-2 Indian cities |
| Weekly earnings | ₹3,500 – ₹6,000 |
| Working pattern | 6–8 hrs/day, typically 6 days/week |
| Device | Android smartphone, Chrome browser |
| Payment | UPI — PhonePe, Google Pay, or Paytm |
| Key vulnerability | No fixed income; 100% exposed to outdoor conditions |

---

### Scenario 1 — The Monsoon That Stopped Deliveries

**Worker:** Ravi, Swiggy partner, Velachery, Chennai
**Situation:** Ravi starts his evening shift at 6 PM. By 7:10 PM, rainfall in his district hits 39mm/hr. The Swiggy app shows near-zero orders in his zone.

**What ShieldRacer does:** OpenWeatherMap reports the rainfall threshold exceeded. IMD has issued a district Orange Alert. The mock platform order API shows a 67% drop in order assignments in Velachery. Both conditions verified. Ravi has been on shift for 40 minutes — past the 30-minute buffer. Trust Score computes to 76. Claim auto-approved.

**Outcome:** Ravi receives ₹238 — his estimated 2-hour income loss — directly to his PhonePe in 9 minutes. He does nothing.

---

### Scenario 2 — The Heat Nobody Can Work In

**Worker:** Meena, Zomato partner, Nagpur
**Situation:** It's the last week of May. Temperature at 1 PM hits 44°C. Meena logged on at 12:30 PM but hasn't received a single order in 45 minutes — restaurants are closed for the afternoon.

**What ShieldRacer does:** Temperature threshold crossed (≥42°C) during the covered heat window (11 AM–4 PM). Nagpur is on the heat-risk city list. Shift has been active 40 minutes. Trigger fires.

**Outcome:** Meena's 3-hour income loss (₹315) is paid to her UPI. The heat window closes at 4 PM — she resumes her shift and earns normally.

---

### Scenario 3 — The Friday Night Curfew

**Worker:** Arjun, Zomato partner, Old City, Hyderabad
**Situation:** At 9:15 AM, the district magistrate issues Section 144 for Arjun's district. He can't ride anywhere. He had just started his morning shift.

**What ShieldRacer does:** NewsAPI detects "curfew Hyderabad Old City" across 4 credible sources within 20 minutes. Admin verifies and confirms. All 34 active workers in that district are covered automatically.

**Outcome:** Each worker receives their estimated disruption income based on their personal hourly rate, automatically.

---

### Scenario 4 — When the Platform Goes Down

**Worker:** Priya, Zomato partner, Banjara Hills, Hyderabad
**Situation:** Priya starts her prime-time Friday shift at 7 PM. Zomato is completely unreachable. After 50 minutes she realizes the platform itself is down — her entire evening shift is lost.

**What ShieldRacer does:** UptimeRobot detects our monitored Zomato endpoint has been erroring for 52 consecutive minutes. Webhook fires to our backend. All workers with active shifts are covered for the outage window.

**Outcome:** ₹156 credited to Priya's UPI before she even decides to end her shift.

---

## 4. How the Application Works

### Onboarding Flow
```
Step 1 — Phone number + OTP verification
Step 2 — Select platform: Zomato or Swiggy
Step 3 — Enter partner ID (visible in the partner app profile)
Step 4 — Upload earnings screenshot from partner app
         → OCR (Tesseract.js) extracts last 8 weeks of weekly earnings automatically
Step 5 — Select daily working hours: 4 / 6 / 8 / 10 hours
Step 6 — Home pincode → auto-detects city and district
Step 7 — Zone risk score computed → weekly premium quoted instantly
Step 8 — Set up UPI AutoPay mandate (one-time, authenticated with UPI PIN via Razorpay)
Step 9 — Policy active from next Monday 12:01 AM
```
Total onboarding time: under 4 minutes.

---

### Active Shift Flow
```
Worker taps "Start Shift"
→ App begins collecting (every 5 minutes):
    - GPS coordinates
    - Accelerometer variance (motion detection via DeviceMotionEvent)
    - Battery level (Battery Status API)
    - IP address (sent to backend → ip-api.com geolocation)

ShieldRacer backend polls every 10 minutes:
    - OpenWeatherMap API for rainfall + temperature
    - WAQI API for AQI at nearest monitoring station
    - IMD district alert feed for Red/Orange alerts
    - UptimeRobot webhook listener for platform outage events
    - NewsAPI for curfew keywords in worker's city

Worker taps "End Shift"
→ Session logged, shift summary shown on dashboard
```

---

### Claim Flow
```
Disruption threshold crossed (any of 5 triggers)
↓
Shift active for ≥30 minutes? → NO → trigger suppressed
↓ YES
Trust Score computed (6 signals, 0–100)
↓
Score ≥70          Score 40–69             Score <40
   ↓                    ↓                      ↓
Auto-approve        Soft hold 2hrs          Flagged
UPI < 10 min        Passive recheck         1-photo evidence
                    1-tap appeal            4hr human review
                         ↓                      ↓
                    Legitimate confirmed → full payout + Trust Score boost
                    ShieldRacer misses 4hr SLA → auto-approve + ₹25 premium credit
```

---

## 5. Weekly Premium Model

**Collection method:** UPI AutoPay mandate — set up once at onboarding, auto-debited every Monday 8 AM. Worker never manually pays again after setup.

**Recalculation:** Every Monday 6 AM, the Zone Risk Scorer re-evaluates the worker's district using the latest forecast. Premium can change week-to-week. Worker notified Sunday evening of the upcoming week's premium with a plain-language reason.

### Tier Structure

| Tier | Zone Risk Score | Weekly Premium | Max Weekly Payout |
|------|----------------|---------------|------------------|
| Basic | 0–33 | ₹30 | ₹600 |
| Standard | 34–66 | ₹50 | ₹1,000 |
| Premium | 67–100 | ₹80 | ₹1,800 |

### Weekly Adjustments

| Condition | Effect |
|-----------|--------|
| Zone had <2 disruptions in past 8 weeks | −10% that week (Safe Zone Discount) |
| 3+ severe weather days forecast for coming week | +₹10–15 surge (notified transparently) |
| 6+ months clean claim history | Permanent −5% loyalty discount |
| Account age <14 days | Locked to Standard tier regardless of zone score |

### Payout Formula

```
hourly_rate  =  avg(last 8 weeks earnings)  ÷  (avg_working_days × daily_hours)
lost_hours   =  disruption_duration  capped at  remaining_shift_hours
payout       =  hourly_rate  ×  lost_hours  ×  0.85
```

The 0.85 multiplier (85% income replacement) is standard parametric insurance practice — it ensures the worker has a small incentive to resume work when conditions improve, while providing meaningful income protection.

---

## 6. Parametric Triggers

> All triggers require the worker's shift to have been active for **≥30 minutes** before firing. This rule prevents the most common gaming pattern: seeing a weather alert and immediately tapping Start Shift.

| # | Trigger | Threshold | Data Source | Secondary Verification |
|---|---------|-----------|-------------|------------------------|
| 1 | Heavy rainfall | ≥35mm/hr | OpenWeatherMap free tier + IMD district alerts | Platform order volume in zone must drop ≥50% (mock API) |
| 2 | Extreme heat | ≥42°C, 11 AM–4 PM only | OpenWeatherMap free tier | Only active in heat-risk cities: Nagpur, Jaipur, Lucknow, Hyderabad, Ahmedabad |
| 3 | Severe AQI | ≥300 for ≥2 consecutive hours | WAQI / aqicn.org (free) | Only active Oct–Jan in high-AQI cities: Delhi NCR, Kanpur, Patna, Faridabad |
| 4 | Platform outage | Endpoint down ≥45 min | UptimeRobot webhook (free) | Worker must have had active shift when outage began |
| 5 | Curfew / Section 144 | 3+ credible news sources | NewsAPI keyword monitoring | Admin must confirm before zone-wide payouts initiate |

**Dual-verification (Triggers 1–3):** Environmental triggers require the weather condition AND a measurable drop in platform order volume. Rain without a delivery slowdown = no payout. This eliminates the majority of false positives.

---

## 7. Platform Choice

**Decision: Mobile-first Progressive Web App (PWA)**

| Factor | Reasoning |
|--------|-----------|
| Workers are 100% mobile | No laptop access during shifts — must work one-handed on a phone |
| Zero install friction | Link shared via WhatsApp → open in Chrome → Add to Home Screen → works like native app |
| Sensor access | Chrome PWA supports: DeviceMotionEvent (accelerometer), Battery Status API, Geolocation, Camera |
| Offline resilience | Service Worker caches active policy data — visible even when connectivity drops in a flood zone |
| Deployment speed | GitHub push → Vercel auto-deploys in 90 seconds. No Play Store review needed |

---

## 8. System Block Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          SHIELDRACER — SYSTEM ARCHITECTURE                  │
└─────────────────────────────────────────────────────────────────────────────┘

 ┌──────────────────────────────────┐
 │        WORKER (PWA — Mobile)     │
 │  ┌──────────┐  ┌──────────────┐  │
 │  │ Start /  │  │  Onboarding  │  │
 │  │ End Shift│  │  (OTP + OCR) │  │
 │  └────┬─────┘  └──────┬───────┘  │
 │       │  Sensors       │ Partner  │
 │  GPS · Accel · Battery · IP Addr  │
 └───────┼────────────────┼──────────┘
         │  HTTPS / WSS   │ Earnings Screenshot
         ▼                ▼
 ┌──────────────────────────────────────────────────────────────┐
 │                    FASTAPI BACKEND (Railway)                  │
 │                                                              │
 │  ┌─────────────────┐   ┌─────────────────┐                  │
 │  │  Session Manager │   │  OCR Processor  │                  │
 │  │  (shift state)   │   │  (Tesseract.js) │                  │
 │  └────────┬─────────┘   └────────┬────────┘                 │
 │           │                      │ hourly_rate               │
 │           ▼                      ▼                           │
 │  ┌────────────────────────────────────────┐                  │
 │  │         TRIGGER ENGINE  (10-min poll)  │                  │
 │  │                                        │                  │
 │  │  ┌──────────┐ ┌────────┐ ┌──────────┐  │                  │
 │  │  │ Weather  │ │  AQI   │ │ Platform │  │                  │
 │  │  │ Checker  │ │Checker │ │ Uptime   │  │                  │
 │  │  └────┬─────┘ └───┬────┘ └────┬─────┘  │                  │
 │  │       │           │           │        │                  │
 │  │  ┌────┴───────────┴───────────┴──────┐  │                  │
 │  │  │     Dual-Verification Gate        │  │                  │
 │  │  │  (env condition + order drop)     │  │                  │
 │  │  └────────────────┬──────────────────┘  │                  │
 │  └───────────────────┼────────────────────┘                  │
 │                      │ TRIGGER FIRED                         │
 │                      ▼                                       │
 │  ┌────────────────────────────────────────┐                  │
 │  │          TRUST SCORE ENGINE            │                  │
 │  │                                        │                  │
 │  │  Accelerometer Variance     (25%)      │                  │
 │  │  IP vs GPS Delta            (20%)      │                  │
 │  │  Shift Duration at Trigger  (20%)      │                  │
 │  │  Peer Worker Density        (15%)      │                  │
 │  │  Temporal Claim Clustering  (10%)      │                  │
 │  │  Account Age                (10%)      │                  │
 │  │                                        │                  │
 │  │   Score ≥ 70   →  AUTO-APPROVE         │                  │
 │  │   Score 40–69  →  SOFT HOLD (2hr)      │                  │
 │  │   Score < 40   →  MANUAL REVIEW (4hr)  │                  │
 │  └────────────────────┬───────────────────┘                  │
 │                       │                                      │
 │  ┌────────────────────▼───────────────────┐                  │
 │  │         PAYOUT ENGINE                  │                  │
 │  │  payout = hourly_rate × lost_hours     │                  │
 │  │           × 0.85 (85% replacement)     │                  │
 │  └────────────────────┬───────────────────┘                  │
 └───────────────────────┼──────────────────────────────────────┘
                         │ Razorpay UPI Payout API
                         ▼
              ┌──────────────────────┐
              │   WORKER'S UPI       │
              │  (PhonePe / GPay /   │
              │       Paytm)         │
              │  ← ₹ in < 10 min     │
              └──────────────────────┘

 ┌─────────────────────────────────────────────────────────────────────────┐
 │                        EXTERNAL DATA SOURCES                             │
 │                                                                         │
 │  ┌──────────────────┐  ┌───────────┐  ┌───────────┐  ┌──────────────┐  │
 │  │ OpenWeatherMap   │  │   WAQI    │  │ NewsAPI   │  │ UptimeRobot  │  │
 │  │ (Rain + Temp)    │  │  (AQI)    │  │ (Curfew)  │  │  (Outage)    │  │
 │  └──────────────────┘  └───────────┘  └───────────┘  └──────────────┘  │
 │  ┌──────────────────┐  ┌───────────┐                                    │
 │  │  IMD District    │  │ ip-api.com│                                    │
 │  │  Alert Feed      │  │ (GeoIP)   │                                    │
 │  └──────────────────┘  └───────────┘                                    │
 └─────────────────────────────────────────────────────────────────────────┘

 ┌─────────────────────────────────────────────────────────────────────────┐
 │                         ML MODULES (scikit-learn)                        │
 │                                                                         │
 │  ┌─────────────────────────┐   ┌──────────────────────────────────────┐ │
 │  │  Zone Risk Scorer        │   │  Income Baseline Estimator           │ │
 │  │  RandomForestRegressor   │   │  OCR → avg earnings → hourly rate    │ │
 │  │  Input: climate + NDMA   │   │  Refreshed every 4 weeks             │ │
 │  │  Output: Risk Score 0–100│   └──────────────────────────────────────┘ │
 │  │  Recalc: Every Monday    │                                           │ │
 │  └─────────────────────────┘   ┌──────────────────────────────────────┐ │
 │                                 │  Ring Fraud Detector (Phase 2)       │ │
 │                                 │  IsolationForest — batch anomalies   │ │
 │                                 └──────────────────────────────────────┘ │
 └─────────────────────────────────────────────────────────────────────────┘

 ┌─────────────────────────────────────────────────────────────────────────┐
 │                            DATA LAYER                                    │
 │                    PostgreSQL (Railway) — ACID Transactions              │
 │                                                                         │
 │   workers │ policies │ shifts │ claims │ trust_scores │ zone_risk_cache  │
 └─────────────────────────────────────────────────────────────────────────┘
```

---

## 9. AI/ML Architecture

### Module 1 — Zone Risk Scorer

**Purpose:** Assign a weekly risk score (0–100) to each district to calculate the worker's weekly premium tier.

**Model:** `RandomForestRegressor` (scikit-learn, n_estimators=100, max_depth=6)

**Input features:**
- Historical average monthly rainfall (mm) — WorldClim public dataset
- Historical flood incident frequency (5-year count) — NDMA public reports
- Average seasonal AQI — CPCB public data
- Historical hartal/curfew frequency by district
- District classification (coastal / flood-plain / metro / inland / tier-2)

**Training data:** Synthetic dataset of 500+ district-week records generated from public climate data (Phase 1). Replaced with real accumulated claim data from Phase 2 onward.

**Why Random Forest:** Robust to overfitting on small datasets. Produces interpretable feature importances — we can tell a worker exactly why their zone is rated high risk.

**Recalculation:** Every Monday 6 AM with updated 7-day forecast as additional input.

---

### Module 2 — Income Baseline Estimator

**Purpose:** Build a personal income fingerprint for fair, proportional payouts rather than flat amounts.

**How it works:**
- OCR (Tesseract.js) extracts 8 weeks of earnings from worker's partner app screenshot at onboarding
- Worker self-reports daily hours (4/6/8/10)
- `hourly_rate = avg_weekly_earnings ÷ (avg_working_days × hours_per_day)`
- New workers (<2 weeks data): uses district-average hourly rate as conservative baseline
- Refreshed every 4 weeks on new screenshot upload

---

### Module 3 — Trust Score Engine

**Purpose:** Route claims through auto-approve, soft-hold, or manual review based on behavioral fraud signals.

| Signal | Weight | Data Source |
|--------|--------|-------------|
| Accelerometer variance | 25% | DeviceMotionEvent (Chrome PWA) |
| IP vs GPS distance | 20% | Server-side ip-api.com lookup |
| Shift duration at trigger | 20% | Shift session table |
| Peer worker density in zone | 15% | Aggregate active sessions, same district |
| Temporal claim clustering | 10% | Backend analytics per event window |
| Account age | 10% | Registration timestamp |

**Phase 1:** Weighted rule-based scoring — deterministic and auditable.
**Phase 2 upgrade:** `IsolationForest` (scikit-learn) for batch-level ring fraud detection.

---

## 10. Adversarial Defense & Anti-Spoofing Strategy

> **The threat:** A 500-member syndicate organized via Telegram used GPS spoofing apps to fake locations inside weather alert zones, triggering mass false payouts and draining a parametric insurance liquidity pool. Simple GPS verification is insufficient.

---

### 10.1 The Differentiation — Genuine Worker vs. GPS Spoofer

ShieldRacer treats GPS as one input of six. A spoofer can fake a GPS coordinate. They cannot simultaneously fake an active accelerometer, a matching IP location, a draining battery, a shift that was active before the rain alert, peer corroboration from other workers, and months of account history.

| Signal | Genuine Stranded Worker | GPS Spoofer at Home |
|--------|------------------------|-------------------|
| Accelerometer | Continuous 0.3–1.5 m/s² variance — on a bike in rain | Near-zero — phone stationary on a table |
| IP vs GPS delta | IP resolves within 5–15km of claimed zone | Home WiFi resolves 15–40km from spoofed coordinates |
| Shift duration | Shift active 45–90 min before disruption | Shift started within 5 min of weather alert publishing |
| Peer density | 4–12 other verified workers independently in same district | Isolated GPS cluster with no peer corroboration |
| Account age | Months of earnings history and clean claims | Account created days before the fraud event |

The Trust Score weights these six signals proportionally. No single signal causes rejection. A genuine worker with patchy GPS and a still phone (parked waiting for an order) still scores ≥70 if their shift history, peer density, and account age are clean.

---

### 10.2 The Data — Detecting a Coordinated Ring

**Temporal spike detection:** Genuine disruptions produce a gradual 20–60 minute claim ramp-up. A Telegram-coordinated syndicate produces a vertical spike — 200+ claims in <15 minutes. ShieldRacer flags any batch where per-zone claim arrival rate exceeds 3× the historical disruption baseline. The entire batch holds before any individual payout fires.

**Geographic clustering:** Real workers spread organically across a zone. Spoofed workers cluster impossibly close to the zone centroid (GPS spoofing apps default to zone center). We flag batches where >60% of claimants are within 500m of the centroid with no movement trail in session data.

**Device behavioral graph:** GPS spoofing apps produce identifiable signatures — perfectly uniform GPS update intervals (1,000ms exactly vs real GPS varying 800–1,400ms), suspiciously precise accuracy reports (5m in heavy rain is physically impossible). 20+ devices sharing these signatures form a connected fraud graph. One confirmed fraudster raises the suspicion penalty for all graph-connected devices by 15 Trust Score points.

**Order flow contradiction:** A genuine disruption suppresses platform order volume. If the mock platform API shows normal order activity during the claimed disruption, secondary verification fails for the entire zone regardless of GPS data.

**New account surge:** Accounts under 14 days old are automatically held for manual review on their first claim and excluded from zone-wide auto-approve batches.

---

### 10.3 The UX Balance — Not Penalizing Honest Workers

A genuine worker in heavy rain will have: patchy GPS, degraded 4G, a wet screen, possibly riding through a dead cell zone. These make them look suspicious by accident. ShieldRacer's response is built on three principles: **no shame, one action, fair history.**

**Three-tier routing:**

```
Trust Score ≥ 70
→ Auto-approve → UPI payout <10 minutes
   Worker sees: "Claim processed. ₹238 sent to your UPI ✓"
   Worker never knows a scoring check happened.

Trust Score 40–69
→ Soft hold (max 2 hours)
   Worker message: "We're verifying your claim — usually under an hour. No action needed."
   System passively rechecks signals every 30 minutes.
   If resolved → auto-approve. If unresolved at 2hrs → 1-tap appeal unlocked.

Trust Score < 40
→ Flagged
   Worker message: "We need a quick confirmation to process your claim. Tap here — 30 seconds."
   Worker taps → camera opens → one photo of surroundings OR order history screenshot.
   Human review within 4 hours.
   Legitimate → full payout + permanent +5 Trust Score baseline boost.
   ShieldRacer misses 4hr SLA → auto-approve + ₹25 next premium credit.
```

**Key protections:**
- **Network drop amnesty:** GPS signal lost for >10 min during verified weather event → gap filled with last valid zone location. Losing connectivity in a flood zone is expected, not suspicious.
- **History anchor:** 12-week rolling clean history gives workers a +8 baseline Trust Score adjustment. One bad-signal session cannot override months of genuine behavior.
- **Language policy:** No worker-facing notification ever contains: "fraud," "suspicious," "flagged," or "under investigation." We say "verifying" and "quick confirmation."
- **One action maximum:** The maximum burden we place on a genuine worker is one tap to open a camera and take one photo. No form. No explanation required.
- **SLA accountability:** We miss the 4-hour review SLA → we pay, not the worker.

---

## 11. Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | React PWA | Mobile-first, sensor access, offline via Service Worker |
| Backend | Python + FastAPI | Async API polling, Python ML ecosystem |
| ML | scikit-learn | Right-sized for our training data; interpretable |
| Database | PostgreSQL | Relational schema; ACID transactions for payment records |
| OCR | Tesseract.js | Browser-based earnings screenshot processing |
| Weather | OpenWeatherMap (free) + IMD alerts | Current conditions + official Indian district alerts |
| AQI | WAQI / aqicn.org (free) | Best free AQI coverage for Indian cities |
| Platform uptime | UptimeRobot (free) | Webhook-based outage detection |
| News | NewsAPI.org (free) | Curfew/bandh keyword monitoring |
| IP geolocation | ip-api.com (free) | Server-side IP→location for fraud detection |
| Payments | Razorpay test mode | UPI AutoPay + instant payout API — only realistic choice for India |
| Frontend deploy | Vercel (free) | Auto-deploy from GitHub |
| Backend deploy | Railway (free) | Python + PostgreSQL, auto-deploy from GitHub |

---

## 13. Coverage Exclusions

ShieldRacer covers **income loss from verified parametric disruption events only.**

| Excluded | Reason |
|----------|--------|
| Health insurance / medical treatment | Requires separate IRDAI health insurance license |
| Life insurance / accidental death | Actuarial and legal requirements out of scope |
| Vehicle repair / maintenance | Worker-controlled expense; not a parametric event |
| Accident compensation | Liability insurance; separate regulatory category |
| Any loss not tied to a verified trigger | No objective threshold breach = no measurable insurable event |

---

<br>

---

<div align="center">

## 🏁 Team Overdrive

**Amrita Vishwa Vidyapeetham**

*Guidewire DEVTrails 2026 — Phase 1 Submission*

**Team Members:**
Pasarla Pydi Sathya Karthikeya, Bellapukonda Karthikeya, M Sai Krishna, P. Rohith


</div>
