---
title: Sicheres Speichern von Anmeldeinformationen auf dem virtuellen Computer für Data Science – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anmeldeinformationen auf der Data Science Virtual Machine sicher speichern können. Sie lernen, wie Sie verwaltete Dienstidentitäten und Azure Key Vault verwenden, um Zugangsanmeldeinformationen zu speichern.
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, räumliche Analysen, Data Science-Teamprozess
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 79dba586a5f7102d0012c381593551a951f1b38e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502341"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Sicheres Speichern von Anmeldeinformationen auf dem virtuellen Computer für Data Science

Eine gängige Herausforderung beim Erstellen von Cloudanwendungen ist die Verwaltung der Anmeldeinformationen, die für die Authentifizierung bei Clouddiensten im Code enthalten sein müssen. Der Schutz dieser Anmeldeinformationen ist eine wichtige Aufgabe. Im Idealfall werden sie nie auf Entwicklerarbeitsstationen angezeigt oder in die Quellcodeverwaltung eingecheckt. 

Mithilfe der [verwalteten Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) kann dieses Problem leichter gelöst werden, indem für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) bereitgestellt wird. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

Eine Möglichkeit zum Schützen der Anmeldeinformationen ist, MSI in Kombination mit [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), einem verwalteten Azure-Dienst zum sicheren Speichern von Geheimnissen und kryptografischen Schlüsseln, zu verwenden. Sie können mit der verwalteten Identität auf einen Schlüsseltresor zugreifen und die autorisierten Geheimnisse und kryptografischen Schlüssel aus dem Schlüsseltresor abrufen. 

Die Dokumentation zu den verwalteten Identitäten für Azure-Ressourcen und Key Vault ist eine umfassende Quelle für ausführliche Informationen zu diesen Diensten. Der Rest dieses Artikels beschreibt die grundlegende Verwendung von MSI und Key Vault auf dem virtuellen Computer für Data Science (Data Science Virtual Machine, DSVM) für den Zugriff auf Azure-Ressourcen. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Erstellen einer verwalteten Identität auf der DSVM 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>Zuweisen von Key Vault-Zugriffsberechtigungen zum VM-Prinzipal
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Zugreifen auf ein Geheimnis im Schlüsseltresor aus der DSVM

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Zugreifen auf Speicherschlüssel von der DSVM

```
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Zugreifen auf den Schlüsseltresor aus Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your key vault URL.
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Zugreifen auf den Schlüsseltresor aus Azure CLI

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
