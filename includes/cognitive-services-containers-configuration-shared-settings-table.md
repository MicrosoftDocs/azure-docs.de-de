---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116728"
---
Dieser Container hat die folgenden Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Wird zum Nachverfolgen von Abrechnungsinformationen verwendet.|
|Nein |[ApplicationInsights](#applicationinsights-setting)|Ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](https://docs.microsoft.com/azure/application-insights)-Telemetriedaten in Ihrem Container.|
|Ja|[Abrechnung](#billing-configuration-setting)|Gibt den Endpunkt-URI der Dienstressource in Azure an.|
|Ja|[Eula](#eula-setting)| Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.|
|Nein |[Fluentd](#fluentd-settings)|Schreibt Protokoll- und optional auch Metrikdaten auf einen Fluentd-Server.|
|Nein |HTTP-Proxy|Konfigurieren Sie einen HTTP-Proxy für ausgehende Anforderungen.|
|Nein |[Protokollierung](#logging-settings)|Bietet Unterstützung für die ASP.NET Core-Protokollierung für Ihren Container. |
|Nein |[Mounts](#mount-settings)|Liest und schreibt Daten vom Hostcomputer in den Container und umgekehrt.|
