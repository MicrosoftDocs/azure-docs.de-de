---
title: Migrieren von Azure-Integrationsressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Integrationsressourcen von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: d85c2e7c1aa3738ce6a9d3130d2ddc400c333a9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151199"
---
# <a name="migrate-integration-resources-to-global-azure"></a>Migrieren von Integrationsressourcen zu Azure weltweit

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Integrationsressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="service-bus"></a>Service Bus

Azure Service Bus-Dienste haben keine Datenexport- und -importfunktionen. Um Service Bus-Ressourcen von Azure Deutschland zu Azure weltweit zu migrieren, können Sie die Ressourcen [als eine Azure Resource Manager-Vorlage](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) exportieren. Passen Sie dann die exportierte Vorlage für Azure weltweit an, und erstellen Sie die Ressourcen erneut.

> [!NOTE]
> Bei einem Exportieren einer Resource Manager-Vorlage werden die Daten (z. B. Nachrichten) nicht kopiert. Bei einem Exportieren einer Vorlage werden nur die Metadaten neu erstellt.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>Service Bus-Metadaten

Die folgenden Service Bus-Metadatenelemente werden neu erstellt, wenn Sie eine Resource Manager-Vorlage exportieren:

- Namespaces
- Warteschlangen
- Themen
- Abonnements
- Regeln
- Autorisierungsregeln

### <a name="keys"></a>Schlüssel

In den vorherigen Schritten zum Exportieren und erneuten Erstellen werden die Shared Access Signature-Schlüssel, die Autorisierungsregeln zugeordnet sind, nicht kopiert. Wenn Sie die Shared Access Signature-Schlüssel beibehalten müssen, verwenden Sie das Cmdlet `New-AzServiceBuskey` mit dem optionalen Parameter `-Keyvalue`, um den Schlüssel als Zeichenfolge zu übernehmen. Das aktualisierte Cmdlet ist im [Az-Modul von Azure PowerShell](/powershell/azure/install-az-ps) verfügbar.

### <a name="usage-example"></a>Verwendungsbeispiel

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> Sie müssen Ihre Anwendungen so aktualisieren, dass sie eine neue Verbindungszeichenfolge verwenden, und zwar selbst dann, wenn Sie die Schlüssel beibehalten. DNS-Hostnamen sind in Azure Deutschland und Azure weltweit unterschiedlich.

### <a name="sample-connection-strings"></a>Beispielverbindungszeichenfolgen

**Azure Deutschland**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**Azure weltweit**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/#step-by-step-tutorials) durcharbeiten.
- Machen Sie sich damit vertraut, wie Sie [Resource Manager-Vorlagen exportieren](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), oder lesen Sie die Übersicht zu [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Lesen Sie die [Übersicht über Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="logic-apps"></a>Logic Apps

Der Azure Logic Apps-Dienst ist in Azure Deutschland nicht verfügbar. Azure Scheduler (verfügbar) ist allerdings bald veraltet. Verwenden Sie Logic Apps, um Planungsaufträge in Azure weltweit zu erstellen.

Weitere Informationen finden Sie unter:

- Machen Sie sich mit Funktionen in Azure Logic Apps vertraut, indem Sie die [Logic Apps-Tutorials](https://docs.microsoft.com/azure/logic-apps/#step-by-step-tutorials) durcharbeiten.
- Lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Speicher](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
