# 🏨 Hotel Reservations Analytics Dashboard

> A complete end-to-end data analytics project built in Microsoft Excel, analyzing 36,238 hotel bookings to uncover €4.3M in revenue lost to cancellations — and providing actionable recommendations for hotel management.

---

## 📌 Project Overview

This project analyzes two years of hotel reservation data (2017–2018) from INN Hotels Group. The goal was to answer 26 business questions across 5 analytical dimensions, build a professional interactive dashboard, and deliver a data-driven story to hotel ownership.

**The headline finding:**

> *"This hotel fills its rooms — but loses a third of its revenue to cancellations, and almost none of its guests ever come back."*

---

## 📊 Key Metrics

| Metric | Value |
|--------|-------|
| Total Bookings Analyzed | 36,238 |
| Overall Cancellation Rate | 32.78% |
| Total Potential Revenue | €11,336,103 |
| Revenue Earned | €7,042,183 |
| Revenue Lost to Cancellations | **€4,293,920** |
| Average Daily Rate (ADR) | €99.94 |
| Average Length of Stay | 3.01 nights |
| Repeat Guest Rate | **2.56%** |

---

## 🗂️ Project Structure

```
Hotel-Reservations-Analytics/
│
├── 📁 Data/
│   └── Hotel_Reservations.csv          # Raw dataset (36,238 rows)
│
├── 📁 Dashboard/
│   └── Tourism_Project.xlsx            # Complete Excel dashboard
│       ├── Q&A                         # All 26 business questions
│       ├── Pivot_table                 # Analysis workings
│       ├── Cancellation                # Page 1 dashboard
│       ├── Pricing                     # Page 2 dashboard
│       ├── Seasonality                 # Page 3 dashboard
│       ├── Customer                    # Page 4 dashboard
│       └── Market                      # Page 5 dashboard
│
├── 📁 Documentation/
│   └── Tourism_KPIs.pptx               # Domain knowledge deck
│
└── README.md
```

---

## 🔧 Tools & Technologies

| Tool | Usage |
|------|-------|
| **Microsoft Excel** | Primary analysis and dashboard tool |
| **Power Query** | Data extraction, cleaning, transformation |
| **Power Pivot** | Star schema data model, DAX measures |
| **DAX** | KPI calculations and business metrics |
| **Pivot Tables** | Data aggregation and analysis |
| **Excel Charts** | Dashboard visualizations |
| **VBA** | Navigation, PDF export, slicer reset |

---

## 🏗️ Data Model

The project uses a **Star Schema** architecture built in Power Pivot:

```
                    ┌─────────────┐
                    │  Dim Date   │
                    │  DateKey    │
                    └──────┬──────┘
                           │
┌──────────────┐    ┌──────┴──────────┐    ┌───────────────────┐
│   Dim Room   │    │                 │    │   Dim Meal Plan   │
│   Room_Key   ├────┤ Fact_Reserva-   ├────│   Meal_Key        │
└──────────────┘    │    tions        │    └───────────────────┘
                    │                 │
┌──────────────┐    │  36,238 rows    │    ┌───────────────────┐
│  Dim Market  ├────┤                 ├────┤  Dim Booking      │
│  Market_Key  │    │                 │    │  Status_Key       │
└──────────────┘    └────────┬────────┘    └───────────────────┘
                             │
                    ┌────────┴────────┐
                    │  Dim Repeated   │
                    │  Guest_Key      │
                    └─────────────────┘
```

**Fact Table:** Fact_Reservations (36,238 rows)
**Dimension Tables:** 6 dimension tables
**Relationships:** Many-to-one (Fact → Dimensions)

---

## 📐 DAX Measures

```dax
-- Core Metrics
Total Bookings := COUNTROWS(Fact_Reservations)

Total Cancellation := 
    CALCULATE(COUNTROWS(Fact_Reservations),
    'Dim Booking Status'[booking_status] = "Canceled")

Cancellation Rate := 
    DIVIDE([Total Cancellation], [Total Bookings])

ADR := 
    DIVIDE(
        CALCULATE(SUM(Fact_Reservations[avg_price_per_room]),
        'Dim Booking Status'[booking_status] = "Not_Canceled"),
        CALCULATE(COUNTROWS(Fact_Reservations),
        'Dim Booking Status'[booking_status] = "Not_Canceled")
    )

Total Revenue := 
    SUMX(
        FILTER(Fact_Reservations,
        'Dim Booking Status'[booking_status] = "Not_Canceled"),
        Fact_Reservations[avg_price_per_room] *
        (Fact_Reservations[no_of_weekend_nights] +
         Fact_Reservations[no_of_week_nights])
    )

Avg Lead Time := AVERAGE(Fact_Reservations[lead_time])

Repeated Guest Rate := 
    DIVIDE(
        CALCULATE([Total Bookings],
        Fact_Reservations[repeated_guest] = 1),
        [Total Bookings]
    )
```

---

## 🧹 Data Cleaning

Issues identified and resolved in Power Query:

| Issue | Action Taken |
|-------|-------------|
| February 29, 2018 (invalid date) | Removed 37 rows — 2018 was not a leap year |
| DateKey formula producing wrong values | Fixed zero-padding using `Text.PadStart` |
| Complementary segment avg price = €3.14 | Flagged as complimentary stays, excluded from pricing analysis |
| 139 bookings with 0 adults | Retained — data entry errors, valid bookings confirmed |
| 545 bookings with price = €0 | Retained for count analysis, excluded from pricing metrics |

---

## ❓ Business Questions Answered

### 🚨 Cancellation Analysis (Q1–Q7)
| Q# | Question | Key Finding |
|----|----------|-------------|
| Q1 | Overall cancellation rate? | **32.78%** — 1 in 3 bookings canceled |
| Q2 | Does lead time increase cancellation? | Very Long (181+ days) = **74% cancellation** |
| Q3 | Which room type cancels most? | Room Type 6 = **42%** |
| Q4 | Which meal plan cancels most? | Meal Plan 2 = **46%** |
| Q5 | Do repeated guests cancel less? | Repeated = **2%** vs New = **34%** |
| Q6 | Do special requests reduce cancellation? | 0 requests = 43% → 3+ requests = **0%** |
| Q7 | Do prior cancelers cancel again? | Inconclusive — small sample size |

### 💶 Pricing Analysis (Q8–Q12)
| Q# | Question | Key Finding |
|----|----------|-------------|
| Q8 | Avg price by room type? | Room Type 6 = **€182** (highest) |
| Q9 | Do families pay more? | With Children = **€141** vs No Children = €100 (+41%) |
| Q10 | Most expensive months? | September = **€116** |
| Q11 | Do canceled bookings cost more? | Canceled avg = **€111** vs Not Canceled = €100 |
| Q12 | Online vs offline pricing? | Online = **€112** vs Corporate = €83 |

### 📅 Seasonality (Q13–Q16)
| Q# | Question | Key Finding |
|----|----------|-------------|
| Q13 | Peak booking months? | October = **5,317 bookings** |
| Q14 | Cancellation rate by month? | July = **45%** (highest) |
| Q15 | Weekend vs weekday dominance? | Weekdays = **73%** of all nights |
| Q16 | 2017 vs 2018 bookings? | 2018 = **4.5x more** (note: 2017 partial data) |

### 👥 Customer Behavior (Q17–Q22)
| Q# | Question | Key Finding |
|----|----------|-------------|
| Q17 | Most reserved room type? | Room Type 1 = **77.5%** of bookings |
| Q18 | Most selected meal plan? | Meal Plan 1 = **76.7%** of bookings |
| Q19 | Do parking requesters cancel less? | With parking = **10.2%** vs without = 33.5% |
| Q20 | Special requests vs cancellation? | Strong negative correlation confirmed |
| Q21 | Average length of stay? | **3.01 nights** (2.20 weekday + 0.81 weekend) |
| Q22 | Repeat guest percentage? | Only **2.56%** of guests return |

### 🌐 Market Segments (Q23–Q26)
| Q# | Question | Key Finding |
|----|----------|-------------|
| Q23 | Most used booking channel? | Online = **64%** of all bookings |
| Q24 | Highest cancellation segment? | Online = **37%** cancellation rate |
| Q25 | Corporate vs online pricing? | Corporate = **€83** vs Online = €112 |
| Q26 | Longest average lead time? | Offline = **123 days** |

---

## 📈 Dashboard Structure

The dashboard consists of 5 interactive pages:

### Page 1 — Cancellation Analysis
**Story:** Why is the hotel losing €4.3M to cancellations?
- Charts: Lead Time vs Cancel Rate, Cancel Rate by Room Type, Cancel Rate by Meal Plan, Special Requests vs Cancel Rate
- Slicer: Booking Status

### Page 2 — Pricing Insights
**Story:** What drives room pricing and where is revenue leaking?
- Charts: Avg Price by Room Type, Avg Price by Month, Family vs Adults Price, Revenue Lost vs Earned
- Slicer: Room Type

### Page 3 — Seasonality
**Story:** When is the hotel busy and when is it at risk?
- Charts: Bookings by Month, Cancellation Rate by Month, Weekday vs Weekend Nights, 2017 vs 2018
- Slicer: Year

### Page 4 — Customer Behavior
**Story:** Who is the typical guest and why don't they come back?
- Charts: Room Type Distribution, Meal Plan Distribution, Parking vs Cancellation, Repeat vs New Guests
- Slicer: Repeated Guest

### Page 5 — Market Segments
**Story:** Which channels bring volume vs which bring reliability?
- Charts: Bookings by Segment, Cancel Rate by Segment, Avg Price by Segment, Lead Time by Segment
- Slicer: Market Segment

---

## 💡 Key Insights & Recommendations

### Insight 1 — The Lead Time Problem
Guests who book more than 6 months in advance cancel **74% of the time**. The hotel should implement non-refundable deposit policies for bookings with lead time exceeding 90 days.

### Insight 2 — The Loyalty Crisis
Only **2.56% of guests return**. The hotel spends continuously on acquiring new guests while ignoring the retention opportunity. A loyalty program targeting returning guests could significantly reduce acquisition costs.

### Insight 3 — The Online Paradox
Online bookings dominate at 64% of volume but carry a **37% cancellation rate**. Corporate bookings cancel at only 11%. Growing the corporate segment — even at a lower price point of €83 — would generate more reliable revenue than online bookings at €112.

### Insight 4 — The Premium Room Risk
Room Type 6 commands the highest price at €182 but also has the highest cancellation rate at **42%**. The hotel's most valuable inventory is its most unreliable. Targeted pre-arrival communication for premium room bookings could reduce this rate.

### Insight 5 — The Engagement Signal
Guests with 3 or more special requests cancel at **0%**. Special requests signal commitment. The hotel should actively encourage guests to make special requests during the booking process to increase engagement and reduce cancellations.

---
## 🎯 Three Recommended Actions for Hotel Management

```
┌─────────────────────────────────────────────────────────┐
│  ACTION 1: Stricter Cancellation Policies               │
│  Apply non-refundable rates for lead time > 90 days     │
│  Potential impact: Reduce €4.3M revenue loss            │
├─────────────────────────────────────────────────────────┤
│  ACTION 2: Launch a Loyalty Program                     │
│  Target the 97.44% of guests who never return           │
│  Potential impact: Reduce new guest acquisition costs   │
├─────────────────────────────────────────────────────────┤
│  ACTION 3: Grow the Corporate Segment                   │
│  11% cancellation vs 37% for online                     │
│  Potential impact: More reliable, predictable revenue   │
└─────────────────────────────────────────────────────────┘
---

*Built with Microsoft Excel, Power Pivot, DAX, Power Query, and VBA*
