# 11) SSL Or TLS

## Overview
SSL (Secure Sockets Layer) and TLS (Transport Layer Security) are cryptographic protocols that provide confidentiality, integrity, and authenticity for data transported across networks (most commonly over TCP). TLS is the modern, secure successor to SSL. In practice people often say "SSL" when they mean TLS.

## Why use TLS
- Encrypts traffic so eavesdroppers cannot read data.
- Ensures data integrity (detects tampering).
- Authenticates endpoints using X.509 certificates (server identity, optional client identity).
- Essential for protecting API traffic, web UIs, service-to-service communication, and anything carrying secrets.

## SSL vs TLS (brief)
- SSL 2.0 / 3.0 are old and insecure. TLS 1.0 / 1.1 are deprecated.
- Use TLS 1.2 or TLS 1.3 (1.3 is preferred: simpler handshake, better performance, stronger defaults).
- The term "SSL" persists for historical reasons, but configure and enable TLS.

## How TLS works (high-level handshake)
1. ClientHello: client proposes protocol version, cipher suites, and extensions (e.g., SNI).
2. ServerHello: server selects version and cipher, returns certificate.
3. Server may send additional messages (CertificateRequest for mutual TLS, etc.).
4. Client verifies server certificate (chain to trusted CA) and sends a key exchange message.
5. Both derive symmetric session keys and finish handshake.
6. Encrypted application data flows using the negotiated keys.

## Certificates and trust
- X.509 certificates contain subject (CN/SAN), public key, validity, and are signed by a CA.
- Trust root: client must trust the issuing CA (system store or Kubernetes CA).
- Self-signed certs are fine for dev/test; production should use CA-signed certs or an internal PKI (e.g., cert-manager, HashiCorp Vault, CFSSL).

## Cipher suites & versions
- Prefer TLS 1.2/1.3. Disable SSLv3, TLS 1.0, TLS 1.1.
- Choose modern cipher suites (AEAD, ECDHE for forward secrecy).
- Let platforms default to secure sets where possible; explicitly configure for hardened posture in production.

## TLS in Kubernetes (practical notes)
- Kubernetes components use TLS extensively (kube-apiserver, kubelet, kube-controller-manager, etcd).
- Typical patterns:
  - Ingress/LoadBalancer: terminate TLS at Ingress controller or external LB.
  - Service-to-service: either mTLS (e.g., Istio, Linkerd) or TLS via sidecars.
  - Kubernetes Secrets: store TLS cert+key as `kubectl create secret tls my-tls --cert=cert.pem --key=key.pem -n <ns>`.
- Example: create a TLS secret for an Ingress
```bash
kubectl create secret tls example-tls \
  --key=tls.key \
  --cert=tls.crt \
  -n my-namespace
```
- Certificate automation:
  - Use cert-manager to obtain/renew certs from ACME (Let's Encrypt) or internal CAs.
  - For cluster bootstrapping, kubeadm and other tools handle some cert rotation automatically; understand certificate lifetimes and rotation policies.

## Mutual TLS (mTLS)
- mTLS adds client certificate verification so both sides authenticate each other.
- Useful for zero-trust and secure service meshes.
- Requires distributing a CA or trust bundle to all parties.

## Common commands & examples
- Inspect remote certs:
```bash
openssl s_client -connect example.com:443 -servername example.com
```
- Dump certificate details:
```bash
openssl x509 -in cert.pem -text -noout
```
- Generate a quick self-signed certificate (dev only):
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt -subj "/CN=example.com"
```
- Create Kubernetes TLS secret (see above).

## Best practices
- Use TLS 1.3 where possible; otherwise TLS 1.2 with strong ciphers.
- Use publicly trusted CA certs for internet-facing endpoints; internal PKI for internal services.
- Automate certificate issuance and rotation (cert-manager, Vault).
- Enforce strict validation (SNI, SANs, minimum TLS versions).
- Prefer mTLS for sensitive service-to-service traffic via service mesh or mutual cert provisioning.

## Further reading
- RFC 8446 (TLS 1.3)
- OpenSSL documentation
- Kubernetes docs: Ingress TLS, Secrets, API server TLS flags
- cert-manager project docs
