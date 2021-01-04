---
title: Automatisches Sichern von Schlüsselwerten aus Azure App Configuration-Speichern
description: Hier erfahren Sie, wie Sie eine automatische Sicherung von Schlüsselwerten zwischen App Configuration-Speichern einrichten.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 04edf2eeb231ff1444c732840def2b78b1373e79
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565924"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Automatisches Sichern von App Configuration-Speichern

In diesem Artikel erfahren Sie, wie Sie eine automatische Sicherung von Schlüsselwerten aus einem primären Azure App Configuration-Speicher in einem sekundären Speicher einrichten. Diese automatische Sicherung nutzt die Integration von Azure Event Grid mit App Configuration. 

Nach der Einrichtung der automatischen Sicherung veröffentlicht App Configuration bei allen Änderungen, die an Schlüsselwerten in einem Konfigurationsspeicher vorgenommen wurden, Ereignisse in Azure Event Grid. Event Grid unterstützt eine Vielzahl von Azure-Diensten, aus denen Benutzer die Ereignisse abonnieren können, die auftreten, wenn Schlüsselwerte erstellt, aktualisiert oder gelöscht werden.

## <a name="overview"></a>Übersicht

In diesem Artikel verwenden Sie Azure Queue Storage, um Ereignisse von Event Grid zu erhalten, und verarbeiten mit einem Azure Functions-Timertrigger Ereignisse in der Warteschlange in Batches. 

Wenn eine Funktion aufgrund der Ereignisse ausgelöst wird, ruft sie die neuesten Werte der Schlüssel, die sich geändert haben, aus dem primären App Configuration-Speicher ab und aktualisiert den sekundären Speicher entsprechend. Dieses Setup unterstützt das Kombinieren mehrerer, in einem kurzen Zeitraum stattfindender Änderungen in einem Sicherungsvorgang und vermeidet so übermäßig häufige Anforderungen an Ihre App Configuration-Speicher.

![Diagramm: Architektur der App Configuration-Speichersicherung](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Ressourcenbereitstellung

Hinter der Sicherung von App Configuration-Speichern steht die Motivation, mehrere Konfigurationsspeicher in verschiedenen Azure-Regionen zu verwenden, um die Georesilienz Ihrer Anwendung zu erhöhen. Um dies zu erreichen, sollten sich Ihre primären und sekundären Speicher in unterschiedlichen Azure-Regionen befinden. Alle anderen Ressourcen, die in diesem Tutorial erstellt werden, können in einer beliebigen Region Ihrer Wahl bereitgestellt werden. Der Grund hierfür ist, dass es bei einem Ausfall der primären Region nichts Neues zu sichern gibt, solange nicht wieder auf die primäre Region zugegriffen werden kann.

In diesem Tutorial erstellen Sie einen sekundären Speicher in der Region `centralus` und alle anderen Ressourcen in der Region `westus`.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Voraussetzungen 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) mit der Workload Azure-Entwicklung.

- [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für dieses Tutorial ist mindestens Version 2.3.1 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `<resource_group_name>` am Standort `westus` erstellt.  Ersetzen Sie `<resource_group_name>` durch einen eindeutigen Namen für Ihre Ressourcengruppe.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Erstellen eines App Configuration-Speichers

Erstellen Sie Ihren primären und sekundären App Configuration-Speicher in unterschiedlichen Regionen.
Ersetzen Sie `<primary_appconfig_name>` und `<secondary_appconfig_name>` durch eindeutige Namen für ihre Konfigurationsspeicher. Der Speichername muss immer eindeutig sein, da er als DNS-Name verwendet wird.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Erstellen Sie ein Speicherkonto und eine Warteschlange zum Empfangen von Ereignissen, die von Event Grid veröffentlicht werden.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Abonnieren Ihrer App Configuration-Speicherereignisse

Sie abonnieren diese beiden Ereignisse aus dem primären App Configuration-Speicher:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Mit dem folgenden Befehl wird ein Event Grid-Abonnement für die beiden Ereignisse erstellt, die an die Warteschlange gesendet werden. Der Endpunkttyp wird auf `storagequeue` festgelegt und der Endpunkt auf die Warteschlangen-ID. Ersetzen Sie `<event_subscription_name>` durch den Namen Ihrer Wahl für das Ereignisabonnement.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Erstellen von Funktionen für die Verarbeitung von Ereignissen aus Queue Storage

### <a name="set-up-with-ready-to-use-functions"></a>Einrichtung mit sofort einsatzbereiten Funktionen

In diesem Artikel arbeiten Sie mit C#-Funktionen, die die folgenden Eigenschaften aufweisen:
- Runtime Stack .NET Core 3.1
- Azure Functions Runtimeversion 3.x
- Funktion wird vom Timer alle 10 Minuten ausgelöst

Wir haben [eine Funktion getestet und veröffentlicht](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup), die Sie ohne Änderungen am Code verwenden können, um Ihnen den Einstieg in das Sichern Ihrer Daten zu erleichtern. Laden Sie die Projektdateien herunter, und [veröffentlichen Sie sie in Ihrer eigenen Azure-Funktions-App aus Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Nehmen Sie keine Änderungen an den Umgebungsvariablen im heruntergeladen Code vor. Im nächsten Abschnitt nehmen Sie die erforderlichen App-Einstellungen vor.
>

### <a name="build-your-own-function"></a>Erstellen einer eigenen Funktion

Wenn der oben bereitgestellte Beispielcode nicht Ihren Anforderungen entspricht, können Sie auch eine eigene Funktion erstellen. Die Funktion muss die folgenden Aufgaben ausführen können, um die Sicherung abzuschließen:
- Lesen Sie regelmäßig die Inhalte Ihrer Warteschlange, um zu überprüfen, ob diese Benachrichtigungen von Event Grid enthält. Implementierungsdetails finden Sie unter [Schnellstart: Azure Queue Storage-Clientbibliothek v12 für .NET](../storage/queues/storage-quickstart-queues-dotnet.md).
- Wenn die Warteschlange [Ereignisbenachrichtigungen von Event Grid](./concept-app-configuration-event.md?branch=pr-en-us-112982#event-schema) enthält, extrahieren Sie alle eindeutigen `<key, label>`-Informationen aus den Ereignisbenachrichtigungen. Die Kombination aus Schlüssel und Bezeichnung ist der eindeutige Bezeichner für Schlüssel-Wert-Änderungen im primären Speicher.
- Lesen Sie alle Einstellungen aus dem primären Speicher. Aktualisieren Sie nur die Einstellungen im sekundären Speicher, für die ein entsprechendes Ereignis in der Warteschlange vorliegt. Löschen Sie alle Einstellungen aus dem sekundären Speicher, die in der Warteschlange, aber nicht im primären Speicher vorhanden waren. Sie können das [App Configuration-SDK](https://github.com/Azure/AppConfiguration#sdks) verwenden, um programmgesteuert auf Ihre Konfigurationsspeicher zuzugreifen.
- Löschen Sie die Benachrichtigungen aus der Warteschlange, wenn bei der Verarbeitung keine Ausnahmen aufgetreten sind.
- Implementieren Sie die Fehlerbehandlung Ihren Bedürfnissen entsprechend. Im obigen Codebeispiel finden Sie einige häufig auftretende Ausnahmen, die Sie behandeln sollten.

Weitere Informationen zum Erstellen von Funktionen finden Sie unter: [Erstellen einer Funktion in Azure, die von einem Timer ausgelöst wird](../azure-functions/functions-create-scheduled-function.md) und [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Wählen Sie den Timerzeitplan nach bestem Wissen abhängig davon aus, wie oft Sie Änderungen an Ihrem primären Konfigurationsspeicher vornehmen. Ein zu häufiges Ausführen der Funktion führt möglicherweise zu einer Drosselung bei Anforderungen an den Speicher.
>


## <a name="create-function-app-settings"></a>Festlegen von Funktions-App-Einstellungen

Wenn Sie eine von uns bereitgestellte Funktion verwenden, sind die folgenden App-Einstellungen in Ihrer Funktions-App erforderlich:
- `PrimaryStoreEndpoint`: Endpunkt für den primären App Configuration-Speicher. z. B. `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Endpunkt für den sekundären App Configuration-Speicher. z. B. `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Hierbei handelt es sich um die Warteschlangen-URI. z. B. `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Der folgende Befehl nimmt die erforderlichen App-Einstellungen in Ihrer Funktions-App vor. Ersetzen Sie `<function_app_name>` durch den Namen der Funktions-App.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Gewähren von Zugriff auf die verwaltete Identität der Funktions-App

Fügen Sie mit dem folgenden Befehl oder über das [Azure-Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) eine systemseitig zugewiesene verwaltete Identität für Ihre Funktions-App hinzu.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Ihr Konto muss über die Berechtigung `Owner` für den geeigneten Bereich (Ihr Abonnement oder die Ressourcengruppe) verfügen, um die erforderliche Ressourcenerstellung und eine Rollenverwaltung durchführen zu können. Hilfe zu Rollenzuweisungen finden Sie ggf. unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

Gewähren Sie mit den folgenden Befehlen oder über das [Azure-Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) der verwalteten Identität Ihrer Funktions-App Zugriff auf Ihre App Configuration-Speicher. Verwenden Sie hierfür die folgenden Rollen:
- Weisen Sie die `App Configuration Data Reader`-Rolle im primären App Configuration-Speicher zu.
- Weisen Sie die `App Configuration Data Owner`-Rolle im sekundären App Configuration-Speicher zu.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Gewähren Sie mit dem folgenden Befehl oder über das [Azure-Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) der verwalteten Identität Ihrer Funktions-App Zugriff auf Ihre Warteschlange. Weisen Sie die `Storage Queue Data Contributor`-Rolle in der Warteschlange zu.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Auslösen eines App Configuration-Ereignisses

Sie können einen Schlüsselwert im primären Speicher erstellen, aktualisieren oder daraus löschen, um zu testen, ob alles funktioniert. Diese Änderung sollte einige Sekunden nach dem Auslösen von Azure Functions durch den Timer automatisch im sekundären Speicher angezeigt werden.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Sie haben das Ereignis ausgelöst. In wenigen Augenblicken sendet Event Grid die Ereignisbenachrichtigung an Ihre Warteschlange. Zeigen Sie *nach der nächsten geplanten Ausführung Ihrer Funktion* die Konfigurationseinstellungen im sekundären Speicher an, um zu überprüfen, ob dieser den aktualisierten Schlüsselwert aus dem primären Speicher enthält.

> [!NOTE]
> Sie können [Ihre Funktion während der Tests und Problembehandlung manuell auslösen](../azure-functions/functions-manually-run-non-http.md), ohne auf den geplanten Timertrigger zu warten.

Nachdem Sie sichergestellt haben, dass die Sicherungsfunktion erfolgreich ausgeführt wurde, können Sie sehen, dass der Schlüssel jetzt im sekundären Speicher vorhanden ist.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Problembehandlung

Wenn die neue Einstellung nicht im sekundären Speicher angezeigt wird:

- Stellen Sie sicher, dass die Sicherungsfunktion ausgelöst wurde, *nachdem* Sie die Einstellung im primären Speicher erstellt haben.
- Es ist möglich, dass Event Grid die Ereignisbenachrichtigung nicht rechtzeitig an die Warteschlange senden konnte. Überprüfen Sie, ob Ihre Warteschlange noch die Ereignisbenachrichtigung von Ihrem primären Speicher enthält. Wenn dies der Fall ist, lösen Sie die Sicherungsfunktion noch mal aus.
- Überprüfen Sie die [Azure Functions-Protokolle](../azure-functions/functions-create-scheduled-function.md#test-the-function) auf Fehler oder Warnungen.
- Stellen Sie im [Azure-Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) sicher, dass die Azure-Funktions-App korrekte Werte für die Anwendungseinstellungen enthält, die Azure Functions zu lesen versucht.
- Sie können auch die Überwachung und Warnungen für Azure Functions mit [Azure Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd) einrichten. 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese App Configuration-Instanz und das Ereignisabonnement weiterverwenden möchten, überspringen Sie die Bereinigung der in diesem Artikel erstellten Ressourcen. Verwenden Sie andernfalls den folgenden Befehl, um die in diesem Artikel erstellten Ressourcen zu löschen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie die automatische Sicherung Ihrer Schlüsselwerte einrichten, sollten Sie sich darüber informieren, wie Sie die Georesilienz Ihrer Anwendung erhöhen können:

- [Resilienz und Notfallwiederherstellung](concept-disaster-recovery.md)
