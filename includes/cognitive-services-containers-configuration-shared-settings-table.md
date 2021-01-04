---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006871"
---
Der Container hat die folgenden Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Nachverfolgen von Abrechnungsinformationen|
|Nein|[ApplicationInsights](#applicationinsights-setting)|Ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](/azure/application-insights)-Telemetriedaten in Ihrem Container.|
|Ja|[Abrechnung](#billing-configuration-setting)|Gibt den Endpunkt-URI der Dienstressource in Azure an.|
|Ja|[Eula](#eula-setting)| Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.|
|Nein|[Fluentd](#fluentd-settings)|Schreibt Protokoll- und optional auch Metrikdaten auf einen Fluentd-Server.|
|Nein|HTTP-Proxy|Konfiguriert einen HTTP-Proxy für ausgehende Anforderungen.|
|Nein|[Logging](#logging-settings)|Bietet Unterstützung für die ASP.NET Core-Protokollierung für Ihren Container. |
|Nein|[Mounts](#mount-settings)|Liest und schreibt Daten vom Hostcomputer in den Container und umgekehrt.|