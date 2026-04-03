# 🎬 CINEMAX — Movie Ticket Booking System

A modern, responsive movie ticket booking web application built with vanilla HTML, CSS, and JavaScript. Features real-time seat selection, user authentication, and integration with Google Sheets for persistent data storage.

---

## ✨ Features

- **Movie Selection**: Browse available movies with ratings, duration, and pricing
- **Dynamic Date & Showtime Selection**: Auto-filtered showtimes for today (no past times)
- **Interactive Seat Map**: Visual 8×12 seat matrix with real-time availability
- **Persistent Seat Blocking**: Booked seats remain blocked across sessions via localStorage + Google Sheets
- **User Authentication**: Simple login/logout with sessionStorage
- **Booking History**: View all bookings with detailed information
- **Google Sheets Integration**: Automatic sync of bookings to Google Sheets
- **Responsive Design**: Mobile-friendly interface with smooth animations
- **Dark Theme**: Modern dark UI with gold accents

---

## 📁 Project Structure

```
CINEMAX/
├── movie_booking.html      # Main ticket booking page
├── login.html              # User login page
├── account.html            # User account & profile page
├── my_bookings.html        # Booking history from Google Sheets
├── .gitignore              # Git ignore rules
└── README.md               # This file
```

---

## 🚀 Quick Start

### 1. Open in Browser
Simply open `movie_booking.html` in any modern web browser (Chrome, Firefox, Safari, Edge).

```bash
# Option 1: Direct file open
open movie_booking.html

# Option 2: Using a local server (recommended)
python -m http.server 8000
# Then visit http://localhost:8000/
```

### 2. Login Flow
- Click **Login** in header → redirects to `login.html`
- Enter email and password (demo accepts any values)
- Session stored in `sessionStorage` (lasts until tab is closed)
- Redirects to `account.html` on success
- To use with real auth: modify `/login` endpoint in `login.html`

### 3. Book Tickets
1. Open `movie_booking.html` or click "Now Showing" from account page
2. Click any movie to open booking modal
3. **Step 1**: Select a date (today, tomorrow, etc.)
4. **Step 2**: Choose showtime (filtered based on date)
5. **Step 3**: Click seats to select (max 8 per booking)
6. **Step 4**: Enter name, email, phone, payment method
7. Click **Confirm & Pay** → Booking saves to localStorage + Google Sheets

### 4. View Bookings
- Click **My Account** → Standalone account page with booking history
- Click **My Bookings** → Pulls from Google Sheets (requires config)

---

## 🔧 Configuration

### Google Sheets Integration

To enable automatic booking sync to Google Sheets:

1. **Create a Google Apps Script Web App**
   - Go to [Google Apps Script](https://script.google.com)
   - Create a new project
   - Paste the following code:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSheet();
  const payload = JSON.parse(e.postData.contents);
  
  const row = [
    payload.bookingId,
    payload.movie,
    payload.date,
    payload.showtime,
    payload.seats,
    payload.seatCount,
    payload.total,
    payload.name,
    payload.email,
    payload.phone,
    payload.payment,
    new Date().toISOString()
  ];
  
  sheet.appendRow(row);
  return ContentService.createTextOutput('{"status":"success"}')
    .setMimeType(ContentService.MimeType.JSON);
}

function doGet(e) {
  if (e.parameter.action === "getBookedSeats") {
    const sheet = SpreadsheetApp.getActiveSheet();
    const data = sheet.getDataRange().getValues();
    // Parse and return booked seats map
  }
  return ContentService.createTextOutput('{"error":"Not found"}');
}
```

2. **Deploy as Web App**
   - Select "Me" as executor
   - Select "Anyone" for access
   - Copy the deployment URL

3. **Update `movie_booking.html`**
   - Find: `const APPS_SCRIPT_URL =`
   - Replace placeholder with your Web App URL

4. **Verify Sheet Headers**
   - Columns: Booking ID, Movie, Date, Showtime, Seats, Seat Count, Total, Name, Email, Phone, Payment, Timestamp

---

## 💾 Data Persistence

### localStorage
- **Stored Bookings**: `localStorage.getItem('bookings')` → Array of booking objects
- **Taken Seats**: `localStorage.getItem('takenSeats')` → Map of booked seats per show
- **User Login**: `localStorage.getItem('user')` → User email & login state

### sessionStorage
- **Session User**: `sessionStorage.getItem('cinemaUser')` → User data for current session

### Google Sheets (Optional)
- All new bookings POST to Sheets if configured
- `my_bookings.html` fetches booking history from Sheets

---

## 📱 Responsive Breakpoints

- **Desktop**: Full layout, 3-column seat grid
- **Tablet**: 2-column seat grid, optimized spacing
- **Mobile**: Single column, dropdown navigation, compact seat selection

---

## 🎨 Theme Colors

| Variable | Value | Usage |
|----------|-------|-------|
| `--gold` | #f0b429 | Primary highlight, buttons |
| `--red` | #e53e3e | Error states, logout |
| `--text` | #f0f0f5 | Main text |
| `--muted` | #7878a0 | Secondary text |
| `--bg` | #0a0a0f | Background |
| `--surface` | #13131a | Card backgrounds |

---

## 🎯 Usage Examples

### Scenario 1: First-Time User
1. Open `movie_booking.html`
2. Browse movies in the hero section
3. Click a movie to open booking modal
4. Select date, time, seats, and details
5. Confirm booking (saves to localStorage automatically)
6. Booked seats now show as "blocked" for other users

### Scenario 2: View Past Bookings
1. Click **My Bookings** in header
2. Enter email & password on login page
3. View all bookings with seat details and amounts paid

### Scenario 3: Refresh Page & See Booked Seats
1. Book tickets
2. Refresh page or close tab
3. Open movie booking again
4. Previously booked seats still show as blocked ✓

---

## 🔐 Security Notes

⚠️ **Demo Application**: This is a frontend-only demo. For production:

- **Authentication**: Implement proper backend auth (OAuth, JWT, etc.)
- **Payment**: Integrate real payment gateway (Stripe, Razorpay, etc.)
- **Validation**: Add server-side validation for all inputs
- **CORS**: Configure CORS policies on Google Sheets endpoint
- **Rate Limiting**: Protect against concurrent overbooking
- **HTTPS**: Always use HTTPS in production

---

## 🖥️ Browser Compatibility

| Browser | Version | Status |
|---------|---------|--------|
| Chrome | Latest | ✅ Full support |
| Firefox | Latest | ✅ Full support |
| Safari | Latest | ✅ Full support |
| Edge | Latest | ✅ Full support |
| IE 11 | - | ❌ Not supported |

---

## 📋 File Descriptions

### `movie_booking.html`
Main application page with:
- Movie grid and hero section
- Booking modal with date/time/seat selection
- Form for user details
- Seat availability management
- Google Sheets integration

### `login.html`
Authentication entry point:
- Email & password form
- Basic session management
- Redirect to account on login
- Demo accepts any credentials

### `account.html`
User profile page:
- User information display
- My Bookings section
- Account settings
- Logout button

### `my_bookings.html`
Advanced booking history:
- Pulls real data from Google Sheets
- Shows booking stats (total spent, seats booked, etc.)
- Detailed booking card layout
- Requires Google Sheets configuration

---

## 🐛 Troubleshooting

**Q: Booked seats disappearing after refresh?**
- A: Ensure Google Sheets URL is configured or bookings are in localStorage

**Q: Seats showing as available but can't select?**
- A: Clear localStorage and refresh
  ```javascript
  localStorage.removeItem('takenSeats');
  localStorage.removeItem('bookings');
  location.reload();
  ```

**Q: "Select a date and showtime" error?**
- A: Make sure to select valid date AND time before selecting seats

**Q: Google Sheets bookings not syncing?**
- A: Check browser console for errors, verify Apps Script URL is correct

---

## 📚 Technologies Used

- **HTML5**: Semantic markup
- **CSS3**: Flexbox, Grid, animations, variables
- **JavaScript (ES6+)**: Async/await, arrow functions, template literals
- **Google Apps Script**: Serverless backend for data persistence
- **localStorage & sessionStorage**: Client-side persistence

---

## 🤝 Contributing

To extend this project:

1. Add more movies to `MOVIES` array
2. Customize colors in CSS `:root`
3. Modify seat map layout via `ROWS` and `SEATS_PER_ROW`
4. Connect real backend authentication
5. Integrate actual payment processing

---

## 📝 License

This project is provided as-is for educational purposes.

---

## 👨‍💻 Author & Support

For bugs, features, or questions:
- Review inline code comments for detailed explanations
- Check Google Sheets integration docs for setup
- Modify `APPS_SCRIPT_URL` to point to your own backend

**Enjoy your CINEMAX experience! 🎬🍿**
