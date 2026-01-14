# Calorie Tracker API

A comprehensive calorie tracking backend application that uses AI to analyze food images and provide detailed nutritional information. Built with Node.js, TypeScript, Express, MongoDB, and OpenAI's Vision API.

## 🚀 Features

### Authentication
- User registration with email and password
- JWT-based authentication
- Password hashing with bcrypt
- Daily calorie goal customization

### Food Tracking
- **AI-Powered Food Analysis**: Upload food images to automatically detect:
  - Food name
  - Calories
  - Macronutrients (protein, carbs, fat)
  - Meal type (breakfast, lunch, dinner, snack)
- **Image Processing**: Automatic image optimization using Sharp
- **Cloud Storage**: Images stored in Cloudflare R2
- **CRUD Operations**: Save, retrieve, and discard food entries

### Reports & Analytics
- **Daily Reports**: Track calories consumed vs. goal with meal breakdown
- **Weekly Reports**: 7-day view with daily summaries and trends
- **Monthly Reports**: Full month analytics with highest consumption days
- **Macro Tracking**: Detailed protein, carbs, and fat breakdown with percentages

## 📁 Project Structure

```
backend/
├── config/
│   ├── db.ts              # MongoDB connection
│   └── r2.ts              # Cloudflare R2 configuration
├── controller/
│   ├── authController.ts   # Authentication logic
│   ├── foodController.ts   # Food entry management
│   └── reportsController.ts # Analytics and reports
├── middleware/
│   ├── auth.ts            # JWT authentication middleware
│   └── upload.ts          # Multer file upload middleware
├── models/
│   ├── User.ts            # User schema
│   └── FoodEntry.ts       # Food entry schema
├── routes/
│   ├── auth.ts            # Authentication routes
│   ├── food.ts            # Food tracking routes
│   └── reports.ts         # Reports routes
├── services/
│   ├── openai.ts          # OpenAI Vision API integration
│   └── colories.ts        # Calorie calculation services
└── server.ts              # Application entry point
```

## 🛠️ Tech Stack

- **Runtime**: Node.js with TypeScript
- **Framework**: Express 5
- **Database**: MongoDB with Mongoose
- **Authentication**: JWT + bcrypt
- **Image Processing**: Sharp
- **Cloud Storage**: Cloudflare R2 (S3-compatible)
- **AI/ML**: OpenAI Vision API
- **File Upload**: Multer

## 📦 Installation

1. **Clone the repository**
```bash
git clone https://github.com/mchamoudadev/colorie-tracker.git
cd colorie-tracker/backend
```

2. **Install dependencies**
```bash
npm install
```

3. **Set up environment variables**

Create a `.env` file in the backend directory:

```env
# Server
PORT=8000

# Database
MONGODB_URI=mongodb://localhost:27017/calorie-tracker

# JWT
JWT_SECRET=your_jwt_secret_key_here

# OpenAI
OPENAI_API_KEY=your_openai_api_key

# Cloudflare R2
R2_ACCOUNT_ID=your_cloudflare_account_id
R2_ACCESS_KEY_ID=your_r2_access_key
R2_SECRET_ACCESS_KEY=your_r2_secret_key
R2_BUCKET_NAME=your_bucket_name
R2_PUBLIC_URL=https://your-public-url.r2.dev
```

4. **Run the application**

Development mode:
```bash
npm run dev
```

Production build:
```bash
npm run build
npm start
```

## 🔌 API Endpoints

### Authentication

#### Register
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123",
  "name": "John Doe",
  "dailyColorieGoal": 2000
}
```

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

#### Get Current User
```http
GET /api/auth/me
Authorization: Bearer <token>
```

### Food Tracking

#### Analyze Food Image
Analyzes a food image and returns nutritional data without saving.

```http
POST /api/food/analyze
Authorization: Bearer <token>
Content-Type: multipart/form-data

image: <file>
```

Response:
```json
{
  "foodName": "Grilled Chicken Salad",
  "calories": 350,
  "protein": 35,
  "carbs": 20,
  "fat": 15,
  "mealType": "lunch",
  "imageUrl": "https://...",
  "storageKey": "colorie-tracker-rec/...",
  "imageBase64": "data:image/jpeg;base64,..."
}
```

#### Save Food Entry
Saves a food entry after user confirmation.

```http
POST /api/food/save
Authorization: Bearer <token>
Content-Type: application/json

{
  "foodName": "Grilled Chicken Salad",
  "calories": 350,
  "protein": 35,
  "carbs": 20,
  "fat": 15,
  "mealType": "lunch",
  "imageUrl": "https://...",
  "storageKey": "colorie-tracker-rec/..."
}
```

#### Discard Analyzed Food
Deletes the uploaded image from storage if user decides not to save.

```http
POST /api/food/discard
Authorization: Bearer <token>
Content-Type: application/json

{
  "storageKey": "colorie-tracker-rec/..."
}
```

### Reports

#### Daily Report
```http
GET /api/reports/daily?date=2026-01-10
Authorization: Bearer <token>
```

Response:
```json
{
  "date": "2026-01-10",
  "goal": 2000,
  "consumed": 1450,
  "remaining": 550,
  "percentComplete": 73,
  "isOverGoal": false,
  "macros": {
    "protein": { "grams": 85, "calories": 340, "percentage": 23.4 },
    "carbs": { "grams": 150, "calories": 600, "percentage": 41.4 },
    "fat": { "grams": 57, "calories": 510, "percentage": 35.2 }
  },
  "mealBreakdown": {
    "breakfast": 400,
    "lunch": 550,
    "dinner": 500,
    "snack": 0
  },
  "entriesCount": 3
}
```

#### Weekly Report
```http
GET /api/reports/weekly
Authorization: Bearer <token>
```

#### Monthly Report
```http
GET /api/reports/monthly?year=2026&month=1
Authorization: Bearer <token>
```

## 🔐 Authentication

All food tracking and reports endpoints require authentication. Include the JWT token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

## 🖼️ Image Processing

Uploaded images are automatically:
- Rotated based on EXIF data
- Resized to max 1024x1024 (maintaining aspect ratio)
- Compressed to JPEG with 85% quality
- Optimized with MozJPEG

## 🧮 Macro Calculations

The app calculates macronutrients based on standard conversions:
- **Protein**: 4 calories per gram
- **Carbohydrates**: 4 calories per gram
- **Fat**: 9 calories per gram

## 📊 Reports Features

### Daily Reports
- Current day's consumption vs. goal
- Macro breakdown
- Meal-by-meal breakdown
- Over/under goal indicator

### Weekly Reports
- Last 7 days of data
- Daily summaries with day names
- Average daily consumption
- Total entries and calories
- Weekly macro averages

### Monthly Reports
- Full month view
- Highest consumption day
- Days tracked
- Average daily intake
- Daily chart data

## 🚦 Error Handling

The API uses standard HTTP status codes:
- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `404` - Not Found
- `500` - Internal Server Error

## 📝 Development

### Scripts

- `npm run dev` - Start development server with hot reload
- `npm run build` - Compile TypeScript to JavaScript
- `npm start` - Run production server

### Environment

The application uses TypeScript with ES modules (`"type": "module"` in package.json).

## 🔧 Configuration

### MongoDB
The database connection is configured in `config/db.ts`. Default database name: `calorie-tracker`

### Cloudflare R2
R2 is configured in `config/r2.ts` using AWS SDK v3. Images are stored with the prefix `colorie-tracker-rec/`.

### OpenAI
The OpenAI service uses GPT-4 Vision to analyze food images. Configure your API key in the `.env` file.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

ISC

## 👤 Author

mchamoudadev

## 🐛 Known Issues

- Make sure MongoDB is running before starting the server
- Ensure Cloudflare R2 credentials are valid
- OpenAI API key must have access to Vision models

## 🔮 Future Enhancements

- [ ] Add food entry editing
- [ ] Add food entry deletion
- [ ] Implement food search/history
- [ ] Add weekly/monthly goal settings
- [ ] Export reports to PDF
- [ ] Add nutrition insights and recommendations
- [ ] Implement meal planning features
- [ ] Add barcode scanning for packaged foods

---

Built with ❤️ using TypeScript, Express, and OpenAI

