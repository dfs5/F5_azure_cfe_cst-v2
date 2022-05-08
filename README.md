# F5_azure_cfe_cst-v2
based on: https://github.com/F5Networks/f5-azure-arm-templates-v2/tree/main/examples/failover
HA deployment with pubic IPs assigned directly to BIG-IP VE. Long failover times!

### HowTo

1. create KeyVault and a new Resource Group
2. create Secret with the name bigip-login and the desired bigip password
3. modify bigip-configurations files for Instance01 and Instance02 to reflect the new SecretID
- e.g.: 
    
    vaultUrl: 'https://"my-keyvault-name".vault.azure.net'
    
    secretId: bigip-login
5. sync to GitHub

    git status

    git add *

    git commit -m "update"

    git push -u origin main
6. create new Deployment and copy&past the content of e.g. full-stack-delpoyment.json
- modify deployment options as required: KeyVault URL, BYOL/PAYG, w/o App, SSH public key, src IP restrictons, usage of AZ
7. run deployment

### Accessing the BIG-IP Instances

- Or if you are going through a bastion host (when **provisionPublicIP** = **false**):
  - Obtain the public IP address of the bastion host:
    - **Console**: Navigate to **Resource Groups > *RESOURCE_GROUP* > Deployments > *DEPLOYMENT_NAME* > Outputs > *bastionPublicIp***.

#### SSH

- OR if you are going through a bastion host (when **provisionPublicIP** = **false**):

        From your desktop client/shell, create an SSH tunnel:
        ```bash
        ssh -i [your-private-ssh-key.pem] -o ProxyCommand='ssh -i [your-private-ssh-key.pem] -W %h:%p [AZURE-USER]@[BASTION-HOST-PUBLIC-IP]' [BIG-IP-USER]@[BIG-IP-MGMT-PRIVATE-IP]
        ```

        Replace the variables in brackets before submitting the command.

        For example:
        ```bash
        ssh -i ~/.ssh/mykey.pem -o ProxyCommand='ssh -i ~/.ssh/mykey.pem -W %h:%p azureuser@34.82.102.190' admin@10.0.0.11
        ```

#### WebUI

- OR when you are going through a bastion host (when **provisionPublicIP** = **false**):
    - From your desktop client/shell, create an SSH tunnel:

        ```bash
        ssh -i [your-private-ssh-key.pem] [AZURE-USER]@[BASTION-HOST-PUBLIC-IP] -L 8443:[BIG-IP-MGMT-PRIVATE-IP]:[BIGIP-GUI-PORT]
        ```
        For example:
        ```bash
        ssh -i ~/.ssh/mykey.pem azureuser@34.82.102.190 -L 8443:10.0.0.11:443
        ```

        NOTE: `[BIGIP-GUI-PORT]` is 443 for multi-NIC deployments and 8443 for single-NIC deployments.

        You should now be able to open a browser to the BIG-IP UI from your desktop:

        https://localhost:8443

Open a browser to the Management URL.
  - *NOTE: By default, the BIG-IP system's WebUI starts with a self-signed cert. Follow your browser's instructions for accepting self-signed certs (for example, if using Chrome, click inside the page and type this "thisisunsafe". If using Firefox, click "Advanced" button, click "Accept Risk and Continue").*
  - To Login: 
    - username: admin
    - password: enter the BIG-IP password from the Azure Key Vault secret you provided in the **bigIpPasswordSecretId** input.