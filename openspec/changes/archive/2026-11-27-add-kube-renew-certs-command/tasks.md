## 1. CLI Script

- [x] 1.1 Add `cli_scripts/nita-cmd_kube_renew-certs` with project header and
      the same command-selection/debug pattern used by existing kube scripts
- [x] 1.2 Implement primary command execution for certificate renewal via
      `kubeadm certs renew all` when available
- [x] 1.3 Return a clear non-zero error and message when certificate renewal is
      unsupported in the current environment
- [x] 1.4 Remove the automatic `systemctl restart kubelet`; after successful
      renewal, print the required manual follow-up guidance

## 2. Help Script

- [x] 2.1 Add `cli_scripts/nita-cmd_kube_renew-certs_help` with one-line usage
      text consistent with existing `_help` scripts

## 3. Documentation

- [x] 3.1 Update `docs/nita-cmd.md` to include `nita-cmd kube renew-certs`
- [x] 3.2 Update `docs/nita-cmd.md` to remove the kubelet-restart claim and
      explain that component restarts are a manual operator step
- [x] 3.3 Update `docs/certificates.md` to explain local-host scope, execution
      on every control-plane node in HA clusters, manual static-Pod restart,
      and external-CA limitations

## 4. Verification

- [x] 4.1 Confirm the new command appears in `nita-cmd kube help`
- [x] 4.2 Use mocked `kubeadm` and `systemctl` commands to confirm validation
      invokes renewal without restarting kubelet or performing a real renewal
- [x] 4.3 Run with `kubeadm` absent and confirm the expected error and non-zero
      exit status
- [x] 4.4 Run with `_CLI_RUNNER_DEBUG=1` and confirm the renewal command is
      printed to stderr before execution
- [x] 4.5 Confirm success output reminds operators to restart control-plane
      static Pods and repeat on each control-plane node in HA clusters
- [x] 4.6 Verify the documented procedure in a disposable kubeadm cluster
      (kind v0.33.0 node, Kubernetes v1.37.0: `nita-cmd kube renew-certs`
      exited 0 and renewed all 11 certificates; restarting each static Pod gave
      new containers and `/readyz` ok. kube-apiserver took 29s to stop, so
      `docs/certificates.md` now says to wait until the Pod has stopped rather
      than a fixed 20s.)
