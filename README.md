# Hotel Booking — Frontend

**Overview:**

- This repository contains the frontend for the Hotel Booking app (React + Vite + TypeScript + MUI).

**Deployment:**

- **Frontend (Netlify):** https://grandbooking.netlify.app/
- **Backend (Railway):** https://hotelbookbackend.up.railway.app (used by the frontend)

**Quick Setup**

- **Prerequisites:** Node.js (16+), npm
- Clone and install:

  npm install

- Start dev server:

  npm run dev

- Build for production:

  npm run build

- Serve production build locally (optional):

  npm run preview

**Config / API Base URL**

- The frontend points to the backend API at [src/services/api.ts](src/services/api.ts).
- By default this project currently uses the Railway URL: `https://hotelbookbackend.up.railway.app/api`.
- To change to a different backend URL, either update `API_BASE_URL` in [src/services/api.ts](src/services/api.ts) or (recommended) switch to an env variable (`VITE_API_BASE_URL`) and use it when creating the axios client.

Example to use an env var in [src/services/api.ts](src/services/api.ts):

```ts
const API_BASE_URL =
  import.meta.env.VITE_API_BASE_URL ||
  "https://hotelbookbackend.up.railway.app/api";
```

Then set the variable locally in a `.env` file at project root:

    VITE_API_BASE_URL=https://hotelbookbackend.up.railway.app/api

And add the same value to Netlify's site settings under Environment → Build & deploy → Environment variables.

**Assumptions**

- Authentication uses JWT stored in `localStorage` (`token`) and `user` (JSON). See [src/services/api.ts](src/services/api.ts) for the axios interceptor.
- Phone numbers are expected to be exactly 10 digits (numeric only) on the frontend; the field enforces digits and a 10-character limit.
- `numberOfRooms` is limited by the UI (`min:1, max:10`). The backend should re-validate capacity and limits.
- Dates use native HTML date inputs; the frontend enforces `checkOutDate` > `checkInDate`.
- CORS must be enabled on the backend for the frontend origin (Netlify URL).

**Room Availability Logic (Frontend)**

- The frontend calls the backend endpoint via `hotelService.checkAvailability(hotelId, roomType, checkInDate, checkOutDate, numberOfRooms)` (see [src/services/api.ts](src/services/api.ts)).
- The backend response should include at least two properties used by the UI:
  - `available` (boolean) — whether the requested rooms are available
  - `availableRooms` (number) — how many rooms are free for the selected type/dates
- On successful availability check, the UI shows a green or red card and a Snackbar with the result. If `available` is true the Confirm button becomes enabled.
- The frontend computes total price in the details view as:

  Total Price = roomPrice _ numberOfRooms _ nights

  where `nights` is calculated from the date difference (the UI uses a days difference with Math.ceil).

- The frontend does not mutate availability on check; final booking is performed via `bookingService.createBooking(...)` and the backend must decrement availability / reserve rooms.

---
