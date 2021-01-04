---
title: Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure Monitor
description: Hochladen von Ressourcenbestandsdaten, Nutzungsdaten, Metriken und Protokollen in Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 66b10efb6ca93bc6b4dd67d700daaf1f9049de68
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183429"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure Monitor

Sie können in regelmäßigen Abständen Nutzungsinformationen zu Abrechnungszwecken, Überwachungsmetriken und Protokolle exportieren und dann in Azure hochladen. Beim Exportieren und Hochladen dieser drei Datentypen werden auch die Ressourcen des Datencontrollers, der verwalteten SQL-Instanz und der PostgreSQL Hyperscale-Servergruppe in Azure erstellt und aktualisiert.

> [!NOTE] 
> Während der Vorschauphase fallen keine Kosten für die Verwendung Azure Arc-fähiger Datendienste an.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Bevor Sie Nutzungsdaten, Metriken oder Protokolle hochladen können, müssen Sie folgende Schritte ausführen:

* Installieren von Tools 
* [Registrieren des Ressourcenanbieters `Microsoft.AzureData`](#register-the-resource-provider) 
* [Erstellen des Dienstprinzipals](#create-service-principal)

## <a name="install-tools"></a>Installieren von Tools

Erforderliche Tools: 
* Azure CLI (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Informationen finden Sie unter [Installieren von Clienttools zum Bereitstellen und Verwalten von Azure Arc-fähigen Datendiensten](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

Vor dem Hochladen von Metriken oder Benutzerdaten in Azure müssen Sie sicherstellen, dass der Ressourcenanbieter `Microsoft.AzureData` für Ihr Azure-Abonnement registriert wurde.

Führen Sie den folgenden Befehl aus, um den Ressourcenanbieter zu überprüfen:

```azurecli
az provider show -n Microsoft.AzureData -o table
```

Falls der Ressourcenanbieter nicht bereits für Ihr Abonnement registriert ist, können Sie ihn registrieren. Führen Sie den folgenden Befehl aus, um ihn zu registrieren:  Die Ausführung dieses Befehls dauert unter Umständen ein bis zwei Minuten.

```azurecli
az provider register -n Microsoft.AzureData --wait
```

## <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

Der Dienstprinzipal wird verwendet, um Nutzungs- und Metrikdaten hochzuladen.

Führen Sie die folgenden Befehle aus, um den Dienstprinzipal für den Metrikupload zu erstellen:

> [!NOTE]
> Zum Erstellen eines Dienstprinzipals sind [bestimmte Berechtigungen in Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) erforderlich.

Aktualisieren Sie zum Erstellen eines Dienstprinzipals das folgende Beispiel. Ersetzen Sie `<ServicePrincipalName>` durch den Namen des Dienstprinzipals, und führen Sie den folgenden Befehl aus:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Wenn Sie den Dienstprinzipal zuvor erstellt haben und nur die aktuellen Anmeldeinformationen abrufen müssen, führen Sie den folgenden Befehl aus, um die Anmeldeinformationen zurückzusetzen:

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Führen Sie zum Erstellen eines Dienstprinzipals mit dem Namen `azure-arc-metrics` beispielsweise den folgenden Befehl aus:

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Beispielausgabe:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Speichern Sie die Werte `appId`, `password` und `tenant` in einer Umgebungsvariable, um sie später zu verwenden. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Weisen Sie nach der Erstellung des Dienstprinzipals diesen der entsprechenden Rolle zu. 

## <a name="assign-roles-to-the-service-principal"></a>Zuweisen von Rollen zum Dienstprinzipal

Führen Sie den folgenden Befehl aus, um dem Dienstprinzipal die Rolle `Monitoring Metrics Publisher` für das Abonnement zuzuweisen, in dem sich Ihre Datenbankinstanzressourcen befinden:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Bei der Ausführung in einer Windows-Umgebung müssen Sie für Rollennamen doppelte Anführungszeichen verwenden.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Beispielausgabe:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Nachdem der Dienstprinzipal der entsprechenden Rolle zugewiesen wurde, können Sie mit dem Hochladen von Metriken oder Benutzerdaten fortfahren. 

## <a name="upload-logs-metrics-or-user-data"></a>Hochladen von Protokollen, Metriken oder Benutzerdaten

Welche Schritte zum Hochladen von Protokollen, Metriken oder Benutzerdaten erforderlich sind, hängt davon ab, welche Art von Informationen Sie hochladen. 

[Hochladen von Protokollen in Azure Monitor](upload-logs.md)

[Hochladen von Metriken in Azure Monitor](upload-metrics.md)

[Hochladen von Nutzungsdaten in Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Allgemeine Anleitung zum Exportieren und Hochladen von Nutzungsdaten und Metriken

Erstellungs-, Lese-, Aktualisierungs- und Löschvorgänge – sogenannte CRUD-Vorgänge (Create, Read, Update, Delete) – in Azure Arc-fähigen Datendiensten werden zu Abrechnungs- und Überwachungszwecken protokolliert. Hintergrunddienste überwachen diese CRUD-Vorgänge und berechnen den Verbrauch entsprechend. Die tatsächliche Berechnung der Nutzung oder des Verbrauchs erfolgt nach einem Zeitplan und wird im Hintergrund durchgeführt. 

Während der Vorschauphase erfolgt dieser Vorgang in der Nacht. Allgemein wird empfohlen, die Nutzungsdaten nur einmal täglich hochzuladen. Wenn Nutzungsinformationen innerhalb desselben 24-Stunden-Zeitraums mehrmals exportiert und hochgeladen werden, wird nur der Ressourcenbestand im Azure-Portal aktualisiert, jedoch nicht der Ressourcenverbrauch.

Beim Hochladen von Metriken können in Azure Monitor nur die Daten der letzten 30 Minuten hochgeladen werden ([weitere Informationen](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Beim Hochladen von Metriken wird empfohlen, die Metriken unmittelbar nach dem Erstellen der Exportdatei hochzuladen, damit Sie das gesamte Dataset im Azure-Portal anzeigen können. Beispiel: Sie haben die Metriken um 14:00 Uhr exportiert und den Befehl für den Upload um 14:50 Uhr ausgeführt. Da in Azure Monitor nur die Daten der letzten 30 Minuten akzeptiert werden, werden im Portal möglicherweise keine Daten angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Dienstprinzipale](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal).

[Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md)

[Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal](view-data-controller-in-azure-portal.md)