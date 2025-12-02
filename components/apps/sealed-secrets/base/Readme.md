# Sealed secrets with custom key

https://github.com/bitnami-labs/sealed-secrets/blob/main/docs/bring-your-own-certificates.md


## Manual step within cluster setup

You may need to restart the controller in namespace sealed secrets after adding it

```bash
oc apply components/apps/sealed-secrets/operator/base/bootstrap-keys/secret.yaml
```

## Recovery if key gets lost or cluster is created

In case the only locally stored key gets lost you need to create a new key and afterwards rotate all secrets

Create certificate and key
```bash
openssl req -x509 -days 358000 -nodes -newkey rsa:4096 -keyout "$PRIVATEKEY" -out "$PUBLICKEY" -subj "/CN=sealed-secret/O=sealed-secret"
```

Create secret
```bash
oc -n "$NAMESPACE" create secret tls "$SECRETNAME" --cert="$PUBLICKEY" --key="$PRIVATEKEY"
```

Label secret
```bash
oc -n "$NAMESPACE" label secret "$SECRETNAME" sealedsecrets.bitnami.com/sealed-secrets-key=active
```

Restart sealed secrets controller
```bash
oc -n  "$NAMESPACE" delete pod -l app.kubernetes.io/name=sealed-secrets
```

## Encrypt secret with cert from added key

```bash
kubeseal --format yaml --cert "${PUBLICKEY}" --scope cluster-wide < <secret-name>.yaml > <sealed-secret-name>.yaml
```

Without local access to cert (Guess its pulling the newest certls and not the one initially created):
```bash
kubeseal --format yaml --controller-namespace=sealed-secrets --controller-name=sealed-secrets-2-10-0  --scope cluster-wide < <secret-name>.yaml > <sealed-secret-name>.yaml
```

```bash
kubeseal --recovery-unseal -o yaml --recovery-private-key "${PRIVATEKEY}" < <sealed-secret>.yaml
```