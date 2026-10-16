# AURA Maritime Analytics Backend

Spring Boot REST API intended for the **AURA maritime fleet analytics** React/Vite frontend in the supplied `aura.zip`. It replaces the mismatched road-vehicle Green Fleet backend with maritime vessel, route, fuel prediction, and fleet-plan endpoints.

## Requirements
- Java 17 or newer
- Maven 3.8+
- The AURA frontend runs separately with Node.js and Vite

## Run locally
From this folder:

```bash
mvn spring-boot:run
```

The API starts at `http://localhost:8080`.

## Endpoints
| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/v1/health` | Health check |
| GET | `/api/v1/fleet/vessels` | Demo vessel list |
| GET | `/api/v1/routes` | Demo maritime routes |
| GET | `/api/v1/config/fuels` | Configured fuel price/emission assumptions |
| POST | `/api/v1/predictions/fuel` | Fuel, cost, CO2 and travel-time estimate |
| POST | `/api/v1/fleet/optimize` | Compare two deterministic fleet/speed plans |

## Example fuel prediction request
```json
{
  "vesselId": "VESSEL-01",
  "distanceNM": 1200,
  "cargoLoadPercent": 82,
  "cruisingSpeedKnots": 14,
  "fuelType": "LNG",
  "seaConditionBeaufort": 3
}
```

## Example optimization request
```json
{
  "routeId": "R-101",
  "objectives": { "minimizeFuel": true, "minimizeCost": true, "minimizeCO2": true },
  "constraints": {
    "cargoDemandTons": 5000,
    "scheduleMaxHours": 100,
    "fuelBudgetLakhINR": 100,
    "maxDelayHours": 12,
    "availableVesselIds": ["VESSEL-01", "VESSEL-03", "VESSEL-05"]
  }
}
```

## Frontend integration
The AURA frontend (Vite, port 3000) now calls this API through `src/config/api.ts` (Predictor, Simulator and Optimizer sections). If the backend is unreachable, it automatically falls back to the local engine in `mathEngine.ts` and shows "Local Engine (backend offline)". Set `VITE_API_URL` in the frontend `.env` to point to a deployed backend.

## Accuracy and academic honesty
This is a **student prototype** using demo vessels, routes, fixed assumptions, and simplified equations. The “Quantum-Inspired QUBO Annealing” plan label is returned for compatibility with the current UI label, but no QUBO solver, quantum hardware, or full MILP solver is executed. The optimizer is a deterministic heuristic comparison. Fuel prices, emission factors, and liter-equivalent units are illustrative and must be validated before real-world use. Do not present the outputs as certified maritime engineering predictions.

## CORS / deployment
Local Vite origins are allowed by default. For deployment set `AURA_CORS_ALLOWED_ORIGINS` to a comma-separated list of exact trusted frontend origins, for example `https://your-aura-site.example`. Do not use wildcard origins for a credentialed production app.
