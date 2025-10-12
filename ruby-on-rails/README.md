# Ruby on Rails on Akash

Deploy a production-ready Ruby on Rails application on Akash decentralized cloud.

## Features
- Exposes port 3000 for web traffic
- Persistent storage for Rails and PostgreSQL
- Environment variables for secrets and database
- PostgreSQL database included

## Prerequisites
- Rails app compatible with Ruby 3.2
- Akash CLI installed ([docs](https://akash.network/docs/cli/install/))
- Akash wallet funded with AKT

## Environment Variables
- `RAILS_ENV`: Set to `production`
- `DATABASE_URL`: Connection string for Rails (default uses SQLite for demo, but PostgreSQL is available)
- `SECRET_KEY_BASE`: Set your Rails secret key
- `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`: Database credentials

## Storage
- Rails app data: `/rails-data` (2Gi)
- PostgreSQL data: `/var/lib/postgresql/data` (2Gi)

## Deployment Steps
1. Place your Rails app code in a Docker image based on `ruby:3.2`.
2. Update the `deploy.yaml` image field to your app image (default is `ruby:3.2`).
3. Set environment variables as needed in `deploy.yaml`.
4. Deploy using Akash CLI:
   ```sh
   akash tx deployment create deploy.yaml --from <your-wallet>
   ```
5. Access your app at `http://<provider-ip>:3000`

## Database Setup
- Default config uses SQLite for demo. For production, set `DATABASE_URL` to use PostgreSQL:
  ```yaml
  DATABASE_URL: "postgres://rails:rails_password@db:5432/rails_production"
  ```
- Run migrations as part of the startup command.

## Notes
- Change all secrets before deploying.
- For custom domains, configure Akash provider networking.

## Resources
- [Ruby on Rails](https://rubyonrails.org/)
- [Akash Deployments](https://akash.network/docs/deployments/overview/)
- [Hacktoberfest](https://luma.com/87pm7k1m)

