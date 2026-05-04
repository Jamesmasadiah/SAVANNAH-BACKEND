 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/README.md b/README.md
index 8b137891791fe96927ad78e64b0aad7bded08bdc..f0ad89d1a511f4af68fbca4f223d9738dc9dfd68 100644
--- a/README.md
+++ b/README.md
@@ -1 +1,105 @@
+# Savannah Backend API
 
+FastAPI backend for a **property management system** with:
+- JWT authentication (admin/accountant/tenant flows)
+- Property, unit, and transaction reporting endpoints
+- M-Pesa STK push initiation, callback handling, and status checks
+- MongoDB persistence with automatic seed data for quick local testing
+
+## Tech Stack
+- Python
+- FastAPI + Uvicorn
+- MongoDB (PyMongo)
+- JWT (`pyjwt`)
+- Requests (for Safaricom M-Pesa API)
+
+## Project Structure
+- `main.py` — API app, auth, dashboard, property/unit/transaction endpoints, payments and M-Pesa callback/status flows.
+- `mpesa_service.py` — M-Pesa OAuth, STK push, and transaction status query helper.
+- `test_endpoints.py` — endpoint smoke/integration test runner script.
+- `requirements.txt` — Python dependencies.
+
+## Prerequisites
+- Python 3.10+
+- MongoDB running locally or reachable remotely
+
+## Setup
+1. Clone the repository and enter it.
+2. Create and activate a virtual environment.
+3. Install dependencies:
+
+```bash
+pip install -r requirements.txt
+```
+
+4. Create a `.env` file in the repository root:
+
+```env
+MONGO_URI=mongodb://localhost:27017
+
+# M-Pesa
+MPESA_CONSUMER_KEY=your_consumer_key
+MPESA_CONSUMER_SECRET=your_consumer_secret
+MPESA_PASSKEY=your_passkey
+MPESA_SHORTCODE=174379
+MPESA_CALLBACK_URL=https://your-public-url/api/mpesa/callback
+
+# Optional local fallback phone
+MPESA_TEST_PHONE=2547XXXXXXXX
+```
+
+## Run the API
+```bash
+uvicorn main:app --reload --host 0.0.0.0 --port 8000
+```
+
+API docs:
+- Swagger UI: `http://localhost:8000/docs`
+- ReDoc: `http://localhost:8000/redoc`
+
+## Seeded Test Users
+On first run (empty database), the app seeds default users:
+- **Admin**: `admin@savannah.co.ke` / `admin123`
+- **Accountant**: `accountant@savannah.co.ke` / `account123`
+- **Tenant**: `tenant001@savannah.co.ke` / `tenant123`
+
+## Main API Endpoints
+### Authentication
+- `POST /api/auth/login`
+- `POST /api/auth/register`
+
+### Dashboard
+- `GET /api/dashboard/stats`
+- `GET /api/dashboard/monthly-collections`
+
+### Core Data
+- `GET /api/properties`
+- `GET /api/units`
+- `GET /api/transactions`
+- `GET /api/arrears`
+
+### Payments & M-Pesa
+- `POST /api/payments/initiate`
+- `POST /api/mpesa/stkpush`
+- `GET /api/mpesa/status/{checkout_request_id}`
+- `POST /api/mpesa/callback`
+- `GET /api/mpesa/pending`
+
+### Health/Root
+- `GET /`
+
+> Most endpoints require a Bearer token from `POST /api/auth/login`.
+
+## Running Endpoint Tests
+With the API running locally on port `8000`:
+
+```bash
+python test_endpoints.py
+```
+
+The script tests authentication and key API workflows and writes results to `test_results.json`.
+
+## Notes
+- CORS is currently configured as open (`*`) for local development convenience.
+- The JWT secret is currently hardcoded in `main.py`; move it to environment variables for production.
+- Use HTTPS and a publicly reachable callback URL when testing real M-Pesa callback flows.
 
EOF
)
