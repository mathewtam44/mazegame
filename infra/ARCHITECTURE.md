# AWS Architecture

## Overview

The Turtle Maze is hosted as a static website using S3 and CloudFront.

## Architecture Diagram

```
┌──────────────┐       ┌─────────────────────┐       ┌──────────────────┐
│              │       │                     │       │                  │
│    User      │──────▶│    CloudFront       │──────▶│    S3 Bucket     │
│   (Browser)  │ HTTPS │    Distribution     │  OAC  │   (Private)      │
│              │◀──────│                     │◀──────│                  │
│              │       │  - HTTPS redirect   │       │  - index.html    │
└──────────────┘       │  - HTTP/2           │       │  - No public     │
                       │  - Caching          │       │    access         │
                       │  - Compression      │       │                  │
                       └─────────────────────┘       └──────────────────┘
```

## Components

### S3 Bucket
- Stores `index.html` (the entire game)
- All public access blocked
- Access granted only via CloudFront Origin Access Control (OAC)

### CloudFront Distribution
- Provides HTTPS with AWS-managed certificate
- Redirects HTTP to HTTPS
- Caches content at edge locations globally
- Gzip/Brotli compression enabled
- Default root object: `index.html`

### Origin Access Control (OAC)
- Secures the connection between CloudFront and S3
- Uses SigV4 signing
- Ensures S3 content is only accessible through CloudFront

## Security

- S3 bucket has no public access
- All traffic is encrypted via HTTPS
- CloudFront is the only entry point to the content
- No server-side code or databases — zero attack surface beyond static file serving

## Cost Estimate

| Resource | Cost |
|----------|------|
| S3 storage | ~$0.001/month |
| S3 requests | $0.0004 per 1,000 GETs |
| CloudFront data transfer | 1TB/month free tier |
| CloudFront requests | 10M/month free (first year) |

**Expected monthly cost: < $0.10**

## Stack Outputs

| Output | Description |
|--------|-------------|
| `BucketName` | S3 bucket name for uploading content |
| `DistributionURL` | Public HTTPS URL for the game |
| `DistributionId` | CloudFront distribution ID (for cache invalidation) |

## Cache Invalidation

After updating `index.html`:

```bash
aws cloudfront create-invalidation \
  --distribution-id <DistributionId> \
  --paths "/index.html"
```
