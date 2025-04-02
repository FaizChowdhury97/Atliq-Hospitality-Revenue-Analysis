# Atliq Hospitality Revenue Analysis

This repository presents a comprehensive Business Intelligence solution for **AtliQ Hospitality**, designed to monitor hotel performance, booking trends, revenue generation, and operational metrics across regions and room classes.

---

## 🧭 Project Objective
To build a dashboard that empowers AtliQ’s hotel managers and executives to:
- Monitor weekly performance metrics (ADR, RevPAR, Occupancy)
- Compare revenue and booking efficiency by property and platform
- Analyze trends across time, segments, and geographies

---

## 📦 Dataset Overview
### Fact Tables:
- **fact_bookings**: Raw bookings including check-in/out, guests, revenue, status
- **fact_aggregated_booking**: Daily room availability and successful check-outs

### Dimension Tables:
- **dim_hotels**: Property, category, city
- **dim_rooms**: Room class and category
- **dim_date**: Full calendar with day types, week/year

---

## 🧠 Data Model
Star schema with relationships:
- `dim_hotels[property_id]` ➝ `fact_bookings[property_id]`
- `dim_hotels[property_id]` ➝ `fact_aggregated_booking[property_id]`
- `dim_date[date]` ➝ `fact_bookings[booking_date]`
- `dim_date[date]` ➝ `fact_aggregated_booking[check_in_date]`
- `dim_rooms[room_id]` ➝ `fact_aggregated_booking[room_id]`

_Disconnected Table:_ `Parameter` (used for dynamic measure switching)

---

## 🧮 DAX Measures
### Key Metrics
```dax
Total Bookings = COUNT(fact_bookings[booking_id])
Total Revenue = SUM(fact_bookings[Revenue Realized])
Total Successful Bookings = SUM(fact_aggregated_booking[successful_bookings])
Total Cancelled Bookings = CALCULATE([Total Bookings], fact_bookings[Booking Status] = "cancelled")
Total Checked out = CALCULATE([Total Bookings], fact_bookings[Booking Status] = "Checked out")
Total Capacity = SUM(fact_aggregated_booking[capacity])
Total No Show Bookings = CALCULATE([Total Bookings], fact_bookings[Booking Status] = "No Show")
```

### Business Metrics
```dax
ADR = DIVIDE([Total Revenue], [Total Bookings])
RevPAR = DIVIDE([Total Revenue], [Total Capacity])
Occupancy = DIVIDE([Total Successful Bookings], [Total Capacity])
Realisation (%) = DIVIDE([Total Checked out], [Total Bookings])
Cancellation Rate (%) = DIVIDE([Total Cancelled Bookings], [Total Successful Bookings])
Average Rating = AVERAGE(fact_bookings[ratings_given])
```

### Time-Comparison Metrics (WoW)
```dax
ADR WoW Change (%) = ...
Occupancy WoW Change % = ...
RevPAR WoW Change (%) = ...
Realisation WoW Change (%) = ...
Revenue WoW Change (%) = ...
DSRN WoW Change (%) = ...
```
*(All WoW metrics use CALCULATE with DATE filtering using dim_date[Week Number])*  

### Platform/Segment Metrics
```dax
Booking by Platform (%) = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(fact_bookings[Booking Platform])))
Booking by Room Class (%) = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(dim_rooms[room_class])))
```

---

## 📊 Visualizations

### Executive Dashboard
---

![image](https://github.com/user-attachments/assets/101f5caa-124b-456c-9c2f-243cf8d88e0e)
