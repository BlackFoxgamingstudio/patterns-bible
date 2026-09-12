# Standard Operating Procedure: Sovereign Patterns Bible

## 1. Service Health Verification
Run `sovereign-patterns-bible --health` to confirm the engine responds with status `HEALTHY`.

## 2. Webhook Adapter Operation
The webhook adapter listens on port `8794`:
```bash
python3 n8n/webhook_adapter.py
```
If port 8794 is occupied, check active processes:
```bash
lsof -i :8794
```

## 3. n8n Integration Testing
Send a probe POST request:
```bash
curl -X POST http://localhost:8794/api/v1/execute \
  -H "Content-Type: application/json" \
  -d '{"action": "health_ping", "payload": {"test": true}}'
```
