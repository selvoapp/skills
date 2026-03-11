# Troubleshooting Webhooks

Solutions for common problems with Beacon webhook deliveries.

## Webhooks are not firing

**Cause:** The webhook endpoint is disabled or the target URL is unreachable from Beacon's servers.

**Solution:**

1. Go to **Settings > Webhooks** and confirm the endpoint status shows **Active**.
2. Check that the target URL is publicly accessible. Beacon cannot deliver webhooks to `localhost` or private network addresses.
3. Click **Send Test Event** next to the endpoint. If the test fails, the response details appear in the delivery log.

> [!TIP]
> Use a tool like [webhook.site](https://webhook.site) to test your endpoint URL before configuring it in Beacon. This confirms the URL is reachable and shows you the exact payload Beacon sends.

## Deliveries return a 401 or 403 error

**Cause:** Your server is rejecting the request because the authentication header is missing or incorrect.

**Solution:**

1. Open the failed delivery in **Settings > Webhooks > Delivery Log** and expand the request headers.
2. Confirm the `X-Beacon-Signature` header is present. Beacon signs every webhook payload with the secret key shown in your endpoint settings.
3. On your server, verify the signature against the raw request body:

```javascript
const crypto = require("crypto");

function verifySignature(payload, signature, secret) {
  const expected = crypto
    .createHmac("sha256", secret)
    .update(payload)
    .digest("hex");
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expected)
  );
}
```

4. If you recently rotated your webhook secret in Beacon, update the secret on your server to match.

> [!WARNING]
> Do not use simple string comparison (`===`) to verify signatures. This is vulnerable to timing attacks. Use a constant-time comparison function like `crypto.timingSafeEqual`.

## Events are delivered out of order

**Cause:** Beacon delivers webhooks asynchronously. If your endpoint responds slowly or times out, retries can arrive before or alongside newer events.

**Solution:**

Every webhook payload includes a `timestamp` field (Unix epoch in milliseconds) and a sequential `event_id`. Use these to process events in the correct order:

```json
{
  "event_id": 48291,
  "event_type": "task.updated",
  "timestamp": 1709312400000,
  "data": {
    "task_id": "BEA-142",
    "changes": {
      "status": { "from": "in_progress", "to": "done" }
    }
  }
}
```

If your system receives an event with a lower `event_id` than the last processed event for that resource, discard it or reprocess based on the `timestamp`.

## Deliveries are delayed by several minutes

**Cause:** Your endpoint is responding slowly, which triggers Beacon's retry queue and backs up subsequent deliveries.

**Solution:**

- Respond with a `200` status within 5 seconds. Process the payload asynchronously after acknowledging receipt.
- If your endpoint consistently takes longer than 5 seconds, Beacon backs off with exponential delays: 10 seconds, 30 seconds, 1 minute, 5 minutes, then 15 minutes.

<details>
<summary>Beacon's retry schedule</summary>

| Attempt | Delay after previous attempt | Total time elapsed |
| --- | --- | --- |
| 1 | Immediate | 0 seconds |
| 2 | 10 seconds | 10 seconds |
| 3 | 30 seconds | 40 seconds |
| 4 | 1 minute | 1 min 40 sec |
| 5 | 5 minutes | 6 min 40 sec |
| 6 (final) | 15 minutes | 21 min 40 sec |

After 6 failed attempts, the delivery is marked as **Failed** in the delivery log. Beacon does not retry further.

</details>

## Webhook endpoint was automatically disabled

**Cause:** Beacon disables endpoints that fail 50 consecutive deliveries. This prevents unnecessary load on both your server and Beacon's delivery system.

**Solution:**

1. Fix the underlying issue (server error, expired credentials, DNS misconfiguration).
2. Go to **Settings > Webhooks**, find the disabled endpoint, and click **Re-enable**.
3. Click **Send Test Event** to confirm the endpoint is working.

> [!CAUTION]
> Re-enabling an endpoint does not replay the failed deliveries. If you need to recover missed events, use the [Events API](/articles/22-events-api) to fetch events for a specific time range.

## Still not working?

If your webhook issue is not covered here, reach out to [Beacon support](mailto:support@beacon.io) with:

- Your workspace ID (found in **Settings > General**)
- The webhook endpoint URL
- A timestamp range for the failed deliveries

Our team can check the delivery logs on our end and help diagnose the issue.

## What to read next

- [Webhook event reference](/articles/21-webhook-events)
- [Events API](/articles/22-events-api)
- [Configuring notifications](/articles/4-configuring-notifications)
