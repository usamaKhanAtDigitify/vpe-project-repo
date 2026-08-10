# VPE Cost Optimization Plan

NonProd/QA and Production

## 1. Executive Summary

This document consolidates the cost optimization recommendations for the VPE project across both AWS accounts.

| Environment | Current Monthly Cost | Estimated Monthly Savings | Projected Monthly Cost |
|---|---|---|---|
| NonProd / QA | $669.27 | ~$425 | ~$225 |
| Production | $420.93 | ~$146.39 | ~$251.80 |

The largest contributors across both environments are:

- ECS (Fargate)
- ElastiCache (Redis Serverless)
- Application Load Balancer (ALB)
- OpenSearch Serverless (future cost growth)

---

## 2. Cost Optimization Items

### 2.1 ECS Optimization

#### NonProd / QA

**Current monthly cost:** $409.93

**Recommendation**

Since nonprod and QA workloads are interruption tolerant, migrate ECS services from Fargate On-Demand to Fargate Spot.

Additionally, several services are currently overprovisioned at 1 vCPU / 2 GB despite low utilization and can be vertically resized to 0.5 vCPU / 1 GB.

**Estimated impact**

| Current | Projected | Estimated Savings |
|---|---|---|
| $409.93 | ~$70 | ~$340/month |

**Action items**

| Action | Responsible Team |
|---|---|
| Migrate ECS services to Fargate Spot | Infrastructure Team |
| Resize ECS task CPU/Memory | Infrastructure Team |

#### Production

**Current monthly cost:** $297.38

**Recommendation**

Production workloads should remain on Fargate On-Demand to avoid interruption risks.

Instead, reduce CPU and memory allocation for:

- `backend`
- `backend-celery`
- `ai-api`

based on current utilization.

**Estimated impact**

| Current | Projected | Estimated Savings |
|---|---|---|
| $297.38 | ~$184.26 | ~$113.12/month |

**Action items**

| Action | Responsible Team |
|---|---|
| Right-size ECS task definitions | Infrastructure Team |

---

### 2.2 Redis Serverless → Valkey Serverless

#### NonProd / QA

**Current monthly cost:** $201.63

**Recommendation**

Migrate from Redis OSS Serverless to Valkey Serverless. Valkey offers approximately 33% lower storage and ECPU pricing while remaining compatible with existing Redis clients.

**Estimated impact**

| Current | Projected | Estimated Savings |
|---|---|---|
| $201.63 | ~$135 | ~$66.63/month |

**Action items**

| Action | Responsible Team |
|---|---|
| Migrate Redis Serverless to Valkey Serverless | Infrastructure Team |
| Validate application functionality after migration | Development Team |

#### Production

**Current monthly cost:** $100.81

**Recommendation**

Migrate production Redis Serverless to Valkey Serverless after successful validation in lower environments.

**Estimated impact**

| Current | Projected | Estimated Savings |
|---|---|---|
| $100.81 | ~$67.54 | ~$33.27/month |

**Action items**

| Action | Responsible Team |
|---|---|
| Migrate Redis Serverless to Valkey Serverless | Infrastructure Team |
| Validate application functionality | Development Team |

---

### 2.3 Application Load Balancer (Optional Optimization)

#### NonProd / QA

**Current monthly cost:** $36.31

Currently, both NonProd and QA maintain separate Application Load Balancers.

**Recommendation**

Consolidate both environments behind a single shared ALB using host-based or path-based routing. This reduces duplicate hourly ALB charges while maintaining environment isolation.

**Estimated impact**

| Current | Projected | Estimated Savings |
|---|---|---|
| $36.31 | ~$18 | ~$18.31/month |

**Action items**

| Action | Responsible Team |
|---|---|
| Consolidate NonProd & QA behind one ALB | Infrastructure Team |
| Remove unused listeners and target groups | Infrastructure Team |

---

### 2.4 OpenSearch Serverless

#### NonProd / QA

OpenSearch Serverless cost increases as indexed data grows, resulting in higher OCU consumption over time.

**Recommendation**

Before implementing cleanup automation, verify with the application team whether indexed data requires long-term retention. Once confirmed, introduce an automated cleanup process.

**Action items**

| Action | Responsible Team |
|---|---|
| Verify data retention requirements | Development Team |
| Implement scheduled cleanup (Cron/Lambda) | Infrastructure Team |

---

## 3. Overall Savings Summary

| Optimization | NonProd / QA Savings | Production Savings |
|---|---|---|
| ECS Optimization | ~$340 | ~$113.12 |
| Redis → Valkey | ~$66.63 | ~$33.27 |
| ALB Consolidation (optional) | ~$18.31 | N/A |
| OpenSearch Cleanup | Prevents future cost growth | Future evaluation |
| **Total Estimated Savings** | **~$425/month** | **~$146.39/month** |

---

## 4. Responsibility Matrix

| Optimization | Infrastructure Team | Development Team |
|---|---|---|
| ECS migration to Fargate Spot (NonProd/QA) | ✅ | |
| ECS task right-sizing | ✅ | |
| Redis → Valkey migration | ✅ | |
| Redis application validation | | ✅ |
| ALB consolidation | ✅ | |
| OpenSearch retention validation | | ✅ |
| OpenSearch cleanup automation (Cron/Lambda) | ✅ | |