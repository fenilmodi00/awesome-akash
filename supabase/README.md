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

### Security Configuration (Important)

⚠️ **Before deploying:** You must edit the `deploy.yaml` file to replace the placeholder values with your own secure keys and secrets:

1. Generate a JWT secret (at least 32 characters) for these fields:
   - `PGRST_JWT_SECRET`
   - `GOTRUE_JWT_SECRET`
   - `JWT_SECRET`

   You can generate a secure random string with:
   ```bash
   openssl rand -base64 32
   ```

2. Generate JWT tokens for Supabase authentication:
   - For `SUPABASE_ANON_KEY` and `ANON_KEY`: Create an anonymous role JWT
   - For `SUPABASE_SERVICE_KEY` and `SERVICE_KEY`: Create a service role JWT
   
   You can use the [jwt.io](https://jwt.io/) website with your JWT secret to create these tokens with the appropriate payloads.

3. Consider changing the default database credentials for production use.

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

Once deployed, you can access the Supabase Studio at the provided URI for the `studio` service. You'll need to use the keys you configured in the deployment YAML.

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
