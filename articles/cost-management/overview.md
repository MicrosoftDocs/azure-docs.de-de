---
title: Übersicht über Cloudyn in Azure | Microsoft-Dokumentation
description: Cloudyn ist eine Kostenverwaltungslösung für mehrere Clouds, die das Verwenden von Azure und anderen Cloudressourcen vereinfacht.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 8b989e5cf5b66d21c19d58f2f64fbba1927f5d69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792800"
---
# <a name="what-is-the-cloudyn-service"></a>Was ist der Cloudyn-Dienst?

Cloudyn, eine Tochtergesellschaft von Microsoft, ermöglicht Ihnen das Nachverfolgen der Cloudnutzung und der Ausgaben für Ihre Azure-Ressourcen sowie andere Cloudanbieter, einschließlich AWS und Google. Leicht verständliche Dashboardberichte unterstützen Sie bei der Kostenzuteilung sowie bei Showbacks und verbrauchsbasierten Kostenzuteilungen. Cloudyn unterstützt Sie beim Optimieren der Ausgaben für Ihre Cloud, indem nicht ausgelastete Ressourcen ermittelt werden, die Sie dann verwalten und anpassen können.

Ein Einführungsvideo finden Sie unter [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Einführung in Azure Cloudyn).

Azure Cost Management bietet ähnliche Funktionen wie Cloudyn. Azure Cost Management ist eine native Azure-Kostenverwaltungslösung. Die Lösung unterstützt Sie beim Analysieren von Kosten, beim Erstellen und Verwalten von Budgets, beim Exportieren von Daten sowie beim Prüfen von Optimierungsempfehlungen und beim Reagieren auf diese – und damit beim Sparen von Geld. Weitere Informationen finden Sie unter [Azure Cost Management](overview-cost-mgt.md).

Sehen Sie sich das [Video zu Azure Cost Management und Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) an, das Empfehlungen gibt, wann Sie auf der Grundlage Ihrer geschäftlichen Anforderungen Azure Cost Management und wann Cloudyn verwenden sollten.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Migration der Cloudyn-Features zu Azure Cost Management

Nach der Übernahme durch Microsoft werden die Cloudyn-Kostenverwaltungsfeatures aus dem Cloudyn-Portal nativ zu Azure migriert. Um die neuen Feature verwenden zu können, müssen Sie sich beim Azure-Portal anmelden und in der Liste mit den Azure-Diensten zu [Kostenverwaltung + Abrechnung](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) navigieren. Im Vergleich zu Cloudyn bietet die native Umgebung eine verbesserte Leistung sowie eine geringere Datenlatenz von etwa acht Stunden.

Die Migration wichtiger Features für Enterprise Agreement, nutzungsbasierte Zahlung und MSDN-Angebotskategorien zu Azure Cost Management ist abgeschlossen. CSP-Abonnements werden gerade zu Azure Cost Management migriert.

Sollte eine Ihrer Angebotskategorien noch nicht migriert worden sein, verwenden Sie am besten weiterhin das Cloudyn-Portal. Alle anderen können Azure Cost Management verwenden.

| Microsoft Azure-Angebote und -Features | Empfohlener Kostenverwaltungsdienst |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Unterstützung cloudübergreifender Kostenanalysen für AWS (in der Vorschauphase) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS-Empfehlungen | [Cloudyn](https://azure.cloudyn.com) |

Einige der folgenden Features sind in Cloudyn verfügbar. In Azure Cost Management stehen dagegen alle zur Verfügung.

- APIs
- Azure Compute-Empfehlungen
- Azure-Reservierungsempfehlungen
- Budgets
- Kostenanalyse
- Exportieren von Daten in ein Azure-Speicherkonto
- Kürzere Wartezeit
- Power BI-Inhaltspaket und -Connector
- Unterstützung von Ressourcentags

## <a name="monitor-usage-and-spending"></a>Überwachen der Nutzung und Ausgaben

Das Überwachen Ihrer Nutzung und Ausgaben ist für Cloudinfrastrukturen ausgesprochen wichtig, da Organisationen für die Ressourcen zahlen, die Sie im Lauf der Zeit verwenden. Wenn die Nutzung die Schwellenwerte der Vereinbarung übersteigt, kann schnell eine unerwartete Überschreitung der Kosten auftreten. Einige wichtige Faktoren können die Ad-hoc-Überwachung erschweren. Erstens wird beim Projizieren von Kosten basierend auf der durchschnittlichen Nutzung davon ausgegangen, dass Ihre Nutzung über einen bestimmten Abrechnungszeitraum konsistent bleibt. Zweitens ist es wichtig, dass Sie proaktiv Benachrichtigungen erhalten, um Ihre Ausgaben anzupassen, wenn die Kosten sich Ihrem Budget annähern oder dieses überschreiten. Zudem bieten Clouddienstanbieter nicht notwendigerweise Berichte für die Kostenprojektion im Vergleich mit den Schwellenwerten oder für einen Vergleich von Zeiträumen an.

Berichte unterstützen Sie bei der Überwachung Ihrer Ausgaben, um die Cloudnutzung, Kosten und Trends zu analysieren und nachzuverfolgen. Durch das Verwenden von Berichten für den Zeitverlauf können Sie Anomalien erkennen, die von den normalen Trends abweichen. Ineffizienzen in Ihrer Cloudbereitstellung werden in Optimierungsberichten angezeigt. Sie können Ineffizienzen auch in Kostenanalyseberichten feststellen.

## <a name="manage-costs"></a>Verwalten von Kosten

Verlaufsdaten können Sie beim Verwalten der Kosten unterstützen, wenn Sie die Nutzung und die Kosten im Lauf der Zeit analysieren, um Trends zu erkennen. Trends werden dann verwendet, um zukünftige Ausgaben vorherzusagen. Cloudyn enthält auch nützliche Berichte über geplante Kosten.

Die Kostenzuteilung verwaltet Kosten, indem Ihre Kosten basierend auf Ihrer Tagrichtlinie analysiert werden. Sie können Tags für Ihre benutzerdefinierten Konten, Ressourcen und Entitäten verwenden, um die Kostenzuteilung zu optimieren. Der Kategorie-Manager organisiert Ihre Tags, um zusätzliche Kontrolle zu ermöglichen. Sie verwenden die Kostenzuteilung ebenfalls für Showback und die verbrauchsbasierte Kostenzuteilung, um die Ressourcennutzung und die zugehörigen Kosten anzuzeigen und das Nutzungsverhalten zu beeinflussen oder diese Mandantenkunden in Rechnung zu stellen.

Die Zugriffssteuerung unterstützt Sie beim Verwalten der Kosten, indem sichergestellt wird, dass Benutzer und Teams nur auf die Daten der Kostenverwaltung zugreifen können, die sie benötigen. Sie verwenden eine Entitätsstruktur, die Benutzerverwaltung und geplante Berichte mit Empfängerlisten, um den Zugriff zuzuweisen.

Warnungen unterstützen Sie beim Verwalten der Kosten, indem Sie automatisch benachrichtigt werden, wenn ungewöhnliche Ausgaben oder eine Budgetüberschreitung auftreten. Durch Warnungen können auch andere Beteiligte automatisch über Anomalien bei den Ausgaben und das Risiko einer Budgetüberschreitung benachrichtigt werden. Verschiedene Berichte unterstützen Warnungen, die auf dem Budget und auf Kostenschwellenwerten basieren. Für Konten oder Abonnements von CSP-Partnern werden Warnungen jedoch derzeit nicht unterstützt.

## <a name="improve-efficiency"></a>Verbessern der Effizienz

Mit Cloudyn können Sie die optimale Nutzung der VMs bestimmen, VMs im Leerlauf identifizieren und VMs im Leerlauf sowie nicht angefügte Datenträger entfernen. Mithilfe der Informationen in den Berichten zu Größenempfehlungen und Ineffizienzen können Sie einen Plan erstellen, um die Größe von VMs im Leerlauf zu reduzieren oder diese zu entfernen. Für Konten oder Abonnements von CSP-Partnern werden Optimierungsberichte jedoch derzeit nicht unterstützt.

Wenn Sie für AWS reservierte Instanzen bereitgestellt haben, können Sie die Nutzung Ihrer reservierten Instanzen mit Optimierungsberichten verbessern, in denen Sie Kaufempfehlungen einsehen, ungenutzte Reservierungen ändern und Bereitstellungen planen können.


## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit Cloudyn vertraut sind, bestehen die nächsten Schritte im Registrieren Ihrer Cloudumgebung und dem Untersuchen Ihrer Daten.

- [Register an individual Azure subscription (Registrieren eines einzelnen Azure-Abonnements)](quick-register-azure-sub.md)
