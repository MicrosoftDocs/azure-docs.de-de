---
title: Verwenden verwalteter Identitäten für Azure-Ressourcen in Azure Cloud Shell | Microsoft-Dokumentation
description: Authentifizieren von Code mit MSI in Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199481"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Verwenden verwalteter Identitäten für Azure-Ressourcen in Azure Cloud Shell

Azure Cloud Shell unterstützt die Autorisierung mit verwalteten Identitäten für Azure-Ressourcen. Nutzen Sie diese Option, um Zugriffstoken für die sichere Kommunikation mit Azure-Diensten abzurufen.

## <a name="about-managed-identities-for-azure-resources"></a>Informationen zu verwalteten Identitäten für Azure-Ressourcen
Eine gängige Herausforderung beim Erstellen von Cloudanwendungen ist die sichere Verwaltung der Anmeldeinformationen, die für die Authentifizierung bei Clouddiensten im Code enthalten sein müssen. In Cloud Shell müssen Sie möglicherweise den Abruf von Anmeldeinformationen aus dem Schlüsseltresor authentifizieren, die ein Skript möglicherweise benötigt.

Mithilfe der verwalteten Identitäten für Azure-Ressourcen kann dieses Problem leichter gelöst werden, indem für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) bereitgestellt wird. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung einschließlich von Key Vault unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

## <a name="acquire-access-token-in-cloud-shell"></a>Abrufen eines Zugriffstokens in Cloud Shell

Führen Sie die folgenden Befehle aus, um Ihr MSI-Zugriffstoken als Umgebungsvariable `access_token` festzulegen.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Vorgehen bei Tokenablauf

Im lokalen MSI-Subsystem werden Token zwischengespeichert. Daher können Sie sie beliebig oft aufrufen, und ein Aufruf an Azure AD über das Netzwerk erfolgt nur in folgenden Fällen:
- Cachefehler aufgrund eines fehlenden Tokens im Cache
- Tokenablauf

Wenn Sie das Token in Ihrem Code zwischenspeichern, sollten Sie auf die Behandlung von Szenarien vorbereitet sein, bei denen die Ressource angibt, dass das Token abgelaufen ist.

Informationen zum Beheben von Tokenfehlern finden Sie unter [Verwenden der verwalteten Dienstidentität (Managed Service Identity, MSI) eines virtuellen Azure-Computers für den Tokenabruf](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zu MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Abrufen von Zugriffstoken von MSI-VMs](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
