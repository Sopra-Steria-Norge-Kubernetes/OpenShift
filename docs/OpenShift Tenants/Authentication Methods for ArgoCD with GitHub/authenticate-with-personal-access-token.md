# Authentication Methods for ArgoCD with GitHub

For the authentication methods, you must add the GitHub repository URL and the basepath for your applications:

- `argocd.main_git_repository.repourl`: Plaintext HTTPS Git URL for repository where your applications are stored 
-  `argocd.main_git_repository.basepath`: Basepath is where argoCD will look to find your application definions. ArgoCD will look in your git repository for path: `$basepath/$environment/applications/`

## Authenticate with Personal Access Token (PAT)

### Create a PAT

PAT's are a token that's personal for you, and is a way to access your github account. This guide help you create a PAT with a minimum access for the intended purpose.

Go to your profile settings, and then choose Developer settings:
![Settings](../../img/Openshift%20Tenants/PAT-Settings.png)

![Developer settings](../../img/Openshift%20Tenants/PAT-Developer_settings.png)

Under personal access token choose generate new token:
![Generate new token](../../img/Openshift%20Tenants/PAT_generate_new_token.png)

Fill out the required fields and choose a sensible Expiration, select the correct repository and set the correct permissions. You only need read access for content and metadata:
![Fill out values](../../img/Openshift%20Tenants/PAT-FillOutValues.png)

![Select permissions](../../img/Openshift%20Tenants/PAT_selecet_permissions.png)

And then press **"Generate token"**. You now have a PAT to use for the next steps.

### Create a secret for PAT

Create a secret for your git repository.
Fill out the yaml below. 

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
      url: <HTTPS url of git repository>
      type: git
      password: <Personal access token>
      username: <Git username associated with personal access token>
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
    type: Opaque
    stringData:
      url: https://github.com/TeamPoseidon/poseidon1_fake_main_repo.git
      type: git
      password: pat_8VUndBHIJQCWIJUkhHWKWMFDAGAGMAGJ202ZIaT
      username: team.poseidon
    ```

!!! Note
    For the git url use the HTTPS url of the git repository.

### Encrypt the Secret with Kubeseal

To seal the secret with `kubeseal`, you can use the following command:

```bash
kubeseal --cert /path/to/pub.cert --scope namespace-wide -f secret.yaml -o yaml
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
    password: AgAKW3TQw5l51HOdRbuDjXpJvv+cm6AdE5E95kNn9yr9sifywF6VMSkToJO/YVs9w5NsG3Qd8CXR1dSB+klb3kJe9lIucY1gVML82XOZL+nRpVrDivyKNpxHfQOI0BOOg+wbQlZpzdhYaUUAkNN9vukAWoeb67BmDClMrhdzaVH19Htvc0GkoCNM/2HPbuKM8BTUn7xeUut6PVMmcNCKyb3uae3w26i8ElmQI10OSOMoFjVm37HNj1QnjbEmUmsvS6H0sVKo3/NiYC6T75FsesG/jp/FyNBDyjRlqpdSlJ0YYv15oEwI46ByxDFHyFA
    type: AgAjVZbAT7lXfEwsRC25vNqceFiDoiCLM/lUx4I7ti1ngfRG50MC8c5v3ZiEaCxTICBqkBJpGKtXcgih8jddhXsldWyKlwVFKP5z43LLv2nqNc5Hb/tKZFCP+OVXa/zeWbVREKm/+dH170t0RlTd9PtUJ947i3I5/uJSlayG+D+zg4XBxj2QfKmKS5hntfE
    url: AgCTo2lVv2zzReuviCxUlkfSF8BTNYX4iQAlQMP7D52AMpLa4fHJ/Ml+N3vzrmdsjeuOp2Wa51q9mz57tVKmYLHA7UQ7Jr2kwwTBNaiz2ZC7oeI2pLPtYuvhkKdz5vMBQxNzAmSavq7jdFulx9Q5k1BY3WNH3TYiQgwhXwHmIJKQS8gZH0JHU62UIZ7RzSNSxmqoejhurDIuzxdpm+Llm21U+VdHGBY+D7FnonEZ5xe5Hx9WayqYgFE
    username: AgCrjo7uFTS2TWJlE7z6bi2bU1Z2GHI8ycF9Ktg7ZSHMJUemC5Zo9kBD393ixQkElda+UnkU5vYT5LaGf6KiggI1y0Ww3LOjunc60VXy3C7jDH4qmlydR/J7qALCrxvqoB1aPWVEadFWLyMLhEqWhtqtV+AugZIzFt4BjL5y5jjl9IgvOlINp8/2HbNWqvn2E02wsMn
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

Fill out the argo-specific section by copying the field `password` of the sealed secret to `argocd.main_git_repository.encrypted_password`. Likewise, copy these fields the same way: 

* `url` --> `encrypted_url`
* `type` --> `encrypted_type`
* `username` --> `encrypted_username`
* `password`--> `encrypted_password`

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
      encrypted_username: <Change here>
      encrypted_password: <Change here>
      github_app: 
        enable_app: false
        id: 
        installation_id: 
        private_key: 
      ssh_key:
        enable_ssh_key: false
        private_key:
...
```