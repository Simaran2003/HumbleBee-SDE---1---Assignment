# HumbleBee-SDE---1---Assignment
Create a backend API service that powers the BeeTrail Field Logger app — allowing beekeepers to log hive placements and retrieve crop-pollination opportunities nearby. The backend must store hive logs, manage crop calendars, and support geo-filtered crop matching.

# BeeTrail Field Logger – Backend API

This is the backend API service for the **BeeTrail Field Logger** app. It enables beekeepers to log hive placements, manage crop flowering calendars, and discover pollination opportunities within a geographic radius. Built with **Node.js, Express, and MongoDB**, it also supports user authentication and CSV export of hive logs.

---

## Setup Instructions

### 1. Clone the repo and install dependencies

```bash
git clone https://github.com/YOUR_USERNAME/beetrail-backend.git
cd beetrail-backend
npm install
```

### 2. Create a `.env` file in the root directory

```env
PORT=5000
MONGO_URI=mongodb+srv://ayushgupta:oIpdzKWcMhhYQ7Gr@cluster0.mgeghsd.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET=superstrongbeetrailsecret
```

### 3. Run the server

```bash
npm run dev
```

---

## 📬 API Endpoints

### 🔐 Authentication

- `POST /api/auth/register`  
  → Register a new user (role: `"beekeeper"` or `"admin"`)

- `POST /api/auth/login`  
  → Login and receive a JWT token

---

###  Hive APIs

- `POST /api/hives`  
  → Add a new hive log  
  **Body:** `hiveId`, `datePlaced`, `latitude`, `longitude`, `numColonies`  
  ✅ Validates unique `hiveId`, lat/lng ranges, and future/past dates

- `GET /api/hives?startDate=&endDate=&page=&limit=`  
  → Get all hive logs with pagination and optional date filtering

- `GET /api/hives/export`  
  → Export all hive logs as a `.csv` file

---

### Crop APIs

- `POST /api/crops`  
  → Add a crop flowering calendar entry  
  **Body:** `name`, `floweringStart`, `floweringEnd`, `latitude`, `longitude`, `recommendedHiveDensity`

- `GET /api/crops/nearby?latitude=&longitude=&radius=&date=`  
  → Get nearby flowering crops based on:
  - Geographical distance (default radius = 100 km)
  - Flowering window (defaults to today's date)

---

## Geo-Filter Logic (Lat/Lng Radius Matching)

To match nearby crops to a hive or beekeeper location, we use the **Haversine formula**, which calculates the great-circle distance between two geo points (latitude, longitude):

```js
function getDistance(lat1, lon1, lat2, lon2) {
  const R = 6371; // Earth radius in km
  const dLat = degToRad(lat2 - lat1);
  const dLon = degToRad(lon2 - lon1);
  const a =
    Math.sin(dLat / 2) ** 2 +
    Math.cos(degToRad(lat1)) *
    Math.cos(degToRad(lat2)) *
    Math.sin(dLon / 2) ** 2;
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  return R * c;
}
```

This helps filter only crops that fall **within the desired radius** and are **flowering on the selected date**.

---

## ✅ Fulfilled Assignment Requirements

### Core Features

- [x] Add Hive Log API
- [x] Get Hive Logs (pagination + date filter)
- [x] Add Crop Calendar Entry
- [x] Nearby Crop Matching (geo-distance + flowering window)

---

### Edge Cases Handled

- [x] Overlapping flowering periods
- [x] Empty crop matches (returns empty array)
- [x] Invalid or out-of-range lat/lng
- [x] Future and past dates in hive logs

---

### Bonus Features

- [x] JWT-based authentication with roles: `"beekeeper"` and `"admin"`
- [x] Export Hive Logs as CSV (`GET /api/hives/export`)

---

## 🧪 Postman Collection

Test all endpoints using the included file:

📄 `Bee-backend.postman_collection.json`

Covers:
- Auth (Register/Login)
- Hive APIs (Add/Get/Export)
- Crop APIs (Add/Nearby)

---

## Assumptions

- `hiveId` must be globally unique
- Default crop search radius = 100 km
- All routes require valid JWT token
- CSV export is publicly accessible (can be restricted later)
- Admin dashboard was optional and not implemented

---

## Author

- **Simaran Gupta**
- Email: simarangupta2003@gmail.com
