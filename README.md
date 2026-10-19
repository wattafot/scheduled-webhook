# Scheduled webhook

A minimal five-minute HTTPS timer. This repository contains no application code,
customer records, service address, credentials, or service responses. Standard
GitHub-hosted runners in public repositories do not consume private Actions minutes.

Configure these repository secrets:

- `WEBHOOK_URL`: an HTTPS endpoint accepting POST requests.
- `WEBHOOK_TOKEN`: its bearer token.

Set repository variable `WEBHOOK_ENABLED=true` to enable requests. The endpoint
must return HTTP 200 and JSON containing `"ok": true`. Responses are discarded
without being printed. Failed requests fail the workflow; inspect private service
logs for details. No third-party actions or application checkout are needed.
Only schedule and manual dispatch events are supported, so pull requests cannot
execute this workflow with its secrets.

The five-minute schedule avoids the top of the hour. GitHub schedules can be
delayed or dropped during high load; this is a maintenance timer, not a guaranteed
delivery deadline. A separate monthly job commits only a UTC heartbeat timestamp,
preventing the 60-day inactivity shutdown of public scheduled workflows. The
request job has no repository permissions; only the heartbeat job can write.
Manual dispatch exercises both jobs.

To pause requests, set `WEBHOOK_ENABLED=false`. For failures, check the Actions
run and the private endpoint's health and logs. Rotate both endpoint authorization
and `WEBHOOK_TOKEN` together. Configure GitHub Actions failure notifications in
your account to receive failed-run alerts.

References: [Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions),
[scheduled events](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule).
