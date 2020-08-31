# team role

The `team` role represents a user that has some service they want to expose and
a machine they want to collect metrics on.

This Docker Compose environment launches an NGINX server on port `8080` and
deploys the nginx_exporter on port `8081`.

For machine monitoring, the Grafana Cloud Agent is running on port `12345`. Note
that it is not set to scrape NGINX, which will be handled by the scraping
service. Rather, the Agent will just run the Agent and Node integrations.
