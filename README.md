# 🛡️ API Defense Intelligence

**Java · Spring Boot · PostgreSQL · Spring AI · JWT · Docker**

An API security middleware and monitoring system built with Java and Spring Boot. It intercepts incoming HTTP requests in real time to detect, block, and log malicious traffic before it reaches your backend endpoints.

## 📌 What is this project?

API Defense Intelligence acts as a protective middleware layer between clients and your backend API. Every incoming request passes through a security filter chain first: malicious requests are blocked and logged to PostgreSQL, while valid requests are forwarded to the target endpoint.

```
Client Request
      ↓
API Defense Intelligence  ←── detects, rate-limits & blocks attacks
      ↓
Backend API               ←── only safe requests reach here
      ↓
PostgreSQL Database

```

## ✨ Features

| Feature | Description | 
| ----- | ----- | 
| 🔍 SQL Injection Detection | Blocks SQL injection patterns automatically | 
| 🔍 XSS Detection | Blocks Cross-Site Scripting payloads | 
| 🚦 Rate Limiting | Limits traffic to 100 requests per minute per IP | 
| 🔐 JWT Authentication | Token-based authentication for protected endpoints | 
| 🗝️ API Key Validation | Extra `X-API-KEY` header verification | 
| 📊 Attack Logging | Persists blocked attack records in PostgreSQL | 
| 🤖 Spring AI Integration | AI-assisted threat analysis using Ollama + `llama3` | 
| 📈 Analytics Endpoints | Real-time attack statistics and log filtering APIs | 
| 🖥️ Frontend Dashboard | Dark-themed UI for viewing, filtering, and deleting logs | 
| 🐳 Docker Support | Multi-container setup via Docker Compose | 

## 🏗️ Architecture

### Request Filter Pipeline

```
┌─────────────────────────────────────────────────────────┐
│                        CLIENT                           │
└───────────────────────────┬─────────────────────────────┘
                            │ HTTP Request
                            ▼
┌─────────────────────────────────────────────────────────┐
│               API DEFENSE INTELLIGENCE                  │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │  1. RateLimitFilter                               │  │
│  │     └── Block if > 100 requests/min → 429         │  │
│  └────────────────────────┬──────────────────────────┘  │
│                           │                             │
│  ┌────────────────────────▼──────────────────────────┐  │
│  │  2. SecurityFilter                                │  │
│  │     ├── SQL Injection detected? → 403             │  │
│  │     └── XSS Attack detected? → 403                │  │
│  └────────────────────────┬──────────────────────────┘  │
│                           │                             │
│  ┌────────────────────────▼──────────────────────────┐  │
│  │  3. JwtAuthenticationFilter                       │  │
│  │     ├── Valid API Key? No → 401                   │  │
│  │     └── Valid JWT Token? No → 401                 │  │
│  └────────────────────────┬──────────────────────────┘  │
└───────────────────────────┼─────────────────────────────┘
                            │ Safe Request
                            ▼
┌─────────────────────────────────────────────────────────┐
│                     BACKEND API                         │
│                                                         │
│    Controller → Service → Repository → PostgreSQL       │
└─────────────────────────────────────────────────────────┘

```

### AI Detection Flow

```
Input: "give me all records where 1=1"
      │
      ▼
Rule-Based Check (fast)
  contains "drop"?   No
  contains "select"? No
  Result: SAFE
      │
      ▼
AI Analysis (smart)
  Sending to llama3 model via Ollama...
  AI reasoning: "1=1 always true = SQL bypass"
  Result: SQL_INJECTION
      │
      ▼
BLOCKED → 403 + logged to database

```

### Layered Architecture

| Layer | Responsibility | 
| ----- | ----- | 
| Controller Layer | REST API endpoints (`/auth`, `/api`, `/threat`, `/ai`) | 
| Agent Layer | Pattern-based and AI-powered attack classification | 
| Middleware Layer | Request interception (`SecurityFilter`, `RateLimitFilter`) | 
| Security Layer | JWT generation/validation and API key checks | 
| Service Layer | Business logic for auth, threat logs, and AI analysis | 
| Repository Layer | Spring Data JPA database access | 
| Model Layer | PostgreSQL entity definitions | 

## 🛠️ Tech Stack

| Layer | Technology | Version | 
| ----- | ----- | ----- | 
| Language | Java | 21 | 
| Framework | Spring Boot | 3.3.2 | 
| Security | Spring Security | 6.3.1 | 
| Authentication | JWT (`jjwt`) | 0.11.5 | 
| Database | PostgreSQL | 16 | 
| ORM | Spring Data JPA | 3.3.2 | 
| AI Framework | Spring AI | 1.0.0-M6 | 
| AI Model | Ollama `llama3` | Latest | 
| Build Tool | Maven / Maven Wrapper | 3.9.6 | 
| Containerization | Docker + Compose | Latest | 
| Utility | Lombok | Latest | 

## 📁 Project Structure

```
api-security-agent/
│
├── src/main/java/com/securityagent/api_security_agent/
│   ├── ApiSecurityAgentApplication.java
│   ├── config/
│   │   ├── SecurityConfig.java          # Spring Security configuration
│   │   └── CorsConfig.java              # CORS settings
│   ├── controller/
│   │   ├── ApiController.java           # Main API endpoints
│   │   ├── AuthController.java          # Login and register
│   │   ├── ThreatController.java        # Attack log endpoints
│   │   └── AiController.java            # AI analysis endpoints
│   ├── agent/
│   │   ├── RequestAnalyzer.java         # Pattern-based detection
│   │   ├── ThreatDetector.java          # Attack classification
│   │   ├── AttackResponseAgent.java     # Response decisions
│   │   └── AiThreatAnalyzer.java        # Spring AI integration
│   ├── middleware/
│   │   ├── SecurityFilter.java          # Main security interceptor
│   │   └── RateLimitFilter.java         # Rate limiting
│   ├── security/
│   │   ├── JwtUtil.java                 # JWT generation and validation
│   │   ├── ApiKeyService.java           # API key validation
│   │   └── JwtAuthenticationFilter.java # JWT filter
│   ├── service/
│   │   ├── ThreatService.java           # Attack log business logic
│   │   ├── AuthService.java             # Authentication logic
│   │   └── AiAnalysisService.java       # AI analysis logic
│   ├── repository/
│   │   ├── ThreatRepository.java        # Attack log DB access
│   │   ├── UserRepository.java          # User DB access
│   │   └── AiAnalysisRepository.java    # AI log DB access
│   ├── model/
│   │   ├── ThreatLog.java               # Attack log entity
│   │   ├── User.java                    # User entity
│   │   └── AiAnalysisLog.java           # AI log entity
│   └── dto/
│       ├── LoginRequest.java            # Login input
│       └── AuthResponse.java            # Token output
│
├── src/main/resources/
│   └── application.properties           # App configuration
│
├── frontend/
│   ├── index.html                       # Dashboard UI
│   ├── style.css                        # Dark theme styles
│   └── script.js                        # API calls
│
├── Dockerfile                           # Docker build instructions
├── docker-compose.yml                   # Multi-container setup
├── .dockerignore                        # Docker ignore list
├── mvnw / mvnw.cmd                      # Maven Wrapper
└── pom.xml                              # Maven dependencies

```

## 🚀 Getting Started

### Option 1 — Run Locally with Maven Wrapper (Recommended on Windows)

**Prerequisites:**

* Java 21

* PostgreSQL running on port `5432`

**Step 1 — Clone the repository and enter the project folder**

```
git clone https://github.com/Adithya-k05/API-Defense-Intelligence.git
cd API-Defense-Intelligence/api-security-agent-main/api-security-agent-main/api-security-agent

```

**Step 2 — Create the PostgreSQL database**

```
CREATE DATABASE security_agent;

```

**Step 3 — Configure `src/main/resources/application.properties`**

```
spring.datasource.url=jdbc:postgresql://localhost:5432/security_agent
spring.datasource.username=postgres
spring.datasource.password=yourpassword

```

**Step 4 — Start the backend (on port `8081` to avoid port `8080` conflicts)**

```
.\mvnw.cmd spring-boot:run "-Dspring-boot.run.arguments=--server.port=8081 --server.address=127.0.0.1"

```

> Make sure `BASE_URL` in `frontend/script.js` matches your backend port (`http://localhost:8081`).

### Option 2 — Run with Docker

**Prerequisites:**

* Docker Desktop installed and running

```
git clone https://github.com/Adithya-k05/API-Defense-Intelligence.git
cd API-Defense-Intelligence/api-security-agent-main/api-security-agent-main/api-security-agent
docker-compose up --build

```

### Option 3 — Enable AI Threat Analysis (Ollama)

```
# 1. Install Ollama from https://ollama.com/download
# 2. Pull the llama3 model
ollama pull llama3

# 3. Verify the model is available
ollama list

```

## 🔑 Authentication

All protected endpoints require both headers:

```
X-API-KEY: myApiKey123
Authorization: Bearer <your-jwt-token>

```

**Register and log in:**

```
POST /auth/register
{
  "username": "your-name",
  "password": "your-password"
}

```

```
POST /auth/login
{
  "username": "your-name",
  "password": "your-password"
}

```

Response:

```
{
  "token": "eyJhbGciOiJIUzI1NiJ9..."
}

```

## 📡 API Endpoints

### Auth Endpoints (Public)

| Method | Endpoint | Description | 
| ----- | ----- | ----- | 
| `POST` | `/auth/register` | Register a new user | 
| `POST` | `/auth/login` | Authenticate and receive a JWT token | 

### API Endpoints (Auth Required)

| Method | Endpoint | Description | 
| ----- | ----- | ----- | 
| `GET` | `/api/test` | Verify API availability | 
| `GET` | `/api/scan?input=...` | Scan input payload for threats | 
| `GET` | `/api/logs` | Retrieve all attack logs | 

### Threat Endpoints (Auth Required)

| Method | Endpoint | Description | 
| ----- | ----- | ----- | 
| `GET` | `/threat/logs` | Get all threat logs | 
| `GET` | `/threat/logs/{id}` | Get a single log by ID | 
| `GET` | `/threat/logs/type/{type}` | Filter logs by attack type | 
| `GET` | `/threat/logs/ip/{ip}` | Filter logs by IP address | 
| `GET` | `/threat/latest` | Get the latest 10 attacks | 
| `GET` | `/threat/stats` | Get attack statistics summary | 
| `DELETE` | `/threat/logs/{id}` | Delete a log entry | 

### AI Endpoints (Auth Required)

| Method | Endpoint | Description | 
| ----- | ----- | ----- | 
| `POST` | `/ai/analyze` | Run AI analysis on an input payload | 
| `GET` | `/ai/logs` | Get all AI analysis logs | 
| `GET` | `/ai/logs/latest` | Get latest AI analysis logs | 
| `GET` | `/ai/logs/threats` | Get AI-detected threats | 

## 🧪 Testing

*(Examples use port `8081` matching the local run command above.)*

### Test SQL Injection

```
GET http://localhost:8081/api/scan?input=drop table users
# Expected: 403 SQL_INJECTION

```

### Test XSS Attack

```
GET http://localhost:8081/api/scan?input=<script>alert(1)</script>
# Expected: 403 XSS_ATTACK

```

### Test Rate Limiting (PowerShell)

```
$token = "your-token-here"
for ($i = 1; $i -le 110; $i++) {
    try {
        $r = Invoke-WebRequest -Uri "http://localhost:8081/api/test" `
            -Headers @{"X-API-KEY"="myApiKey123";"Authorization"="Bearer $token"}
        Write-Host "Request $i : 200 OK"
    } catch {
        Write-Host "Request $i : 429 BLOCKED"
    }
}
# First 100 -> 200 OK
# After 100 -> 429 BLOCKED

```

### Test AI Detection

```
POST http://localhost:8081/ai/analyze
{
  "input": "give me all records where 1=1"
}

```

### View Attack Statistics

```
GET http://localhost:8081/threat/stats
{
  "totalAttacks": 10,
  "attacksByType": {
    "SQL_INJECTION": 6,
    "XSS_ATTACK": 4
  }
}

```

## 🛡️ Attacks Prevented

| Attack | Detection Method | HTTP Response | 
| ----- | ----- | ----- | 
| SQL Injection | Pattern matching + AI | `403 Forbidden` | 
| XSS Attack | Pattern matching + AI | `403 Forbidden` | 
| Brute Force | Rate limiting | `429 Too Many Requests` | 
| DDoS | Rate limiting per IP | `429 Too Many Requests` | 
| Unauthorized Access | API Key check | `401 Unauthorized` | 
| Token Tampering | JWT signature verification | `401 Unauthorized` | 
| Unknown Attacks | AI analysis | `403 Forbidden` | 

## 🖥️ Frontend Dashboard

Open `frontend/index.html` in your browser (or use **Live Server** in VS Code).

* Login with JWT authentication (no prefilled password)

* View total attacks, SQL injection count, and XSS attack count

* Filter attack logs by category and delete individual entries

* Auto-refresh every 30 seconds

## 📊 Database Schema

| Table | Key Columns | 
| ----- | ----- | 
| `threat_log` | `id` (PK), `ip_address`, `endpoint`, `attack_type`, `timestamp` | 
| `app_users` | `id` (PK), `username` (UNIQUE), `password` (BCrypt), `role` | 
| `ai_analysis_log` | `id` (PK), `input`, `rule_result`, `ai_result`, `final_decision`, `ip_address`, `timestamp` | 

## ⚙️ Configuration

| Property | Default | Description | 
| ----- | ----- | ----- | 
| `server.port` | `8080` (overridden to `8081` locally) | Server port | 
| `spring.datasource.url` | `jdbc:postgresql://localhost:5432/security_agent` | PostgreSQL JDBC URL | 
| `spring.ai.ollama.base-url` | `http://localhost:11434` | Ollama API URL | 
| `spring.ai.ollama.chat.model` | `llama3` | AI model name | 

## 👨‍💻 Author

**Adithya K** — [GitHub (@Adithya-k05)](https://github.com/Adithya-k05)

Built with Java 21 · Spring Boot · Spring AI · PostgreSQL
