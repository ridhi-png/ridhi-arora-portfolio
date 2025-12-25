# Deployment Guide for Portfolio Application

This guide will help you deploy your full-stack portfolio application to Render, a cloud hosting platform. The deployment is mostly automated, making it easy even for beginners!

## 📋 Overview

Your portfolio application consists of:
- **Frontend**: React application (served as a static site)
- **Backend**: Python FastAPI server with MongoDB database

After following this guide, your app will be live on the internet with automatic updates whenever you push to GitHub!

## 🎯 What You'll Need

Before starting, make sure you have:

1. **GitHub Account** (already have this since you're reading this!)
2. **Render Account** - Free tier available (we'll create this together)
3. **MongoDB Atlas Account** - Free tier available (for database hosting)

## 📝 Step-by-Step Setup

### Step 1: Set Up MongoDB Atlas (Database)

Your backend needs a MongoDB database to store data. We'll use MongoDB Atlas (free tier).

1. **Sign up for MongoDB Atlas**
   - Go to [https://www.mongodb.com/cloud/atlas/register](https://www.mongodb.com/cloud/atlas/register)
   - Sign up with your email or GitHub account
   - Choose the **FREE tier** (M0 Sandbox)

2. **Create a Database Cluster**
   - After signing up, click "Build a Database"
   - Select **M0 (Free tier)**
   - Choose a cloud provider and region (pick one closest to you)
   - Name your cluster (e.g., "portfolio-cluster")
   - Click "Create"

3. **Create Database User**
   - Go to "Database Access" in the left sidebar
   - Click "Add New Database User"
   - Choose "Password" authentication
   - Create a username and **strong password** (save these!)
   - Set user privileges to "Read and write to any database"
   - Click "Add User"

4. **Allow Network Access**
   - Go to "Network Access" in the left sidebar
   - Click "Add IP Address"
   - Click "Allow Access from Anywhere" (for Render to connect)
   - Confirm by clicking "Confirm"

5. **Get Your Connection String**
   - Go to "Database" in the left sidebar
   - Click "Connect" on your cluster
   - Choose "Connect your application"
   - Copy the connection string (looks like: `mongodb+srv://<username>:<password>@...`)
   - **Important**: Replace `<password>` with your actual password
   - **Save this connection string** - you'll need it for Render!

### Step 2: Sign Up for Render

1. **Create Render Account**
   - Go to [https://render.com](https://render.com)
   - Click "Get Started" or "Sign Up"
   - Sign up with your **GitHub account** (recommended for easier deployment)
   - Authorize Render to access your GitHub repositories

2. **Verify Your Account**
   - Check your email for verification link
   - Click the link to verify your account

### Step 3: Connect Your Repository to Render

Render offers two deployment methods. We'll use the **Blueprint method** which is simpler and uses the `render.yaml` file already in your repository.

#### Method A: Using Blueprint (Recommended)

1. **Create New Blueprint**
   - In your Render dashboard, click "New +" button
   - Select "Blueprint"
   
2. **Connect Repository**
   - Click "Connect GitHub repository"
   - Find and select your `ridhi-arora-portfolio` repository
   - Click "Connect"

3. **Render will automatically detect the `render.yaml` file**
   - It will show you 2 services to be created:
     - `portfolio-backend` (Web Service)
     - `portfolio-frontend` (Static Site)
   - Review the services and click "Apply"

#### Method B: Manual Setup (Alternative)

If Blueprint doesn't work, you can create services manually:

**Create Backend Service:**
1. Click "New +" → "Web Service"
2. Connect your GitHub repository
3. Configure:
   - **Name**: `portfolio-backend`
   - **Region**: Choose one close to you
   - **Branch**: `main`
   - **Root Directory**: Leave blank
   - **Environment**: `Python 3`
   - **Build Command**: 
     ```bash
     cd Portfolio/backend && pip install --upgrade pip && pip install -r requirements.txt
     ```
   - **Start Command**: 
     ```bash
     cd Portfolio/backend && uvicorn server:app --host 0.0.0.0 --port $PORT
     ```
   - **Plan**: Free

**Create Frontend Service:**
1. Click "New +" → "Static Site"
2. Connect your GitHub repository
3. Configure:
   - **Name**: `portfolio-frontend`
   - **Branch**: `main`
   - **Root Directory**: Leave blank
   - **Build Command**: 
     ```bash
     cd Portfolio/frontend && npm install && npm run build
     ```
   - **Publish Directory**: `Portfolio/frontend/build`

### Step 4: Configure Environment Variables

Your backend needs environment variables to work properly.

1. **Go to Backend Service**
   - In Render dashboard, click on `portfolio-backend`
   - Go to "Environment" tab
   - Click "Add Environment Variable"

2. **Add Required Variables**
   
   Add these three variables:

   | Key | Value | Description |
   |-----|-------|-------------|
   | `MONGO_URL` | `mongodb+srv://...` | Your MongoDB connection string from Step 1 |
   | `DB_NAME` | `portfolio` | Name of your database |
   | `CORS_ORIGINS` | `https://portfolio-frontend.onrender.com` | Your frontend URL (update after deployment) |

   **Important Notes:**
   - For `MONGO_URL`: Use the connection string you saved in Step 1
   - For `CORS_ORIGINS`: Initially, you can use `*` (allow all), but **after frontend deploys**, update it to your actual frontend URL (e.g., `https://your-portfolio-frontend.onrender.com`)

3. **Save Environment Variables**
   - Click "Save Changes"
   - Your backend service will automatically redeploy

### Step 5: Update CORS Settings (After First Deploy)

After both services are deployed:

1. **Get Your Frontend URL**
   - Go to `portfolio-frontend` service in Render
   - Copy the URL (e.g., `https://portfolio-frontend-xyz.onrender.com`)

2. **Update Backend CORS**
   - Go to `portfolio-backend` service
   - Go to "Environment" tab
   - Update `CORS_ORIGINS` variable:
     - Change from `*` to your actual frontend URL
     - Example: `https://portfolio-frontend-xyz.onrender.com`
   - Save changes

3. **Update Frontend API URL (if needed)**
   - If your React app needs to know the backend URL
   - Add environment variable to frontend:
     - Key: `REACT_APP_API_URL`
     - Value: Your backend URL (e.g., `https://portfolio-backend-xyz.onrender.com`)

### Step 6: Trigger Your First Deployment

If you used the Blueprint method, deployment starts automatically. Otherwise:

1. **Manual Deploy**
   - Go to each service in Render dashboard
   - Click "Manual Deploy" → "Deploy latest commit"
   - Wait for deployment to complete (5-10 minutes first time)

2. **Monitor Deployment**
   - You'll see build logs in real-time
   - Wait for "Live" status (green indicator)
   - Any errors will be shown in the logs

### Step 7: Access Your Deployed Application

1. **Find Your URLs**
   - **Frontend**: Click on `portfolio-frontend` service → Copy the URL at the top
   - **Backend**: Click on `portfolio-backend` service → Copy the URL at the top

2. **Test Your Application**
   - Open the frontend URL in your browser
   - Your portfolio should be live! 🎉

3. **Test Backend API**
   - Visit: `https://your-backend-url.onrender.com/api/`
   - You should see: `{"message": "Hello World"}`

## 🔄 Automatic Updates

Great news! Future deployments are **automatic**:

1. **Make changes** to your code locally
2. **Commit and push** to the `main` branch:
   ```bash
   git add .
   git commit -m "Your update message"
   git push origin main
   ```
3. **GitHub Actions runs** (tests your code)
4. **Render automatically deploys** your changes
5. Wait 2-5 minutes, and your changes are live!

You can watch the deployment progress in:
- Render Dashboard (shows build logs)
- GitHub Actions tab (shows test results)

## 🔧 Troubleshooting Common Issues

### Issue 1: Backend Won't Start

**Symptom**: Backend service shows "Deploy failed" or keeps crashing

**Solutions**:
- Check environment variables are set correctly (especially `MONGO_URL`)
- Verify your MongoDB IP whitelist includes `0.0.0.0/0` (allow all)
- Check build logs in Render for specific error messages
- Make sure your MongoDB user has correct permissions

### Issue 2: Frontend Shows White Screen

**Symptom**: Frontend URL loads but shows blank/white page

**Solutions**:
- Check browser console for errors (F12 → Console tab)
- Verify build completed successfully in Render logs
- Make sure `package.json` exists in `Portfolio/frontend/`
- Check that `REACT_APP_API_URL` points to correct backend URL

### Issue 3: CORS Errors

**Symptom**: Frontend can't connect to backend, "CORS error" in console

**Solutions**:
- Update `CORS_ORIGINS` in backend environment variables
- Add your frontend URL to `CORS_ORIGINS`
- Format: `https://your-frontend.onrender.com` (no trailing slash)
- Save and redeploy backend

### Issue 4: Database Connection Failed

**Symptom**: Backend logs show "Failed to connect to MongoDB"

**Solutions**:
- Verify `MONGO_URL` is correct in Render environment variables
- Check MongoDB Atlas:
  - Network Access allows `0.0.0.0/0`
  - Database user exists with correct password
  - Connection string has password (not `<password>` placeholder)

### Issue 5: Build Takes Too Long or Times Out

**Symptom**: Deployment stuck or times out

**Solutions**:
- First deployment can take 10-15 minutes (normal)
- Check if `package-lock.json` or `yarn.lock` exists for faster installs
- Render free tier has limited resources; be patient
- If it fails, try "Manual Deploy" again

### Issue 6: 404 Errors on Frontend Routes

**Symptom**: Direct URLs (like `/about`) show 404 error

**Solutions**:
- This is already configured in `render.yaml`
- Verify the routes section is present in your render.yaml:
  ```yaml
  routes:
    - type: rewrite
      source: /*
      destination: /index.html
  ```

## 📱 Monitoring Your Application

### Check Application Health

1. **Render Dashboard**
   - Shows service status (green = healthy)
   - View recent deployments
   - Monitor resource usage (free tier limits)

2. **View Logs**
   - Click on a service → "Logs" tab
   - See real-time application logs
   - Useful for debugging issues

3. **Performance**
   - Free tier services sleep after 15 minutes of inactivity
   - First request after sleep takes ~30 seconds to wake up
   - Consider upgrading to paid tier for always-on services

## 🎓 Next Steps

Congratulations on deploying your portfolio! Here are some next steps:

1. **Custom Domain** (Optional)
   - Purchase a domain name (e.g., from Namecheap, GoDaddy)
   - Add custom domain in Render: Service → Settings → Custom Domain
   - Update DNS records as shown in Render

2. **Upgrade for Better Performance** (Optional)
   - Free tier has limitations (services sleep when inactive)
   - Starter plan ($7/month) keeps services always-on
   - Upgrade: Service → Settings → Change Instance Type

3. **Add More Features**
   - Add authentication
   - Implement contact form
   - Add analytics (Google Analytics, Plausible)
   - Set up monitoring (UptimeRobot, Pingdom)

4. **Security Best Practices**
   - Never commit `.env` files to Git
   - Use strong passwords for MongoDB
   - Regularly update dependencies
   - Enable 2FA on Render and GitHub accounts

## 📚 Additional Resources

- [Render Documentation](https://render.com/docs)
- [MongoDB Atlas Documentation](https://docs.atlas.mongodb.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Create React App Deployment](https://create-react-app.dev/docs/deployment/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

## 🆘 Getting Help

If you're stuck:

1. **Check Render Logs**: Most issues show up in build/runtime logs
2. **MongoDB Atlas Logs**: Check database connection logs
3. **GitHub Actions**: Check workflow runs for CI/CD errors
4. **Render Community**: [community.render.com](https://community.render.com)
5. **Stack Overflow**: Search for specific error messages

## 🎉 Success Checklist

- [ ] MongoDB Atlas cluster created and configured
- [ ] Render account created and verified
- [ ] Repository connected to Render
- [ ] Backend service deployed and running
- [ ] Frontend service deployed and running
- [ ] Environment variables configured
- [ ] CORS settings updated with frontend URL
- [ ] Application accessible via public URLs
- [ ] Automatic deployments working on push to main

---

**Made with ❤️ for beginners**

Questions or issues? Create an issue on GitHub or check the troubleshooting section above.
