# matrix-docker
Deploy your own Matrix homeserver with a single Docker Compose file using the Conduit homeserver and Cinny Matrix client with Caddy for SSL termination and reverse proxying.

## Prerequisites
- Docker and Docker Compose installed on your server
- Ports `80/tcp` and `443/tcp` open on your server
- A domain name (e.g., `example.com`) with DNS records configured
- Directories created for Caddy and Cinny configurations
- Configuration files adjusted to your liking

## DNS Configuration
Ensure you have the following DNS records set up for your domain:
- `A` record for `matrix.example.com` pointing to your server's IP
- `A` record for `cinny.example.com` pointing to your server's IP
- `A` record for `example.com` pointing to another Caddy instance serving `.well-known` (Optional)

## Setting Up `.well-known` (Optional)
For Matrix federation to work correctly, you need to serve a `.well-known` header on your root domain. This can be done easily with Caddy:

`Caddyfile`
```
{
	email email@example.com
}
example.com {
	root * /sites/example.com
	file_server
	header {
		Permissions-Policy interest-cohort=()
		Strict-Transport-Security max-age=31536000;
		X-Content-Type-Options nosniff
		X-Frame-Options DENY
		Referrer-Policy no-referrer-when-downgrade
	}
	header /.well-known/matrix/* Content-Type application/json
	header /.well-known/matrix/* Access-Control-Allow-Origin *
	respond /.well-known/matrix/server `{"m.server": "matrix.example.com:443"}`
	respond /.well-known/matrix/client `{"m.homeserver": {"base_url": "https://matrix.example.com"}}`
}
```

## Test Federation (Optional)
Once you have configured DNS and deployed your Matrix homeserver, test federation via the Matrix Federation Tester: https://federationtester.matrix.org
