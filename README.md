# Node.js CI/CD Application

![CI/CD Pipeline](https://github.com/aniieee-p/CI-CD-Pipeline-Project/actions/workflows/ci-cd.yml/badge.svg)
![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

A complete Node.js application with a production-ready CI/CD pipeline using GitHub Actions.

## 🚀 Features

- ✅ Automated testing with Jest
- 🔨 Build automation
- 🐳 Docker containerization
- 📦 Automated deployment
- 🔐 Secure secrets management
- 📊 Code coverage reporting
- 🎯 Health check endpoints

## 📋 Prerequisites

- Node.js 18.x or higher
- Docker (for containerization)
- GitHub account (for CI/CD)

## 🛠️ Installation

```bash
# Clone the repository
git clone https://github.com/aniieee-p/CI-CD-Pipeline-Project.git
cd CI-CD-Pipeline-Project

# Install dependencies
npm install

# Run in development mode
npm run dev

# Run tests
npm test

# Build the application
npm run build
```

## 🐳 Docker

```bash
# Build Docker image
docker build -t nodejs-cicd-app .

# Run Docker container
docker run -p 3000:3000 nodejs-cicd-app

# Using Docker Compose
docker-compose up
```

## 🔧 Configuration

### GitHub Secrets

Configure the following secrets in your GitHub repository (Settings → Secrets and variables → Actions):

| Secret Name | Description | Required |
|------------|-------------|----------|
| `DOCKER_USERNAME` | Docker Hub username | Yes (for Docker push) |
| `DOCKER_PASSWORD` | Docker Hub password/token | Yes (for Docker push) |
| `DEPLOY_KEY` | SSH key for deployment | Optional |
| `DEPLOY_HOST` | Deployment server hostname | Optional |
| `DEPLOY_USER` | Deployment server username | Optional |

### Setting up Docker Hub credentials:

1. Go to your GitHub repository
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add `DOCKER_USERNAME` with your Docker Hub username
5. Add `DOCKER_PASSWORD` with your Docker Hub password or access token

## 📊 CI/CD Pipeline

The pipeline consists of 5 jobs that run sequentially:

### 1. **Test Job**
- Checks out code
- Sets up Node.js environment
- Installs dependencies
- Runs linter
- Executes tests with coverage
- Uploads coverage reports

### 2. **Build Job**
- Builds the application
- Uploads build artifacts
- Runs only after tests pass

### 3. **Docker Job**
- Builds Docker image
- Pushes to Docker Hub
- Tags with branch name, commit SHA, and 'latest'
- Uses build cache for faster builds
- Only runs on main branch pushes

### 4. **Deploy Job**
- Downloads build artifacts
- Deploys to production environment
- Runs health checks
- Only runs on main branch pushes

### 5. **Notify Job**
- Sends notifications on pipeline failure
- Provides detailed failure information

## 🔄 Workflow Triggers

The pipeline runs on:
- Every push to `main` branch (full pipeline)
- Every pull request to `main` branch (test and build only)

## 📈 Status Badges

Add these badges to your README by replacing `YOUR_USERNAME` and `YOUR_REPO`:

```markdown
![CI/CD Pipeline](https://github.com/aniieee-p/CI-CD-Pipeline-Project/actions/workflows/ci-cd.yml/badge.svg)
![Tests](https://img.shields.io/badge/tests-passing-brightgreen)
![Coverage](https://img.shields.io/badge/coverage-90%25-brightgreen)
```

## 🏗️ Project Structure

```
.
├── .github/
│   └── workflows/
│       └── ci-cd.yml          # CI/CD pipeline configuration
├── src/
│   ├── index.js               # Main application file
│   └── index.test.js          # Test file
├── Dockerfile                 # Docker configuration
├── .dockerignore             # Docker ignore file
├── package.json              # Node.js dependencies
├── jest.config.js            # Jest configuration
├── .eslintrc.js              # ESLint configuration
└── README.md                 # This file
```

## 🧪 Testing

```bash
# Run tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm test -- --coverage
```

## 🔍 Linting

```bash
# Run linter
npm run lint

# Fix linting issues
npm run lint:fix
```

## 📝 API Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check endpoint
- `GET /api/status` - API status

## 🚀 Deployment

The application can be deployed to various platforms:

- **Docker**: Use the provided Dockerfile
- **Kubernetes**: Create deployment manifests
- **Cloud Platforms**: AWS, Azure, GCP
- **PaaS**: Heroku, Vercel, Railway

## 🔐 Security

- Non-root user in Docker container
- Environment variables for sensitive data
- GitHub secrets for credentials
- Health checks for monitoring
- Automated security updates

## 📚 How CI/CD Improves Reliability

### 1. **Automated Testing**
- Catches bugs before they reach production
- Ensures code quality with every commit
- Provides immediate feedback to developers

### 2. **Consistent Builds**
- Same build process every time
- Eliminates "works on my machine" issues
- Reproducible builds across environments

### 3. **Fast Feedback Loop**
- Developers know immediately if changes break anything
- Reduces time between writing code and deployment
- Enables rapid iteration

### 4. **Reduced Human Error**
- Automated processes eliminate manual mistakes
- Consistent deployment procedures
- No forgotten steps

### 5. **Version Control Integration**
- Every change is tracked
- Easy rollback if issues occur
- Clear audit trail

### 6. **Continuous Monitoring**
- Health checks ensure application is running
- Automated notifications on failures
- Quick detection of issues

### 7. **Scalability**
- Easy to add more tests and checks
- Can handle multiple deployments per day
- Supports team growth

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License.

## 🆘 Troubleshooting

### Pipeline fails on Docker push
- Verify Docker Hub credentials are set correctly
- Check if Docker Hub repository exists
- Ensure you have push permissions

### Tests fail
- Run tests locally first: `npm test`
- Check for environment-specific issues
- Review test logs in GitHub Actions

### Deployment fails
- Verify deployment secrets are configured
- Check server connectivity
- Review deployment logs

## 📞 Support

For issues and questions:
- Open an issue on GitHub
- Check existing issues for solutions
- Review GitHub Actions logs for detailed error messages

---

Made with ❤️ using GitHub Actions
