# Authentication Methods for ArgoCD with GitHub

For the authentication methods, you must add the GitHub repository URL and the basepath for your applications:

- `argocd.main_git_repository.repourl`: Plaintext HTTPS Git URL for repository where your applications are stored 
-  `argocd.main_git_repository.basepath`: Basepath is where argoCD will look to find your application definions. ArgoCD will look in your git repository for path: `$basepath/$environment/applications/`

## Authenticate with a GitHub App


### Create a GitHub App for ArgoCD
Follow the steps below to create a GitHub App suited for ArgoCD:

1. Follow the steps in the [Github documentation](https://docs.github.com/en/enterprise-cloud@latest/apps/creating-github-apps/registering-a-github-app/registering-a-github-app) to register a GitHub App.

2. For connecting to ArgoCD, ensure that your application has at least `Read-only` permissions to the `Contents` and `metadata` of the repository.  This is the minimum requirement. See  [ArgoCD documentation](https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/#github-app-credential) for further explanation.

3. Generate a Private key inside the GitHub App. This will later be mapped and encrypted to the tenant variable `github_app.private_key`

4. Go to the tab Install App, install your new app, and choose the repositories you want to include. Finally, press install.

5. From the web URL inside your app extract the `githubAppInstallationID`. This will later be mapped to the variable `github_app.installation_id` in the tenant definition. 

6. Click on app settings and extract the App ID, which will later be mapped to the tenant variable `github_app.id`

   ![Github App Dashboard](../../img/Openshift%20Tenants/PAT_github-app-dashboard.png)

### Create a secret for Github App

Create a secret for your Github App.
Fill out the yaml below.


=== "Secret"

    ```yaml title="secret.yml"
    apiVersion: v1
    kind: Secret
    metadata:
      name: <Name of your tenant>
      namespace: gitops-developers
      labels:
        argocd.argoproj.io/secret-type: repository
    stringData:
      type: git
      url: <HTTPS url of git repository>
      githubAppId: <GitHub App Id>
      githubAppInstallationID: <GitHub Installation Id>
      githubAppPrivateKey: |
        -----BEGIN RSA PRIVATE KEY-----
        ** secret key here **
        -----END RSA PRIVATE KEY-----
    type: Opaque
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
      type: git
      url: https://github.com/TeamPoseidon/poseidon1_fake_main_repo.git
      githubAppId: 124145
      githubAppInstallationID: 5214652346
      githubAppPrivateKey: |
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
kubeseal --cert /path/to/pub.cert --scope namespace-wide -f secret.yaml -o yaml
```
Replace `/path/to/pub.cert` with the path to your public certificate provided by Sopra Steria. `secret.yaml` is the path to the Secret YAML file you created in the first step. The sealed secret will be outputted to `sealed_secret.yaml`.

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
    githubAppId: AgCfjVQbZOkLwnY4Dpwit0W0LOh90SU1sf40s2hkrqJHOWRJ3fElO51sc6thSX/P9v9AgMnETpxi7KQamrSldwJISDp8XLFtXrnCxLc6C7gfcQL3I6Nld1NjSfMsvNy7Wd1gsyaQmIJV0u3UVAHm4VP/48A1ss+J7yXJoGVnI2ujlBy3n9SX7T82RWbhAt
    githubAppInstallationID: AgAafCX0Xln0LymsikcxK35f10hLm3XLhu35kmvLfltwJ2odxEP58Iq2Wrjl8qe0/1N2tSWbdhxnrVXZ5YEi48EiAUSlGfZ7zYAg1wIPSN7svJAY1mNzHDbJdRLZtpWGzrhYf5mS8SyjMIktbVnwYE9Mgapzm7kE6OiYeuw3ZLIuvohkcqzwgem9cVVKpf+PLgibaMaar3NDEYWbf6Iq5S5EC3Cv7gBqE8OoPjU7U/ZaTGOV8GAojn982fT4pkeqRzfAk63Swu9v
    githubAppPrivateKey: AgCLwhDTUapl/m7IsJkRUbdV47QQ9SZjFUkZ3hC2cvNQjmknBuNwJMHF7y6Et0osXXRLvgFduMTflBp80YjW6G/0RmUqHe+rT14ThemBwWnSd57H3krZmY4KtHoACG4bHvqnzs7yt0BGItWul0dqI4Ys0mJVF7jXmtkdRlDx142K3yufAEDQ+cpR+jjZerycWCETL1S78WQJf97w4uPvT7+59K5GPSX59/f+ZMP3tIAzG1hc1x36DHk4Dh+C+uaH1ozForHSzW2luV6/gVtmMMKEh5y8nernWB9jWJo6n/4A4buqhDpT8MEOaZELvPZTnf/tNSNREPOVdsyc3qvxcvqfp3zlv+fgaY9dN/GQY+Vx1DOhoPwkoVk8wk4WPAWMZ9kNY0wbIUJPHfiMFRpjJYIh5qHrJcK1Dzzjt+lyS+ZPFcX9R/Rr4wE8atMB2GYly2AYi5VIc7Kyw5PDYfjlQAdg7I
    type: AgBnObKmkh/cUKMHUAvWKcrBbcz9mgy7NC9mNF4YDM4RIVTNDRu+1UNa7KTjbIopOSzExq3UgdU75/5RVfV40oCPsO7mItdYTFFx6/zbBfldoZO135rkOddMZL9fPMiB6vvJLMMXNMaCncbLqJI2fj/YB7hXOE4dzupEuWweos+jtKMeYHyY+rVaG3Nfq8RxqDbbOyb/uxgdq5pgcC8j+ekrElIINCC6EdXrtwEtOnSrjd
    url: AgAu8s4oBkIR0yKQ9jvWI7amKa9C56cHnvb8z19fN6Ctv7M50WCBCbgIRGIq2sU0rmc94EpQ/L3cvgQ1ehzDNz/BbZ8ap18ceJXRydM8NktQ5Kp1ZJBcOmjYWA987d7b1R1xtHPmCK/ZAWpRHInfy6CA9mQeSmWBVhILKD+kj2+9nkjuRgXzOzsQ/IgumlC37Xg5stcm61MXE9OWbajDzcNiIpnotkycIEtDsx/W9/zQXra0t3nQzIwOB9YhcPnCd+EVoqBP5Mt9JBKMTp8b7hso/Up5hVOwdSZnUCG7nLpMkkVKQl6iwGIqpDV/Y58NzD45NdW8kBcFwO1zAj9ZUXlL1lP3s99vSWpNQC1nSo6BdLopOAP3e/AD4v2REyrOHc6qv/
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
Fill out the argo-specific section by copying the following fields under the `spec.encryptedData` in the Sealed secret to the tenant variable `argocd.main_git_repository` inside your tenant definition:

   * `true` --> `github_app.enable_app`
   * `type` --> `encrypted_type`
   * `url` --> `encrypted_url`
   * `githubAppId` -> `github_app.id`
   * `githubAppInstallationID` -> `github_app.installation_id`
   * `githubAppPrivateKey` -> `github_app.private_key`

Remember to also add the required variables `argocd.main_git_repository.repourl` and `argocd.main_git_repository.basepath` to the tenant definition.

This should result in a argo-specific section looking like this:
```yaml
...
argocd: 
    main_git_repository:
      repourl: 
      basepath:
      encrypted_url: <Change here>
      encrypted_type: <Change here>
      encrypted_username:
      encrypted_password: 
      github_app: 
        enable_app: true
        id: <Change here>
        installation_id: <Change here>
        private_key: <Change here>
      ssh_key:
        enable_ssh_key: false
        private_key:
...
```



