# Platform Beta - Docker Compose Setup

This directory contains the Docker Compose configuration to deploy the complete platform stack.

## Services

1. **MongoDB** - Database service running on port 27017
2. **Qdrant** - Vector database service running on ports 6333 (REST) and 6334 (gRPC)
3. **Open Responses** - AI service running on port 6644 (depends on MongoDB and Qdrant)
4. **Platform UI** - Web interface running on port 6645 (depends on Open Responses)

## Prerequisites

- Docker and Docker Compose installed
- OpenAI API key
- The following ports must be available on your host system:
  - **27017** (MongoDB)
  - **6333** (Qdrant REST API)
  - **6334** (Qdrant gRPC API)
  - **6644** (Open Responses API)
  - **6645** (Platform UI)

## Setup Instructions

1. **Configure Environment Variables**
   ```bash
   # Copy the .env file and update with your actual values
   cp .env.example .env
   # Edit .env and set your OpenAI API key
   ```

2. **Start All Services**
   ```bash
   docker-compose up -d
   ```

3. **Check Service Status**
   ```bash
   docker-compose ps
   ```

4. **View Logs**
   ```bash
   # All services
   docker-compose logs
   
   # Specific service
   docker-compose logs open-responses
   ```

5. **Stop Services**
   ```bash
   docker-compose down
   ```

## Service Dependencies

- `open-responses` depends on `mongo` and `qdrant`
- `platform-ui` depends on `open-responses`

Docker Compose will automatically handle the startup order based on these dependencies.

## Ports

- MongoDB: 27017
- Qdrant REST API: 6333
- Qdrant gRPC API: 6334
- Open Responses: 6644
- Platform UI: 6645

## Volumes

- `mongo_data`: Persistent MongoDB data
- `qdrant_data`: Persistent Qdrant data

## Environment Variables

All environment variables are defined in the `.env` file:

- `OPENAI_API_KEY`: Your OpenAI API key
- `SPRING_PROFILES_ACTIVE`: Spring profile (set to "platform")
- `VITE_DASHBOARD_API_URL`: Dashboard API URL for the UI (points to open-responses service on port 6644)
- `QDRANT_ALLOW_CREATION_ON_FILE_NOT_FOUND`: Qdrant configuration flag
- `OPEN_RESPONSES_STORE_VECTOR_SEARCH_QDRANT_HOST`: Qdrant host (set to "qdrant" for Docker service)
- `OPEN_RESPONSES_MONGODB_URI`: MongoDB connection URI (set to "mongodb://mongo:27017" for Docker service) 