# Authentication Methods for ArgoCD with GitHub

For the authentication methods, you must add the GitHub repository URL and the basepath for your applications:

- `argocd.main_git_repository.repourl`: SSH Git URL for repository where your applications are stored 
-  `argocd.main_git_repository.basepath`: Basepath is where argoCD will look to find your application definions. ArgoCD will look in your git repository for path: `$basepath/$environment/applications/`

## Authenticate with SSH

### Create a SSH key-pair

1. On your local computer navigate to the .ssh folder

    ```bash
    cd  ~/.ssh
    ```

2. Create the SSH key-pair and give it a name of your choice

    ```bash
    ssh-keygen -t rsa -f <NAME>
    ```

### Add SSH public key to GitHub

Log in to your GitHub profile that has access to the repository you want access to and follow the steps below:

1. Go to **Settings** --> **SSH and GPG keys**
2. Click on the **New SSH key** button on the top right corner
3. Add the newly created public key starting with `ssh-rsa ...` and give it an informative name.

### Create a secret for SSH

Create a secret for your Github SSH. 
Fill out the yaml below.

!!! Warning 
    For the git url use the SSH url of the git repository. This should look something like this: **git@github.com:TeamPoseidon/poseidon1_fake_main_repo.git**

=== "Secret"

    ```yaml 
    apiVersion: v1
    kind: Secret
    metadata:
      name: <Name of your tenant>
      namespace: gitops-developers
      labels:
        argocd.argoproj.io/secret-type: repository
    type: Opaque
    stringData:
      url: <SSH url of git repository>
      type: git
      sshPrivateKey: |
        -----BEGIN RSA PRIVATE KEY-----
        ** secret key here **
        -----END RSA PRIVATE KEY-----
    ```

=== "Demo"

    ```yaml 
    apiVersion: v1
    kind: Secret
    metadata:
      name: poseidon1
      namespace: gitops-developers
    labels:
        argocd.argoproj.io/secret-type: repository
    stringData:
      url: git@github.com:TeamPoseidon/poseidon1_fake_main_repo.git
      type: git
      sshPrivateKey: |
        -----BEGIN OPENSSH PRIVATE KEY-----
        b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
        NhAAAAAwEAAQAAAYEAucA6uzBvuWiyA4quhSFXHr1ypmvOBHwccLVDSq44OnvJyiDu/Vw3
        wvoGmXWk15AP+FXH+S+sA+vNbEkUcuj5CLQAzsNMGLtWvAMDzXpkGWME1AkrB9VV6kobww
        FTxh6tQEU5jTAj8F6ttUjuYlNpZr1NrmlBbI4JjrqoPdDTJql0VUA7LNGPYwe6/17Z6bJx
        lgnGWueP+GGJBOBPLfB7i3mKZ9xle7YbYocRO9/PMb3pWaemy/4Y22QPoir9zWhzinjDlh
        ppVqeMr5G0EADv5nktgbuKErQ8V27UYcr6Ffs2AAKw47CFSDyMoLXjGGBGxRBBWQe7naPe
        X64k9vFP/Eh29cjmRE+JEeJlwEVGoN66vFb3dGWR2aFcSQXri+BmdQRvwr0pQkk57MLqPX
        gwOaggDEmUAxWa7k5zmdF2D9useHx3MxF6YICGKRtEAR0XJt0UzVgdVy4QDjUnsJZAM5No
        4nQNb7SLMa6TiEAt385Ot662bOa4z/jva4ez4gkpAAAFkBGCcWgRgnFoAAAAB3NzaC1yc2
        EAAAGBALnAOrswb7losgOKroUhVx69cqZrzgR8HHC1Q0quODp7ycog7v1cN8L6Bpl1pNeQ
        D/hVx/kvrAPrzWxJFHLo+Qi0AM7DTBi7VrwDA816ZBljBNQJKwfVVepKG8MBU8YerUBFOY
        0wI/BerbVI7mJTaWa9Ta5pQWyOCY66qD3Q0yapdFVAOyzRj2MHuv9e2emycZYJxlrnj/hh
        iQTgTy3we4t5imfcZXu2G2KHETvfzzG96Vmnpsv+GNtkD6Iq/c1oc4p4w5YaaVanjK+RtB
        AA7+Z5LYG7ihK0PFdu1GHK+hX7NgACsOOwhUg8jKC14xhgRsUQQVkHu52j3l+uJPbxT/xI
        dvXI5kRPiRHiZcBFRqDeurxW93RlkdmhXEkF64vgZnUEb8K9KUJJOezC6j14MDmoIAxJlA
        MVmu5Oc5nRdg/brHh8dzMRemCAhikbRAEdFybdFM1YHVcuEA41J7CWQDOTaOJ0DW+0izGu
        k4hALd/OTreutmzmuM/472uHs+IJKQAAAAMBAAEAAAGAO4UlTKYQptCtegUONwqf5/G8sy
        cINNewJU1v6pY43kScPHChI/5Qv+FuC+5ui8RV2bVjBq4H6Jm+kVw5eTi909QaDib2U1Z0
        -----END OPENSSH PRIVATE KEY-----
    type: Opaque
    ```


### Encrypt the Secret with Kubeseal

To seal the secret with `kubeseal`, you can use the following command:

```bash
kubeseal --cert /path/to/pub.cert --scope namespace-wide -f secret.yaml -o yaml > sealed_secret.yaml
```
Replace `/path/to/pub.cert` with the path to your public certificate. `secret.yaml` is the path to the Secret YAML file you created in the first step. The sealed secret will be outputted to `sealed_secret.yaml`.

```yaml title="sealed_secret.yaml"
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  annotations:
    sealedsecrets.bitnami.com/namespace-wide: "true"
  creationTimestamp: null
  name: poseidon1
  namespace: gitops-developers
spec:
  encryptedData:
    sshPrivateKey: AgBokMbk9urpdte9hxrpGQoOma367oA41G3yiLaP8Btc4Aqr4TIcxdKbFjcbZu6W1ytU4MMgyM2m9c7vTPqH/61AnF6pA6BKkNcrViuXziLNWzesDtR8Egj3yNEcqp0la6wHzdmVavYhjcnqhJ9O/2KavLIP/BqEoKx7/vhbf9c8z7J/lgqp5Siq4xadgasgdgaX5dbHpZap+SyCz4GyYpUOhWWYLuoAhCpuY/QzGu0elzOC4BeCmj2bNCLV3lmeHme7skam12n3g0IAU9dnhK+L3lC5BdsmkKWswYuvhLAvEWzF7VKOHqkQEFqlMoOD8f+bBjkaFK3omGf4ir0C4RAq
    type: AgAV02zTTM7hLij9q2cKRs/cXNnS5EWEMS52vZe2b0E2N2i1twr1SuSNnvXGQB4upV1QO++Viwm150UmT+beWIP2QcC+0nqtfqujSzP670ZZvTVKuu7gOa3b5RVg0UHHK0lv+yUDrydK+UZjHDP2MWVmXsc5YanMknwjseo8Q4ad69adgasgSWAzfBRKlZPh
    url: AgBjm9LUY7SC3WXcgBtMBXb6CAZ4GV3/x3NEZ1GY6oVHPDSBifoATPh3jKtn5GUBktw1YsqFRREyuO9A1VNyi4DQu/H86vjo4pwo0G/egWkr4HbQKS6VpbUbwq2U2B3Cnp/A9H+giNuQ3Oj7UWnbOWPndpOTXL6oxFETTpnFDH5asgasgOgrTe6YM2XTNuizyDd
  template:
    metadata:
      annotations:
        sealedsecrets.bitnami.com/namespace-wide: "true"
      creationTimestamp: null
      labels:
        argocd.argoproj.io/secret-type: repository
      name: poseidon1
      namespace: gitops-developers
    type: Opaque

```

### Fill out the argo-specific section in your tenant definition

Fill out the argo-specific section by copying the following fields in the Sealed secret to the tenant variable `argocd.main_git_repository` inside your tenant definition:

* `url` --> `encrypted_url`
* `type` --> `encrypted_type`
* `sshPrivateKey` --> `ssh_key.private_key`


!!! Note
    It is very important that you also change the `argocd.main_git_repository.repourl` to the SSH url of the git repository

    `argocd.main_git_repository.repourl` --> `git@github.com:TeamPoseidon/poseidon1_fake_main_repo.git`

**Important:**

* `argocd.main_git_repository.repourl` --> `git@github.com:TeamPoseidon/poseidon1_fake_main_repo.git`

Remember to also add the required variable `argocd.main_git_repository.basepath` to the tenant definition.

This should result in a argo-specific section looking like this:
```yaml hl_lines="4 4 6 7 16 17"
...
argocd: 
    main_git_repository:
      repourl: <Change here>
      basepath:
      encrypted_url: <Change here>
      encrypted_type: <Change here>
      encrypted_username: 
      encrypted_password: 
      github_app: 
        enable_app: false
        id: 
        installation_id: 
        private_key:
      ssh_key:
        enable_ssh_key: true
        private_key: <Change here>
...
```