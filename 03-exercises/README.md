# Introduction

1. Import the private key into local gpg key store
2. Check the fingerprint of the public key imported
3. Configure `.sops.yaml` properly (there's already something wrong)
4. Extract any value or values from `values.yaml` to `secrets.values.yaml`
5. Encrypt secrets.values.yaml using `helm secrets`
6. Execute the previous exercice command (using dry-run): `helm upgrade --dry-run ...`

* Why is there an issue with Helm upgrade?

7. Execute the right command and check the upgrade is done properly