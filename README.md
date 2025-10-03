# sabkhi_lab_secrets

This repo holds SealedSecrets applied by ArgoCD (`sealed-secrets-manifests` app). Do NOT commit plaintext Secrets.

## Prereqs
- kubeseal installed
- access to cluster (to fetch SealedSecrets public cert)

## Create Immich PostgreSQL credentials
```bash
# 1) Create a temporary Secret manifest (not committed)
cat > /tmp/immich-postgresql-secret.yaml <<'YAML'
apiVersion: v1
kind: Secret
metadata:
  name: immich-postgresql-secret
  namespace: immich
stringData:
  username: immich
  password: <strong-random-password>
YAML

# 2) Seal it for the cluster
kubeseal --format yaml < /tmp/immich-postgresql-secret.yaml \
  > immich-postgresql-secret.yaml

# 3) Commit and push this sealed file to this repo
```

## Create Immich Redis password
```bash
cat > /tmp/immich-redis-secret.yaml <<'YAML'
apiVersion: v1
kind: Secret
metadata:
  name: immich-redis-secret
  namespace: immich
stringData:
  password: <strong-random-password>
YAML

kubeseal --format yaml < /tmp/immich-redis-secret.yaml \
  > immich-redis-secret.yaml
```

## Create Spotizerr Redis password
```bash
cat > /tmp/spotizerr-redis-secret.yaml <<'YAML'
apiVersion: v1
kind: Secret
metadata:
  name: spotizerr-redis-secret
  namespace: spotizerr
stringData:
  redis-password: <strong-random-password>
YAML

kubeseal --format yaml < /tmp/spotizerr-redis-secret.yaml \
  > spotizerr-sealed-secret.yaml
```

## Notes
- Sealed files produced above are safe to commit. Only the controller can decrypt them in-cluster.
- Rotate regularly. Replace `<strong-random-password>` and reseal.
- Ensure the namespace exists or is created by ArgoCD before sync.