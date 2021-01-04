---
title: Außerbetriebnahme der Bereitstellungs-APIs für Azure Monitor-Metriken und automatische Skalierung
description: Metriken und klassische APIs für Autoskalierung, auch Azure Service Management (ASM) oder RDFE-Bereitstellungsmodell genannt, werden eingestellt.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536692"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor: Einstellung der APIs des klassischen Bereitstellungsmodells für Metriken und Autoskalierung

Azure Monitor (Azure Insights bei der ersten Veröffentlichung) bietet zurzeit die Möglichkeit zum Erstellen und Verwalten von Einstellungen für Autoskalierung sowie zum Nutzen von Metriken aus klassischen virtuellen Computern und klassischen Clouddiensten. Die ursprüngliche Sammlung der klassischen, auf einem Bereitstellungsmodell basierenden APIs wird **ab dem 30. Juni 2019** in allen öffentlichen und privaten Azure-Clouds in allen Regionen eingestellt.   

Die gleichen Vorgänge werden seit über einem Jahr durch eine Reihe von Azure Resource Manager-basierten APIs unterstützt. Das Azure-Portal verwendet die neuen REST-APIs für Autoskalierung und Metriken. Ein neues SDK, PowerShell und eine CLI, die auf diesen Resource Manager-APIs basieren, sind ebenfalls verfügbar. Unsere Partnerdienste für die Überwachung nutzen die neuen Resource Manager-basierten REST-APIs in Azure Monitor.  

## <a name="who-is-not-affected"></a>Wer ist nicht betroffen?

Wenn Sie Autoskalierung über das Azure-Portal, das [neue Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, die CLI oder Resource Manager-Vorlagen verwalten, ist keine Aktion erforderlich.  

Wenn Sie Metriken über das Azure-Portal oder über verschiedene [Partnerüberwachungsdienste](./partners.md) nutzen, ist keine Aktion erforderlich. Microsoft arbeitet mit den Überwachungspartnern zusammen, um zu den neuen APIs zu migrieren.

## <a name="who-is-affected"></a>Wer ist betroffen?

Dieser Artikel ist für Sie relevant, wenn Sie die folgenden Komponenten verwenden:

- **Klassisches Azure Insights SDK**: Bei Verwendung des [klassischen Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) müssen Sie auf das neue Azure Monitor SDK für [.NET](https://github.com/azure/azure-libraries-for-net#download) oder [Java](https://github.com/azure/azure-libraries-for-java#download) umsteigen. Laden Sie das [Azure Monitor SDK-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) herunter.

- **Klassische Autoskalierung**: Wenn Sie die [APIs für klassische Autoskalierungseinstellungen](/previous-versions/azure/reference/mt348562(v=azure.100)) aus Ihren benutzerdefinierten Tools aufrufen oder das [klassische Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) verwenden, sollten Sie auf die Verwendung der [Resource Manager Azure Monitor-REST-API](/rest/api/monitor/autoscalesettings) umsteigen.

- **Klassische Metriken**: Wenn Sie Metriken mit den [klassischen REST-APIs](/previous-versions/azure/reference/dn510374(v=azure.100)) oder dem [klassischen Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) aus benutzerdefinierten Tools nutzen, sollten Sie auf die Verwendung der [Resource Manager Azure Monitor-REST-API](/rest/api/monitor/autoscalesettings) umsteigen. 

Wenn Sie sich nicht sicher sind, ob Ihr Code oder Ihre benutzerdefinierten Tools die klassischen APIs aufrufen, untersuchen Sie Folgendes:

- Überprüfen Sie den URI, auf den in Ihrem Code oder Tool verwiesen wird. Die klassischen APIs verwenden den URI https://management.core.windows.net. Sie sollten den neueren URI für die Resource Manager-basierten APIs verwenden, der mit `https://management.azure.com/` beginnt.

- Vergleichen Sie den Assemblynamen auf Ihrem Computer. Die ältere klassische Assembly befindet sich unter https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Wenn Sie zertifikatbasierte Authentifizierung für den Zugriff auf Metriken oder Autoskalierungs-APIs verwenden, verwenden Sie einen klassischen Endpunkt und eine klassische Bibliothek. Die neueren Resource Manager-APIs erfordern Azure Active Directory-Authentifizierung über einen Dienst- oder Benutzerprinzipal.

- Wenn Sie Aufrufe verwenden, auf die in der Dokumentation unter einem der folgenden Links verwiesen wird, verwenden Sie die älteren klassischen APIs.

  - [Windows.Azure.Management.Monitoring-Klassenbibliothek](/previous-versions/azure/dn510414(v=azure.100))

  - [Überwachen (klassisch) von .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations-Schnittstelle](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Warum Sie umsteigen sollten

Alle vorhandenen Funktionen für Autoskalierung und Metriken funktionieren über die neuen APIs weiterhin.  

Die Migration zu neueren APIs bietet Resource Manager-basierte Funktionen, z. B. Unterstützung einer konsistenten rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) in allen Ihren Überwachungsdiensten. Sie erhalten auch zusätzliche Funktionen für Metriken: 

- Unterstützung für Dimensionen
- Konsistente Metrikgranularität von einer Minute für alle Dienste 
- Bessere Abfragen
- Längere Datenaufbewahrung (93 Tage für Metriken im Vergleich zu 30 Tagen) 

Insgesamt bieten die auf Resource Manager basierenden Azure Monitor-APIs (wie alle anderen Dienste in Azure) bessere Leistung, Skalierbarkeit und Zuverlässigkeit. 

## <a name="what-happens-if-you-do-not-migrate"></a>Was geschieht, wenn Sie nicht migrieren?

### <a name="before-retirement"></a>Vor der Einstellung

Es gibt keine direkten Auswirkungen auf Ihre Azure-Dienste oder deren Workloads.  

### <a name="after-retirement"></a>Nach der Einstellung

Jeder Aufruf der oben aufgeführten klassischen APIs schlägt fehl und gibt Fehlermeldungen ähnlich den folgenden zurück:

Für die automatische Skalierung: *Diese API ist veraltet. Verwenden Sie das Azure-Portal, das Azure Monitor SDK, PowerShell, die CLI oder Resource Manager-Vorlagen zum Verwalten von Einstellungen für Autoskalierung*.  

Für Metriken: *Diese API ist veraltet. Verwenden Sie das Azure-Portal, das Azure Monitor SDK, PowerShell oder die CLI zum Abfragen von Metriken*.

## <a name="email-notifications"></a>E-Mail-Benachrichtigungen

Eine Einstellungsbenachrichtigung wurde an E-Mail-Adressen für die folgenden Kontorollen gesendet: 

- Konto- und Dienstadministratoren
- Co-Administratoren  

Wenden Sie sich unter MonitorClassicAPIhelp@microsoft.com an uns, wenn Sie Fragen haben.  

## <a name="references"></a>References

- [Neuere REST-APIs für Azure Monitor](/rest/api/monitor/) 
- [Neueres Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

