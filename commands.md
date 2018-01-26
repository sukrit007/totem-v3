# Commands

This document contains some useful command snippets for totem v3.

## Determine S3 Bucket for Totem V3

```bash
set -o pipefail
TOTEM_BUCKET="$(aws cloudformation describe-stack-resource \
  --logical-resource-id=TotemBucket \
  --stack-name=totem-environment \
  --output text | tail -1 | awk '{print $1}')"
echo $TOTEM_BUCKET
```
