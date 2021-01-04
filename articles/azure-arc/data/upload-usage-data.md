---
title: Hochladen von Nutzungsdaten in Azure Monitor
description: Hochladen von Nutzungsdaten für Datendienste mit Azure Arc-Unterstützung in Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92373900"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Hochladen von Nutzungsdaten in Azure Monitor

Sie können regelmäßig Informationen zur Nutzung exportieren. Beim Exportieren und Hochladen dieser Informationen werden die Ressourcen des Datencontrollers, der verwalteten SQL-Instanz und der PostgreSQL Hyperscale-Servergruppe in Azure erstellt und aktualisiert.

> [!NOTE] 
> Während der Vorschauphase fallen keine Kosten für die Verwendung Azure Arc-fähiger Datendienste an.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Warten Sie nach dem Erstellen des Azure Arc-Datencontrollers mindestens 24 Stunden, bevor Sie Nutzungsdaten hochladen.

## <a name="create-service-principal-and-assign-roles"></a>Erstellen des Dienstprinzipals und Zuweisen von Rollen

Vergewissern Sie sich zunächst, dass Sie den erforderlichen Dienstprinzipal erstellt und einer geeigneten Rolle zugewiesen haben. Details hierzu finden Sie in den folgenden Abschnitten:
* [Erstellen eines Dienstprinzipals](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Zuweisen von Rollen zum Dienstprinzipal](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Hochladen von Nutzungsdaten

Nutzungsdaten wie Bestand und Ressourcenverbrauch können wie folgt in zwei Schritten in Azure hochgeladen werden:

1. Melden Sie sich beim Datencontroller an. Geben Sie die Werte an der Eingabeaufforderung ein. 

   ```console
   azdata login
   ```

1. Exportieren Sie die Nutzungsdaten wie folgt mit dem Befehl `azdata arc dc export`:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Mit diesem Befehl wird die Datei `usage.json` mit allen Azure Arc-fähigen Datenressourcen erstellt, dies sind z. B. verwaltete SQL-Instanzen und PostgreSQL Hyperscale-Instanzen, die auf dem Datencontroller erstellt werden.

2. Hochladen der Nutzungsdaten mit dem Befehl ```azdata upload```

   ```console
   azdata arc dc upload --path usage.json
   ```

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

## <a name="next-steps"></a>Nächste Schritte

[Hochladen von Metriken in Azure Monitor](upload-metrics.md)

[Hochladen von Protokollen in Azure Monitor](upload-logs.md)

[Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md)

[Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal](view-data-controller-in-azure-portal.md)
