# Webhook Server

A simple Node.js webhook server for handling real-time updates and notifications.

## Features

- ✅ Webhook signature verification for security
- 🔄 Support for various event types (user creation, updates, messages, status changes)
- 🛡️ Security middleware (Helmet, CORS)
- 📝 Comprehensive logging
- 🔍 Health check endpoint
- 🚀 Easy deployment and configuration

## Quick Start

### 1. Install Dependencies

```bash
npm install
```

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` and set your webhook secret:

```bash
WEBHOOK_SECRET=your-actual-webhook-secret-here
PORT=3000
```

### 3. Start the Server

For development:
```bash
npm run dev
```

For production:
```bash
npm start
```

## Configuration

### Webhook Setup

Based on your webhook configuration interface:

1. **Callback URL**: `http://your-domain.com/webhook` (or `https://your-domain.com/webhook` for production)
2. **Verify Token**: Set this to match your `WEBHOOK_SECRET` environment variable
3. **Enable Options**:
   - ✅ Include field names and values for changed fields
   - ✅ Attach client certificate to webhook requests (if needed)

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `PORT` | Server port (default: 3000) | No |
| `WEBHOOK_SECRET` | Secret token for webhook verification | Recommended |

## API Endpoints

### POST /webhook
Main webhook endpoint that receives and processes webhook events.

**Headers:**
- `Content-Type: application/json`
- `X-Signature: sha256=...` (for signature verification)

**Response:**
```json
{
  "received": true,
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

### GET /health
Health check endpoint for monitoring.

**Response:**
```json
{
  "status": "OK",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

## Event Types

The server handles the following event types:

- `user.created` - New user registration
- `user.updated` - User information changes
- `message.received` - New message received
- `status.changed` - Status updates

## Security

- **Signature Verification**: Validates webhook authenticity using HMAC-SHA256
- **CORS Protection**: Configurable cross-origin request handling
- **Helmet**: Security headers for common vulnerabilities
- **Input Validation**: Validates and sanitizes incoming webhook data

## Development

### Testing Webhooks Locally

1. Use ngrok to expose your local server:
   ```bash
   ngrok http 3000
   ```

2. Use the ngrok URL as your webhook callback URL:
   ```
   https://abc123.ngrok.io/webhook
   ```

### Testing with curl

```bash
# Test webhook endpoint
curl -X POST http://localhost:3000/webhook \
  -H "Content-Type: application/json" \
  -H "X-Signature: sha256=test" \
  -d '{"type": "user.created", "user": {"id": "123", "name": "Test User"}}'

# Test health check
curl http://localhost:3000/health
```

## Deployment

### Using PM2 (Recommended for production)

```bash
npm install -g pm2
pm2 start server.js --name webhook-server
pm2 startup
pm2 save
```

### Using Docker

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### Environment Setup for Production

1. Set a strong `WEBHOOK_SECRET`
2. Use HTTPS (configure reverse proxy like nginx)
3. Set up proper logging and monitoring
4. Configure firewall rules
5. Use environment-specific configurations

## Customization

To add custom event handlers:

1. Add a new case in the `handleWebhookEvent` function
2. Create a corresponding handler function
3. Implement your custom logic (database updates, notifications, etc.)

Example:
```javascript
case 'custom.event':
  handleCustomEvent(data);
  break;

function handleCustomEvent(data) {
  console.log('🎯 Custom event received:', data);
  // Your custom logic here
}
```

## Troubleshooting

### Common Issues

1. **Invalid Signature**: Check that `WEBHOOK_SECRET` matches your webhook configuration
2. **Connection Refused**: Ensure the server is running and accessible
3. **400 Bad Request**: Check that webhook payload is valid JSON

### Logs

The server provides detailed logging for debugging:
- ✅ Successful signature verification
- ❌ Invalid signatures
- 📥 Incoming webhook data
- 🔔 Event processing status

## License

MIT