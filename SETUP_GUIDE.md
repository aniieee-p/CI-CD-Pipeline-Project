# CI/CD Setup Guide

This guide will walk you through setting up the complete CI/CD pipeline for your Node.js application.

## 📋 Prerequisites

Before you begin, ensure you have:

- ✅ GitHub account
- ✅ Docker Hub account (optional, for Docker image hosting)
- ✅ Node.js 18.x or higher installed locally
- ✅ Git installed

## 🚀 Quick Start

### 1. Initialize the Project

```bash
# Install dependencies
npm install

# Run the application locally
npm start

# Run tests
npm test

# Run linter
npm run lint
```

### 2. Configure GitHub Repository

#### Step 1: Create a new repository on GitHub
```bash
git init
git add .
git commit -m "Initial commit with CI/CD pipeline"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

#### Step 2: Configure GitHub Secrets

Navigate to your repository on GitHub:
1. Go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add the following secrets:

| Secret Name | Description | How to Get |
|------------|-------------|------------|
| `DOCKER_USERNAME` | Your Docker Hub username | Your Docker Hub account username |
| `DOCKER_PASSWORD` | Docker Hub access token | Create at hub.docker.com → Account Settings → Security |
| `DEPLOY_KEY` | SSH private key for deployment | Generate with `ssh-keygen -t ed25519` |
| `DEPLOY_HOST` | Deployment server hostname | Your server's IP or domain |
| `DEPLOY_USER` | Deployment server username | SSH username for your server |

### 3. Docker Hub Setup (Optional)

If you want to push Docker images to Docker Hub:

1. **Create Docker Hub Account**: Visit [hub.docker.com](https://hub.docker.com)
2. **Create Access Token**:
   - Go to Account Settings → Security
   - Click "New Access Token"
   - Give it a name (e.g., "GitHub Actions")
   - Copy the token (you won't see it again!)
3. **Add to GitHub Secrets**:
   - Add `DOCKER_USERNAME` with your Docker Hub username
   - Add `DOCKER_PASSWORD` with the access token

### 4. Test the Pipeline

#### Trigger the pipeline:
```bash
# Make a change
echo "# Test" >> README.md

# Commit and push
git add .
git commit -m "Test CI/CD pipeline"
git push origin main
```

#### Monitor the pipeline:
1. Go to your GitHub repository
2. Click on the **Actions** tab
3. Watch the pipeline execute in real-time

## 🔧 Pipeline Configuration

### Pipeline Jobs Overview

The pipeline consists of 5 jobs:

```
test → build → docker → deploy → notify-failure
                  ↓
              (parallel)
```

#### 1. **Test Job**
- Installs dependencies
- Runs ESLint
- Executes Jest tests
- Generates coverage reports

#### 2. **Build Job**
- Builds the application
- Creates production artifacts
- Uploads artifacts for deployment

#### 3. **Docker Job** (main branch only)
- Builds Docker image
- Pushes to Docker Hub
- Tags with multiple versions

#### 4. **Deploy Job** (main branch only)
- Downloads build artifacts
- Deploys to production
- Runs health checks

#### 5. **Notify Job** (on failure)
- Sends failure notifications
- Provides debugging information

### Customizing the Pipeline

#### Change Node.js Version
Edit `.github/workflows/ci-cd.yml`:
```yaml
env:
  NODE_VERSION: '20.x'  # Change to your desired version
```

#### Add More Test Steps
```yaml
- name: Run integration tests
  run: npm run test:integration

- name: Run E2E tests
  run: npm run test:e2e
```

#### Customize Docker Image Name
```yaml
env:
  DOCKER_IMAGE_NAME: my-custom-name
```

## 🐳 Docker Configuration

### Build Docker Image Locally

```bash
# Build the image
docker build -t nodejs-cicd-app .

# Run the container
docker run -p 3000:3000 nodejs-cicd-app

# Test the application
curl http://localhost:3000/health
```

### Using Docker Compose

```bash
# Start the application
docker-compose up -d

# View logs
docker-compose logs -f

# Stop the application
docker-compose down
```

## 🚢 Deployment Options

### Option 1: SSH Deployment

Add this to the deploy job in `.github/workflows/ci-cd.yml`:

```yaml
- name: Deploy via SSH
  uses: appleboy/ssh-action@master
  with:
    host: ${{ secrets.DEPLOY_HOST }}
    username: ${{ secrets.DEPLOY_USER }}
    key: ${{ secrets.DEPLOY_KEY }}
    script: |
      cd /path/to/app
      docker pull ${{ secrets.DOCKER_USERNAME }}/nodejs-cicd-app:latest
      docker-compose down
      docker-compose up -d
```

### Option 2: Kubernetes Deployment

```yaml
- name: Deploy to Kubernetes
  run: |
    kubectl set image deployment/nodejs-app \
      nodejs-app=${{ secrets.DOCKER_USERNAME }}/nodejs-cicd-app:${{ github.sha }}
    kubectl rollout status deployment/nodejs-app
```

### Option 3: Cloud Platform Deployment

**AWS ECS:**
```yaml
- name: Deploy to AWS ECS
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: task-definition.json
    service: my-service
    cluster: my-cluster
```

**Azure:**
```yaml
- name: Deploy to Azure
  uses: azure/webapps-deploy@v2
  with:
    app-name: my-app-name
    images: ${{ secrets.DOCKER_USERNAME }}/nodejs-cicd-app:latest
```

**Google Cloud Run:**
```yaml
- name: Deploy to Cloud Run
  run: |
    gcloud run deploy nodejs-app \
      --image ${{ secrets.DOCKER_USERNAME }}/nodejs-cicd-app:latest \
      --platform managed \
      --region us-central1
```

## 📊 Monitoring and Notifications

### Add Slack Notifications

1. Create a Slack webhook
2. Add `SLACK_WEBHOOK` to GitHub secrets
3. Add this step to the notify job:

```yaml
- name: Slack Notification
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### Add Email Notifications

```yaml
- name: Send Email
  uses: dawidd6/action-send-mail@v3
  with:
    server_address: smtp.gmail.com
    server_port: 465
    username: ${{ secrets.EMAIL_USERNAME }}
    password: ${{ secrets.EMAIL_PASSWORD }}
    subject: CI/CD Pipeline Failed
    body: The pipeline failed for commit ${{ github.sha }}
    to: team@example.com
```

## 🔍 Troubleshooting

### Pipeline Fails on Test Job

**Problem**: Tests fail or timeout

**Solutions**:
```bash
# Run tests locally first
npm test

# Check for environment-specific issues
npm test -- --verbose

# Increase timeout in jest.config.js
testTimeout: 10000
```

### Docker Build Fails

**Problem**: Docker build or push fails

**Solutions**:
1. Verify Docker Hub credentials are correct
2. Check if repository exists on Docker Hub
3. Ensure you have push permissions
4. Try building locally: `docker build -t test .`

### Deployment Fails

**Problem**: Deployment step fails

**Solutions**:
1. Verify all deployment secrets are set
2. Test SSH connection manually
3. Check server logs
4. Verify Docker is installed on deployment server

### Secrets Not Working

**Problem**: Pipeline can't access secrets

**Solutions**:
1. Verify secrets are set in repository settings
2. Check secret names match exactly (case-sensitive)
3. Ensure you're pushing to the correct branch
4. Secrets are not available in pull requests from forks

## 📈 Best Practices

### 1. Branch Protection Rules

Set up branch protection:
1. Go to Settings → Branches
2. Add rule for `main` branch
3. Enable:
   - Require pull request reviews
   - Require status checks to pass
   - Require branches to be up to date

### 2. Caching Dependencies

The pipeline already uses caching:
```yaml
- uses: actions/setup-node@v4
  with:
    cache: 'npm'  # Caches node_modules
```

### 3. Matrix Testing

Test multiple Node.js versions:
```yaml
strategy:
  matrix:
    node-version: [16.x, 18.x, 20.x]
```

### 4. Conditional Deployments

Deploy only on specific conditions:
```yaml
if: github.event_name == 'push' && github.ref == 'refs/heads/main'
```

### 5. Environment Protection

Use GitHub environments for additional security:
1. Go to Settings → Environments
2. Create "production" environment
3. Add required reviewers
4. Set deployment branches

## 🎯 Next Steps

1. ✅ Set up branch protection rules
2. ✅ Configure deployment secrets
3. ✅ Add monitoring and alerting
4. ✅ Set up staging environment
5. ✅ Add performance testing
6. ✅ Implement blue-green deployment
7. ✅ Add security scanning
8. ✅ Set up automated rollbacks

## 📚 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Jest Documentation](https://jestjs.io/)
- [ESLint Documentation](https://eslint.org/)

## 🆘 Getting Help

If you encounter issues:
1. Check the Actions tab for detailed logs
2. Review this guide's troubleshooting section
3. Check GitHub Actions status page
4. Open an issue in the repository

---

Happy deploying! 🚀
