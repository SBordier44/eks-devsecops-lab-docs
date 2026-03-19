# Ingress and TLS Runbook

## Purpose

This runbook provides practical guidance for operating and troubleshooting ingress and TLS in the EKS DevSecOps Lab.

It focuses on the current public exposure path and helps answer questions such as:

- is the application ingress working?
- is Traefik routing traffic correctly?
- is the hostname resolving properly?
- why is HTTPS not working?
- why is cert-manager not issuing or renewing certificates?

## Scope

This runbook covers:

- ingress validation
- service and routing checks
- DNS path checks
- cert-manager checks
- TLS secret checks
- common failure patterns in the current lab

It does not attempt to document every ingress or ACME feature in depth.

## Current Context

The current public exposure path of the lab is:

1. OVH DNS resolves the public hostname
2. traffic reaches the ingress layer
3. Traefik routes the request to the demo application service
4. cert-manager obtains and renews the certificate through Let's Encrypt
5. the ingress uses the generated TLS secret for HTTPS

The main current public hostname is:

- `app-demo.sedecy-it.net`

## Basic Validation Workflow

When ingress or HTTPS looks broken, use this order:

1. check DNS resolution
2. check the ingress resource
3. check the service behind the ingress
4. check Traefik pods and service
5. check cert-manager resources
6. check the TLS secret
7. only then inspect application-level behavior

This sequence helps isolate whether the issue is DNS, ingress routing, certificate issuance, or the application itself.

## Check the Ingress Resource

Start by checking the application ingress:

```bash
kubectl -n demo-app-dev get ingress
kubectl -n demo-app-dev describe ingress demo-app
```

What to look for:

- the ingress exists
- the expected hostname is present
- `ingressClassName: traefik` is set
- TLS configuration exists
- the cert-manager issuer annotation is present

If the ingress definition itself is wrong, the rest of the public path will not work.

## Check the Service Behind the Ingress

Confirm that the target service exists and points to the expected application ports.

```bash
kubectl -n demo-app-dev get svc
kubectl -n demo-app-dev describe svc demo-app
kubectl -n demo-app-dev get endpoints
```

What to look for:

- the service exists
- the service port matches the ingress target
- endpoints are present
- backing pods are available

If the service or endpoints are wrong, Traefik may receive traffic but fail to route correctly to the application.

## Check the Application Pods

```bash
kubectl -n demo-app-dev get pods
kubectl -n demo-app-dev logs deployment/demo-app
```

What to look for:

- pods are running
- readiness is successful
- application container is healthy

If the application is not healthy, ingress may appear broken when the root cause is actually workload-side.

## Check Traefik

Validate that the ingress controller itself is healthy:

```bash
kubectl -n traefik get pods
kubectl -n traefik get svc
kubectl -n traefik logs deployment/traefik
```

What to look for:

- Traefik pods are running
- the service exists
- there are no obvious routing or controller errors

## Check DNS Resolution

From a client machine, validate that the hostname resolves correctly:

```bash
dig app-demo.sedecy-it.net
```

or:

```bash
nslookup app-demo.sedecy-it.net
```

What to look for:

- the hostname resolves
- the returned target is the expected public entrypoint
- there is no stale or incorrect DNS record

If DNS is wrong, the cluster ingress may be healthy while the public path is still broken.

## Check cert-manager

Validate the certificate automation layer:

```bash
kubectl -n cert-manager get pods
kubectl get clusterissuer
kubectl -n demo-app-dev get certificate
kubectl -n demo-app-dev describe certificate
```

What to look for:

- cert-manager pods are running
- the relevant ClusterIssuer exists
- the certificate resource exists
- issuance status is healthy

## Check the TLS Secret

```bash
kubectl -n demo-app-dev get secret
kubectl -n demo-app-dev describe secret demo-app-tls
```

What to look for:

- the TLS secret exists
- it is the secret referenced by the ingress
- it was created or managed by cert-manager

If the TLS secret is missing, HTTPS will not work even if routing is otherwise correct.

## Typical Troubleshooting Scenarios

### Scenario 1 — Hostname resolves, but application is unreachable

Possible reasons:

- ingress misconfiguration
- service or endpoints issue
- Traefik not routing correctly
- application pods unhealthy

Validation path:

1. check ingress
2. check service and endpoints
3. check application pods
4. check Traefik logs

### Scenario 2 — HTTP works, HTTPS does not

Possible reasons:

- certificate not issued
- TLS secret missing
- wrong issuer annotation
- cert-manager issue

Validation path:

1. check certificate resource
2. check ClusterIssuer
3. check TLS secret
4. check cert-manager logs if needed

### Scenario 3 — cert-manager not issuing the certificate

Possible reasons:

- ClusterIssuer misconfiguration
- ACME challenge issue
- DNS or routing issue affecting validation
- ingress annotation issue

Validation path:

```bash
kubectl get clusterissuer
kubectl -n demo-app-dev get certificate
kubectl -n demo-app-dev describe certificate
kubectl -n cert-manager logs deployment/cert-manager
```

### Scenario 4 — Everything in Kubernetes looks correct, but public access still fails

Possible reasons:

- DNS issue outside the cluster
- public entrypoint mismatch
- stale record or propagation delay
- client-side caching

In that case, validate the end-to-end path starting from DNS rather than changing Kubernetes resources immediately.

## Current Good Practices

For the current lab, the most useful practices are:

- keep ingress definitions in GitOps
- validate DNS, ingress, service, and certificate layers separately
- avoid changing several layers at once during troubleshooting
- keep the current public exposure path simple and stable
- document recurring ingress or certificate issues when they occur

## Known Current Constraints

The current exposure model is intentionally simple:

- one main public hostname
- one main ingress path
- limited number of TLS use cases
- no advanced routing governance yet

This is acceptable because the objective is to validate a strong but understandable public HTTPS baseline.

## Next Steps

The next logical improvements for this runbook are:

1. add more certificate troubleshooting examples
2. document renewal-related checks more explicitly
3. expand ingress validation examples if more services are added
4. later connect ingress controls with future governance policies

## Summary

The ingress and TLS path is already one of the strongest practical parts of the current lab.

When troubleshooting it, the best approach is to move step by step from DNS to ingress to service to Traefik to cert-manager to TLS secret, rather than treating public access as a single opaque problem.
