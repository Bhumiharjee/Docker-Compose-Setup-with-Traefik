# Docker Compose Setup with Traefik

This repository contains Docker Compose configurations for a production environment using Traefik as a reverse proxy with automatic SSL certificate management. The setup includes frontend, backend, and PostgreSQL database services.

## Prerequisites

- Docker and Docker Compose installed
- Domain names pointed to your server
- Basic understanding of Docker and networking concepts

## Project Structure

```
.
├── traefik-compose.yml
├── frontend-compose.yml
├── backend-compose.yml
├── .env
└── README.md
```

## Quick Start

1. Create the Traefik network:
```bash
docker network create traefik-public
```

2. Set up environment variables in `.env` file (see Environment Variables section)

3. Start Traefik:
```bash
docker-compose -f traefik-compose.yml up -d
```

4. Start the backend services:
```bash
docker-compose -f backend-compose.yml up -d
```

5. Start the frontend services:
```bash
docker-compose -f frontend-compose.yml up -d
```

## Configuration Details

### Traefik Setup

The `traefik-compose.yml` configures Traefik with:
- Automatic SSL certificate generation using Let's Encrypt
- HTTP to HTTPS redirection
- Docker provider integration
- TLS challenge for certificate validation

### Frontend Configuration

The frontend service:
- Uses custom Dockerfile for building
- Exposed through Traefik at `test.sauravkumar.cloud`
- Automatically receives SSL certificates
- Configured for single replica deployment

### Backend Configuration

The backend setup includes:
- FastAPI/Django application (web service)
- PostgreSQL database
- Exposed through Traefik at `backend.sauravkumar.cloud`
- Health checks for both web and database services
- Automatic SSL certificate management

## Environment Variables

Create a `.env` file with the following variables:

### Frontend Variables
```
VITE_BASE_URL=your_api_base_url
```

### Backend Variables
```
ENVIRONMENT=production
DEBUG=0
SECRET_KEY=your_secret_key
DATABASE_URL=postgresql://postgres:password@db:5432/dbname
POSTGRES_PASSWORD=your_db_password
POSTGRES_DB=your_db_name

# Twilio Configuration
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_PHONE_NUMBER=your_twilio_phone

# Email Configuration
MAIL_USERNAME=your_mail_username
MAIL_PASSWORD=your_mail_password
MAIL_FROM=your_mail_from
MAIL_PORT=587
MAIL_SERVER=your_mail_server
MAIL_FROM_NAME=your_mail_from_name

# Payment Configuration
PAYPAL_BASE_URL=your_paypal_url
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_secret

# Authentication
GOOGLE_APPLICATION_CREDENTIALS=your_google_credentials
APPLE_KEYS_URL=your_apple_keys_url
APPLE_ISSUER=your_apple_issuer
APPLE_CLIENT_ID=your_apple_client_id
```

## Networks

The setup uses two Docker networks:
- `traefik-public`: External network for Traefik communication
- `backend-network`: Internal network for backend services

## Volumes

- `letsencrypt`: Stores SSL certificates
- `postgres_data`: Persists database data

## Health Checks

- Backend web service: Checks `/health` endpoint every 30s
- PostgreSQL: Checks database connectivity every 10s

## Security Notes

- Traefik dashboard is disabled for security
- All services use HTTPS
- Database is not exposed to the public internet
- Sensitive data is managed through environment variables

## Maintenance

### Viewing Logs
```bash
# Traefik logs
docker-compose -f traefik-compose.yml logs -f traefik

# Backend logs
docker-compose -f backend-compose.yml logs -f web

# Frontend logs
docker-compose -f frontend-compose.yml logs -f hurry-solv-web
```

### Updating Services
```bash
# Update specific service
docker-compose -f <compose-file>.yml pull <service-name>
docker-compose -f <compose-file>.yml up -d --no-deps <service-name>

# Update all services
docker-compose -f <compose-file>.yml pull
docker-compose -f <compose-file>.yml up -d
```

## Troubleshooting

1. Certificate Issues:
   - Check Traefik logs for certificate generation errors
   - Verify domain DNS settings
   - Ensure ports 80 and 443 are accessible

2. Service Connection Issues:
   - Verify network connectivity: `docker network inspect traefik-public`
   - Check service logs for errors
   - Verify environment variables are set correctly

3. Database Connection Issues:
   - Check if PostgreSQL container is healthy
   - Verify database credentials
   - Check network connectivity between services

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
