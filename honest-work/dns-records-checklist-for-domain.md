Whenever you're setting up a domain either entirely new or migrating the site,

make sure:

1. HTTP to HTTPs redirect works. Ensure there are no double redirects or loops.
2. `www` redirects to your primary record. if it would be http request, with #1, it would become https.
3. #2 is especially important Whenever you're migrating away from a saas.

Always check your DNS thorougly (HTTP, HTTPS, Upgrade to HTTPS, SSL Certificates, CNAMES, MX records) etc.
