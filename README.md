# healthz

Interactive HTTP health monitoring tool with real-time metrics visualization.

## Features

- 🚀 **Constant-rate load testing** - Send requests at exact intervals
- 📊 **Real-time metrics** - Live p50/p95/p99 latency percentiles
- 📈 **Time-series graphs** - Visualize performance trends
- ⌨️  **Interactive controls** - Adjust rate and window on-the-fly
- 🎯 **Blocking detection** - Track pending requests and timeouts
- 💾 **Export data** - Save metrics to CSV for analysis

## Installation

```bash
# From source
cd healthz
pip install -e .

# Or install dependencies directly
pip install aiohttp textual click
```

## Usage

### Basic

```bash
# Monitor a health endpoint at 30 requests/second
healthz http://localhost:8000/healthz --rate 30
```

### Advanced

```bash
# POST request with custom headers
healthz http://api.example.com/endpoint \
  --rate 50 \
  --method POST \
  --header "Authorization: Bearer token" \
  --data '{"check": true}'
```

## Interactive Controls

While the TUI is running:

| Key | Action |
|-----|--------|
| `↑` / `+` | Increase request rate |
| `↓` / `-` | Decrease request rate |
| `w` | Increase time window |
| `s` | Decrease time window |
| `p` | Pause/resume sending requests |
| `r` | Reset all statistics |
| `e` | Export current metrics to CSV |
| `q` | Quit |

## Display

The TUI shows:

- **Metrics Panel**: Request counts, success rate, pending requests
- **Latency Stats**: Min, avg, p50, p95, p99, max response times
- **Histogram**: Response time distribution over the current window
- **Time Series**: Graph of p99 latency over time

### Health Status

- 🟢 **Green** (Healthy): Pending < rate
- 🟡 **Yellow** (Degraded): Pending < rate × 3
- 🔴 **Red** (Blocked): Pending ≥ rate × 3

## Use Cases

### Detect Blocking in Async Services

Monitor `/healthz` while load testing other endpoints to detect when your async event loop gets saturated:

```bash
# Terminal 1: Monitor health check
healthz http://localhost:8000/healthz --rate 30

# Terminal 2: Load your API
while true; do
  curl -X POST http://localhost:8000/api/endpoint \
    -H "Content-Type: application/json" \
    -d '{"data": "test"}'
done
```

If the health check starts showing high pending counts or timeouts, your service is blocking!

### Find Resource Limits

Gradually increase the rate to find when your service starts degrading:

1. Start at low rate: `healthz URL --rate 10`
2. Press `↑` repeatedly to increase rate
3. Watch for pending count to rise or p95/p99 to spike
4. That's your service's limit!

### Compare Before/After

Export baseline metrics, make code changes, run again and compare:

```bash
# Before optimization
healthz http://localhost:8000/healthz --rate 50
# Press 'e' to export baseline.csv

# After optimization
healthz http://localhost:8000/healthz --rate 50
# Press 'e' to export optimized.csv
```

## Development

```bash
# Install in development mode
pip install -e ".[dev]"

# Run tests
pytest

# Format code
black healthz/
ruff check healthz/
```

## License

MIT
# healthz
