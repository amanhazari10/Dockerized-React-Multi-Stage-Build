# Dockerize a React Application with Multi-Stage Build
        This React app is built in a Node stage and served by Nginx in a
        lightweight production image.
      
    
  );
}
```
---
##
 Build and Run
Prerequisites:
-
 Docker installed and running
Steps:
1)
 Clone the repo
```
git clone https://github.com/amanhazari10/Dockerized-React-Multi-Stage-Build.git
cd Dockerized-React-Multi-Stage-Build
```
2)
 Build the image
```
docker build -t react-multistage:latest .
```
3)
 Run the container
```
docker run --rm -p 80:80 react-multistage:latest
```
4)
 Open the app
-
 Visit http://localhost/ in your browser.
##
 Validate Results
-
 Image size: The final image should be a small nginx:alpine based image (typically under ~30–50MB plus your static assets), much smaller than a full Node image with dev dependencies.
-
 Separation of stages: Inspect the Dockerfile to see distinct build and production stages.
-
 Functionality: The page should render the heading "Dockerized React - Multi-Stage Build".
##
 Development Notes
-
 We use npm ci for deterministic installs and better layer caching.
-
 The .dockerignore keeps dev clutter out of the build context, reducing build time and image size.
-
 To develop locally without Docker, from the app directory run 
`
npm install
`
 then 
`
npm start
`
.

---

## CI/CD Pipeline with GitHub Actions

This repository includes an automated CI/CD pipeline using GitHub Actions that runs on every push to the main branch.

### Workflow Overview

The workflow is defined in `.github/workflows/main.yml` and performs the following tasks:

1. **Trigger Events**: 
   - Runs on every push to the `main` branch
   - Also runs on pull requests to the `main` branch

2. **Environment Setup**:
   - Sets up Node.js runtime (tested on versions 18.x and 20.x)
   - Enables npm caching for faster builds

3. **Build Steps**:
   - **Checkout**: Clones the repository code
   - **Dependencies**: Installs project dependencies using `npm install`
   - **Tests**: Runs the test suite using `npm test`
   - **Build**: Compiles the project using `npm run build`
   - **Artifacts**: Uploads the build output as artifacts

4. **Optional Deployment** (Commented Examples):
   - GitHub Pages deployment example included
   - Netlify deployment example included
   - To enable, uncomment the respective section and configure secrets

### Viewing Workflow Status

To check the status of your CI/CD pipeline:

1. Navigate to your repository on GitHub
2. Click on the **"Actions"** tab in the top navigation
3. View the list of workflow runs:
   - ✅ **Green checkmark**: Build passed successfully
   - ❌ **Red X**: Build failed
   - ⏳ **Yellow circle**: Build is in progress
4. Click on any run to view detailed logs

### Workflow File Location

The CI/CD workflow configuration can be found at: `.github/workflows/main.yml`

### Customization

To customize the workflow:

- Edit the `.github/workflows/main.yml` file
- Modify Node.js versions in the `matrix` section
- Add or remove build steps as needed
- Configure deployment secrets in repository settings → Secrets and variables

### Enabling Deployments

To enable automatic deployments:

1. **GitHub Pages**:
   - Uncomment the "Deploy to GitHub Pages" section in `main.yml`
   - Enable GitHub Pages in repository settings

2. **Netlify**:
   - Uncomment the "Deploy to Netlify" section in `main.yml`
   - Add `NETLIFY_AUTH_TOKEN` and `NETLIFY_SITE_ID` secrets in repository settings

3. **AWS S3**:
   - Add your own deployment step using AWS S3 actions
   - Configure AWS credentials as repository secrets
