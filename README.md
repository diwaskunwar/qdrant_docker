# Qdrant Distributed Cluster with Docker Compose

This setup provides a production-ready, distributed Qdrant cluster using Docker Compose. It includes 3 Qdrant nodes configured for high availability and persistence.

## Prerequisites

- Docker Engine 20.10.0+
- Docker Compose 1.29.0+
- 4GB RAM (minimum), 8GB+ recommended
- At least 10GB of free disk space for storage

## Getting Started

1. **Configure Environment Variables**
   - Copy `.env.example` to `.env` and update the values as needed.
   - Set a strong `QDRANT_API_KEY` for security.

2. **Start the Cluster**
   ```bash
   docker-compose up -d
   ```

3. **Verify the Cluster**
   - Check the status of all services:
     ```bash
     docker-compose ps
     ```
   - View logs for a specific service:
     ```bash
     docker-compose logs -f qdrant-node1
     ```

4. **Access the Cluster**
   - HTTP API: `http://localhost:6333`
   - Dashboard: `http://localhost:8080`

## Cluster Configuration

- **Nodes**: 3-node cluster (qdrant-node1, qdrant-node2, qdrant-node3)
- **Ports**:
  - 6333: HTTP API (exposed on host)
  - 6334: gRPC (exposed on host)
  - 6335: Internal cluster communication
- **Persistence**: Each node has its own named volume for data persistence
- **Health Checks**: Automatic health monitoring and container restart on failure

## Connecting Your Application

Use the following connection details in your application:

```python
from qdrant_client import QdrantClient

# Connect to any node (they're all equal in the cluster)
client = QdrantClient(url="http://localhost:6333", api_key="your-api-key")

# Or connect to the cluster via the first node
# The client will automatically discover other nodes
client = QdrantClient(
    url="http://localhost:6333",
    api_key="your-api-key",
    prefer_grpc=True  # Recommended for better performance
)
```

## Maintenance

- **Scaling**: To add more nodes, update the `docker-compose.yml` file and add new services following the same pattern.
- **Backup**: Backup the named volumes (`qdrant_storage_node*`) for data persistence.
- **Updates**: To update Qdrant version, change the image tag in `docker-compose.yml`.

## Troubleshooting

- If nodes fail to start, check logs with `docker-compose logs`.
- Ensure ports 6333-6335 are available on your host machine.
- For persistent storage issues, verify Docker has write permissions to the volumes.

## License

This setup is provided as-is under the MIT License.
