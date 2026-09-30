# 🍽️ Restaurant App MVP (Frontend Only - React Native)

**Course Assignment:** Assignment No. 1 — Fall 2026  
**Repository Name:** `restaurant-app-mvp`  
**Framework:** React Native (Expo SDK managed workflow)  

---

## 📌 Project Overview
FoodieExpress is a feature-packed React Native MVP mobile application designed for diners and restaurant managers. Built completely frontend-only without external APIs or state management libraries (no Redux), it relies purely on React built-in hooks (`useState`, `useEffect`, `useRef`, `useContext`, `useReducer`, `useMemo`, `useCallback`) and custom hooks.

---

## 🚀 Features Implemented

### 1. Authentication & Role-Based Navigation
- Switch between **Login** and **Signup** modes.
- Role selector: **Customer** or **Restaurant Manager**.
- Form validation (email format, 8+ char password with digits, matching passwords).
- Simulated 1s network delay with loading state.
- Manager Dashboard tab dynamically restricted to `manager` role only.

### 2. Interactive Menu Browsing & Search
- 16 Pakistani cuisine items across 4 categories (Starters, Mains, Desserts, Drinks).
- Simulated 1.5s Promise fetch with loading indicator & pull-to-refresh.
- Real-time debounced search bar (400ms delay via `useRef` and `useDebounce`).
- Render counter label using `useRef` for performance monitoring.
- Floating "Back to Top" button visible after 300px scroll (`scrollToOffset`).
- Derived filtered & sorted items (Price ↑, Price ↓, Name A-Z) optimized with `useMemo`.

### 3. Cart Management (`useReducer` & `CartContext`)
- Pure `cartReducer` managing actions: `ADD_ITEM`, `REMOVE_ITEM`, `INCREMENT`, `DECREMENT`, `UPDATE_NOTE`, `CLEAR_CART`, `APPLY_PROMO`, `REMOVE_PROMO`.
- Custom item instructions field per item (e.g. "no onions").
- Mock promo code validation (`WELCOME10` → 10%, `FEAST20` → 20%, `SPECIAL15` → 15%, `GRAND25` → 25%).
- Live cart count badge on bottom tab icon.

### 4. Order Summary & Calculations (`useMemo`)
- Dynamic bill breakdown: Subtotal, Service Charge (5%), Sales Tax (15%), Promo Discount, Grand Total.
- Dine-in (with table assignment) vs Takeaway (with pickup time) options.
- Optimized calculation logic preventing unnecessary re-renders.

### 5. Table Reservation System (`useReservation` Custom Hook)
- Hourly time slots from 12:00 to 22:00 with auto-disabled full slots.
- Interactive table selection by capacity (2, 4, 6, 8 seats).
- Pakistani mobile number validation (`03XX-XXXXXXX`).
- Pre-booking confirmation modal and "My Reservations" list with cancellation.

### 6. Live Order Tracking & Manager Dashboard
- Automated status progression (Pending → Preparing → Ready → Served) with timer updates using `setInterval`.
- Manager Dashboard with 3 tabs: **Incoming Orders** (manual status updates), **Reservations** (Accept/Decline), and **Menu Management** (Add item, toggle in/out of stock).
- Shared state updates menu immediately across customer and manager roles.
- `AsyncStorage` persistence for orders, reservations, and menu edits.

---

## 🔑 Mock Login Credentials

| Role | Email | Password |
|------|-------|----------|
| **Customer** | `ali@test.com` | `Pass1234` |
| **Customer** | `sara@test.com` | `Pass1234` |
| **Manager** | `admin@test.com` | `Admin123` |
| **Manager** | `chef@test.com` | `Chef1234` |

---

## 🎣 React Hooks Matrix Across Screens

| Screen | `useState` | `useEffect` | `useRef` | `useContext` | `useReducer` | `useMemo` | `useCallback` | Custom Hooks |
|--------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **LoginScreen** | ✅ | — | — | ✅ | — | — | — | `useForm`, `useAuth`, `useTheme` |
| **MenuScreen** | ✅ | ✅ | ✅ | ✅ | — | ✅ | ✅ | `useDebounce`, `useMenu`, `useCart`, `useTheme` |
| **CartScreen** | ✅ | — | — | ✅ | ✅ | — | — | `useCart`, `useTheme` |
| **OrderSummaryScreen** | ✅ | — | — | ✅ | — | ✅ | — | `useCart`, `useOrders`, `useAuth`, `useTheme` |
| **ReservationScreen** | ✅ | ✅ | — | ✅ | — | — | — | `useReservation`, `useAuth`, `useTheme` |
| **OrderTrackingScreen** | ✅ | ✅ | — | ✅ | ✅ | — | — | `useOrders`, `useTheme` |
| **ProfileScreen** | — | — | — | ✅ | — | — | — | `useAuth`, `useTheme` |
| **ManagerDashboardScreen** | ✅ | — | — | ✅ | — | — | — | `useOrders`, `useReservations`, `useMenu`, `useTheme` |

---

## 📝 Conceptual Explanations & Written Requirements

### Q6: Context API vs. Prop Drilling & Drawbacks
- **Why Context Suits Global State:** Data like `user` authentication state and `theme` (light/dark) is required by nearly every screen across the navigation tree. Passing this data through props across 4-5 screen layers ("prop drilling") creates unnecessary coupling and boilerplate code. Context API provides clean, direct access anywhere in the app tree via custom hooks like `useAuth()` and `useTheme()`.
- **One Drawback of Context:** Every component that consumes a context (via `useContext`) will automatically re-render whenever *any* property of the context value changes, even if that specific component only uses an un-changed property.

### Q7: Cart Reducer Test Cases Table & `useReducer` vs. `useState`

| # | Action | Initial State | Expected State |
|---|--------|---------------|----------------|
| 1 | `ADD_ITEM` (New Item) | `{ items: [], promoCode: null, discountPercent: 0 }` | `{ items: [{ id: '1', name: 'Biryani', quantity: 1 }], ... }` |
| 2 | `ADD_ITEM` (Existing) | `{ items: [{ id: '1', quantity: 1 }], ... }` | `{ items: [{ id: '1', quantity: 2 }], ... }` |
| 3 | `DECREMENT` (Qty > 1) | `{ items: [{ id: '1', quantity: 2 }], ... }` | `{ items: [{ id: '1', quantity: 1 }], ... }` |
| 4 | `DECREMENT` (Qty = 1) | `{ items: [{ id: '1', quantity: 1 }], ... }` | `{ items: [], ... }` |
| 5 | `APPLY_PROMO` (`FEAST20`) | `{ items: [...], promoCode: null, discountPercent: 0 }` | `{ items: [...], promoCode: 'FEAST20', discountPercent: 20 }` |
| 6 | `CLEAR_CART` | `{ items: [{ id: '1' }], promoCode: 'FEAST20', discountPercent: 20 }` | `{ items: [], promoCode: null, discountPercent: 0 }` |

- **`useReducer` vs `useState` for Cart Management:**
  The cart involves multiple interdependent state transitions (updating item lists, quantities, item notes, applying discount percentages, clearing state). `useReducer` consolidates all state logic into a single pure reducer function (`cartReducer.js`), making state mutations predictable and easy to test. `useState` would have been sufficient if the cart only held a simple count or a flat array without complex action types.

### Q8: When `useMemo` and `useCallback` Should NOT Be Used
- `useMemo` and `useCallback` introduce overhead because React must allocate memory to track dependencies and execute comparison checks on every render.
- **Do NOT use them when:**
  1. The computation is inexpensive (e.g. basic string concatenation or simple primitive math).
  2. The component renders infrequently or wraps simple primitive elements.
  3. The callback function is passed to basic native components like `<TouchableOpacity>` or `<View>` where reference stability provides no re-render prevention benefit.

---

## 🛠️ How to Setup & Run the App

### Prerequisites
- Node.js (v18 or higher recommended)
- Expo Go app on iOS / Android (for mobile testing)

### Installation Steps
```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/restaurant-app-mvp.git

# 2. Navigate to project directory
cd restaurant-app-mvp

# 3. Install dependencies
npm install

# 4. Start Expo development server
npx expo start
```

### Running Options:
- **Mobile (Expo Go):** Scan the terminal QR code using Expo Go app.
- **Web Browser:** Run `npx expo start --web` or press `w` in terminal.
