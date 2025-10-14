# Supabase on Akash

This template deploys [Supabase](https://supabase.com/), an open source Firebase alternative offering all the backend features developers need to build a product: PostgreSQL database, authentication, instant APIs, realtime subscriptions, and storage.

## Features

- PostgreSQL database
- RESTful API with PostgREST
- Authentication with GoTrue
- Realtime subscriptions
- File storage
- Supabase Studio (Web UI)

## Deployment

### Prerequisites

- Akash account with funds
- Akash CLI installed and configured

### Deploying on Akash

1. Create a deployment with the provided SDL:

```bash
provider-services tx deployment create deploy.yaml --from <YOUR_KEY_NAME>
```

2. List deployments and find your deployment ID:

```bash
provider-services query deployment list --owner <YOUR_AKASH_ADDRESS>
```

3. Create a lease:

```bash
provider-services tx market lease create --dseq <DEPLOYMENT_ID> --provider <PROVIDER_ADDRESS> --from <YOUR_KEY_NAME>
```

4. Send manifest:

```bash
provider-services send-manifest deploy.yaml --dseq <DEPLOYMENT_ID> --provider <PROVIDER_ADDRESS> --from <YOUR_KEY_NAME>
```

5. Get lease status (includes your Supabase Studio URL):

```bash
provider-services lease-status --dseq <DEPLOYMENT_ID> --provider <PROVIDER_ADDRESS> --from <YOUR_KEY_NAME>
```

## Access and Configuration

Once deployed, you can access the Supabase Studio at the provided URI for the `studio` service. By default, the following credentials are available:

- **Supabase Anon Key**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyAgCiAgICAicm9sZSI6ICJhbm9uIiwKICAgICJpc3MiOiAic3VwYWJhc2UtZGVtbyIsCiAgICAiaWF0IjogMTY0MTc2OTIwMCwKICAgICJleHAiOiAxNzk5NTM1NjAwCn0.dc_X5iR_VP_qT0zsiyj_I_OZ2T9FtRU2BBNWN8Bu4GE`
- **Supabase Service Key**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyAgCiAgICAicm9sZSI6ICJzZXJ2aWNlX3JvbGUiLAogICAgImlzcyI6ICJzdXBhYmFzZS1kZW1vIiwKICAgICJpYXQiOiAxNjQxNzY5MjAwLAogICAgImV4cCI6IDE3OTk1MzU2MDAKfQ.knhOrPKq1GjejLQr7fuiRBFILwzKxrpxBvzYEJXKy8E`
- **JWT Secret**: `super-secret-jwt-token-with-at-least-32-characters`

⚠️ **Important Security Notice**: The deployment uses default keys and secrets for demonstration purposes. For production use, you should modify the `deploy.yaml` file to use your own secure keys and secrets.

### Modifying Security Settings

For a production deployment, you should change these values in the `deploy.yaml` file:

1. `JWT_SECRET` and `PGRST_JWT_SECRET` - Generate a secure random string
2. Database passwords
3. Generate new anon and service keys

## Data Persistence

This deployment includes persistent storage for:

- PostgreSQL data at `/var/lib/postgresql/data`
- Storage API files at `/var/lib/storage`

Your data should persist across restarts as long as the lease is maintained.

## Connecting to Your Supabase Instance

Once Supabase is running, you can use the Studio web interface to:

1. Create and manage database tables
2. Set up authentication providers
3. Create API keys
4. Configure storage buckets
5. View realtime logs and more

For client applications, use the public URL and anon key to connect using the [Supabase client libraries](https://github.com/supabase/supabase-js).

## Additional Resources

- [Supabase Documentation](https://supabase.com/docs)
- [Supabase GitHub](https://github.com/supabase/supabase)
- [Akash Network Documentation](https://akash.network/docs/)
