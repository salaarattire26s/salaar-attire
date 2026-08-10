# Prerequisites: Install Node.js 18+ (nodejs.org)

# Create project directory
mkdir salaar-attire && cd salaar-attire

# Initialize Next.js (full-stack ready)
npx create-next-app@latest . --typescript --tailwind --app

# Install core dependencies
npm install prisma @prisma/client stripe axios bcryptjs jsonwebtoken
npm install --save-dev stripe nodemon

# Install AI/email dependencies
npm install openai resend brevo axios
salaar-attire/
├── app/
│   ├── layout.tsx           # Global layout (navbar, footer)
│   ├── page.tsx             # Homepage
│   ├── api/
│   │   ├── products/        # GET /api/products
│   │   ├── orders/          # POST /api/orders
│   │   ├── chat/            # POST /api/chat (AI chatbot)
│   │   ├── subscribe/       # POST /api/subscribe (email)
│   │   ├── stripe/          # Stripe webhooks
│   │   └── auth/            # User auth
│   ├── products/
│   │   └── [slug]/          # Dynamic product pages
│   ├── collections/
│   │   └── [slug]/          # Collection pages
│   ├── cart/                # Shopping cart
│   ├── checkout/            # Checkout flow
│   └── admin/               # Admin dashboard (manage products)
├── components/
│   ├── Navbar.tsx
│   ├── Footer.tsx
│   ├── ProductCard.tsx
│   ├── ChatBot.tsx
│   └── ...
├── lib/
│   ├── db.ts                # Database connection
│   ├── stripe.ts            # Stripe helpers
│   └── email.ts             # Email service
├── prisma/
│   └── schema.prisma        # Database schema
├── public/
│   └── fonts/               # Your custom fonts
├── styles/
│   └── globals.css          # Global Tailwind + custom CSS
└── .env.local               # Secrets (API keys, DB URL, etc.)
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Product {
  id        String   @id @default(cuid())
  title     String
  slug      String   @unique
  description String
  price     Float
  originalPrice Float?
  images    String[]
  collection String
  variants  Variant[]
  inventory Int
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Variant {
  id        String   @id @default(cuid())
  productId String
  product   Product  @relation(fields: [productId], references: [id])
  size      String
  color     String
  stock     Int
}

model Customer {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  newsletter Boolean @default(false)
  orders    Order[]
  createdAt DateTime @default(now())
}

model Order {
  id        String   @id @default(cuid())
  customerId String
  customer  Customer @relation(fields: [customerId], references: [id])
  items     OrderItem[]
  total     Float
  status    String   @default("pending")
  stripeId  String?
  paypalId  String?
  createdAt DateTime @default(now())
}

model OrderItem {
  id        String   @id @default(cuid())
  orderId   String
  order     Order    @relation(fields: [orderId], references: [id])
  productId String
  quantity  Int
  price     Float
}
/* ============================================
   SALAAR ATTIRE — LUXURY MINIMALIST AESTHETIC
   ============================================ */

/* Color System */
:root {
  --primary: #0A0E1A;         /* Deep Navy */
  --accent: #C9A84C;          /* Gold */
  --bg: #FFFFFF;              /* Clean White */
  --text: #1a1a1a;            /* Near Black */
  --border: #E8E8E8;          /* Subtle Gray */
  --success: #2ecc71;
  --error: #e74c3c;
  
  /* Typography */
  --serif: 'Canela', 'Cormorant Garamond', serif;
  --sans: 'Josefin Sans', 'Inter', sans-serif;
  
  /* Spacing */
  --spacing-xs: 0.5rem;
  --spacing-sm: 1rem;
  --spacing-md: 1.5rem;
  --spacing-lg: 2.5rem;
  --spacing-xl: 4rem;
  
  /* Transitions */
  --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Typography */
@font-face {
  font-family: 'Canela';
  src: url('/fonts/Canela-Regular.woff2') format('woff2');
  font-display: swap;
  font-weight: 400;
}

@font-face {
  font-family: 'Canela';
  src: url('/fonts/Canela-Bold.woff2') format('woff2');
  font-display: swap;
  font-weight: 700;
}

@font-face {
  font-family: 'Josefin Sans';
  src: url('/fonts/JosefinSans-Regular.woff2') format('woff2');
  font-display: swap;
}

/* Reset & Base Styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: var(--sans);
  color: var(--text);
  background: var(--bg);
  line-height: 1.6;
  letter-spacing: 0.5px;
}

h1, h2, h3, h4, h5, h6 {
  font-family: var(--serif);
  font-weight: 600;
  letter-spacing: -0.5px;
}

h1 { font-size: 3.5rem; line-height: 1.1; }
h2 { font-size: 2.5rem; line-height: 1.2; }
h3 { font-size: 1.8rem; line-height: 1.3; }
h4 { font-size: 1.3rem; }

p {
  font-size: 1rem;
  line-height: 1.8;
}

/* Button Styles */
.btn {
  padding: var(--spacing-sm) var(--spacing-lg);
  border: 2px solid var(--text);
  background: transparent;
  color: var(--text);
  font-family: var(--sans);
  cursor: pointer;
  transition: var(--transition);
  text-transform: uppercase;
  font-size: 0.875rem;
  letter-spacing: 1px;
}

.btn:hover {
  background: var(--primary);
  color: var(--accent);
  border-color: var(--accent);
}

.btn--primary {
  background: var(--primary);
  color: var(--bg);
  border-color: var(--primary);
}

.btn--primary:hover {
  background: var(--accent);
  border-color: var(--accent);
}

/* Product Card */
.product-card {
  position: relative;
  overflow: hidden;
  cursor: pointer;
}

.product-card img {
  width: 100%;
  height: 450px;
  object-fit: cover;
  transition: var(--transition);
}

.product-card:hover img {
  opacity: 0.9;
  transform: scale(1.02);
}

.product-info {
  padding: var(--spacing-md);
}

.product-title {
  font-size: 1.1rem;
  margin-bottom: var(--spacing-xs);
}

.product-price {
  font-family: var(--sans);
  font-size: 1rem;
  color: var(--accent);
  font-weight: 600;
}

/* Footer */
footer {
  border-top: 1px solid var(--border);
  padding: var(--spacing-xl) var(--spacing-md);
  text-align: center;
  color: var(--text);
  font-size: 0.875rem;
  letter-spacing: 0.5px;
}
import './globals.css'
import Navbar from '@/components/Navbar'
import Footer from '@/components/Footer'

export const metadata = {
  title: 'Salaar Attire | Luxury Fashion Beyond Imagination',
  description: 'Discover ultra-luxury women\'s fashion at Salaar Attire.',
  keywords: 'luxury fashion, evening wear, designer dresses',
  openGraph: {
    title: 'Salaar Attire',
    description: 'Luxury Fashion Beyond Imagination',
    images: ['/og-image.png'],
  },
}

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <head>
        <meta name="viewport" content="width=device-width, initial-scale=1" />
      </head>
      <body>
        <Navbar />
        <main>{children}</main>
        <Footer />
      </body>
    </html>
  )
}
'use client'

import Link from 'next/link'
import { useState } from 'react'

export default function Navbar() {
  const [menuOpen, setMenuOpen] = useState(false)

  return (
    <nav className="fixed top-0 w-full bg-white border-b border-gray-200 z-50">
      <div className="max-w-7xl mx-auto px-6 py-4 flex justify-between items-center">
        <Link href="/" className="text-2xl font-bold tracking-widest">
          SALAAR
        </Link>

        <ul className="hidden md:flex gap-8 text-sm uppercase tracking-wide">
          <li><Link href="/collections/dresses">Dresses</Link></li>
          <li><Link href="/collections/eveningwear">Evening Wear</Link></li>
          <li><Link href="/collections/wedding">Wedding</Link></li>
          <li><Link href="/about">About</Link></li>
          <li><Link href="/contact">Contact</Link></li>
        </ul>

        <div className="flex gap-4 items-center">
          <Link href="/cart" className="text-sm">Cart</Link>
          <Link href="/account" className="text-sm">Account</Link>
        </div>
      </div>
    </nav>
  )
}
export default function Footer() {
  return (
    <footer className="bg-white border-t border-gray-200 mt-20">
      <div className="max-w-7xl mx-auto px-6 py-12">
        
        <div className="grid grid-cols-4 gap-8 mb-12">
          <div>
            <h4 className="font-serif text-lg mb-4">Shop</h4>
            <ul className="text-sm space-y-2">
              <li><a href="/collections/dresses">Dresses</a></li>
              <li><a href="/collections/wedding">Wedding</a></li>
              <li><a href="/collections/eveningwear">Evening Wear</a></li>
            </ul>
          </div>

          <div>
            <h4 className="font-serif text-lg mb-4">Customer Care</h4>
            <ul className="text-sm space-y-2">
              <li><a href="/contact">Contact Us</a></li>
              <li><a href="/shipping">Shipping</a></li>
              <li><a href="/returns">Returns</a></li>
            </ul>
          </div>

          <div>
            <h4 className="font-serif text-lg mb-4">Company</h4>
            <ul className="text-sm space-y-2">
              <li><a href="/about">About</a></li>
              <li><a href="/privacy">Privacy Policy</a></li>
              <li><a href="/terms">Terms & Conditions</a></li>
            </ul>
          </div>

          <div>
            <h4 className="font-serif text-lg mb-4">Connect</h4>
            <ul className="text-sm space-y-2">
              <li><a href="https://instagram.com">Instagram</a></li>
              <li><a href="https://tiktok.com">TikTok</a></li>
              <li><a href="https://pinterest.com">Pinterest</a></li>
            </ul>
          </div>
        </div>

        <div className="border-t border-gray-200 pt-8 text-center">
          <p className="text-sm tracking-wider">
            Salaar Attire all rights reserved 2025 Shahab Shahzad LLC
          </p>
        </div>
      </div>
    </footer>
  )
}
import Link from 'next/link'

export default function Home() {
  return (
    <div>
      {/* Hero Section */}
      <section className="h-screen flex items-center justify-center bg-gradient-to-b from-gray-50 to-white pt-20">
        <div className="text-center max-w-2xl px-6">
          <h1 className="text-6xl font-serif mb-6 tracking-tight">
            Luxury Beyond Imagination
          </h1>
          <p className="text-lg text-gray-600 mb-8">
            Discover ultra-luxury women's fashion designed for the discerning eye.
          </p>
          <Link href="/collections" className="btn btn--primary">
            Shop Now
          </Link>
        </div>
      </section>

      {/* Featured Collections */}
      <section className="py-20 px-6">
        <div className="max-w-7xl mx-auto">
          <h2 className="text-4xl font-serif mb-12 text-center">Featured Collections</h2>
          <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
            {/* Collection Cards */}
            <Link href="/collections/evening">
              <div className="product-card group cursor-pointer">
                <img 
                  src="/images/evening-wear.jpg" 
                  alt="Evening Wear" 
                  className="w-full h-96 object-cover"
                />
                <div className="p-4">
                  <h3 className="text-xl font-serif">Evening Wear</h3>
                  <p className="text-sm text-gray-600">Timeless elegance</p>
                </div>
              </div>
            </Link>
            {/* Repeat for other collections */}
          </div>
        </div>
      </section>
    </div>
  )
}
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

// GET all products or filtered
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const collection = searchParams.get('collection')

  try {
    const products = await prisma.product.findMany({
      where: collection ? { collection } : {},
      include: { variants: true },
    })
    return Response.json(products)
  } catch (error) {
    return Response.json({ error: 'Failed to fetch products' }, { status: 500 })
  }
}
import Stripe from 'stripe'
import { PrismaClient } from '@prisma/client'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)
const prisma = new PrismaClient()

export async function POST(request: Request) {
  const { items, customerEmail } = await request.json()

  // Convert cart items to Stripe line items
  const lineItems = items.map((item: any) => ({
    price_data: {
      currency: 'gbp',
      product_data: {
        name: item.title,
        images: [item.image],
      },
      unit_amount: Math.round(item.price * 100), // Convert to cents
    },
    quantity: item.quantity,
  }))

  try {
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: lineItems,
      mode: 'payment',
      success_url: `${process.env.NEXT_PUBLIC_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${process.env.NEXT_PUBLIC_URL}/cart`,
      customer_email: customerEmail,
    })

    return Response.json({ sessionId: session.id })
  } catch (error) {
    return Response.json({ error: 'Checkout failed' }, { status: 500 })
  }
}
import axios from 'axios'

const PAYPAL_API = 'https://api.paypal.com/v1/oauth2/token'
const PAYPAL_CREATE_ORDER = 'https://api.paypal.com/v2/checkout/orders'

async function getPayPalAccessToken() {
  const auth = Buffer.from(
    `${process.env.PAYPAL_CLIENT_ID}:${process.env.PAYPAL_SECRET}`
  ).toString('base64')

  const response = await axios.post(PAYPAL_API, 'grant_type=client_credentials', {
    headers: { Authorization: `Basic ${auth}` },
  })

  return response.data.access_token
}

export async function POST(request: Request) {
  const { items, total } = await request.json()

  try {
    const accessToken = await getPayPalAccessToken()

    const order = await axios.post(
      PAYPAL_CREATE_ORDER,
      {
        intent: 'CAPTURE',
        purchase_units: [
          {
            amount: {
              currency_code: 'GBP',
              value: total.toString(),
              breakdown: {
                item_total: { currency_code: 'GBP', value: total.toString() },
              },
            },
            items: items.map((item: any) => ({
              name: item.title,
              unit_amount: { currency_code: 'GBP', value: item.price.toString() },
              quantity: item.quantity.toString(),
            })),
          },
        ],
        application_context: {
          return_url: `${process.env.NEXT_PUBLIC_URL}/success`,
          cancel_url: `${process.env.NEXT_PUBLIC_URL}/cart`,
        },
      },
      { headers: { Authorization: `Bearer ${accessToken}` } }
    )

    return Response.json({ orderId: order.data.id })
  } catch (error) {
    console.error('PayPal error:', error)
    return Response.json({ error: 'PayPal order creation failed' }, { status: 500 })
  }
}
import { OpenAI } from 'openai'

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
})

export async function POST(request: Request) {
  const { message, conversationHistory } = await request.json()

  try {
    const response = await openai.chat.completions.create({
      model: 'gpt-4-turbo',
      messages: [
        {
          role: 'system',
          content: `You are a luxury fashion customer service assistant for Salaar Attire. 
          You are knowledgeable about our collections (dresses, evening wear, wedding attire).
          You provide personalized recommendations, sizing guidance, and shipping information.
          Keep responses under 150 words. Be warm, professional, and sophisticated.
          Never discuss topics unrelated to our store.`,
        },
        ...conversationHistory,
        { role: 'user', content: message },
      ],
      max_tokens: 300,
      temperature: 0.7,
    })

    return Response.json({
      reply: response.choices[0].message.content,
    })
  } catch (error) {
    console.error('OpenAI error:', error)
    return Response.json({ error: 'Chat failed' }, { status: 500 })
  }
}
'use client'

import { useEffect, useState } from 'react'
import { useState as useCartState } from 'react'

export default function ProductPage({ params }: { params: { slug: string } }) {
  const [product, setProduct] = useState(null)
  const [selectedSize, setSelectedSize] = useState('')
  const [selectedColor, setSelectedColor] = useState('')
  const [quantity, setQuantity] = useState(1)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetch(`/api/products/${params.slug}`)
      .then(res => res.json())
      .then(data => {
        setProduct(data)
        setLoading(false)
      })
  }, [params.slug])

  const handleAddToCart = () => {
    const cartItem = {
      id: product.id,
      title: product.title,
      price: product.price,
      image: product.images[0],
      size: selectedSize,
      color: selectedColor,
      quantity,
    }
    
    // Save to localStorage for now
    const cart = JSON.parse(localStorage.getItem('cart') || '[]')
    cart.push(cartItem)
    localStorage.setItem('cart', JSON.stringify(cart))
    
    alert('Added to cart!')
  }

  if (loading) return <div className="h-screen flex items-center justify-center">Loading...</div>
  if (!product) return <div>Product not found</div>

  return (
    <div className="max-w-7xl mx-auto px-6 py-20">
      <div className="grid grid-cols-2 gap-12">
        
        {/* Images */}
        <div>
          <img 
            src={product.images[0]} 
            alt={product.title} 
            className="w-full aspect-square object-cover mb-4"
          />
          <div className="grid grid-cols-3 gap-4">
            {product.images.slice(1).map((img, i) => (
              <img 
                key={i} 
                src={img} 
                alt={`${product.title} ${i + 2}`}
                className="w-full aspect-square object-cover cursor-pointer hover:opacity-70"
              />
            ))}
          </div>
        </div>

        {/* Details */}
        <div>
          <h1 className="text-4xl font-serif mb-2">{product.title}</h1>
          <p className="text-gray-600 mb-6">{product.description}</p>
          
          <div className="mb-8">
            <p className="text-2xl font-serif text-amber-700 mb-2">
              £{product.price.toFixed(2)}
            </p>
            {product.originalPrice && (
              <p className="text-sm line-through text-gray-500">
                £{product.originalPrice.toFixed(2)}
              </p>
            )}
          </div>

          {/* Size Selection */}
          <div className="mb-6">
            <label className="block text-sm font-serif mb-3">Size</label>
            <div className="grid grid-cols-4 gap-2">
              {['XS', 'S', 'M', 'L', 'XL', 'XXL'].map(size => (
                <button
                  key={size}
                  onClick={() => setSelectedSize(size)}
                  className={`py-2 border-2 ${
                    selectedSize === size 
                      ? 'border-amber-700 bg-amber-700 text-white' 
                      : 'border-gray-300'
                  }`}
                >
                  {size}
                </button>
              ))}
            </div>
          </div>

          {/* Color Selection */}
          <div className="mb-8">
            <label className="block text-sm font-serif mb-3">Color</label>
            <div className="flex gap-4">
              {product.variants.map(variant => (
                <button
                  key={variant.id}
                  onClick={() => setSelectedColor(variant.color)}
                  className={`w-10 h-10 rounded-full border-2 ${
                    selectedColor === variant.color 
                      ? 'border-amber-700' 
                      : 'border-gray-300'
                  }`}
                  style={{ backgroundColor: variant.color }}
                  title={variant.color}
                />
              ))}
            </div>
          </div>

          {/* Quantity */}
          <div className="mb-8">
            <label className="block text-sm font-serif mb-3">Quantity</label>
            <div className="flex items-center gap-4">
              <button onClick={() => setQuantity(Math.max(1, quantity - 1))}>−</button>
              <input 
                type="number" 
                value={quantity} 
                onChange={(e) => setQuantity(Number(e.target.value))}
                className="w-16 text-center border border-gray-300 py-2"
              />
              <button onClick={() => setQuantity(quantity + 1)}>+</button>
            </div>
          </div>

          <button 
            onClick={handleAddToCart}
            className="btn btn--primary w-full mb-4"
          >
            Add to Cart
          </button>

          {/* Info Sections */}
          <div className="space-y-4 text-sm">
            <div className="border-t pt-4">
              <p className="font-serif mb-2">Shipping & Returns</p>
              <p className="text-gray-600">Free shipping on orders over £150. Returns accepted within 30 days.</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}
'use client'

import { useEffect, useState } from 'react'
import Link from 'next/link'
import { loadStripe } from '@stripe/js'

export default function CartPage() {
  const [cart, setCart] = useState([])
  const [loading, setLoading] = useState(false)

  useEffect(() => {
    const savedCart = JSON.parse(localStorage.getItem('cart') || '[]')
    setCart(savedCart)
  }, [])

  const total = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0)

  const handleCheckout = async () => {
    setLoading(true)

    // Create checkout session
    const response = await fetch('/api/checkout', {
      method: 'POST',
      body: JSON.stringify({
        items: cart,
        customerEmail: 'customer@example.com', // Get from user input
      }),
    })

    const { sessionId } = await response.json()

    // Redirect to Stripe
    const stripe = await loadStripe(process.env.NEXT_PUBLIC_STRIPE_KEY!)
    await stripe?.redirectToCheckout({ sessionId })

    setLoading(false)
  }

  const removeItem = (id: string) => {
    setCart(cart.filter(item => item.id !== id))
    localStorage.setItem('cart', JSON.stringify(cart.filter(item => item.id !== id)))
  }

  return (
    <div className="max-w-4xl mx-auto px-6 py-20">
      <h1 className="text-4xl font-serif mb-12">Shopping Cart</h1>

      {cart.length === 0 ? (
        <div className="text-center py-12">
          <p className="text-gray-600 mb-6">Your cart is empty</p>
          <Link href="/collections" className="btn btn--primary">
            Continue Shopping
          </Link>
        </div>
      ) : (
        <>
          <div className="space-y-6 mb-12">
            {cart.map((item, i) => (
              <div key={i} className="flex gap-6 border-b pb-6">
                <img src={item.image} alt={item.title} className="w-24 h-24 object-cover" />
                <div className="flex-1">
                  <h3 className="font-serif text-lg">{item.title}</h3>
                  <p className="text-sm text-gray-600">Size: {item.size} | Color: {item.color}</p>
                  <p className="text-amber-700 font-serif">£{item.price.toFixed(2)}</p>
                </div>
                <div className="text-right">
                  <p className="mb-4">Qty: {item.quantity}</p>
                  <button 
                    onClick={() => removeItem(item.id)}
                    className="text-sm text-red-600 hover:underline"
                  >
                    Remove
                  </button>
                </div>
              </div>
            ))}
          </div>

          <div className="bg-gray-50 p-8 rounded">
            <div className="flex justify-between text-lg font-serif mb-6">
              <span>Total</span>
              <span className="text-amber-700">£{total.toFixed(2)}</span>
            </div>

            <div className="space-y-3 mb-6">
              <button 
                onClick={handleCheckout}
                disabled={loading}
                className="btn btn--primary w-full"
              >
                {loading ? 'Processing...' : 'Checkout with Stripe'}
              </button>
              
              <button className="btn w-full">
                Checkout with PayPal
              </button>
            </div>

            <Link href="/collections" className="text-sm text-center block text-gray-600 hover:underline">
              Continue Shopping
            </Link>
          </div>
        </>
      )}
    </div>
  )
}
'use client'

import { useState } from 'react'

export default function AdminDashboard() {
  const [title, setTitle] = useState('')
  const [price, setPrice] = useState('')
  const [description, setDescription] = useState('')
  const [collection, setCollection] = useState('dresses')

  const handleAddProduct = async (e: React.FormEvent) => {
    e.preventDefault()

    const response = await fetch('/api/admin/products', {
      method: 'POST',
      body: JSON.stringify({
        title,
        price: parseFloat(price),
        description,
        collection,
        slug: title.toLowerCase().replace(/\s+/g, '-'),
      }),
    })

    if (response.ok) {
      alert('Product added!')
      setTitle('')
      setPrice('')
      setDescription('')
    }
  }

  return (
    <div className="max-w-2xl mx-auto px-6 py-20">
      <h1 className="text-4xl font-serif mb-12">Product Management</h1>

      <form onSubmit={handleAddProduct} className="space-y-6">
        <div>
          <label className="block text-sm font-serif mb-2">Product Title</label>
          <input 
            type="text" 
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            className="w-full border border-gray-300 p-3 rounded"
            required
          />
        </div>

        <div>
          <label className="block text-sm font-serif mb-2">Price (£)</label>
          <input 
            type="number" 
            step="0.01"
            value={price}
            onChange={(e) => setPrice(e.target.value)}
            className="w-full border border-gray-300 p-3 rounded"
            required
          />
        </div>

        <div>
          <label className="block text-sm font-serif mb-2">Description</label>
          <textarea 
            value={description}
            onChange={(e) => setDescription(e.target.value)}
            className="w-full border border-gray-300 p-3 rounded h-32"
            required
          />
        </div>

        <div>
          <label className="block text-sm font-serif mb-2">Collection</label>
          <select 
            value={collection}
            onChange={(e) => setCollection(e.target.value)}
            className="w-full border border-gray-300 p-3 rounded"
          >
            <option value="dresses">Dresses</option>
            <option value="evening">Evening Wear</option>
            <option value="wedding">Wedding</option>
          </select>
        </div>

        <button type="submit" className="btn btn--primary w-full">
          Add Product
        </button>
      </form>
    </div>
  )
}
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/salaar_attire

# Stripe
STRIPE_SECRET_KEY=sk_test_...
NEXT_PUBLIC_STRIPE_KEY=pk_test_...

# PayPal
PAYPAL_CLIENT_ID=xxx
PAYPAL_SECRET=xxx

# OpenAI
OPENAI_API_KEY=sk-xxx

# Email
RESEND_API_KEY=re_xxx

# Domain
NEXT_PUBLIC_URL=https://salaar-attire.com
