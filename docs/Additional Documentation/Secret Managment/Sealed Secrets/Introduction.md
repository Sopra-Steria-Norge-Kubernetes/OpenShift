

To authenticate with Azure Key Vault, OpenShift requires credentials from an Azure App Registration. Since these credentials are stored in a Git repository, they must be encrypted to ensure confidentiality. This is achieved using SealedSecrets, which provides one-way encryption such that only the SealedSecrets controller running within the OpenShift cluster can decrypt the data. 
