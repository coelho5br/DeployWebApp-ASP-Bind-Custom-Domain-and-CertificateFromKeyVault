# On an existing Web App, Create the DNS record to validate, Bind a custom domain, Create a Managed Certificate (Free) and Bind it

[![Deploy To Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fcoelho5br%2FDeployWebApp-ASP-Bind-Custom-Domain-and-CertificateFromKeyVault%2Fmaster%2Fazuredeploy.json)
[![Visualize](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fcoelho5br%2FDeployWebApp-ASP-Bind-Custom-Domain-and-CertificateFromKeyVault%2Fmaster%2Fazuredeploy.json)


### To deploy this template, you need to have the following resources:

1. Certificate on a Key Vault already with a Secret with certificate.
2. Domain purchased and the DNS zone that is hosted in azure. DNS zone does not need to be on the same resource Group.


This arm deployment will:

1. Create an App Service Plan.
2. Create an Web App.
3. Import a certificate from a Key Vault secret to the web App.
4. Create a DNS record with ASUID and CNAME when the DNS zone is on a different resource Group, so you can validate the bind
5. Bind a Custom Domain to the Web App.
6. Bind the certificate imported to the Web App.


To Deploy you will need the Key Vault Resource ID and the secret name. 

This can be accomplished getting:
```powershell
PS C:\> Get-AzKeyVault

Vault Name          : myvault1
Resource Group Name : myrg
Location            : westus
Resource ID         : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Ke
                      yVault/vaults/myvault1
Tags                :
```

Get-AzKeyVaultSecret -VaultName 'Contoso'


```
PS C:\> Get-AzKeyVaultSecret -VaultName 'Contoso'

Vault Name   : contoso
Name         : secret1
Version      :
Id           : https://contoso.vault.azure.net:443/secrets/secret1
Enabled      : True
Expires      : 4/6/2018 3:59:43 PM
Not Before   :
Created      : 4/5/2018 11:46:28 PM
Updated      : 4/6/2018 11:30:17 PM
Content Type :
Tags         :

Vault Name   : contoso
Name         : secret2
Version      :
Id           : https://contoso.vault.azure.net:443/secrets/secret2
Enabled      : True
Expires      :
Not Before   :
Created      : 4/11/2018 11:45:06 PM
Updated      : 4/11/2018 11:45:06 PM
Content Type :
Tags         :
```

It's important that the Microsoft.Azure.WebSites resource provider to have GET Access Policy right on the Key Vault, or it won't be able to import it.

On a public multi-tenanat, the Service Principal name is abfa0a7c-a6b6-4736-8310-5855508787cd .

You should run this command to give the permission:


```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId SUBSCRIPTION_ID
Set-AzKeyVaultAccessPolicy -VaultName YOUR_KEYVAULT_NAME -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd -PermissionsToSecrets get
```

More information on the [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/en-us/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-5.5.0).