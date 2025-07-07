## Core Stack

- **Frontend Framework**: Next.js (React-based framework)
- **Styling**: Tailwind CSS
- **Map Integration**: react-map-gl (React wrapper for Mapbox)
- **Authentication**: NextAuth.js with Google Sign-In
- **Backend API**: FastAPI (Python-based)
- **Deployment**: Vercel (frontend) + separate server for FastAPI

## Architecture

- Next.js application handles UI, routing, and client-side logic
- FastAPI backend processes satellite imagery data
- The two services communicate via HTTP API calls

