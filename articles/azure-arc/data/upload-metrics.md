---
title: Hochladen von Metriken in Azure Monitor
description: Hochladen von Metriken Azure Arc-fähiger Datendienste in Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f319f912520a69a0c68f89a3d4178f63cc45ca1f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356547"
---
# <a name="upload-metrics-to-azure-monitor"></a>Hochladen von Metriken in Azure Monitor

Sie können in regelmäßigen Abständen Überwachungsmetriken exportieren und diese dann in Azure hochladen. Beim Exportieren und Hochladen von Daten werden auch die Ressourcen des Datencontrollers, der verwalteten SQL-Instanz und der PostgreSQL Hyperscale-Servergruppe in Azure erstellt und aktualisiert.

> [!NOTE] 
> Während der Vorschauphase fallen keine Kosten für die Verwendung Azure Arc-fähiger Datendienste an.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Fortfahren sicher, dass Sie den erforderlichen Dienstprinzipal erstellt und einer geeigneten Rolle zugewiesen haben. Details hierzu finden Sie in den folgenden Abschnitten:
* [Erstellen eines Dienstprinzipals](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Zuweisen von Rollen zum Dienstprinzipal](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Hochladen von Metriken

Mit Azure Arc-Datendiensten können Sie optional Metriken in Azure Monitor hochladen, um Metriken aggregieren und analysieren, Warnungen auslösen, Benachrichtigungen senden oder automatisierte Aktionen auslösen zu können. 

Wenn Sie Ihre Daten an Azure Monitor senden, können Sie auch Metrikdaten außerhalb des Standorts und in riesigem Umfang für die langfristige Aufbewahrung der Daten für erweiterte Analysen speichern.

Wenn Sie Azure Arc-Datendienste an mehreren Standorten verwenden, können Sie Azure Monitor als zentralen Speicherort verwenden, um alle Protokolle und Metriken für Ihre Standorte zu erfassen.

## <a name="set-final-environment-variables-and-confirm"></a>Festlegen und Bestätigen der endgültigen Umgebungsvariablen

Legen Sie die URL der SPN-Autorität in einer Umgebungsvariablen fest:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Stellen Sie sicher, dass alle erforderlichen Umgebungsvariablen festgelegt sind:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Hochladen von Metriken in Azure Monitor

Zum Hochladen von Metriken für Ihre Azure Arc-fähigen verwalteten SQL-Instanzen und Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen führen Sie die folgenden CLI-Befehle aus:

1. Melden Sie sich mit `azdata` beim Datencontroller an.
 
1. Exportieren Sie alle Metriken in die angegebene Datei:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Laden Sie die Metriken in Azure Monitor hoch:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Warten Sie mindestens 30 Minuten, nachdem die Azure Arc-fähigen Dateninstanzen für den ersten Upload erstellt wurden.
   >
   >Führen Sie direkt nach dem `export` unbedingt den `upload` durch, da Azure Monitor nur Metriken für die letzten 30 Minuten akzeptiert. [Weitere Informationen](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Wenn beim Exportieren Fehler vom Typ „Failure to get metrics“ (Fehler beim Abrufen der Metriken) angezeigt werden, überprüfen Sie, ob die Datensammlung auf `true` festgelegt ist, indem Sie den folgenden Befehl ausführen:

```console
azdata arc dc config show
```

Sehen Sie sich den Abschnitt „security“ an.

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Überprüfen Sie, ob die Eigenschaften `allowNodeMetricsCollection` und `allowPodMetricsCollection` auf `true` festgelegt sind.

## <a name="view-the-metrics-in-the-portal"></a>Anzeigen der Metriken im Portal

Nachdem die Metriken hochgeladen wurden, können Sie sie im Azure-Portal anzeigen.
> [!NOTE]
> Beachten Sie, dass es einige Minuten dauern kann, bis die hochgeladenen Daten verarbeitet wurden und die Metriken im Portal angezeigt werden.


Zum Anzeigen der Metriken im Portal verwenden Sie folgenden Link, um das Portal zu öffnen: <https://portal.azure.com>. Suchen Sie anschließend in der Suchleiste anhand ihres Namens nach Ihrer Datenbankinstanz:

Sie können die CPU-Auslastung auf der Übersichtsseite anzeigen, oder Sie können ausführlichere Metriken anzeigen, indem Sie im linken Navigationsbereich auf „Metriken“ klicken.

Wählen Sie SQL Server als Metriknamespace aus:

Wählen Sie die Metrik aus, die Sie visualisieren möchten (Sie können auch mehrere Metriken auswählen):

Ändern Sie die Häufigkeit in die letzten 30 Minuten:

> [!NOTE]
> Metriken können nur für die letzten 30 Minuten hochgeladen werden. Azure Monitor lehnt Metriken ab, die älter als 30 Minuten sind.

## <a name="automating-uploads-optional"></a>Automatisieren von Uploads (optional)

Wenn Sie Metriken und Protokolle nach einem Zeitplan hochladen möchten, können Sie ein Skript erstellen und es mit einem Timer alle paar Minuten ausführen. Im Folgenden finden Sie ein Beispiel für die Automatisierung der Uploads mithilfe eines Linux-Shellskripts.

Fügen Sie in Ihrem bevorzugten Text-Editor oder Code-Editor das folgende Skript in eine Datei ein, und speichern Sie die Datei als ausführbare Skriptdatei, z. B. als SH-Datei (Linux/Mac) oder als CMD-, BAT- oder PS1-Datei.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Ausführen der Skriptdatei

```console
chmod +x myuploadscript.sh
```

Führen Sie das Skript alle 20 Minuten aus:

```console
watch -n 1200 ./myuploadscript.sh
```

Sie können auch einen Auftragsplaner wie cron oder den Windows-Taskplaner bzw. einen Orchestrator wie Ansible, Puppet oder Chef verwenden.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Allgemeine Anleitung zum Exportieren und Hochladen von Nutzungsdaten und Metriken

Erstellungs-, Lese-, Aktualisierungs- und Löschvorgänge – sogenannte CRUD-Vorgänge (Create, Read, Update, Delete) – in Azure Arc-fähigen Datendiensten werden zu Abrechnungs- und Überwachungszwecken protokolliert. Hintergrunddienste überwachen diese CRUD-Vorgänge und berechnen den Verbrauch entsprechend. Die tatsächliche Berechnung der Nutzung oder des Verbrauchs erfolgt nach einem Zeitplan und wird im Hintergrund durchgeführt. 

Während der Vorschauphase erfolgt dieser Vorgang in der Nacht. Allgemein wird empfohlen, die Nutzungsdaten nur einmal täglich hochzuladen. Wenn Nutzungsinformationen innerhalb desselben 24-Stunden-Zeitraums mehrmals exportiert und hochgeladen werden, wird nur der Ressourcenbestand im Azure-Portal aktualisiert, jedoch nicht der Ressourcenverbrauch.

Beim Hochladen von Metriken können in Azure Monitor nur die Daten der letzten 30 Minuten hochgeladen werden ([weitere Informationen](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Beim Hochladen von Metriken wird empfohlen, die Metriken unmittelbar nach dem Erstellen der Exportdatei hochzuladen, damit Sie das gesamte Dataset im Azure-Portal anzeigen können. Beispiel: Sie haben die Metriken um 14:00 Uhr exportiert und den Befehl für den Upload um 14:50 Uhr ausgeführt. Da in Azure Monitor nur die Daten der letzten 30 Minuten akzeptiert werden, werden im Portal möglicherweise keine Daten angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

[Hochladen von Protokollen in Azure Monitor](upload-logs.md)

[Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure Monitor](upload-usage-data.md)

[Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md)

[Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal](view-data-controller-in-azure-portal.md)
