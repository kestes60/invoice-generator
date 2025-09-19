# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a simple invoice generator web application designed for freelancers and small businesses. The project is based on a Power Pack static starter template and follows a single-page application architecture with vanilla HTML, CSS, and JavaScript.

**Architecture**: Single-page application (SPA) with all functionality contained in `index.html`, `styles.css`, and inline JavaScript. No backend or database required - entirely client-side.

**Target Implementation**: As described in `invoice-generator-PRD.md`, this will become a PDF invoice generator with:
- Real-time invoice form and preview
- PDF generation using jsPDF library
- Stripe integration for premium features ($5 unlock to remove watermarks)
- Client-side only with localStorage persistence

## Key Development Commands

```bash
# Set Node version (uses LTS)
nvm use

# Development server (Vercel dev environment)
npm run dev

# Deploy to production (Vercel)
npm run deploy

# Authentication setup (one-time)
npm run login
```

## Project Structure

- **`index.html`** - Main application entry point (currently starter template, needs invoice generator implementation)
- **`styles.css`** - Application styles (currently minimal starter styles)
- **`package.json`** - Contains only deployment scripts for Vercel
- **`vercel.json`** - Vercel deployment configuration (clean URLs, no trailing slash)
- **`invoice-generator-PRD.md`** - Complete product requirements document with detailed feature specifications
- **`.nvmrc`** - Node version specification (LTS)

## Technical Constraints

1. **Single File Architecture**: All functionality must be contained within `index.html` with embedded CSS and JavaScript
2. **No Backend**: Client-side only application - no server, no database
3. **CDN Libraries Only**: Use CDN imports for external libraries (jsPDF, Stripe.js, optionally Tailwind CSS)
4. **Build Time Target**: Implementation should take less than 1 hour according to PRD
5. **Static Deployment**: Designed for GitHub Pages/Vercel static hosting

## External Dependencies (To Be Added)

When implementing the invoice generator, these CDN libraries should be used:
- **jsPDF**: `https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js`
- **Stripe.js**: `https://js.stripe.com/v3/`
- **Tailwind CSS**: For quick UI styling (optional but recommended per PRD)

## Implementation Notes

- Current state is a basic starter template that needs to be replaced with invoice generator functionality
- All business logic should be implemented in vanilla JavaScript within `index.html`
- Form data should persist using localStorage for user convenience
- Premium features are controlled by client-side flags set after Stripe payment
- Mobile-responsive design required using CSS media queries