# Rate My Bite
https://project-9-client.vercel.app/

## 🍔 Overview
Rate My Bite is a Next.js web application where users can discover, post, and review street food spots. The platform features premium content for subscribed users, an admin dashboard for content moderation, and a comprehensive street food discovery experience.

## ✨ Features
- **User Authentication**
  - Register and login with email and password
  - JWT-based secure authentication
  - User profile management (update name, avatar, etc.)
- **Food Discovery**
  - Browse street food spots
  - Search by name, category, or location
  - Filter by price range, rating, and popularity
  - View detailed information about each food spot (images, reviews, location, price)
- **Content Creation**
  - Post new street food discoveries
  - Upload multiple images
  - Categorize and tag posts
  - Set price ranges and location information
- **Interaction**
  - Upvote/downvote food spots
  - Rate spots from 1-5 stars
  - Comment on posts
  - View user reviews and ratings
- **Premium Features**
  - Subscribe to access exclusive premium content
  - Integrated payment gateway (ShurjoPay/SSLCommerz)
  - Manage subscription status
- **Admin Dashboard**
  - Review and approve/reject pending posts
  - Mark content as premium/exclusive
  - Moderate comments and reviews
  - Manage users (ban, role change) and categories
- **Responsive Design**
  - Fully responsive UI for all devices
  - Mobile-first approach with Tailwind CSS

## 🛠️ Tech Stack
- **Framework**: Next.js (App Router)
- **Styling**: Tailwind CSS
- **Form Handling**: React Hook Form
- **Validation**: Zod
- **Authentication**: JWT (stored in HTTP-only cookies or localStorage)
- **Payment**: ShurjoPay / SSLCommerz
- **State Management**: Context API or Zustand (not specified)
- **Other**: TypeScript, Axios/Fetch for API calls

## 📂 Project Folder Structure
```text
project-9-client/
├── public/                  # Static assets
│   ├── images/
│   └── favicon.ico
├── src/
│   ├── app/                 # App Router (pages & layouts)
│   │   ├── api/             # (optional) Next.js API routes
│   │   ├── (auth)/          # Grouped auth routes
│   │   ├── posts/           # /posts & /posts/[id]
│   │   ├── profile/
│   │   ├── admin/           # Protected admin routes
│   │   ├── layout.tsx
│   │   ├── page.tsx         # Home
│   │   └── globals.css
│   ├── components/          # Reusable components
│   │   ├── ui/              # Button, Card, Modal, etc.
│   │   ├── layout/          # Navbar, Footer, Sidebar
│   │   ├── food/            # FoodSpotCard, FoodSpotDetail, RatingStars
│   │   ├── auth/            # LoginForm, RegisterForm
│   │   ├── admin/           # Admin tables, modals
│   │   └── premium/         # Premium badge, gated content
│   ├── lib/                 # Utilities
│   │   ├── auth.ts          # JWT & auth helpers
│   │   ├── api.ts           # API client (axios/fetch)
│   │   └── constants.ts
│   ├── types/               # TypeScript interfaces
│   │   ├── index.ts
│   │   ├── user.ts
│   │   ├── post.ts
│   │   └── ...
│   ├── hooks/               # Custom hooks (useAuth, usePayment, etc.)
│   └── models/              # Data model interfaces (optional)
├── .env.local               # Environment variables (gitignored)
├── next.config.ts
├── tailwind.config.js
├── tsconfig.json
├── package.json
└── README.md
```


## 📊 Data Models (TypeScript Interfaces)

```ts
// src/types/user.ts
export interface User {
  _id: string;
  name: string;
  email: string;
  role: 'user' | 'admin';
  avatar?: string;
  isPremium: boolean;
  subscription?: {
    status: 'active' | 'expired' | 'cancelled';
    expiryDate?: string;
  };
  createdAt: string;
}

// src/types/post.ts
export interface FoodSpot {
  _id: string;
  title: string;
  description: string;
  images: string[];           // Array of image URLs
  category: string;
  tags?: string[];
  priceRange: string;         // e.g. "10-50 TK"
  location: {
    city: string;
    area?: string;
    coordinates?: { lat: number; lng: number };
  };
  rating: number;             // Average rating (1-5)
  ratingsCount: number;
  upvotes: number;
  downvotes: number;
  isPremium: boolean;
  status: 'pending' | 'approved' | 'rejected';
  author: User | string;
  createdAt: string;
  updatedAt: string;
}

// src/types/review.ts
export interface Review {
  _id: string;
  postId: string;
  user: User | string;
  rating: number;             // 1-5
  comment: string;
  createdAt: string;
}

// src/types/category.ts
export interface Category {
  _id: string;
  name: string;
  description?: string;
  icon?: string;
}
```

## 🔗 API Endpoints

The frontend communicates with the backend using a RESTful API.

**Base URL** (set in `.env.local` or `.env`):
```env
NEXT_PUBLIC_API=https://your-api-domain.com/api
# Example: https://api.yourfoodapp.com/api

Authentication & User

POST /api/auth/register — Create new user account
POST /api/auth/login — Login and receive JWT token
POST /api/auth/logout — Logout (optional)
GET /api/auth/me — Get current logged-in user profile
PUT /api/auth/profile — Update profile (name, phone, avatar, etc.)
POST /api/auth/forgot-password — Send password reset email
POST /api/auth/reset-password — Reset password with token

Restaurants & Shops

GET /api/restaurants — List all restaurants (with filters & pagination)
GET /api/restaurants/:id — Get single restaurant details
GET /api/restaurants/:id/menu — Get menu items of a restaurant
GET /api/restaurants/nearby — Restaurants near user location
GET /api/restaurants/search — Search restaurants by name, cuisine, location

Menu & Items

GET /api/menu — Search menu items globally
GET /api/menu/:id — Get single food item details
GET /api/categories — List all categories / cuisines

Cart

GET /api/cart — Get current user's cart
POST /api/cart/add — Add item to cart
PUT /api/cart/update — Update item quantity
DELETE /api/cart/remove/:itemId — Remove item from cart
DELETE /api/cart/clear — Clear entire cart

Orders

POST /api/orders — Create new order (place order / checkout)
GET /api/orders — List user's order history
GET /api/orders/:id — Get order details
PUT /api/orders/:id/cancel — Cancel order (if allowed)
GET /api/orders/:id/status — Get order status

Payments

POST /api/payments/create — Create payment session (Razorpay, SSLCommerz, Stripe, etc.)
POST /api/payments/verify — Verify payment (webhook/callback)
GET /api/payments/:orderId — Get payment status for order

Addresses

GET /api/addresses — List saved delivery addresses
POST /api/addresses — Add new address
PUT /api/addresses/:id — Update address
DELETE /api/addresses/:id — Delete address
GET /api/addresses/default — Get default address

Reviews & Ratings

POST /api/reviews — Add review & rating
GET /api/reviews/restaurant/:id — Get reviews for a restaurant
GET /api/reviews/order/:id — Get review for specific order

Offers / Coupons

GET /api/offers — List active promotions
POST /api/coupons/apply — Apply coupon to cart/order
POST /api/coupons/validate — Check if coupon is valid

Admin / Restaurant Panel (protected)

GET /api/admin/orders — View all orders
PUT /api/admin/orders/:id/status — Update order status (preparing, delivered, etc.)
GET /api/restaurant-owner/menu — Get own restaurant menu
POST /api/restaurant-owner/items — Add new menu item

Note:

Most endpoints support query parameters for filtering, sorting, pagination (e.g. ?page=1&limit=20&sort=rating).
Refer to your backend documentation or Swagger/OpenAPI for exact request/response formats and required fields.

```
