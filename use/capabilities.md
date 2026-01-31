# Home

## AI4I-Core Microservices Platform



A comprehensive microservices platform built with FastAPI, following the AI4I-Core Platform's Frontend-Backend Communication Flow pattern. This platform implements a 6-step communication process with enterprise-grade API services and capabilities.

### 🏗️ Architecture Overview



The platform consists of **11 microservices**, **1 frontend application**, and **9 infrastructure components**:

#### Microservices



* **API Gateway Service** (Port 8080) - Central entry point with routing, rate limiting, and authentication
* **Authentication & Authorization Service** (Port 8081) - Identity management with JWT and OAuth2
* **Configuration Management Service** (Port 8082) - Centralized configuration and feature flags
* **Metrics Collection Service** (Port 8083) - System and application metrics collection
* **Telemetry Service** (Port 8084) - Log aggregation, distributed tracing, and event correlation
* **Alerting Service** (Port 8085) - Proactive issue detection and notification
* **Dashboard Service** (Port 8086) - Visualization and reporting with Streamlit UI (Port 8501)
* **ASR Service** (Port 8087) - Speech-to-Text with 22+ Indian languages
* **TTS Service** (Port 8088) - Text-to-Speech with multiple voice options
* **NMT Service** (Port 8089) - Neural Machine Translation for Indian languages
* **Model Management Service** (Port 8094) - Centralized model registry and version management

#### Frontend



* **Simple UI Frontend** (Port 3000) - Web interface for testing AI services

#### Infrastructure Components



* **PostgreSQL** (Port 5432) - Primary database with separate schemas for each service
* **Redis** (Port 6379) - Caching, session management, and rate limiting
* **InfluxDB** (Port 8086) - Time-series database for metrics storage
* **OpenSearch** (Port 9204) - Log storage and search engine (backend storage used by Fluent Bit)
* **OpenSearch Dashboards** (Port 5602) - Log visualization UI (frontend that connects to OpenSearch)
* **Kafka** (Port 9092) - Event streaming and message queuing
* **Zookeeper** (Port 2181) - Kafka coordination service
* **Jaeger** (Port 16686) - Distributed tracing UI for request flow visualization
* **Fluent Bit** - Log collection and processing from Docker containers

### 🚀 Quick Start



#### Prerequisites



* Docker 20.10+
* Docker Compose 2.0+
* 8GB RAM minimum
* 20GB disk space

#### Installation



1.  **Clone the repository**

    ```
    git clone <repository-url>
    cd AI4I-Core-microservices
    ```
2.  **Set up environment variables**

    ```
    cp env.template .env
    # Edit .env with your configuration values
    ```
3.  **Start all services**

    ```
    ./scripts/start-all.sh
    ```
4.  **Verify installation**

    ```
    ./scripts/health-check-all.sh
    ```

### 📊 Service URLs



#### Once running, access the services at:



* **API Gateway**: [http://localhost:8080](http://localhost:8080/)
* **Auth Service**: [http://localhost:8081](http://localhost:8081/)
* **Config Service**: [http://localhost:8082](http://localhost:8082/)
* **Metrics Service**: [http://localhost:8083](http://localhost:8083/)
* **Telemetry Service**: [http://localhost:8084](http://localhost:8084/)
* **Alerting Service**: [http://localhost:8085](http://localhost:8085/)
* **Dashboard Service**: [http://localhost:8086](http://localhost:8086/)
* **ASR Service**: [http://localhost:8087](http://localhost:8087/)
* **TTS Service**: [http://localhost:8088](http://localhost:8088/)
* **NMT Service**: [http://localhost:8089](http://localhost:8089/)
* **Model Management Service**: [http://localhost:8094](http://localhost:8094/)
* **Simple UI**: [http://localhost:3000](http://localhost:3000/)
* **Streamlit Dashboard**: [http://localhost:8501](http://localhost:8501/)

#### API Documentation (Swagger UI)



* **ASR Service**: [http://localhost:8087/docs](http://localhost:8087/docs)
* **TTS Service**: [http://localhost:8088/docs](http://localhost:8088/docs)
* **NMT Service**: [http://localhost:8089/docs](http://localhost:8089/docs)
* **Model Management Service**: [http://localhost:8094/docs](http://localhost:8094/docs)
* **API Gateway**: [http://localhost:8080/docs](http://localhost:8080/docs)

#### Infrastructure



* **PostgreSQL**: localhost:5432
* **Redis**: localhost:6379
* **InfluxDB**: [http://localhost:8086](http://localhost:8086/)
* **OpenSearch**: [http://localhost:9204](http://localhost:9204/)
* **OpenSearch Dashboards**: [http://localhost:5602](http://localhost:5602/)
* **Jaeger UI**: [http://localhost:16686](http://localhost:16686/)
* **Kafka**: localhost:9092

### 🔐 Auth Service



**Port**: 8081

#### Capabilities



**User Authentication (JWT)**



* Email/password registration and login
* Secure password hashing and strength validation
* Short-lived access tokens and refresh tokens

**Session Management**



* Persistent user\_sessions table in PostgreSQL
* Session creation on login (device info, IP, user agent)
* Session validation and refresh with expiry checks
* Logout from a single session or all sessions
* Session cleanup and invalidation on password change

**RBAC (Role-Based Access Control)**



* Central role and permission model (roles, permissions, user\_roles, role\_permissions)
* Role assignment and removal per user
* Permission lookup for users (consumed by gateway and services)

**API Key Management**



* Create, list, update, and deactivate API keys
* Per-key permissions and expiry
* API key validation endpoint used by gateway and services

**OAuth2 (Social Login)**



* Support for external providers (e.g., Google; extensible to others)
* Authorization and callback flow to create or log in users
* Session creation and JWT issuance after successful OAuth login

**Service Integration**



* Token and API key validation endpoints for API Gateway
* Status endpoint advertising service capabilities (observability)

#### Endpoints



**Core Authentication**



* `GET /api/v1/auth/status` – Returns service status and supported features
* `POST /api/v1/auth/register` – Register a new user using email, username, password, and profile info
* `POST /api/v1/auth/login` – Email/password login; returns access token, refresh token, and expiry
* `POST /api/v1/auth/refresh` – Issue a new access token using a valid refresh token after session validation
* `POST /api/v1/auth/logout` – Logout a specific session using refresh token or all sessions if none provided
* `GET/POST /api/v1/auth/validate` – Validate access token and return user details, roles, and permissions
* `POST /api/v1/auth/validate-api-key` – Validate API key and return associated user, permissions, and key status

**User Self-Service**



* `GET /api/v1/auth/me` – Fetch current user profile and assigned roles
* `PUT /api/v1/auth/me` – Update current user profile fields (name, language, timezone)
* `POST /api/v1/auth/change-password` – Change password and invalidate all other active sessions
* `POST /api/v1/auth/request-password-reset` – Initiate password reset flow (email-based stub)
* `POST /api/v1/auth/reset-password` – Reset password using a reset token (placeholder)

**API Key Management**



* `POST /api/v1/auth/api-keys` – Create a new API key with permissions, expiry, and active flag
* `GET /api/v1/auth/api-keys` – List API keys for the current user
* `GET /api/v1/auth/api-keys/all` (Admin) – List all API keys across users
* `DELETE /api/v1/auth/api-keys/{key_id}` – Soft-deactivate an API key
* `PATCH /api/v1/auth/api-keys/{key_id}` (Admin) – Update API key name, permissions, status, or expiry

**RBAC / Admin**



* `POST /api/v1/auth/roles/assign` – Assign a role to a user
* `POST /api/v1/auth/roles/remove` – Remove a role from a user
* `GET /api/v1/auth/roles/user/{user_id}` – Retrieve roles assigned to a specific user
* `GET /api/v1/auth/roles/list` – List all available roles
* `GET /api/v1/auth/users/{user_id}` (Admin) – Get detailed information for a specific user
* `GET /api/v1/auth/users` (Admin) – List users with summary information
* `GET /api/v1/auth/permissions` (Admin) – List detailed permission objects
* `GET /api/v1/auth/permission/list` – List permission names for role assignment

**OAuth2**



* `GET /api/v1/auth/oauth2/providers` – List configured OAuth2 providers and authorization URLs
* `GET /api/v1/auth/oauth2/google/authorize` – Start Google OAuth2 login flow
* `GET /api/v1/auth/oauth2/google/callback` – Handle Google OAuth2 callback, create/login user, issue JWT, and redirect

#### Key Features (Summary)



* JWT and Refresh Tokens for secure, stateless access with controlled expiry
* Database-backed session management tracking devices, IPs, user agents, and expiration
* Fine-grained RBAC enforced consistently across all services
* Complete API Key lifecycle management with per-key permissions and expiry
* OAuth2-based SSO support alongside traditional authentication
* Centralized validation endpoints enabling API Gateway and microservices to rely on a single source of truth

### 📊 Telemetry Service



**Port**: 8084

#### Capabilities



* Log aggregation and processing
* Distributed tracing coordination
* Event correlation and enrichment
* Integration with OpenSearch, Kafka, and PostgreSQL

#### Endpoints



* `GET /health` - Health check with dependency status
* `GET /api/v1/telemetry/status` - Service status and features
* `POST /api/v1/telemetry/logs` - Log ingestion
* `POST /api/v1/telemetry/traces` - Trace ingestion
* `GET /api/v1/telemetry/logs/search` - Search logs
* `GET /api/v1/telemetry/traces/search` - Search traces

### 🤖 AI/ML Services



#### ASR Service (Automatic Speech Recognition)



**Port**: 8087

**Capabilities**



* Speech-to-text conversion for 22+ Indian languages
* Real-time streaming ASR via WebSocket
* Batch processing with multiple audio inputs
* Audio format support: WAV, MP3, FLAC, OGG
* Voice Activity Detection (VAD)
* Inverse Text Normalization (ITN) and punctuation restoration

**Endpoints**



* `POST /api/v1/asr/inference` - Batch inference
* `WebSocket /socket.io/asr` - Streaming inference
* `GET /api/v1/asr/models` - List available models
* `GET /api/v1/asr/health` - Health check

**Supported Languages**: English, Hindi, Tamil, Telugu, Kannada, Malayalam, Bengali, Gujarati, Marathi, Punjabi, Oriya, Assamese, Urdu, Sanskrit, and more

#### TTS Service (Text-to-Speech)



**Port**: 8088

**Capabilities**



* Text-to-speech conversion for 22+ Indian languages
* Multiple voice options (male/female)
* Real-time streaming TTS via WebSocket
* Audio format output: WAV, MP3, OGG
* Audio duration adjustment
* Voice catalog with 6 pre-configured voices

**Endpoints**



* `POST /api/v1/tts/inference` - Batch inference
* `WebSocket /socket.io/tts` - Streaming inference
* `GET /api/v1/tts/voices` - List available voices
* `GET /api/v1/tts/health` - Health check

**Voice Models**: Dravidian (Kannada, Malayalam, Tamil, Telugu), Indo-Aryan (Hindi, Bengali, Gujarati, Marathi, Punjabi), Miscellaneous (English, Bodo, Manipuri)

#### NMT Service (Neural Machine Translation)



**Port**: 8089

**Capabilities**



* Neural machine translation for 22+ Indian languages
* Bidirectional translation (any language pair)
* Batch processing (max 90 texts per request)
* Script code support (Devanagari, Arabic, Tamil, etc.)
* Language detection and confidence scoring

**Endpoints**



* `POST /api/v1/nmt/inference` - Batch inference
* `GET /api/v1/nmt/models` - List available models and language pairs
* `GET /api/v1/nmt/health` - Health check

**Supported Language Pairs**: 400+ combinations including English↔Hindi, English↔Tamil, Hindi↔Tamil, and more

#### Model Management Service



**Port**: 8094

**Capabilities**



* Centralized model registry and catalog management
* Model versioning with status management (ACTIVE/DEPRECATED)
* Service-to-model version association
* Configurable active version limits per model
* Model metadata management (task types, languages, domains, benchmarks)
* Service lifecycle management
* Redis caching for improved performance
* RESTful API endpoints for frontend integration

**Endpoints**



* `GET /models` - List all models with filtering (task type, model name, include deprecated)
* `GET /models/{model_id}` - Get model details by ID (with optional version)
* `POST /models` - Create a new model version
* `PATCH /models` - Update model metadata or version status
* `POST /services/admin/create/model` - Admin endpoint for model creation
* `PATCH /services/admin/update/model` - Admin endpoint for model updates
* `POST /services/details/view_model` - View model details
* `GET /services/details/list_models` - List models with filters
* `GET /services/details/list_services` - List all services
* `POST /services/details/view_service` - View service details
* `GET /health` - Health check

**Key Features**



* **Model Versioning**: Support for multiple versions per model with independent status management
* **Version Status**: ACTIVE versions are available for use, DEPRECATED versions are archived
* **Active Version Limits**: Configurable maximum active versions per model (default: 5)
* **Service Management**: Associate services with specific model versions
* **Task Type Support**: Filter and manage models by task type (ASR, TTS, NMT, etc.)
* **Metadata Tracking**: Comprehensive model information including languages, domains, benchmarks, and inference endpoints

**Technology Stack**: FastAPI, PostgreSQL, Redis, SQLAlchemy

### 🎨 Simple UI Frontend



**Port**: 3000

#### Features



* Modern, responsive web interface built with Next.js 13 and Chakra UI
* ASR testing with microphone recording and file upload
* TTS testing with text input and voice selection
* NMT testing with language pair selection
* Real-time WebSocket streaming for ASR and TTS
* API key management
* Request/response visualization with stats
* Audio waveform visualization

**Technology Stack**: Next.js 13, TypeScript, Chakra UI, TanStack React Query, Socket.IO Client

### 🛠️ Management Scripts



The platform includes several utility scripts for easy management:

#### Start/Stop Services



```
# Start all services
./scripts/start-all.sh

# Stop all services
./scripts/stop-all.sh

# Stop and remove volumes (clean restart)
./scripts/stop-all.sh --volumes
```

#### Service Management



```
# Restart a specific service
./scripts/restart-service.sh api-gateway-service

# Restart with rebuild
./scripts/restart-service.sh auth-service --build
```

#### Monitoring



```
# View logs for all services
./scripts/logs.sh

# View logs for specific service
./scripts/logs.sh api-gateway-service

# Follow logs in real-time
./scripts/logs.sh -f metrics-service

# Check health status
./scripts/health-check-all.sh
```

#### Infrastructure



```
# Initialize infrastructure (run after first start)
./scripts/init-infrastructure.sh
```

### 🔧 Configuration



#### Environment Variables



The platform uses environment variables for configuration. Copy `env.template` to `.env` and customize:

```
# Global Configuration
COMPOSE_PROJECT_NAME=AI4I-Core-microservices
ENVIRONMENT=development

# Database Configuration
POSTGRES_USER=AI4I-Core_user
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=AI4I-Core_platform

# Redis Configuration
REDIS_PASSWORD=your_redis_password

# JWT Configuration
JWT_SECRET_KEY=your_jwt_secret_key
JWT_REFRESH_SECRET_KEY=your_refresh_secret_key

# Service URLs
API_GATEWAY_URL=http://api-gateway-service:8080
# ... (see env.template for complete list)
```

#### Service-Specific Configuration



Each microservice has its own environment file in `services/<service-name>/env.template`:

* `services/api-gateway-service/env.template`
* `services/auth-service/env.template`
* `services/config-service/env.template`
* `services/metrics-service/env.template`
* `services/telemetry-service/env.template`
* `services/alerting-service/env.template`
* `services/dashboard-service/env.template`
* `services/asr-service/env.template`
* `services/tts-service/env.template`
* `services/nmt-service/env.template`
* `frontend/simple-ui/.env.template`

### 🏥 Health Checks



All services include comprehensive health checks:

* **HTTP Health Endpoints**: Each service exposes `/health` endpoint
* **Docker Health Checks**: Built-in container health monitoring
* **Dependency Checks**: Services verify connectivity to their dependencies
* **Automated Monitoring**: Health check script monitors all services

### 🔒 Security



#### Authentication & Authorization



* JWT tokens with configurable expiration
* OAuth2 provider integration (Google, GitHub, Microsoft)
* Role-based access control (RBAC)
* API key management
* Session management with Redis

#### Data Protection



* Input validation and sanitization
* SQL injection prevention
* XSS protection
* CSRF protection
* Data encryption at rest and in transit

#### Network Security



* Service isolation with Docker networks
* Rate limiting per user and IP
* CORS configuration
* Trusted host middleware

### 📈 Monitoring & Observability



#### Metrics Collection



* API usage metrics (request count, response time, error rate)
* System performance metrics (CPU, memory, disk I/O)
* Custom business metrics
* Real-time data streaming

#### Logging



* Structured JSON logging
* Centralized log aggregation with OpenSearch
* Log collection via Fluent Bit from Docker containers
* Request tracing with correlation IDs
* Error logging with stack traces

#### Distributed Tracing



* Jaeger integration for distributed tracing across microservices
* OpenTelemetry SDK integration
* Request flow visualization
* Trace correlation with logs
* Performance analysis and error tracking

#### Alerting



* Machine learning-based anomaly detection
* Threshold-based alerting
* Multi-channel notifications (email, Slack, SMS)
* Alert escalation policies

#### Dashboards



* Real-time metrics visualization
* Interactive charts and graphs
* Customizable dashboard layouts
* Executive reporting

### 🚀 Deployment



#### Development



```
# Start with hot-reloading (uses docker-compose.override.yml)
docker-compose up -d

# View logs
docker-compose logs -f
```

#### Production



```
# Build and start
docker-compose up -d --build

# Scale services
docker-compose up -d --scale api-gateway-service=3
```

#### Kubernetes (Future)



* Kubernetes deployment manifests
* Service mesh integration (Istio)
* Horizontal Pod Autoscaling (HPA)
* ConfigMaps and Secrets management

### 🧪 Testing



#### Running Tests



**Install Test Dependencies**:

```
cd tests
pip install -r requirements.txt
```

**Run All Tests**:

```
pytest
```

**Run Integration Tests**:

```
pytest -m integration
```

**Run Specific Service Tests**:

```
pytest tests/integration/test_asr_service.py
pytest tests/integration/test_tts_service.py
pytest tests/integration/test_nmt_service.py
```

**Run WebSocket Streaming Tests**:

```
pytest -m streaming
```

**Run E2E Tests**:

```
pytest -m e2e
```

**Generate Coverage Report**:

```
pytest --cov=services --cov-report=html
open htmlcov/index.html
```

#### Frontend Tests



**Run Frontend Tests**:

```
cd frontend/simple-ui
npm test
```

**Run with Coverage**:

```
npm test -- --coverage
```

#### Test Categories



* **Unit Tests**: Test individual functions/classes in isolation
* **Integration Tests**: Test service endpoints with real dependencies
* **E2E Tests**: Test complete user workflows with browser automation
* **Performance Tests**: Test response times and throughput

### 🔧 Troubleshooting



#### Common Issues



**Services not starting**:

```
# Check logs
./scripts/logs.sh

# Check health
./scripts/health-check-all.sh
```

**Database connection issues**:

```
# Check PostgreSQL
docker-compose exec postgres pg_isready -U AI4I-Core_user

# Check Redis
docker-compose exec redis redis-cli ping
```

**Port conflicts**:

```
# Check port usage
netstat -tulpn | grep :8080

# Stop conflicting services
sudo systemctl stop apache2  # if using port 80
```

#### Logs and Debugging



```
# View all logs
./scripts/logs.sh

# View specific service logs
./scripts/logs.sh api-gateway-service

# Follow logs in real-time
./scripts/logs.sh -f

# View logs with timestamps
./scripts/logs.sh -t
```

### 📚 Development Guidelines



#### Code Structure



```
AI4I-Core-microservices/
├── services/                 # Microservices
│   ├── api-gateway-service/
│   ├── auth-service/
│   ├── config-service/
│   ├── metrics-service/
│   ├── telemetry-service/
│   ├── alerting-service/
│   ├── dashboard-service/
│   ├── asr-service/
│   ├── tts-service/
│   └── nmt-service/
├── frontend/                 # Frontend applications
│   └── simple-ui/
├── infrastructure/           # Infrastructure setup
│   ├── postgres/            # Database schemas
│   ├── influxdb/            # InfluxDB setup
│   ├── opensearch/          # OpenSearch setup
│   ├── fluent-bit/          # Fluent Bit log collection
│   ├── kafka/               # Kafka setup
│   ├── redis/               # Redis configuration
│   └── health-checks/       # Health check scripts
├── tests/                    # Test suites
│   ├── integration/
│   ├── e2e/
│   ├── fixtures/
│   └── conftest.py
├── docs/                     # Documentation
│   ├── API_DOCUMENTATION.md
│   ├── DEPLOYMENT.md
│   └── TROUBLESHOOTING.md
├── scripts/                  # Management scripts
├── docker-compose.yml        # Main compose file
├── docker-compose.override.yml  # Development overrides
├── env.template              # Environment template
└── README.md                 # This file
```

#### Adding New Services



1. Create service directory in `services/`
2. Add Dockerfile, requirements.txt, and main.py
3. Add service to `docker-compose.yml`
4. Create database schema in `infrastructure/postgres/`
5. Add health check script
6. Update management scripts

#### Contributing



1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

### 🎬 Demo & Resources



#### Demo Video



AI4I-Orchestrate V0.2\
[https://www.youtube.com/watch?v=imGN0sT\_YoE](https://www.youtube.com/watch?v=imGN0sT_YoE)

#### Test Cases



[https://drive.google.com/drive/folders/1RKDyEHJge8RqfbtlXDQ4Mqok2Y\_Nfkbc?usp=share\_link](https://drive.google.com/drive/folders/1RKDyEHJge8RqfbtlXDQ4Mqok2Y_Nfkbc?usp=share_link)

### 📄 License



This project is licensed under the MIT License - see the LICENSE file for details.

### 🤝 Support



For support and questions:

* Create an issue in the repository
* Check the troubleshooting section
* Review the logs for error details

### 🎯 Roadmap



* ASR microservice implementation
* TTS microservice implementation
* NMT microservice implementation
* Simple UI frontend
* WebSocket streaming support
* API documentation with Swagger/OpenAPI
* Integration testing infrastructure
* Kubernetes deployment manifests
* Service mesh integration (Istio)
* Advanced monitoring with Prometheus/Grafana
* CI/CD pipeline setup
* Performance optimization
* Multi-tenant support
