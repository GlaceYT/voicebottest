# Railway Deployment Guide

## Issues Fixed

### 1. **Missing PORT Environment Variable Handling**
   - **Problem**: Railway provides a `PORT` environment variable, but the app wasn't using it
   - **Fix**: Added `Procfile` with proper uvicorn command that uses `$PORT`
   - **Fix**: Added `if __name__ == "__main__"` block to handle PORT when running directly

### 2. **Missing Startup Configuration**
   - **Problem**: Railway didn't know how to start the application
   - **Fix**: Created `Procfile` with: `web: uvicorn voice:app --host 0.0.0.0 --port $PORT`
   - **Fix**: Created `railway.json` with deployment configuration

### 3. **Incomplete WebSocket Handler**
   - **Problem**: The websocket handler wasn't receiving messages from SignalWire or processing Deepgram STT responses
   - **Fix**: Added proper message receiving and STT processing loops

### 4. **Missing Environment Variable Validation**
   - **Problem**: No validation for required environment variables
   - **Fix**: Added `validate_env()` function to check for required variables

### 5. **Missing Health Check Endpoints**
   - **Problem**: No way to verify the app is running
   - **Fix**: Added `/` and `/health` endpoints

### 6. **Python Version Specification**
   - **Problem**: Railway needs to know which Python version to use
   - **Fix**: Created `runtime.txt` with Python 3.11.0

## Required Environment Variables

Make sure to set these in Railway's environment variables:

```
SIGNALWIRE_PROJECT_ID=your_project_id
SIGNALWIRE_TOKEN=your_token
SIGNALWIRE_SPACE_URL=your_space_url
SIGNALWIRE_FROM_NUMBER=your_phone_number
OPENAI_API_KEY=your_openai_key
DEEPGRAM_API_KEY=your_deepgram_key
PUBLIC_HOST=your-railway-app.railway.app
```

**Important**: For `PUBLIC_HOST`, use your Railway app's domain (e.g., `your-app-name.railway.app`). Railway will provide this after deployment.

## Deployment Steps

1. **Push your code to GitHub** (if not already done)

2. **Connect Railway to your GitHub repository**
   - Go to Railway dashboard
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Choose your repository

3. **Set Environment Variables**
   - In Railway project settings, go to "Variables"
   - Add all required environment variables listed above
   - **Note**: Railway will automatically set `PORT` - don't override it

4. **Deploy**
   - Railway will automatically detect the `Procfile` and deploy
   - The build process will:
     - Install Python 3.11.0 (from `runtime.txt`)
     - Install dependencies (from `requirements.txt`)
     - Start the app using the `Procfile` command

5. **Verify Deployment**
   - Check the Railway logs for any errors
   - Visit `https://your-app-name.railway.app/health` to verify it's running
   - The response should be: `{"status": "healthy"}`

## Troubleshooting

### App won't start
- Check Railway logs for errors
- Verify all environment variables are set
- Ensure `PUBLIC_HOST` matches your Railway domain

### Port binding errors
- Railway automatically sets `PORT` - don't set it manually
- The `Procfile` uses `$PORT` which Railway provides

### WebSocket connection issues
- Ensure `PUBLIC_HOST` is set correctly (use `wss://` protocol in URLs)
- Check that your Railway app supports WebSocket connections (it should by default)

### Missing dependencies
- All required packages are in `requirements.txt`
- If you add new packages, update `requirements.txt` and redeploy

## Files Added/Modified

- ✅ `Procfile` - Tells Railway how to start the app
- ✅ `railway.json` - Railway deployment configuration
- ✅ `runtime.txt` - Specifies Python version
- ✅ `voice.py` - Fixed PORT handling, websocket handler, and validation
- ✅ `RAILWAY_DEPLOYMENT.md` - This guide

## Testing Locally

Before deploying, test locally:

```bash
# Set environment variables
export PORT=8000
export PUBLIC_HOST=localhost:8000
# ... set other variables ...

# Run the app
uvicorn voice:app --host 0.0.0.0 --port 8000
```

Visit `http://localhost:8000/health` to verify it's working.

