---
title: Datenverkehrsansicht im Azure Traffic Manager
description: In dieser Einführung erfahren Sie, wie die Datenverkehrsanzeige in Traffic Manager funktioniert.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: f1a3cc5ccb326d6a198895f9f459e661f7805b1e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003836"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager-Datenverkehrsansicht

Traffic Manager stellt Ihnen ein Routing auf DNS-Ebene zur Verfügung, sodass Ihre Endbenutzer auf Grundlage der Routingmethode, die Sie beim Erstellen des Profils angegeben haben, an fehlerfreie Endpunkte weitergeleitet werden. Die Datenverkehrsansicht bietet Traffic Manager einen Überblick über Ihre Benutzergruppen (auf Granularitätsebene des DNS-Resolvers) und deren Datenverkehrsmuster. Wenn Sie die Datenverkehrsansicht aktivieren, werden diese Informationen verarbeitet, um Ihnen verwertbare Erkenntnisse zu liefern. 

Die Datenverkehrsansicht ermöglicht Folgendes:
- Verstehen, wo sich Ihre Benutzergruppen befinden (bis zur Granularitätsebene eines lokalen DNS-Resolvers)
- Anzeigen des Umfangs des Datenverkehrs (als DNS-Abfragen beobachtet, die von Azure Traffic Manager bearbeitet wurden), der aus diesen Regionen stammt.
- Erhalten von Einblicken hinsichtlich der repräsentativen Wartezeit dieser Benutzer.
- Detailliertes Untersuchen der spezifischen Datenverkehrsmuster jeder Benutzergruppen in Azure-Regionen, in denen Sie Endpunkte haben 

So können Sie z.B. in der Datenverkehrsansicht nachvollziehen, welche Regionen zwar ein großes Volumen an Datenverkehr haben, aber unter längeren Wartezeiten leiden. Als Nächstes können Sie diese Informationen nutzen, um Ihre Expansion in neue Azure-Regionen zu planen, damit diese Benutzer kürzere Wartezeiten haben.

## <a name="how-traffic-view-works"></a>Funktionsweise der Datenverkehrsansicht

Grundlage der Datenverkehrsansicht ist das Vergleichen der in den letzten sieben Tagen eingegangenen Abfragen durch Traffic Manager mit einem Profil, für das dieses Feature aktiviert ist. Aus den Informationen der eingegangenen Abfragen extrahiert die Datenverkehrsansicht die Quell-IP-Adresse des DNS-Resolvers, die als Darstellung des Standorts der Benutzer verwendet wird. Diese werden anschließend mit einer Granularität auf DNS-Resolverebene gruppiert, um Regionen von Benutzergruppen zu erstellen, indem die geografischen Informationen der von Traffic Manager verwalteten IP-Adressen verwendet werden. Traffic Manager sieht sich dann die Azure-Regionen an, an die die Abfrage geleitet wurde, und erstellt eine Karte des Datenverkehrsflusses der Benutzer aus diesen Regionen.  
Im nächsten Schritt korreliert Traffic Manager die Benutzergruppenregion mit der Azure-Regionskarte mithilfe der Informationen in den Tabellen mit den Netzwerkwartezeiten. Diese werden für verschiedene Endbenutzernetzwerke verwaltet, um die durchschnittliche Wartezeit für Benutzer aus diesen Regionen zu ermitteln, wenn eine Verbindung mit Azure-Regionen hergestellt wird. Alle diese Berechnungen werden dann kombiniert, und zwar pro lokaler IP-Adressebene des DNS-Resolvers, bevor sie Ihnen angezeigt werden. Sie können die Informationen auf verschiedene Weise nutzen.

Die Häufigkeit der Datenaktualisierung in der Datenverkehrsanzeige hängt von mehreren internen Dienstvariablen ab. Allerdings werden die Daten in der Regel einmal alle 24 Stunden aktualisiert.

>[!NOTE]
>Die in der Datenverkehrsansicht beschriebene Wartezeit ist eine repräsentative Wartezeit zwischen dem Endbenutzer und den Azure-Regionen, mit denen eine Verbindung hergestellt wurde, und nicht die Wartezeit von DNS-Lookup. Die Datenverkehrsansicht liefert eine bestmögliche Schätzung der Wartezeit zwischen der lokalen DNS-Auflösung und der Azure-Region, an die die Abfrage weitergeleitet wurde. Sollten nicht genügend Daten zur Verfügung stehen, wird für die Wartezeit ein Nullwert zurückgegeben. 

## <a name="visual-overview"></a>Visuelle Übersicht

Wenn Sie zum Abschnitt **Datenverkehrsansicht** auf Ihrer Traffic Manager-Seite navigieren, sehen Sie eine geografische Karte, die mit Erkenntnissen der Datenverkehrsansicht überlagert ist. Die Karte bietet Informationen zur Benutzergruppe und den Endpunkten für Ihr Traffic Manager-Profil.

![Traffic Manager-Datenverkehrsansicht, Geografische Ansicht][1]

### <a name="user-base-information"></a>Benutzergruppeninformationen

Für die lokalen DNS-Resolver, für die Standortinformationen verfügbar sind, werden diese auf der Karte angezeigt. Die Farbe des DNS-Resolvers kennzeichnet die durchschnittliche Wartezeit für Endbenutzer, die diesen DNS-Resolver für ihre Traffic Manager-Abfragen verwendet haben.

Wenn Sie auf den Standort eines DNS-Resolvers auf der Karte zeigen, wird Folgendes eingeblendet:
- die IP-Adresse des DNS-Resolvers
- das Datenverkehrsvolumen für DNS-Abfragen aus der Sicht von Traffic Manager
- die Endpunkte, an die Datenverkehr vom DNS-Resolver weitergeleitet wurde, als eine Linie zwischen dem Endpunkt und dem DNS-Resolver 
- die durchschnittliche Wartezeit von diesem Standort bis zum Endpunkt, dargestellt als Farbe der verbindenden Linie

### <a name="endpoint-information"></a>Endpunktinformationen

Die Azure-Regionen, in denen sich die Endpunkte befinden, werden als blaue Punkte auf der Karte angezeigt. Externe Endpunkte ohne zugeordnete Azure-Region werden am oberen Rand der Karte angezeigt. Klicken Sie auf einen beliebigen Endpunkt, um die verschiedenen Standorte anzuzeigen (basierend auf dem verwendeten DNS-Resolver), von denen Datenverkehr an diesen Endpunkt weitergeleitet wurde. Die Verbindungen werden als Linie zwischen dem Endpunkt und dem Standort des DNS-Resolvers angezeigt, und die Farbe entspricht der repräsentativen Wartezeit zwischen diesen beiden. Darüber hinaus sehen Sie den Namen des Endpunkts, die Azure-Region, in der er ausgeführt wird, und das Gesamtvolumen der Anforderungen, die von diesem Traffic Manager-Profil hierhin weitergeleitet wurden.


## <a name="tabular-listing-and-raw-data-download"></a>Tabellarische Auflistung und Rohdatendownload

Sie können die Daten der Datenverkehrsansicht in einem tabellarischen Format im Azure-Portal anzeigen. Für jede Kombination aus IP-Adresse der DNS-Auflösung und Endpunkt steht ein Eintrag mit Folgendem zur Verfügung: IP-Adresse der DNS-Auflösung, Name und geografischer Standort der Azure-Region, in der sich der Endpunkt befindet (sofern vorhanden), Anforderungsvolumen im Zusammenhang mit dieser DNS-Auflösung für diesen Endpunkt und repräsentative Wartezeit für Endbenutzer, die diesen DNS verwenden (sofern verfügbar). Sie können die Daten der Datenverkehrsansicht auch als CSV-Datei herunterladen, die dann als Teil eines gewünschten Analyseworkflows verwendet werden kann.

## <a name="billing"></a>Abrechnung

Wenn Sie die Datenverkehrsansicht verwenden, erfolgt die Abrechnung anhand der Anzahl der Datenpunkte, die zur Erstellung der gezeigten Erkenntnisse verwendet wurden. Der derzeit einzige verwendete Datenpunkt sind die Abfragen, die vom Traffic Manager-Profil empfangen werden. Weitere Informationen zu den Preisen finden Sie auf der [Traffic Manager-Seite mit Preisdetails](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Häufig gestellte Fragen

* [Wozu dient die Datenverkehrsansicht?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Welche Vorteile ergeben sich für mich durch die Verwendung der Datenverkehrsansicht?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Inwiefern unterscheidet sich die Datenverkehrsansicht von den Traffic Manager-Metriken, die über Azure Monitor verfügbar sind?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Werden für die Datenverkehrsansicht EDNS-Clientsubnetzinformationen verwendet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Wie viele Tage mit Daten werden von der Datenverkehrsansicht genutzt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Wie werden externe Endpunkte von der Datenverkehrsansicht behandelt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Muss ich die Datenverkehrsansicht für jedes Profil meines Abonnements aktivieren?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Wie kann ich die Datenverkehrsansicht deaktivieren?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Wie funktioniert die Abrechnung für die Datenverkehrsansicht?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Funktionsweise von Traffic Manager](traffic-manager-overview.md)
- Informieren Sie sich über die von Traffic Manager unterstützten [Methoden für das Datenverkehrsrouting](traffic-manager-routing-methods.md) .
- Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png