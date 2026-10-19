## 1. Manifests

- [ ] 1.1 Remove `hostPort: 8443` from the Jenkins container `ports` entry in `k8s/jenkins-deployment.yaml` (keep `containerPort: 8443`)
- [ ] 1.2 In `k8s/jenkins-network-policy.yaml`, add a `podSelector` for `io.kompose.service: proxy` to the 8080 ingress rule
- [ ] 1.3 In `k8s/jenkins-network-policy.yaml`, restrict the 8443 ingress rule to `podSelector: io.kompose.service: jenkins`

## 2. Operator documentation

- [ ] 2.1 `install.sh`: closing message gives `https://${HOST}/jenkins/` for the Jenkins UI
- [ ] 2.2 `docs/nita-examples.md`: replace the `:8443` URL with the `/jenkins/` path
- [ ] 2.3 `docs/containers.md` / `docs/pods.md`: remove or refresh output that shows `0.0.0.0:8443->8443/tcp` or a `:8443` Jenkins URL

## 3. Verification

- [ ] 3.1 `kubectl apply --dry-run=client` on both manifests succeeds; `apply-k8s.sh` rolls out cleanly
- [ ] 3.2 From outside the cluster, `https://<host>:8443/` is refused and `https://<host>/jenkins/` loads through the proxy
- [ ] 3.3 `nita-cmd jenkins version` and `nita-cmd jenkins jobs ls` still succeed
- [ ] 3.4 Webapp trigger, SSE console, and lifecycle history still work (webapp → `jenkins:8080` path unaffected)
- [ ] 3.5 `openspec validate close-jenkins-direct-exposure --strict` passes
