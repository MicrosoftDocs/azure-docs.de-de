---
title: Hinzufügen eines Dienstprinzipals zur Azure Analysis Services-Administratorrolle | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Automatisierungsdienstprinzipal der Azure Analysis Services-Serveradministratorrolle hinzufügen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100727"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle 

 Um unbeaufsichtigte PowerShell-Tasks zu automatisieren, muss ein Dienstprinzipal auf dem verwalteten Analysis Services-Server über die Rechte eines **Serveradministrators** verfügen. In diesem Artikel wird beschrieben, wie Sie auf einem Azure AS-Server einen Dienstprinzipal zur Serveradministratorrolle hinzufügen. Sie können dies mithilfe von SQL Server Management Studio oder einer Resource Manager-Vorlage durchführen. 

> [!NOTE]
> Dienstprinzipale müssen direkt zur Serveradministratorrolle hinzugefügt werden. Das Hinzufügen eines Dienstprinzipals zu einer Sicherheitsgruppe und das anschließende Hinzufügen dieser Sicherheitsgruppe zur Serveradministratorrolle wird nicht unterstützt. 

## <a name="before-you-begin"></a>Voraussetzungen
Bevor Sie diese Aufgabe ausführen, muss ein Dienstprinzipal in Azure Active Directory registriert sein.

[Erstellen eines Dienstprinzipals – Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Erstellen eines Dienstprinzipals – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Verwendung von SQL Server Management Studio

Sie können mit SQL Server Management Studio (SSMS) Serveradministrator konfigurieren. Für die Durchführung dieser Aufgabe benötigen Sie die Rechte eines [Serveradministrators](analysis-services-server-admins.md) auf dem Azure AS-Server. 

1. Stellen Sie in SSMS eine Verbindung mit Ihrem Azure AS-Server her.
2. Klicken Sie unter **Servereigenschaften** > **Sicherheit** auf **Hinzufügen**.
3. Suchen Sie unter **Benutzer oder Gruppe auswählen** anhand des Namens nach Ihrer registrierten App, und klicken Sie dann auf **Hinzufügen**.

    ![Suchen nach dem Dienstprinzipalkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Überprüfen Sie die ID des Dienstprinzipalkontos, und klicken Sie dann auf **OK**.
    
    ![Screenshot: ID des Dienstprinzipalkontos mit hervorgehobener Schaltfläche „OK“](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Verwenden einer Resource Manager-Vorlage

Sie können Serveradministratoren auch durch Bereitstellen des Analysis Services-Servers mithilfe einer Azure Resource Manager-Vorlage konfigurieren. Die Identität, die die Bereitstellung ausführt, muss zu der Rolle **Mitwirkender** für die Ressource in der [rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md) gehören.

> [!IMPORTANT]
> Der Dienstprinzipal muss mit dem Format `app:{service-principal-client-id}@{azure-ad-tenant-id}` hinzugefügt werden.

Mit der folgenden Resource Manager-Vorlage wird ein Analysis Services-Server mit einem angegebenen Dienstprinzipal bereitgestellt, der der Rolle „Analysis Services-Administrator“ hinzugefügt wurde:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Verwenden von verwalteten Identitäten

Eine verwaltete Identität kann auch der Liste der Analysis Services-Administratoren hinzugefügt werden. Beispielsweise ist es möglich, dass Sie über eine [Logik-App mit einer vom System zugewiesenen verwalteten Identität](../logic-apps/create-managed-service-identity.md) verfügen und dieser die Möglichkeit geben, Ihren Analysis Services-Server zu verwalten.

Im Azure-Portal und den APIs werden verwaltete Identitäten überwiegend über ihre Dienstprinzipalobjekt-ID identifiziert. Analysis Services erfordert jedoch, dass sie über ihre Client-ID identifiziert werden. Zum Abrufen der Client-ID für einen Dienstprinzipal können Sie die Azure-Befehlszeilenschnittstelle verwenden:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Alternativ können Sie auch PowerShell verwenden:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Mit dieser Client-ID können Sie dann in Verbindung mit der Mandanten-ID der Liste der Analysis Services Administratoren die verwaltete Identität hinzuzufügen, wie oben beschrieben.

## <a name="related-information"></a>Verwandte Informationen

* [Herunterladen des SQL Server PowerShell-Moduls](/sql/ssms/download-sql-server-ps-module)   
* [Herunterladen von SSMS](/sql/ssms/download-sql-server-management-studio-ssms)