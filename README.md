# Dockerize a React Application with Multi-Stage Build

## Title
Dockerize a React Application with Multi-Stage Build

## Objective
Learn how to create a production-ready Docker image for a React application using a multi-stage Docker build. This helps you reduce image size, separate build dependencies from runtime, and prepare your app for deployment.

## Task Description
Build a simple React application (for example, created using Create React App). Write a multi-stage Dockerfile:
- Use a Node.js image as the first stage to install dependencies and build the React app.
- Use an Nginx image as the second stage to serve the compiled static files.
- Add a .dockerignore file to exclude unnecessary files.
- Build the Docker image locally and run it to test if the React app is correctly served on localhost.
- Verify that the final image size is smaller than including all dev dependencies.

## Expected Output
- A working Docker container that serves your React app at http://localhost/.
- Optimized Docker image with significantly smaller size.
- Clear separation between build and production stages in the Dockerfile.

---

# Repository Contents
This repo contains a minimal React app, a multi-stage Dockerfile, a .dockerignore, and instructions to build, run, and validate the result.

```
Dockerized-React-Multi-Stage-Build/
├─ README.md
├─ Dockerfile
├─ .dockerignore
└─ app/
   ├─ package.json
   ├─ package-lock.json
   ├─ public/
   │  ├─ index.html
   │  └─ favicon.ico
   └─ src/
      ├─ index.js
      └─ App.js
```

## Files and Code

### Dockerfile
```Dockerfile
# ---------- Build stage ----------
FROM node:20-alpine AS build
WORKDIR /app

# Install deps first (better layer caching)
COPY app/package*.json ./
RUN npm ci --no-audit --no-fund

# Copy source and build
COPY app/ ./
RUN npm run build

# ---------- Production stage ----------
FROM nginx:alpine AS production
# Remove default nginx static assets and copy our build
RUN rm -rf /usr/share/nginx/html/*
COPY --from=build /app/build /usr/share/nginx/html

# Expose and run
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### .dockerignore
```gitignore
# Node and build artifacts
node_modules
build

# Logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# git and editor
.git
.gitignore
.DS_Store
*.swp
.vscode
.idea
```

### app/package.json
```json
{
  "name": "dockerized-react-msb",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "18.3.1",
    "react-dom": "18.3.1",
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

### app/public/index.html
```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Dockerized React Multi-Stage Build</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

### app/src/index.js
```js
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

### app/src/App.js
```js
import React from 'react';

export default function App() {
  return (
    <div style={{fontFamily: 'sans-serif', padding: 24}}>
      <h1>Dockerized React - Multi-Stage Build</h1>
      <p>
        This React app is built in a Node stage and served by Nginx in a
        lightweight production image.
      </p>
    </div>
  );
}
```

---

## Build and Run

Prerequisites:
- Docker installed and running

Steps:
1) Clone the repo
```
git clone https://github.com/amanhazari10/Dockerized-React-Multi-Stage-Build.git
cd Dockerized-React-Multi-Stage-Build
```

2) Build the image
```
docker build -t react-multistage:latest .
```

3) Run the container
```
docker run --rm -p 80:80 react-multistage:latest
```

4) Open the app
- Visit http://localhost/ in your browser.

## Validate Results
- Image size: The final image should be a small nginx:alpine based image (typically under ~30–50MB plus your static assets), much smaller than a full Node image with dev dependencies.
- Separation of stages: Inspect the Dockerfile to see distinct build and production stages.
- Functionality: The page should render the heading "Dockerized React - Multi-Stage Build".

## Development Notes
- We use npm ci for deterministic installs and better layer caching.
- The .dockerignore keeps dev clutter out of the build context, reducing build time and image size.
- To develop locally without Docker, from the app directory run `npm install` then `npm start`.
