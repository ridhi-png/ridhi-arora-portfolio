# ridhi-arora-portfolio

A full-stack portfolio application with React frontend and Python FastAPI backend.

## 🚀 Quick Start

### Local Development

**Backend:**
```bash
cd Portfolio/backend
pip install -r requirements.txt
# Set up .env file (see Portfolio/.env.example)
uvicorn server:app --reload
```

**Frontend:**
```bash
cd Portfolio/frontend
npm install
npm start
```

### Deployment

This project is configured for automatic deployment to Render. See **[DEPLOYMENT.md](./DEPLOYMENT.md)** for complete deployment instructions.

## 📚 Documentation

- [Deployment Guide](./DEPLOYMENT.md) - Step-by-step guide to deploy this application
- [Environment Variables](./Portfolio/.env.example) - Required environment variables

## 🛠️ Technology Stack

- **Frontend**: React (Create React App with CRACO)
- **Backend**: Python FastAPI with Motor (async MongoDB driver)
- **Database**: MongoDB Atlas
- **Deployment**: Render (with GitHub Actions CI/CD)

## 📦 Project Structure

```
ridhi-arora-portfolio/
├── Portfolio/
│   ├── backend/         # FastAPI backend application
│   ├── frontend/        # React frontend application
│   └── .env.example     # Environment variables template
├── .github/
│   └── workflows/
│       └── deploy.yml   # GitHub Actions CI/CD workflow
├── render.yaml          # Render deployment configuration
└── DEPLOYMENT.md        # Comprehensive deployment guide
```