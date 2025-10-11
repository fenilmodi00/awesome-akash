# React on Akash

Deploy production-ready React applications on Akash Network's decentralized cloud platform.

## About React

React is a popular JavaScript library for building user interfaces, maintained by Meta (formerly Facebook) and a community of individual developers and companies. React makes it painless to create interactive UIs with component-based architecture, declarative views, and efficient rendering.

**Official Website:** [https://react.dev/](https://react.dev/)

## Prerequisites

Before deploying, ensure you have:

- An Akash wallet with AKT tokens for deployment
- Akash CLI installed or access to Akash Console
- A React application built and ready for production

## Deployment Options

### Option 1: Deploy Pre-built React App (Recommended)

This method uses a Docker image with your pre-built React application.

#### Step 1: Build Your React App

```bash
# Navigate to your React project
cd my-react-app

# Install dependencies
npm install

# Create production build
npm run build
```

#### Step 2: Create a Dockerfile

Create a `Dockerfile` in your React project root:

```dockerfile
# Build stage
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### Step 3: Create nginx.conf

Create an `nginx.conf` file for proper routing:

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Enable gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/json;

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

#### Step 4: Build and Push Docker Image

```bash
# Build your Docker image
docker build -t your-dockerhub-username/react-app:latest .

# Push to Docker Hub
docker push your-dockerhub-username/react-app:latest
```

#### Step 5: Update deploy.yaml

Update the `image` field in `deploy.yaml`:

```yaml
services:
  web:
    image: your-dockerhub-username/react-app:latest
    # ... rest of configuration
```

### Option 2: Use Default Template

The provided `deploy.yaml` uses a basic nginx:alpine image. This is suitable for testing the deployment process.

## Deployment Instructions

### Using Akash Console (Easiest)

1. Visit [Akash Console](https://console.akash.network/)
2. Connect your Keplr wallet
3. Click "Deploy" and select "Build Your Template"
4. Upload or paste the contents of `deploy.yaml`
5. Review and approve the deployment
6. Wait for the deployment to complete
7. Access your React app via the provided URL

### Using Akash CLI

1. **Install Akash CLI:**
   ```bash
   # Follow instructions at:
   # https://akash.network/docs/deployments/akash-cli/installation/
   ```

2. **Set up your wallet:**
   ```bash
   akash keys add mykey
   # Or import existing key
   akash keys add mykey --recover
   ```

3. **Create deployment:**
   ```bash
   akash tx deployment create deploy.yaml --from mykey --node https://rpc.akash.forbole.com:443 --chain-id akashnet-2 --fees 5000uakt
   ```

4. **View bids:**
   ```bash
   akash query market bid list --owner <your-address>
   ```

5. **Accept a bid:**
   ```bash
   akash tx market lease create --dseq <deployment-sequence> --provider <provider-address> --from mykey --fees 5000uakt
   ```

6. **Get lease status:**
   ```bash
   akash provider lease-status --dseq <deployment-sequence> --provider <provider-address> --from mykey
   ```

## Configuration

### Environment Variables

To add environment variables to your React app, uncomment and modify the `env` section in `deploy.yaml`:

```yaml
services:
  web:
    image: your-image
    env:
      - "REACT_APP_API_URL=https://api.example.com"
      - "REACT_APP_ENVIRONMENT=production"
      - "REACT_APP_FEATURE_FLAG=true"
```

**Note:** React environment variables must be prefixed with `REACT_APP_` to be accessible in your application.

### Resource Allocation

Adjust resources based on your application needs in the `profiles` section:

```yaml
profiles:
  compute:
    web:
      resources:
        cpu:
          units: 0.5  # CPU units (0.5 = half a CPU core)
        memory:
          size: 512Mi  # RAM allocation
        storage:
          size: 1Gi    # Storage space
```

### Pricing

The `pricing` section determines your maximum bid:

```yaml
pricing:
  web: 
    denom: uakt
    amount: 10000  # Maximum price in uAKT per block
```

## Common React Deployment Scenarios

### Single Page Application (SPA)

The default configuration works perfectly for SPAs. Ensure your nginx.conf includes:

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
```

### React with API Backend

If your React app needs to communicate with a backend API, you can:

1. **Deploy backend separately** and use environment variables:
   ```yaml
   env:
     - "REACT_APP_API_URL=https://your-backend.akash.network"
   ```

2. **Deploy both in one SDL** (advanced):
   ```yaml
   services:
     frontend:
       image: your-react-app
       expose:
         - port: 80
           as: 80
           to:
             - global: true
     backend:
       image: your-backend-api
       expose:
         - port: 3000
           to:
             - service: frontend
   ```

### React with Routing

For React Router or similar routing libraries, the nginx configuration provided handles client-side routing by redirecting all requests to `index.html`.

## Troubleshooting

### Deployment Fails

- **Insufficient funds:** Ensure your wallet has enough AKT tokens
- **Image pull errors:** Verify your Docker image is public or credentials are provided
- **Resource constraints:** Try reducing CPU/memory requirements

### App Not Loading

- **Check build output:** Ensure `npm run build` completed successfully
- **Verify nginx config:** Make sure nginx.conf is properly configured
- **Check browser console:** Look for CORS or API connection errors

### Environment Variables Not Working

- **Prefix check:** All React env vars must start with `REACT_APP_`
- **Build time:** Environment variables are embedded at build time, not runtime
- **Rebuild required:** After changing env vars, rebuild your Docker image

## Performance Optimization

### Enable Compression

The provided nginx.conf includes gzip compression for better performance.

### Optimize Build Size

```bash
# Analyze bundle size
npm install --save-dev webpack-bundle-analyzer
npm run build -- --stats

# Use code splitting and lazy loading
import React, { lazy, Suspense } from 'react';
const Component = lazy(() => import('./Component'));
```

### CDN for Static Assets

Consider hosting large static assets on IPFS or a CDN and referencing them in your React app.

## Security Considerations

1. **Environment Variables:** Never commit sensitive keys to your repository
2. **HTTPS:** Akash deployments support HTTPS through provider configurations
3. **Content Security Policy:** Add CSP headers in nginx.conf if needed
4. **API Keys:** Use backend proxies for sensitive API calls

## Cost Estimation

Typical React app deployment costs on Akash:

- **Small app (512Mi RAM, 0.5 CPU):** ~$5-10/month
- **Medium app (1Gi RAM, 1 CPU):** ~$10-20/month
- **Large app (2Gi RAM, 2 CPU):** ~$20-40/month

*Costs vary based on provider bids and resource usage.*

## Additional Resources

- [Akash Network Documentation](https://akash.network/docs/)
- [Akash Discord Community](https://discord.akash.network)
- [React Documentation](https://react.dev/)
- [Akash Deployment Guide](https://akash.network/docs/deployments/overview/)
- [Hacktoberfest on Akash](https://luma.com/87pm7k1m)

## Example React Apps

Here are some popular React applications you can deploy:

- **Create React App:** Standard React starter template
- **Vite React:** Modern, faster alternative to CRA
- **Next.js:** React framework with SSR (see separate template)
- **Gatsby:** Static site generator for React
- **React Admin Dashboard:** Admin panels and dashboards
- **React E-commerce:** Online store frontends

## Support

For issues or questions:

- **Akash Discord:** [https://discord.akash.network](https://discord.akash.network)
- **Akash Forum:** [https://forum.akash.network](https://forum.akash.network)
- **GitHub Issues:** [awesome-akash repository](https://github.com/akash-network/awesome-akash)

## Contributing

Contributions are welcome! Please submit pull requests or open issues for improvements to this template.

---

**Happy Deploying! 🚀**

*Deploy decentralized. Deploy on Akash.*
