# MongoDB Stack Documentation

## What Gets Created

When you provision this MongoDB Golden Path, the following resources are automatically created:

### 🗄️ MongoDB Database
- **Type**: Document database
- **Storage**: ${{ values.storageSize | default(10) }}GB
- **Connection**: Available via connection string in status

### ☸️ Kubernetes Cluster
- **Nodes**: 3 (high availability)
- **Size**: Medium (4 vCPU, 8GB RAM per node)
- **Version**: Kubernetes 1.31.1
- **Provider**: ${{ values.cloudProvider | default('Azure') }}

### 🌐 DNS Record
- **Type**: A record
- **Name**: mongodb-${{ values.name }}.mongodbs.example.com
- **Purpose**: Stable endpoint for database access

### 🔒 Firewall Rules
- **Inbound**: Restricted to cluster
- **Outbound**: Database connections only
- **Security**: Zero-trust network model

## Architecture Diagram

```mermaid
graph TB
    subgraph "MongoDB Golden Path Stack"
        MongoDB[MongoDB Instance]
        Cluster[Kubernetes Cluster]
        DNS[DNS Record]
        FW[Firewall Rules]
        
        MongoDB -->|runs on| Cluster
        MongoDB -->|accessible via| DNS
        Cluster -->|protected by| FW
        DNS -->|points to| Cluster
    end
    
    User[Developer] -->|single request| MongoDB
```

## Why Golden Path?

This template implements the **Golden Path** pattern - an opinionated, production-ready way to provision infrastructure. Instead of requiring developers to:

1. ❌ Manually provision a cluster
2. ❌ Configure DNS separately
3. ❌ Set up firewall rules
4. ❌ Deploy MongoDB
5. ❌ Wire everything together

The Golden Path does it all with: ✅ **One simple request**

## Connection Information

After provisioning, retrieve your connection string:

```bash
kubectl get mongodb ${{ values.name }} -n ${{ values.namespace | default('default') }} -o jsonpath='{.status.connString}'
```

Expected format:
```
mongodb+srv://username:password@mongodb-${{ values.name }}.mongodbs.example.com/${{ values.name }}
```

## Monitoring

All components are visible in the Backstage catalog under the system:
**${{ values.name }}-stack**

## Cost Breakdown

| Resource | Monthly Cost (Est.) |
|----------|-------------------|
| MongoDB Instance | $50-100 |
| Kubernetes Cluster (3 nodes) | $300-500 |
| DNS | $1 |
| **Total** | **$351-601** |

## Support

- **System Owner**: ${{ values.owner | default('group:platform') }}
- **Documentation**: [MongoDB Golden Path Guide](https://github.com/open-service-portal/docs/golden-path)
- **Issues**: [GitHub Issues](https://github.com/open-service-portal/service-mongodb-golden-path-template/issues)