---
title: Zentrales Hochskalieren von Features und Kapazitäten – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine App in Azure App Service zentral hochskalieren, um Kapazität und Features hinzuzufügen.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832107"
---
# <a name="scale-up-an-app-in-azure"></a>Zentrales Hochskalieren einer App in Azure

> [!NOTE]
> Der neue Tarif **PremiumV2** bietet Ihnen schnellere CPUs und SSD-Speicher und verdoppelt das Verhältnis von Speicher zu Kern im Vergleich mit vorhandenen Tarifen. Mit diesem Leistungsvorteil können Sie Geld sparen, da Apps in weniger Instanzen ausgeführt werden. Informationen zum zentralen Hochskalieren auf den Tarif **PremiumV2** finden Sie unter [Konfigurieren der PremiumV2-Ebene für App Service](app-service-configure-premium-tier.md).
>

In diesem Artikel wird die Vorgehensweise beim Skalieren Ihrer App in Azure App Service beschrieben. Es gibt zwei Workflows für die Skalierung: zentrales Hochskalieren und horizontales Hochskalieren. Dieser Artikel befasst sich mit dem Workflow zum zentralen Hochskalieren.

* [Zentrales Hochskalieren](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Bei dieser Methode erhalten Sie mehr CPUs, mehr Arbeitsspeicher und mehr Speicherplatz sowie zusätzliche Features wie dedizierte virtuelle Computer, benutzerdefinierte Domänen und Zertifikate, Stagingslots, automatische Skalierung und mehr. Zum zentralen Hochskalieren muss der Tarif des App Service-Plans geändert werden, zu dem die App gehört.
* [Horizontales Skalieren](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Bei dieser Methode wird die Anzahl von VM-Instanzen erhöht, die Ihre App ausführen.
  Die Instanzenanzahl kann je nach Tarif auf bis zu 20 erhöht werden. [App Service-Umgebungen](environment/intro.md) im Tarif **Isolated** erhöhen die Anzahl der Instanzen beim horizontalen Hochskalieren weiter auf 100. Weitere Informationen zum horizontalen Hochskalieren finden Sie unter [Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Manuelles oder automatisches Skalieren der Instanzanzahl). Dort erfahren Sie, wie Sie die automatische Skalierung verwenden, d.h. das automatische Skalieren der Instanzenzahl basierend auf vordefinierten Regeln und Zeitplänen.

Diese Skalierungseinstellungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Apps im [App Service-Plan](../app-service/overview-hosting-plans.md)aus.
Dazu müssen Sie weder Ihren Code ändern noch Ihre Anwendung erneut bereitstellen.

Informationen zur Preisgestaltung und zu den Funktionen der einzelnen App Service-Pläne finden Sie unter [App Service Preise](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Bevor Sie einen App Service-Plan aus dem Tarif **Free** entfernen, müssen Sie zunächst die [Ausgabenlimits](https://azure.microsoft.com/pricing/spending-limits/) für Ihr Azure-Abonnement aufheben. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Zentrales Hochskalieren Ihres Tarifs
1. Öffnen Sie das [Azure-Portal][portal] in Ihrem Browser.
2. Klicken Sie auf der Seite Ihrer App Service-App auf **Alle Einstellungen** und anschließend auf **Zentral hochskalieren**.
   
    ![Navigieren Sie, um Ihre Azure-App zentral hochzuskalieren.][ChooseWHP]
3. Wählen Sie Ihren Tarif aus, und klicken Sie auf **Übernehmen**.
   
    Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, nachdem der Vorgang abgeschlossen wurde.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skalieren zugehöriger Ressourcen
Wenn Ihre App von anderen Diensten abhängt, beispielsweise von Azure SQL-Datenbank oder Azure Storage, können Sie diese Ressourcen gesondert zentral hochskalieren. Diese Ressourcen werden vom App Service-Plan nicht verwaltet.

1. Klicken Sie in **Grundlegende Informationen** auf den Link **Ressourcengruppe**.
   
    ![Zentrales Hochskalieren der zugehörigen Ressourcen Ihrer Azure-App](./media/web-sites-scale/RGEssentialsLink.png)
2. Klicken Sie im Bereich **Zusammenfassung** der Seite **Ressourcengruppe** auf eine Ressource, die Sie skalieren möchten. Der folgende Screenshot zeigt eine SQL-Datenbank-Ressource und eine Azure Storage-Ressource.
   
    ![Navigieren zur Seite „Ressourcengruppe“, um Ihre Azure-App zentral hochzuskalieren](./media/web-sites-scale/ResourceGroup.png)
3. Klicken Sie für eine SQL-Datenbank-Ressource auf **Einstellungen** > **Tarif**, um den Tarif zu skalieren.
   
    ![Zentrales Hochskalieren des SQL-Datenbank-Back-Ends für Ihre Azure-App](./media/web-sites-scale/ScaleDatabase.png)
   
    Sie können auch die [Georeplikation](../sql-database/sql-database-geo-replication-overview.md) für Ihre SQL-Datenbankinstanz aktivieren.
   
    Klicken Sie für eine Azure Storage-Ressource auf **Einstellungen** > **Konfiguration**, um Ihre Speicheroptionen zentral hochzuskalieren.
   
    ![Zentrales Hochskalieren des Azure Storage-Kontos, das von Ihrer Azure-App verwendet wird](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Vergleichen von Tarifen
Ausführliche Informationen, z.B. VM-Größen für jeden Tarif, finden Sie unter [App Service-Preisdetails](https://azure.microsoft.com/pricing/details/web-sites/).
Eine Tabelle der Dienstbeschränkungen, Kontingente und Einschränkungen sowie unterstützte Features in jedem Tarif finden Sie unter [App Service-Grenzwerte](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Nächste Schritte
* Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links:
  
    [Preisdetails zur Datenübertragung](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Supportpläne für Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/)
  
    [Preisdetails für SQL-Datenbanken](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Größen virtueller Computer und Clouddienste für Microsoft Azure][vmsizes]
  
* Informationen zu den bewährten Methoden für Azure App Service, einschließlich dem Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Bewährte Methoden: Azure App Service-Web-Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videos zum Skalieren von App Service-Apps finden Sie in den folgenden Ressourcen:
  
  * [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
