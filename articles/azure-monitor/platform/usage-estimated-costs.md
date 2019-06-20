---
title: Überwachen der Nutzung und geschätzten Kosten in Azure Monitor
description: Übersicht über die Verwendung der Azure Monitor-Seite für die Nutzung und geschätzten Kosten
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60453835"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Überwachen der Nutzung und geschätzten Kosten in Azure Monitor

> [!NOTE]
> In diesem Artikel wird beschrieben, wie die Nutzung und geschätzten Kosten über mehrere Azure-Überwachungsfeatures hinweg für unterschiedliche Preismodelle angezeigt werden.  Entsprechende Informationen finden Sie in den folgenden Artikeln.
> - [Verwalten der Kosten durch die Steuerung der Datenmenge und -aufbewahrung in Log Analytics](manage-cost-storage.md) beschreibt, wie Sie Ihre Kosten durch Ändern der Datenaufbewahrungsdauer steuern.
> - [Analysieren der Datennutzung in Log Analytics](../../azure-monitor/platform/data-usage.md) beschreibt, wie Sie Ihre Datennutzung analysieren und Warnungen dazu ausgeben.
> - [Verwalten von Preisen und Datenvolumen in Application Insights](../../azure-monitor/app/pricing.md) beschreibt, wie Sie die Datennutzung in Application Insights analysieren.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die Seite **Nutzung und geschätzte Kosten** im Monitor-Hub des Azure-Portals erläutert die Nutzung wichtiger Überwachungsfeatures wie [Metriken, Warnungen, Benachrichtigungen](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) und [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Für Kunden mit Tarifen, die vor April 2018 verfügbar waren, umfasst dies auch die Log Analytics-Nutzung, die über das Insights- und Analytics-Angebot erworben wurde.

Auf dieser Seite können Benutzer ihre Ressourcennutzung der letzten 31 Tage, aggregiert pro Abonnement, einsehen. Detailansichten zeigen die Nutzungstrends über den Zeitraum von 31 Tagen. Für diese Schätzung müssen viele Daten zusammengefasst werden, sodass Sie beim Laden der Seite etwas Geduld aufbringen müssen.

Dieses Beispiel zeigt die Nutzungsüberwachung und eine Schätzung der anfallenden Kosten:

![Screenshot: „Nutzung und geschätzte Kosten“ im Portal](./media/usage-estimated-costs/001.png)

Wählen Sie in der Spalte „Monatliche Nutzung“ den Link, um ein Diagramm mit den Nutzungstrends für die letzten 31 Tage anzuzeigen:

![Screenshot des Balkendiagramms: „Pro Knoten enthalten“](./media/usage-estimated-costs/002.png)

Hier ist eine andere ähnliche Verwendung und Kostenzusammenfassung. Dieses Beispiel zeigt ein Abonnement aus dem neuen nutzungsbasierten Preismodell von April 2018. Beachten Sie, dass keine knotenbasierte Abrechnung vorhanden ist. Die Informationen zur Datenerfassung und -aufbewahrung für Log Analytics und Application Insights werden jetzt über eine neue gemeinsame Anzeige angegeben.

![Screenshot: „Nutzung und geschätzte Kosten“ im Portal – April 2018](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Neues Preismodell

Im April 2018 wurde ein [neues Preismodell für die Überwachung eingeführt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Es handelt sich hierbei um für die Cloud optimierte und verbrauchsbasierte Preise. Sie zahlen ausschließlich nutzungsabhängig und gehen keine knotenbasierten Verpflichtungen ein. Die Details des neuen Preismodells sind jetzt für [Metriken, Warnungen, Benachrichtigungen](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) und [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) verfügbar. 

Für Kunden, die das Onboarding für Log Analytics oder Application Insights nach dem 2. April 2018 durchführen, ist das neue Preismodell die einzige Option. Für Kunden, die diese Dienste bereits nutzen, ist die Umstellung auf das neue Preismodell optional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Auswirkungen des neuen Preismodells
Das neue Preismodell hat für jeden Kunden je nach Nutzungsmuster der Überwachung unterschiedliche Auswirkungen. Für Kunden, die Log Analytics oder Application Insights vor dem 2. April 2018 verwendet haben, wird auf der Seite **Nutzung und geschätzte Kosten** in Azure Monitor alle Änderungen der Kosten geschätzt, wenn sie auf das neue Preismodell umsteigen. Sie bietet die Möglichkeit, ein Abonnement in das neue Modell zu übernehmen. Für die meisten Kunden wird das neue Preismodell von Vorteil sein. Bei Kunden mit besonders hohem Datennutzungsverhalten oder in Regionen mit höheren Kosten ist dies möglicherweise nicht der Fall.

Um eine Kostenschätzung für die Abonnements zu sehen, die Sie auf der Seite **Nutzung und geschätzte Kosten** ausgewählt haben, wählen Sie das blaue Banner oben auf der Seite aus. Am besten ist es, wenn Sie dies für nur ein Abonnement auf einmal ausführen, denn das ist die Grundlage, auf der das neue Preismodell übernommen werden kann.

![Screenshot: Überwachen der Nutzung und der geschätzten Kosten im neuen Preismodell](./media/usage-estimated-costs/004.png)

Die neue Seite zeigt eine ähnliche Version der vorherigen Seite mit einem grünen Banner:

![Screenshot: Überwachen der Nutzung und der geschätzten Kosten im aktuellen Preismodell](./media/usage-estimated-costs/005.png)

Die Seite zeigt auch andere Anzeigen, die für das neue Preismodell verwendet werden. Diese Liste ist ein Beispiel:

- Insight & Analytics\Überschreitung pro Knoten
- Insight & Analytics\Pro Knoten enthalten
- Application Insights\Basic-Überschreitungsdaten
- Application Insights\Enthaltene Daten

Das neue Preismodell enthält keine knotenbasierten Datenzuordnungen. Daher werden diese Anzeigen für die Datenerfassung zu einer neuen gemeinsamen Anzeige für die Datenerfassung namens **Shared Services\Datenerfassung** zusammengefasst. 

Es gibt eine weitere Änderung der in Log Analytics oder Application Insights erfassten Daten in Regionen mit höheren Kosten. Daten für diese hochpreisigeren Regionen werden mit den neuen regionalen Verbrauchseinheiten angezeigt. Ein Beispiel ist **Datenerfassung (USA, Westen-Mitte)** .

> [!NOTE]
> Die Kontoebenenberechtigungen pro Knoten für das OMS-Abonnement (Operations Management Suite) werden nicht in die geschätzten Kosten pro Abonnement einbezogen. Wenden Sie sich in diesem Fall an Ihren Kontobeauftragten, um ausführlichere Informationen zum neuen Preismodell zu erhalten.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Neues Preismodell und Berechtigungen für Operations Management Suite-Abonnement

Kunden, die Microsoft Operations Management Suite E1 und E2 erworben haben, verfügen über Datenerfassungsberechtigungen pro Knoten für [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) und [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Um diese Berechtigungen für Log Analytics-Arbeitsbereiche oder Application Insights-Ressourcen in einem bestimmten Abonnement zu erhalten, muss folgende Voraussetzung erfüllt sein: 

- Als Preismodell für dieses Abonnement muss das vor April 2018 gültige Modell weiterverwendet werden.
- Log Analytics-Arbeitsbereiche sollten den Tarif „Pro-Knoten (OMS)“ verwenden.
- Application Insights-Ressourcen sollten den „Enterprise“-Preisplan verwenden.

Je nach Anzahl von Knoten einer Suite, die von Ihrer Organisation erworben wurde, könnte die Umstellung von Abonnements auf das neue Preismodell trotzdem vorteilhaft sein. Dieser Schritt bedarf aber sorgfältiger Überlegung. Im Allgemeinen ist es empfehlenswert, einfach wie oben beschrieben das vor April 2018 gültige Modell beizubehalten.

> [!WARNING]
> Wenn Ihre Organisation Microsoft Operations Management Suite E1 und E2 erworben hat, sollten Sie normalerweise für Ihre Abonnements das vor April 2018 gültige Preismodell beibehalten. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Änderungen bei der Umstellung auf das neue Preismodell

Das neue Preismodell reduziert Log Analytics- und Application Insights-Preisoptionen auf nur einen einzigen Tarif (oder Plan). Die Umstellung eines Abonnements auf das neue Preismodell hat folgende Konsequenzen:

- Der Tarif für jede Log Analytics-Instanz wird in einen neuen Pro-GB-Tarif (im Azure Resource Manager „pergb2018“ genannt) umgewandelt.
- Alle Application Insights-Ressourcen des Enterprise-Tarifs werden in den Basic-Tarif geändert.

Die Kostenschätzung zeigt die Auswirkungen dieser Änderungen.

> [!WARNING]
> Beachten Sie den folgenden wichtigen Hinweis, wenn Sie Azure Resource Manager oder PowerShell verwenden, um [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) oder [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) in einem Abonnement bereitzustellen, das Sie auf das neue Preismodell umgestellt haben. Wenn Sie einen anderen Tarif/Plan als „pergb2018“ für Log Analytics oder „Basic“ für Application Insights angeben, wird die Bereitstellung nicht wegen Angabe eines ungültigen Tarifs/Plans erfolglos sein, **aber es wird nur der gültige Tarif/Plan verwendet**. (Dies gilt nicht für den Log Analytics-Tarif „Free“, da dabei eine Meldung über einen ungültige Tarif generiert wird).
>

## <a name="moving-to-the-new-pricing-model"></a>Durchführen der Umstellung auf das neue Preismodell

Wenn Sie entschieden haben, das neue Preismodell für ein bestimmtes Abonnement zu übernehmen, navigieren Sie zu jeder Application Insights-Ressource, öffnen Sie **Nutzung und geschätzte Kosten**, stellen Sie sicher, dass der Basic-Tarif gewählt ist, und navigieren Sie dann zu jedem Log Analytics-Arbeitsbereich, öffnen Sie die **Tarif**-Seite, und ändern Sie den Tarif in **Pro GB (2018)** . 

> [!NOTE]
> Die Anforderung, dass alle Application Insights-Ressourcen und Log Analytics-Arbeitsbereiche innerhalb eines bestimmten Abonnements das neueste Preismodell übernehmen, wurde jetzt aufgehoben, um die Flexibilität zu steigern und die Konfiguration zu vereinfachen. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatisieren der Umstellung auf das neue Preismodell

Wie bereits erwähnt, ist es nicht mehr notwendig, alle Überwachungsressourcen in einem Abonnement zur gleichen Zeit in das neue Preismodell zu verschieben, und damit hat die ``migratetonewpricingmodel``-Aktion keine Auswirkung mehr. Jetzt können Sie Application Insights-Ressourcen und Log Analytics-Arbeitsbereiche getrennt in die neuesten Tarife verschieben.  

Die Automatisierung dieser Änderung ist für Application Insights unter Verwendung von [Set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) mit ``-PricingPlan "Basic"`` und für Log Analytics unter Verwendung von [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) mit ``-sku "PerGB2018"`` dokumentiert. 

