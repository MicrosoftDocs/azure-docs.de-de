---
title: Automatisches Skalieren in Azure mit einer benutzerdefinierten Metrik
description: Erfahren Sie, wie Sie Ihre Ressource durch eine benutzerdefinierte Metrik in Azure skalieren.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: e6423f2ce3659fd3dd738dcc8a990261bc7bf60c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334340"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Erste Schritte mit der automatischen Skalierung durch eine benutzerdefinierte Metrik in Azure
In diesem Artikel wird beschrieben, wie Sie Ihre Ressource durch eine benutzerdefinierte Metrik im Azure-Portal skalieren.

Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Erste Schritte
Dieser Artikel setzt voraus, dass Sie eine mit Application Insights konfigurierte Web-App haben. Wenn dies noch nicht geschehen ist, können Sie [Application Insights für Ihre ASP.NET-Website einrichten][1].

- Öffnen Sie das [Azure-Portal][2].
- Klicken Sie im linken Navigationsbereich auf das Azure Monitor-Symbol.
  ![Azure Monitor starten][3]
- Klicken Sie auf die Autoskalierungseinstellung, um alle Ressourcen, für die die automatische Skalierung angewendet wird, zusammen mit dem aktuellen Autoskalierungsstatus anzuzeigen. ![Ermitteln der automatischen Skalierung in Azure Monitor][4]
- Öffnen Sie in Azure Monitor das Blatt „Automatisch skalieren“, und wählen Sie eine Ressource aus, die skaliert werden soll.
  > Hinweis: Die folgenden Schritte beruhen auf einem App Service-Plan, der einer mit Application Insights konfigurierten Web-App zugeordnet ist.
- Beachten Sie, dass die aktuelle Anzahl der Instanzen für die Ressource auf dem Blatt „Skalierungseinstellung“ 1 beträgt. Klicken Sie auf „Automatische Skalierung aktivieren“.
  ![Skalierungseinstellung für die neue Web-App][5]
- Geben Sie einen Namen für die Skalierungseinstellung an, und klicken Sie dann auf „Regel hinzufügen“. Beachten Sie die Optionen für die Skalierungsregel, die auf der rechten Seite als Kontextbereich geöffnet wird. Standardmäßig wird die Option zum Skalieren der Anzahl Ihrer Instanzen auf „1“ festgelegt, wenn der CPU-Prozentsatz der Ressource 70 % überschreitet. Ändern Sie die Metrikquelle im oberen Bereich in „Application Insights“, wählen Sie in der Dropdownliste „Ressource“ die Application Insights-Ressource aus, und wählen Sie dann die benutzerdefinierte Metrik basierend auf dem zu skalierenden Inhalt.
  ![Skalieren anhand einer benutzerdefinierten Metrik][6]
- Fügen Sie ähnlich wie beim obigen Schritt eine Skalierungsregel zum horizontalen Herunterskalieren hinzu, und verringern Sie die Anzahl der Skalierungen um 1, wenn die benutzerdefinierte Metrik unterhalb eines bestimmten Schwellenwerts liegt.
  ![Skalieren basierend auf der CPU][7]
- Legen Sie die Instanzgrenzwerte fest. Wenn beispielsweise 2 bis 5 Instanzen abhängig von den Schwankungen benutzerdefinierter Metriken skaliert werden sollen, setzen Sie „Minimum“ auf „2“, „Maximum“ auf „5“ und „Standard“ auf „2“.
  > Hinweis: Falls ein Problem beim Lesen der Ressourcenmetriken vorliegt und die aktuelle Kapazität unterhalb der Standardkapazität liegt, stellen Sie zum Gewährleisten der Verfügbarkeit der Ressource sicher, dass die Autoskalierung horizontal auf den Standardwert skaliert. Wenn die aktuelle Kapazität bereits über der Standardkapazität liegt, wird die automatische Skalierung nicht horizontal herunterskaliert.
- Klicken Sie auf „Speichern“.

Herzlichen Glückwunsch. Sie haben nun Ihre Skalierungseinstellung erfolgreich für die automatische Skalierung Ihrer Web-App basierend auf einer benutzerdefinierten Metrik konfiguriert.

> Hinweis: Diese Schritte gelten auch für die ersten Schritte mit VMSS oder einer Clouddienstrolle.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

