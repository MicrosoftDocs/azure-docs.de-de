---
title: Protokollierung und Diagnose
description: Ausführliche Erklärung der Vorgehensweise zum Generieren und Abrufen von Protokollierung und Diagnose in Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494640"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Protokollierung und Diagnose in Azure Spatial Anchors

Azure Spatial Anchors bietet einen für die App-Entwicklung nützlichen Standardprotokollierungsmechanismus. Der Diagnoseprotokollierungsmodus in Spatial Anchors ist nützlich, wenn Sie weitere Informationen zum Debuggen benötigen. Die Diagnoseprotokollierung speichert Bilder der Umgebung.

## <a name="standard-logging"></a>Standardprotokollierung
In der Spatial Anchors-API können Sie den Protokollierungsmechanismus abonnieren, um nützliche Protokolle für die Anwendungsentwicklung und zum Debuggen erhalten. Die Standard-APIs für die Protokollierung speichern keine Bilder der Umgebung auf dem Datenträger des Geräts. Das SDK stellt diese Protokolle als Ereignisrückrufe bereit. Es steht Ihnen frei, diese Protokolle in den Protokollierungsmechanismus der Anwendung zu integrieren.

### <a name="configuration-of-log-messages"></a>Konfiguration und Protokollmeldungen
Es sind zwei für den Benutzer relevante Ereignisrückrufe verfügbar. Das folgende Beispiel zeigt, wie die Sitzung konfiguriert werden kann:

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Ereignisse und Eigenschaften

Diese Ereignisrückrufe stehen zur Verfügung, um Protokolle und Fehler aus der Sitzung zu verarbeiten:

- [LogLevel:](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel) Gibt die Detailebene für die Ereignisse an, die von der Laufzeit empfangen werden.
- [OnLogDebug:](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug) Stellt Standardprotokollereignisse zum Debuggen bereit.
- [Fehler:](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error) Stellt Protokollereignisse bereit, die von der Laufzeit als Fehler betrachtet werden.

## <a name="diagnostics-logging"></a>Diagnoseprotokollierung

Zusätzlich zum Standardmodus für den Protokollierungsvorgang bietet Spatial Anchors auch einen Diagnosemodus. Der Diagnosemodus erfasst Bilder der Umgebung und protokolliert diese auf den Datenträger. Sie können diesen Modus zum Debuggen von bestimmten Problemen verwenden, beispielsweise wenn ein Anker nicht zuverlässig gefunden wird. Aktivieren Sie die Diagnoseprotokollierung ausschließlich zum Reproduzieren eines bestimmten Problems. Deaktivieren Sie sie anschließend wieder. Aktivieren Sie die Diagnoseprotokollierung nicht, wenn Sie Ihre Anwendungen ganz normal ausführen.

Wenn Sie den Microsoft-Support kontaktieren, wird Sie der Microsoft-Mitarbeiter möglicherweise bitten, zur weiteren Untersuchung ein Diagnosepaket zu übermitteln. In diesem Fall können Sie die Diagnoseprotokollierung aktivieren und das Problem reproduzieren, damit Sie das Diagnosepaket übermitteln können.

Wenn Sie ohne vorherige Bestätigung eines Microsoft-Mitarbeiters ein Diagnoseprotokoll an Microsoft übermitteln, wird die Übermittlung unbeantwortet bleiben.

In den folgenden Abschnitten wird erklärt, wie der Diagnosemodus aktiviert wird und wie Diagnoseprotokolle an Microsoft übermittelt werden.

### <a name="enable-diagnostics-logging"></a>Aktivieren der Diagnoseprotokollierung

Wenn Sie eine Sitzung für die Diagnoseprotokollierung aktivieren, weisen alle Vorgänge in der Sitzung eine entsprechende Diagnoseprotokollierung im lokalen Dateisystem auf. Während der Protokollierung werden Bilder der Umgebung auf den Datenträger gespeichert.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Übermitteln des Diagnosepakets

In dem folgenden Codeausschnitt wird gezeigt, wie ein Diagnosepaket an Microsoft übermittelt wird. Dieses Paket enthält Bilder der Umgebung, die von der Sitzung erfasst worden sind, nachdem Sie die Diagnoseprotokollierung aktiviert haben.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Teile eines Diagnosepakets
Das Diagnosepaket enthält möglicherweise die folgenden Informationen:

- **Keyframe-Bilder:** Während der Sitzung bei deaktivierter Diagnoseprotokollierung erfasste Bilder der Umgebung.
- **Protokolle:** Von der Laufzeit erfasste Protokollereignisse.
- **Sitzungsmetadaten:** Metadaten, die die Sitzung identifizieren.