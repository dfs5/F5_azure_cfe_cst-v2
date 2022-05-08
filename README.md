# F5_azure_cfe_cst-v2
based on: https://github.com/F5Networks/f5-azure-arm-templates-v2/tree/main/examples/failover

# HowTo

1. create KeyVault and a new Resource Group
2. create Secret with the name bigip-login and the desired bigip password
3. modify bigip-configurations files for Instance01 and Instance02 to reflect the new SecretID
- e.g.: 
    
    vaultUrl: 'https://"my-keyvault-name".vault.azure.net'
    
    secretId: bigip-login
5. sync to GitHub
6. create new Deployment and copy&past the content of e.g. full-stack-delpoyment.json
- modify deployment options as required
7. run deployment
