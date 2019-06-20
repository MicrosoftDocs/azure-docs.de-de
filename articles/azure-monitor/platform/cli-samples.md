---
title: CLI-Beispiele für den Schnellstart mit Azure Monitor
description: Beispiel-CLI-Befehle für Azure Monitor-Features Azure Monitor ist ein Microsoft Azure-Dienst, der das Senden von Warnbenachrichtigungen, das Aufrufen von Web-URLs basierend auf Werten konfigurierter Telemetriedaten und die automatische Skalierung von Clouddiensten, virtuellen Computern und Web-Apps ermöglicht.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: fa3293346fee6f6666db01dab5587dd760df84b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60740882"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>CLI-Beispiele für den Schnellstart mit Azure Monitor
In diesem Artikel werden Beispielbefehle für die Befehlszeilenschnittstelle (Command Line Interface, CLI) beschrieben, mit denen Sie auf Azure Monitor-Features zugreifen können. Azure Monitor ermöglicht die automatische Skalierung von Clouddiensten, virtuellen Computern und Web-Apps sowie das Senden von Warnbenachrichtigungen oder das Aufrufen von Web-URLs basierend auf Werten konfigurierter Telemetriedaten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie die Azure CLI noch nicht installiert haben, befolgen Sie die Anweisungen zum [Installieren der Azure CLI](/cli/azure/install-azure-cli). Sie können auch [Azure Cloud Shell](/azure/cloud-shell) für die Ausführung der CLI als interaktive Oberfläche in Ihrem Browser verwenden. Eine vollständige Referenz aller verfügbaren Befehle finden Sie in der [Azure Monitor-CLI-Referenz](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Anmelden an Azure
Der erste Schritt besteht darin, sich am Azure-Konto anzumelden.

```azurecli
az login
```

Nach der Ausführung dieses Befehls müssen Sie sich über die Anweisungen auf dem Bildschirm anmelden. Alle Befehle funktionieren im Kontext Ihres Standardabonnements.

Verwenden Sie den folgenden Befehl, um die Details Ihres aktuellen Abonnements aufzulisten.

```azurecli
az account show
```

Verwenden Sie den folgenden Befehl, um den Arbeitskontext in ein anderes Abonnement zu ändern.

```azurecli
az account set -s <Subscription ID or name>
```

Führen Sie Folgendes aus, um eine Liste mit allen unterstützten Azure Monitor-Befehlen anzuzeigen.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Anzeigen des Aktivitätsprotokolls für ein Abonnement

Führen Sie die folgenden Schritte aus, um eine Liste mit Aktivitätsprotokollereignissen anzuzeigen.

```azurecli
az monitor activity-log list
```

Probieren Sie Folgendes aus, um alle verfügbaren Optionen anzuzeigen:

```azurecli
az monitor activity-log list -h
```

Beispiel für das Auflisten von Protokollen nach resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Beispiel für das Auflisten von Protokollen nach Aufrufer

```azurecli
az monitor activity-log list --caller myname@company.com
```

Beispiel für das Auflisten von Protokollen nach Aufrufer für einen Ressourcentyp innerhalb eines Datumsbereichs

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Verwenden von Warnungen 
> [!NOTE]
> Aktuell wird in der CLI nur „Warnungen (klassisch)“ unterstützt. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Abrufen klassischer Warnungsregeln in einer Ressourcengruppe

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Erstellen einer klassischen Metrikwarnungsregel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Löschen einer klassischen Warnungsregel

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Protokollprofile

Verwenden Sie die Informationen in diesem Abschnitt, um Protokollprofile zu verwenden.

### <a name="get-a-log-profile"></a>Abrufen eines Protokollprofils

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Hinzufügen eines Protokollprofils mit Aufbewahrung

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Hinzufügen eines Protokollprofils mit Aufbewahrung und Event Hub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Entfernen eines Protokollprofils

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnose

Verwenden Sie die Informationen in diesem Abschnitt, um Diagnoseeinstellungen zu verwenden.

### <a name="get-a-diagnostic-setting"></a>Abrufen einer Diagnoseeinstellung

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Erstellen einer Einstellung für das Diagnoseprotokoll 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Löschen einer Diagnoseeinstellung

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Autoscale

Nutzen Sie die Informationen in diesem Abschnitt, um Einstellungen für die automatische Skalierung zu verwenden. Sie müssen diese Beispiele ändern.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Abrufen der Einstellungen für die automatische Skalierung für eine Ressourcengruppe

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Abrufen der Einstellungen für die automatische Skalierung nach Name in einer Ressourcengruppe

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Festlegen von Einstellungen zum automatischen Skalieren

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

