### ElastiCache

Managed Redis or Memcached - in-memory datastore with hi perf and lo latency.

:exclamation: Using ElastiCache requires app code changes

**Redis**:
* Multi-AZ with auto failover
* RRs to scale reads and hi availability
* persistent
* backup and restore features
* encryption at rest and in-flight (with TLS)
* supports Geospatial data
* HIPAA eligible and PCI DSS compliant

**Memcached**:
* Multi-node data partitioning (sharding)
* not hi availability (no replication)
* not persistent
* no backup and restore
* supports multi-threading

#### Cache security
* does not support IAM auth
* SGs
* REDIS native auth token can be used for auth. Memcached supports SASL-based auth.
* REDIS supports TLS in-flight encryption

#### Use patterns for caching
* Lazy loading - all read data is cached, data can become stale.
* Write through - app updates cache when writes to DB, no stale data

### Use cases
* To reduce load on RDS
* Session store - store temp client session data using TTL  
* REDIS: "gaming leaderboard" with "Redis Sorted sets" which guarantees uniqueness and element ordering in RT.
