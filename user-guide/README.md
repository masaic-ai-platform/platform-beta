# Platform Beta - Docker Compose Setup

This directory contains Docker Compose configurations to deploy the platform stack in multiple modes, supporting both internal Docker services and external databases.

## Deployment Modes

### Internal Services (Docker-based)

#### 1. Default Mode (Basic)
- **Services**: Open Responses + Platform UI
- **Features**: Basic functionality without vector search or database persistence
- **Ports**: 6644 (Open Responses), 6645 (Platform UI)
- **Command**: `docker-compose up -d`

#### 2. Platform with Internal Qdrant
- **Services**: Open Responses + Platform UI + Qdrant (Docker)
- **Features**: Vector search capabilities with internal Qdrant
- **Ports**: 6333/6334 (Qdrant), 6644 (Open Responses), 6645 (Platform UI)
- **Command**: `docker-compose --profile qdrant up -d`

#### 3. Platform with Internal MongoDB
- **Services**: Open Responses + Platform UI + MongoDB (Docker)
- **Features**: Database persistence with internal MongoDB
- **Ports**: 27017 (MongoDB), 6644 (Open Responses), 6645 (Platform UI)
- **Command**: `docker-compose --profile mongo up -d`

#### 4. Platform with Internal Qdrant and MongoDB
- **Services**: Open Responses + Platform UI + Qdrant (Docker) + MongoDB (Docker)
- **Features**: Full functionality with internal vector search and database persistence
- **Ports**: 27017 (MongoDB), 6333/6334 (Qdrant), 6644 (Open Responses), 6645 (Platform UI)
- **Command**: `docker-compose --profile full up -d`

### External Services

#### 5. Platform with External Databases
- **Services**: Open Responses + Platform UI
- **Features**: Connects to your existing Qdrant and/or MongoDB instances
- **Ports**: 6644 (Open Responses), 6645 (Platform UI)
- **Command**: `docker-compose --profile external up -d`

## Prerequisites

- Docker and Docker Compose installed
- OpenAI API key
- For external database modes: Access to external Qdrant and/or MongoDB instances

## Setup Instructions

### For Internal Services (Docker-based)

1. **Configure Environment Variables**
   ```bash
   # Copy the basic .env file and update with your OpenAI API key
   cp .env.example .env
   # Edit .env and set your OPENAI_API_KEY
   ```

2. **Choose Your Deployment Mode**
   ```bash
   # Default Mode (Basic)
   docker-compose up -d

   # Platform with Internal Qdrant
   docker-compose --profile qdrant up -d

   # Platform with Internal MongoDB
   docker-compose --profile mongo up -d

   # Platform with Internal Qdrant and MongoDB
   docker-compose --profile full up -d
   ```

### For External Services

1. **Configure Environment Variables for External Databases**
   
   **Option A: External Qdrant + External MongoDB**
   ```bash
   # Copy the external configuration template
   cp .env.external-qdrant .env
   # Edit .env and configure:
   # - OPENAI_API_KEY
   # - OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_HOST (your Qdrant server)
   # - OPEN_RESPONSES_MONGODB_URI (your MongoDB connection string)
   ```

   **Option B: External Qdrant only (with internal MongoDB)**
   ```bash
   # Use the external Qdrant template and run with mixed profile
   cp .env.external-qdrant .env
   # Edit .env and set OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_HOST, then run:
   docker-compose --profile mongo --profile external up -d
   ```

   **Option C: External MongoDB only (with internal Qdrant)**
   ```bash
   # Use the external MongoDB template and run with mixed profile  
   cp .env.external-qdrant .env
   # Edit .env and set OPEN_RESPONSES_MONGODB_URI, then run:
   docker-compose --profile qdrant --profile external up -d
   ```

2. **Deploy with External Services**
   ```bash
   # For fully external setup
   docker-compose --profile external up -d
   
   # For mixed setups (see examples above)
   docker-compose --profile mongo --profile external up -d
   docker-compose --profile qdrant --profile external up -d
   ```

## Common Commands

### Check Service Status
```bash
docker-compose ps
```

### View Logs
```bash
# All services
docker-compose logs

# Specific service
docker-compose logs open-responses
```

### Stop Services
```bash
docker-compose down
```

### Clean Up (removes volumes)
```bash
docker-compose down -v
```

## Configuration Files

- `.env.example` - Basic configuration template
- `.env.external-qdrant` - Template for external Qdrant + MongoDB setup
- `.env.external-mongodb` - Template for external MongoDB + Qdrant setup

## Environment Variables

### Required for All Modes
- `OPENAI_API_KEY`: Your OpenAI API key (mapped to `OPEN_RESPONSES_EMBEDDINGS_API_KEY`)
- `VITE_DASHBOARD_API_URL`: Dashboard API URL (default: http://localhost:6644)
- `SPRING_PROFILES_ACTIVE`: Spring profile (default: platform)

### For External Database Connections
- `OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_HOST`: External Qdrant host
- `OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_API_KEY`: External Qdrant API key (optional)
- `OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_USE_TLS`: Use TLS for Qdrant connection (default: false)
- `OPEN_RESPONSES_STORE_VECTOR_SEARCH_COLLECTION_NAME`: Qdrant collection name (default: open_responses)
- `OPEN_RESPONSES_MONGODB_URI`: External MongoDB connection string
- `OPEN_RESPONSES_MONGODB_DATABASE`: MongoDB database name (default: open_responses)

## Use Cases

| Scenario | Profile | Configuration | Use Case |
|----------|---------|---------------|----------|
| Development/Testing | `default` | Basic .env | Quick local testing |
| Need Vector Search | `qdrant` | Basic .env | Testing vector features |
| Need Persistence | `mongo` | Basic .env | Testing data persistence |
| Full Local Stack | `full` | Basic .env | Complete local development |
| Production (External DBs) | `external` | External .env | Production with managed databases |
| Hybrid Setup | `mongo` + `external` | External .env | External Qdrant + Docker MongoDB |

## Accessing the Application

Once deployed, access the Platform UI at: `http://localhost:6645`

The Open Responses API is available at: `http://localhost:6644` 