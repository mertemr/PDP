![PDP.png](imgs/PDP.png)
# Permit.io PDP
The PDP (Policy decision point) syncs with the authorization service and maintains up-to-date policy cache for open policy agent.

## Running a PDP
PDPs are connected to your [Permit.io account](https://docs.permit.io/quickstart) using an API Key.
Check out the [Permit.io documentation](https://docs.permit.io/manage-your-account/projects-and-env#fetching-and-rotating-the-api-key) to learn how to get an Environment API Key.

You can run a PDP in a docker container by running the following command:
```bash
docker run -it -p 7766:7000 -e PDP_API_KEY=<YOUR_API_KEY> -e PDP_DEBUG=True permitio/pdp-v2:latest
```

### Deploying PDP to Production
You can deploy the PDP to production in multiple designs. See the [Permit.io documentation](https://docs.permit.io/concepts/pdp/overview) for more information.

## Contributing

### Setting up the development environment
1. Clone the repository
2. Install the dependencies
```bash
pip install ".[dev]"
```

### Running locally (during development)
```
PDP_API_KEY=<YOUR_API_KEY> uvicorn horizon.main:app --reload --port=7000
```

You can pass environment variables to control the behavior of the PDP image.
For example, running a local PDP against the Permit API:
```
PDP_CONTROL_PLANE=https://api.permit.io PDP_API_KEY=<YOUR_API_KEY> uvicorn horizon.main:app --reload --port=7000
```

## Configuration and Observability

### Environment Variables

The PDP supports various environment variables for configuring runtime behavior, logging, and health checks:

#### Logging Configuration
- `PDP_HORIZON_LOG_LEVEL` - Log level for the Python application (default: `INFO`)
  - Supported values: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`
  - Example: `PDP_HORIZON_LOG_LEVEL=DEBUG`

- `PDP_HORIZON_HEALTH_CHECK_LOG_ENABLED` - Enable logging for health check endpoint requests (default: `false`)
  - Set to `true` to log all health check requests (useful for debugging but can be noisy)
  - Example: `PDP_HORIZON_HEALTH_CHECK_LOG_ENABLED=true`

#### Health Check Configuration
- `PDP_HORIZON_HEALTH_CHECK_INTERVAL` - Interval between health checks in seconds (default: `5`)
  - Example: `PDP_HORIZON_HEALTH_CHECK_INTERVAL=10`

- `PDP_HORIZON_HEALTH_CHECK_DISABLED` - Disable health checks entirely (default: `false`)
  - Set to `true` to disable the health check mechanism completely
  - Example: `PDP_HORIZON_HEALTH_CHECK_DISABLED=true`

- `PDP_HORIZON_HEALTH_CHECK_TIMEOUT` - Health check endpoint timeout in seconds (default: `1`)
  - Example: `PDP_HORIZON_HEALTH_CHECK_TIMEOUT=2`

- `PDP_HORIZON_HEALTH_CHECK_FAILURE_THRESHOLD` - Number of consecutive health check failures before restarting (default: `12`)
  - Example: `PDP_HORIZON_HEALTH_CHECK_FAILURE_THRESHOLD=5`

#### Uvicorn/Gunicorn Configuration
- `PDP_HORIZON_UVICORN_ARGS` - Additional Uvicorn arguments (default: `""`)
  - Pass custom arguments to Uvicorn for advanced configuration
  - Example: `PDP_HORIZON_UVICORN_ARGS="--workers 4 --timeout 120"`

#### Example: Running with Custom Configuration
```bash
docker run -it -p 7766:7000 \
  -e PDP_API_KEY=<YOUR_API_KEY> \
  -e PDP_HORIZON_LOG_LEVEL=DEBUG \
  -e PDP_HORIZON_HEALTH_CHECK_LOG_ENABLED=false \
  -e PDP_HORIZON_HEALTH_CHECK_INTERVAL=10 \
  -e PDP_HORIZON_UVICORN_ARGS="--workers 2" \
  permitio/pdp-v2:latest
```

## Building a Custom PDP Docker image
For ARM architecture:
```
VERSION=<TAG> make build-arm64
```
For AMD64 architecture:
```
VERSION=<TAG> make build-amd64
```

### Running the image in development mode
```
VERSION=<TAG> API_KEY=<PDP_API_KEY> make run
```
