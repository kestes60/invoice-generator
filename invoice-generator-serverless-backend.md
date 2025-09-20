The Stripe Error: Why priceData Is Invalid
The key error—"Invalid stripe.redirectToCheckout parameter: lineItems.0.priceData is not an accepted parameter"—happens because priceData (and lineItems with dynamic pricing) is not supported in client-side redirectToCheckout. This method is deprecated and limited: It only accepts a sessionId (from a server-created Checkout Session) or pre-defined items (legacy format with fixed price IDs from your Stripe dashboard, not dynamic priceData for variable amounts like your invoice total).

From Stripe docs and community reports (e.g., Stack Overflow threads on similar errors), client-side dynamic pricing requires a backend to create the session securely (using your secret key). Your static site can't do this alone—priceData is server-side only, causing the rejection. The "DEMO MODE" fallback isn't triggering because initialization succeeded, but the param fails validation.stackoverflow.com

Solution: Add a Serverless Backend (Vercel Handles This Easily)
For your static app, create a simple /api/create-checkout-session.js endpoint (Vercel auto-turns it into a secure function—no full server needed). This creates the session with dynamic price_data server-side, then passes sessionId client-side.

Step 1: Install Stripe SDK

bash
cd ~/code/invoice-generator
npm install stripe
Step 2: Create the API File
Make a new folder/file: mkdir api && touch api/create-checkout-session.js
Paste this code (replace sk_test_your_secret_key with your Stripe secret key from dashboard.stripe.com > Developers > API keys > Test mode):

javascript
const stripe = require('stripe')('sk_test_your_secret_key_here');

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    res.setHeader('Allow', 'POST');
    res.status(405).end('Method Not Allowed');
    return;
  }

  try {
    const { amountCents, invoiceNumber } = req.body; // From frontend: e.g., 82150 for $821.50

    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: [{
        price_data: {
          currency: 'usd',
          product_data: {
            name: 'Yahtsar LLC Invoice Payment',
            description: `Payment for invoice #${invoiceNumber}`,
          },
          unit_amount: amountCents, // Dynamic amount in cents
        },
        quantity: 1,
      }],
      mode: 'payment',
      success_url: `${req.headers.origin}/?payment=success`,
      cancel_url: `${req.headers.origin}/?payment=cancel`,
    });

    res.status(200).json({ sessionId: session.id });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
}
Step 3: Update Frontend (index.html JS)
Replace your createCheckoutSession() function with this (calls the API, then redirects with sessionId):

javascript
async function createCheckoutSession() {
  try {
    setPremiumButtonLoading(true);

    const calculations = updateCalculations();
    const invoiceTotal = calculations.grandTotal;
    const amountCents = Math.max(500, Math.round(invoiceTotal * 100)); // $5 min
    const invoiceNumber = document.getElementById('invoice-number')?.value || 'INV-2025-09-18-001';

    console.log('💰 Charging $' + (amountCents / 100).toFixed(2));

    // Call serverless API
    const response = await fetch('/api/create-checkout-session', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ amountCents, invoiceNumber }),
    });

    const { sessionId } = await response.json();

    // Redirect with sessionId (valid client-side param)
    const stripe = Stripe(STRIPE_CONFIG.publishableKey);
    const { error } = await stripe.redirectToCheckout({ sessionId });

    if (error) {
      console.error('Redirect error:', error);
      showMessage(error.message, 'error');
    }
  } catch (error) {
    console.error('API error:', error);
    showMessage(error.message, 'error');
  } finally {
    setPremiumButtonLoading(false);
  }
}
