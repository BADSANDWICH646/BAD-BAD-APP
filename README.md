

/* ========================= FILE: package.json ========================= */ { "name": "bad-sandwich", "private": true, "version": "1.0.0", "type": "module", "scripts": { "dev": "vite", "build": "vite build", "preview": "vite preview" }, "dependencies": { "@stripe/stripe-js": "^3.0.0", "@supabase/supabase-js": "^2.39.0", "react": "^18.3.1", "react-dom": "^18.3.1", "react-router-dom": "^6.22.3" }, "devDependencies": { "@vitejs/plugin-react": "^4.2.0", "autoprefixer": "^10.4.18", "postcss": "^8.4.35", "tailwindcss": "^3.4.1", "vite": "^5.1.0" } }

/* ========================= FILE: src/main.jsx ========================= */ import React from 'react' import ReactDOM from 'react-dom/client' import { BrowserRouter } from 'react-router-dom' import App from './App' import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render( <BrowserRouter> <App /> </BrowserRouter> )

/* ========================= FILE: src/App.jsx ========================= */ import { Routes, Route } from 'react-router-dom' import Home from './pages/Home' import Pricing from './pages/Pricing' import Dashboard from './pages/Dashboard' import Merch from './pages/Merch' import Login from './pages/Login'

export default function App() { return ( <Routes> <Route path="/" element={<Home />} /> <Route path="/pricing" element={<Pricing />} /> <Route path="/dashboard" element={<Dashboard />} /> <Route path="/merch" element={<Merch />} /> <Route path="/login" element={<Login />} /> </Routes> ) }

/* ========================= FILE: src/lib/supabase.js ========================= */ import { createClient } from '@supabase/supabase-js'

export const supabase = createClient( import.meta.env.VITE_SUPABASE_URL, import.meta.env.VITE_SUPABASE_ANON_KEY )

/* ========================= FILE: src/lib/stripe.js ========================= */ import { loadStripe } from '@stripe/stripe-js' export const stripePromise = loadStripe(import.meta.env.VITE_STRIPE_PUBLISHABLE_KEY)

/* ========================= FILE: src/pages/Pricing.jsx ========================= */ import { stripePromise } from '../lib/stripe'

const plans = [ { id: 'price_peer', name: 'Peer', price: '$9/mo' }, { id: 'price_pro', name: 'Pro', price: '$29/mo' }, { id: 'price_impact', name: 'Impact', price: '$59/mo' } ]

export default function Pricing() { const subscribe = async (priceId) => { const stripe = await stripePromise await stripe.redirectToCheckout({ lineItems: [{ price: priceId, quantity: 1 }], mode: 'subscription', successUrl: window.location.origin + '/dashboard', cancelUrl: window.location.origin + '/pricing' }) }

return ( <div className="p-8 grid grid-cols-1 md:grid-cols-3 gap-6"> {plans.map(p => ( <div key={p.id} className="border rounded-xl p-6"> <h2 className="text-2xl font-bold">{p.name}</h2> <p className="my-4">{p.price}</p> <button onClick={() => subscribe(p.id)} className="bg-black text-white px-4 py-2 rounded">Subscribe</button> </div> ))} </div> ) }

/* ========================= FILE: src/pages/Dashboard.jsx ========================= */ export default function Dashboard() { return ( <div className="p-8"> <h1 className="text-3xl font-bold">Bad Sandwich Dashboard</h1> <ul className="mt-4 space-y-2"> <li>✔ Peer‑to‑Peer Support</li> <li>✔ Therapist & Legal Advocacy Access</li> <li>✔ Sponsored Subscriptions (Bipolar I, II, TBI)</li> </ul> </div> ) }

/* ========================= FILE: src/pages/Merch.jsx ========================= */ export default function Merch() { const items = ['T‑Shirt', 'Hoodie', 'Hat', 'Journal', 'Backpack'] return ( <div className="p-8 grid grid-cols-2 md:grid-cols-3 gap-4"> {items.map(i => ( <div key={i} className="border p-4 rounded"> <h3 className="font-bold">{i}</h3> <p>Print‑on‑Demand Fulfilled</p> <button className="mt-2 bg-black text-white px-3 py-1 rounded">Buy</button> </div> ))} </div> ) }

/* ========================= FILE: src/pages/Home.jsx ========================= */ export default function Home() { return ( <div className="p-12 text-center"> <h1 className="text-4xl font-black">BAD SANDWICH</h1> <p className="mt-4">Bipolar Awareness Demonstration — Shedding All Needless Doubt</p> </div> ) }

/* ========================= FILE: supabase.sql ========================= */ -- USERS create table users ( id uuid primary key default gen_random_uuid(), email text unique, role text, created_at timestamp default now() );

-- SUBSCRIPTIONS create table subscriptions ( id uuid primary key default gen_random_uuid(), user_id uuid references users(id), stripe_customer_id text, tier text, status text );

-- PROFESSIONAL VENDORS create table vendors ( id uuid primary key default gen_random_uuid(), name text, credentials text, approved boolean default false );

-- INSURANCE PARTNERS create table insurance_providers ( id uuid primary key default gen_random_uuid(), company text, state text, revenue_cap numeric default 0.30 );

/* ========================= ENVIRONMENT VARIABLES ========================= */ VITE_SUPABASE_URL= VITE_SUPABASE_ANON_KEY= VITE_STRIPE_PUBLISHABLE_KEY=

/* ========================= DEPLOYMENT ========================= */



