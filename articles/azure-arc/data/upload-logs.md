---
title: Hochladen von Protokollen in Azure Monitor
description: Hochladen von Protokollen für Azure Arc-fähige Datendienste in Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92373905"
---
# <a name="upload-logs-to-azure-monitor"></a>Hochladen von Protokollen in Azure Monitor

Sie können Protokolle in regelmäßigen Abständen exportieren und dann in Azure hochladen. Beim Exportieren und Hochladen von Protokollen werden auch die Ressourcen des Datencontrollers, der verwalteten SQL-Instanz und der PostgreSQL Hyperscale-Servergruppe in Azure erstellt und aktualisiert.

> [!NOTE] 
> Während der Vorschauphase fallen keine Kosten für die Verwendung Azure Arc-fähiger Datendienste an.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie Protokolle hochladen können, müssen die folgenden Schritte erfolgen: 

1. [Erstellen eines Log Analytics-Arbeitsbereichs](#create-a-log-analytics-workspace)
1. [Zuweisen von ID und gemeinsam verwendetem Schlüssel zu Umgebungsvariablen](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Führen Sie zum Erstellen eines Arbeitsbereichs für die Protokollanalyse diese Befehle aus, um einen Log Analytics-Arbeitsbereich zu erstellen und die Zugriffsinformationen in Umgebungsvariablen festzulegen.

> [!NOTE]
> Überspringen Sie diesen Schritt,wenn Sie bereits über einen Arbeitsbereich verfügen.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Beispielausgabe:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Zuweisen von ID und gemeinsam verwendetem Schlüssel zu Umgebungsvariablen

Speichern Sie die `customerId` des Arbeitsbereichs für die Protokollanalyse als Umgebungsvariable zur späteren Verwendung:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Mit diesem Befehl werden die Zugriffsschlüssel zurückgegeben, die für die Verbindung mit Ihrem Arbeitsbereich für die Protokollanalyse erforderlich sind:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Beispielausgabe:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Speichern Sie den Primärschlüssel zur späteren Verwendung in einer Umgebungsvariablen:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Festlegen und Bestätigen der endgültigen Umgebungsvariablen

Legen Sie die URL der SPN-Autorität in einer Umgebungsvariablen fest:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Überprüfen von Umgebungsvariablen

Stellen Sie sicher, dass alle erforderlichen Umgebungsvariablen festgelegt sind:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Nach Festlegen der Umgebungsvariablen können Sie Protokolle in den Arbeitsbereich für die Protokollanalyse hochladen. 

## <a name="upload-logs-to-azure-monitor"></a>Hochladen von Protokollen in Azure Monitor

 Zum Hochladen von Protokollen für Ihre Azure Arc-fähigen verwalteten SQL-Instanzen und Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen führen Sie die folgenden CLI-Befehle aus:

1. Melden Sie sich mit [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] beim Azure Arc-Datencontroller an.

   ```console
   azdata login
   ```

   Befolgen Sie die Anweisungen, um den Namespace, den Benutzernamen des Administrators und das Kennwort festzulegen. 

1. Exportieren Sie alle Protokolle in die angegebene Datei:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Laden Sie die Protokolle in einen Log Analytics-Arbeitsbereich in Azure Monitor hoch:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Anzeigen Ihrer Protokolle im Azure-Portal

Nachdem Ihre Protokolle hochgeladen wurden, sollten Sie sie mit dem Abfrage-Explorer für Protokolle wie folgt abfragen können:

1. Öffnen Sie das Azure-Portal. Suchen Sie dann oben in der Suchleiste anhand des Namens nach Ihrem Arbeitsbereich, und wählen Sie ihn aus.
2. Wählen Sie im linken Bereich „Protokolle“ aus.
3. Wählen Sie „Erste Schritte“ aus (bzw. die Links auf der Seite „Erste Schritte“, um mehr über Log Analytics zu erfahren, falls Sie noch nicht damit vertraut sind).
4. Wenn Sie Log Analytics erstmals verwenden, arbeiten Sie das Tutorial durch, um weitere Informationen zu Log Analytics zu erfahren.
5. Erweitern Sie unten in der Liste mit den Tabellen die Option „Benutzerdefinierte Protokolle“. Eine Tabelle mit dem Namen „sql_instance_logs_CL“ wird angezeigt.
6. Wählen Sie neben dem Tabellennamen das Augensymbol aus.
7. Wählen Sie die Schaltfläche „View in query editor“ (Im Abfrage-Editor anzeigen) aus.
8. Sie verfügen nun über eine Abfrage im Abfrage-Editor, mit der die letzten 10 Ereignisse im Protokoll angezeigt werden.
9. Sie können jetzt mit Abfragen für die Protokolle experimentieren, indem Sie den Abfrage-Editor verwenden, Warnungen festlegen usw.

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

[Hochladen von Metriken und Protokollen in Azure Monitor](upload-metrics.md)

[Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure Monitor](upload-usage-data.md)

[Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md)

[Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal](view-data-controller-in-azure-portal.md)
