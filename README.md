# Node.js CI/CD Pipeline Project

![CI/CD Pipeline](https://github.com/aniieee-p/CI-CD-Pipeline-Project/actions/workflows/ci-cd.yml/badge.svg)
![Node.js](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)

A simple Node.js app I built to learn CI/CD with GitHub Actions. The pipeline automatically runs tests, builds the app, and creates a Docker image whenever I push code.

## What I Learned

- Setting up automated testing with Jest
- Creating GitHub Actions workflows
- Building and pushing Docker images
- Handling test coverage and linting
- Debugging pipeline failures (learned this the hard way!)

## Quick Start

```bash
# Install dependencies
npm install

# Run the app
npm start

# Run tests
npm test
```

The app runs on `http://localhost:3000`

## Endpoints

- `/` - Welcome message
- `/health` - Health check
- `/api/status` - API status

## CI/CD Pipeline

The pipeline runs automatically on every push:

1. **Tests** - Runs Jest tests and checks code coverage
2. **Build** - Builds the application
3. **Docker** - Creates a Docker image (on main branch)
4. **Deploy** - Simulated deployment step

## Running with Docker

```bash
# Build image
docker build -t nodejs-cicd-app .

# Run container
docker run -p 3000:3000 nodejs-cicd-app
```

## Project Structure

```
├── .github/workflows/ci-cd.yml  # Pipeline configuration
├── src/
│   ├── index.js                 # Main app
│   └── index.test.js            # Tests
├── Dockerfile
└── package.json
```

## Notes

- The Docker push step needs Docker Hub credentials (not configured yet)
- Coverage thresholds are set to 60% for branches and functions
- Tests use supertest for HTTP endpoint testing

## What's Next

- [ ] Add more endpoints
- [ ] Improve test coverage
- [ ] Set up actual deployment
- [ ] Add database integration

---

Built while learning DevOps practices 🚀
