## 1. Controller and routing

- [x] 1.1 Select and pin a supported multi-architecture Traefik release; vendor and document its minimal controller, class, RBAC and any required CRD resources.
- [x] 1.2 Replace the main `proxy` Deployment with Traefik while preserving its selector, label, restart interface, host port, readiness and internal HTTP listener.
- [x] 1.3 Define dedicated-class routes, Jenkins session forward-auth, identity/header spoof protection and login redirect, TLS selection, request-size limits and stream-safe timeout settings.
- [x] 1.4 Verify Jenkins NetworkPolicy permits the proxy without broadening other access; ensure the deployment workflow applies it.

## 2. Installation and migration

- [x] 2.1 Update fresh installation, resource preflight and apply ordering for controller configuration and TLS Secrets; preserve existing path overrides.
- [x] 2.2 Implement and document an idempotent existing-install migration that retains certificate identity, reports failures and supports rollback.
- [x] 2.3 Replace the MCP Nginx sidecar, preserving the loopback backend, port 8090, `/mcp` normalization, secret-backed token injection and 3600-second streaming timeout.
- [x] 2.4 Implement and document optional MCP upgrade, token rotation, error handling and rollback; update NITA documentation and software inventory for all affected images.
- [x] 2.5 Coordinate the companion `nita-webapp` change for Compose, proxy specifications and tests; coordinate Jenkins session-gate changes across repositories. (Companion `nita-webapp/migrate-proxy-to-traefik` covers Compose, proxy specs and `tests/test_traefik_proxy.py`; the session-gate auth delta states the Traefik forward-auth contract; deployment order is `close-jenkins-direct-exposure`, then Jenkins hardening with the webapp gate.)

## 3. Verification

- [x] 3.1 Update installer regression and manifest validation tests; check RBAC, Ingress class isolation, image pinning and certificate prerequisite failures.
- [x] 3.2 Extend Kind CI to exercise the proxy over HTTPS, including certificate identity, webapp and Jenkins routes, forwarded headers, slash redirects, request-size limits, login redirects, session-based identity and spoofed-header rejection.
- [x] 3.3 Test both SSE routes for incremental delivery and the full 1850-second idle threshold; verify Jenkins NetworkPolicy with a policy-enforcing cluster.
- [x] 3.4 Exercise fresh install, upgrade, rerun and rollback, preserving an existing certificate; verify optional MCP access, client Authorization replacement, token rotation, streaming and absence when declined. (Kind v1.37 amd64 with local `nita-webapp` and `nita-jenkins` images: fresh `apply-k8s.sh` install and `verify-proxy.py` pass with no MCP resources and only ports 443/8000 when MCP is declined; `test_traefik_kubernetes.py` and `test_mcp_kubernetes.py` pass 15/15 plus the NetworkPolicy check on kindnet; `test_mcp_traefik.py` passes with Traefik v3.7.19, including a 10-second idle MCP stream.)
- [ ] 3.5 Run relevant tests and strict OpenSpec validation; verify amd64 and the existing gated arm64 integration path. (Done: unit suite, strict validation, and the full amd64 Kind CI sequence including the integration suite. Open: arm64 needs an `ubuntu-24.04-arm` runner with `NITA_ARM_CI_ENABLED`, which upstream has not enabled; all E2E images publish `linux/arm64`. Upstream CI also needs the companion `Juniper/nita-webapp#76`, because webapp `main` fails the forward-auth compatibility check with HTTP 403.)
- [x] 3.6 Validate MCP's 3600-second idle threshold with a controllable backend; verify secrets and tokens are not exposed in logs or client responses.
