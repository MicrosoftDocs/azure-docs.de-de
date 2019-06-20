---
title: Back-Ends und Back-End-Pools in Azure Front Door Service | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, welche Back-Ends und Back-End-Pools in der Front Door-Konfiguration enthalten sind.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 543e237a4a8390a8ebf74d0eb2a1f4be41dcd911
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193712"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-Ends und Back-End-Pools in Azure Front Door Service
In diesem Artikel werden die Konzepte für die Zuordnung Ihrer App-Bereitstellung zu Azure Front Door Service beschrieben. Darüber hinaus werden die verschiedenen Begriffe erläutert, die bei der Front Door-Konfiguration rund um App-Back-Ends verwendet werden.

## <a name="backends"></a>Back-Ends
Ein Back-End entspricht einer App-Bereitstellungsinstanz in einer Region. Front Door Service unterstützt sowohl Azure- als auch Nicht-Azure-Back-Ends. Daher ist die Region nicht auf Azure-Regionen beschränkt. Die Instanz kann auch Ihr lokales Rechenzentrum oder eine App-Instanz in einer anderen Cloud sein.

Front Door Service-Back-Ends verweisen auf den Hostnamen oder die öffentliche IP-Adresse Ihrer App, die Clientanforderungen verarbeiten kann. Back-Ends sollten nicht mit einer Datenbankebene, Speicherebene usw. verwechselt werden. Back-Ends sind als öffentlicher Endpunkt Ihres App-Back-Ends zu betrachten. Wenn Sie dem Front Door-Back-End-Pool ein Back-End hinzufügen, müssen Sie auch Folgendes hinzufügen:

- **Back-End-Hosttyp**: Der Typ der Ressource, die hinzugefügt werden soll. Front Door Service unterstützt die automatische Erkennung Ihrer App-Back-Ends, wenn diese aus dem App Service, Cloud-Dienst oder Speicher stammen. Wenn Sie eine andere Ressource in Azure oder sogar ein Nicht-Azure-Back-End hinzufügen möchten, wählen Sie **Benutzerdefinierter Host** aus.

    >[!IMPORTANT]
    >Bei der Konfiguration überprüfen die APIs nicht, ob aus Front Door-Umgebungen auf das Back-End zugegriffen werden kann. Stellen Sie daher sicher, dass Front Door Ihr Back-End erreichen kann.

- **Abonnement und Back-End-Hostname**: Wenn Sie als Back-End-Hosttyp nicht **Benutzerdefinierter Host** ausgewählt haben, müssen Sie Ihr Back-End auswählen. Wählen Sie dazu das entsprechende Abonnement und den entsprechenden Back-End-Hostnamen auf der Benutzeroberfläche aus.

- **Back-End-Hostheader**: Der Hostheaderwert, der für jede Anforderung an das Back-End gesendet wird. Weitere Informationen finden Sie unter [Back-End-Hostheader](#hostheader).

- **Priorität**. Weisen Sie Ihren verschiedenen Back-Ends Prioritäten zu, wenn Sie ein primäres Dienst-Back-End für den gesamten Datenverkehr verwenden möchten. Stellen Sie außerdem Sicherungen bereit, wenn das primäre Back-End oder die Sicherungs-Back-Ends nicht verfügbar sind. Weitere Informationen finden Sie unter [Priorität](front-door-routing-methods.md#priority).

- **Gewichtung**: Weisen Sie Ihren verschiedenen Back-Ends Gewichtungen zu, um den Datenverkehr entweder gleichmäßig oder anhand von Gewichtungskoeffizienten auf eine Gruppe von Back-Ends zu verteilen. Weitere Informationen finden Sie unter [Gewichtung](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Back-End-Hostheader

Von Front Door an ein Back-End weitergeleitete Anforderungen enthalten ein Hostheaderfeld, mit dem das Back-End die Zielressource abruft. Der Wert für dieses Feld stammt in der Regel aus dem Back-End-URI und gibt Host und Port an.

Eine Anforderung für „www\.contoso.com“ weist beispielsweise den Hostheader „www\.contoso.com“ auf. Wenn Sie Ihr Back-End im Azure-Portal konfigurieren, wird standardmäßig der Hostname des Back-Ends als Wert für dieses Feld verwendet. Wenn Ihr Back-End den Namen „contoso-westus.azurewebsites.net“ hat, wird im Azure-Portal automatisch der Wert „contoso-westus.azurewebsites.net“ als Hostheader des Back-Ends eingetragen. Wenn Sie jedoch Azure Resource Manager-Vorlagen oder eine andere Methode verwenden und dieses Feld nicht explizit festlegen, sendet Front Door Service den Hostnamen für die eingehende Anforderung als Wert für den Hostheader. Ist die Anforderung beispielsweise für „www\.contoso.com“ erfolgt, und das Back-End ist „contoso-westus.azurewebsites.net“, wobei das Feld für den Header leer ist, legt Front Door Service den Hostheader als „www\.contoso.com“ fest.

Bei den meisten App-Back-Ends (Azure Web-Apps, Blob Storage und Cloud Services) muss der Hostheader der Domäne des Back-Ends entsprechen. Der Front-End-Host, der die Weiterleitung an Ihr Back-End vornimmt, hat jedoch einen anderen Hostnamen, z.B. „www\.contoso.azurefd.net“.

Wenn der Hostheader für Ihr Back-End dem Back-End-Hostnamen entsprechen muss, müssen Sie sicherstellen, dass der Back-End-Hostheader den Back-End-Hostnamen enthält.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurieren des Back-End-Hostheaders für das Back-End

So konfigurieren Sie im Abschnitt „Back-End-Pools“ das Feld **Back-End-Hostheader** für ein Back-End

1. Öffnen Sie die Front Door-Ressource, und wählen Sie den Back-End-Pool mit dem zu konfigurierenden Back-End aus.

2. Fügen Sie ein Back-End hinzu, sofern noch keines hinzugefügt wurde, oder bearbeiten Sie ein vorhandenes Back-End.

3. Legen Sie das Feld „Back-End-Hostheader“ auf einen benutzerdefinierten Wert fest, oder lassen Sie es leer. Der Hostname für die eingehende Anforderung wird als Hostheaderwert verwendet werden.

## <a name="backend-pools"></a>Back-End-Pools
Ein Back-End-Pool in Front Door Service bezieht sich auf eine Gruppe von Back-Ends, die ähnlichen Datenverkehr für ihre App empfangen. Mit anderen Worten: Es handelt sich um eine logische Gruppierung Ihrer weltweiten App-Instanzen, die den gleichen Datenverkehr empfangen und mit dem erwarteten Verhalten reagieren. Diese Back-Ends werden regionsübergreifend oder innerhalb derselben Region bereitgestellt. Alle Back-Ends können im Aktiv/Aktiv-Bereitstellungsmodus oder in einer sogenannten Aktiv/Passiv-Konfiguration bereitgestellt werden.

Ein Back-End-Pool definiert, wie die verschiedenen Back-Ends über Integritätstests ausgewertet werden sollen. Er definiert ebenso, wie der Lastenausgleich zwischen den Back-Ends erfolgen soll.

### <a name="health-probes"></a>Integritätstests
Front Door Service sendet regelmäßige HTTP/HTTPS-Testanforderungen an jedes konfigurierte Back-End. Anhand der Testanforderungen werden Näherung und Integrität jedes Back-Ends für den Lastenausgleich der Endbenutzeranforderungen bestimmt. Mit den Integritätstesteinstellungen für einen Back-End-Pool wird festgelegt, wie der Integritätsstatus Ihrer App-Back-Ends abgefragt wird. Die folgenden Einstellungen sind für die Konfiguration des Lastenausgleichs verfügbar:

- **Pfad**: Die URL, die für Testanforderungen für alle Back-Ends im Back-End-Pool verwendet wird. Beispiel: Wenn ein Back-End auf „contoso-westus.azurewebsites.net“ und der Pfad auf „/probe/test.aspx“ festgelegt ist, gehen Front Door Service-Umgebungen davon aus, dass das Protokoll auf HTTP festgelegt ist und senden Integritätstestanforderungen an „http\://contoso-westus.azurewebsites.net/probe/test.aspx“.

- **Protokoll**. Legt fest, ob die Integritätstestanforderungen von Front Door Service an Ihre Back-Ends über das HTTP-Protokoll oder das HTTPS-Protokoll gesendet werden.

- **Intervall (Sekunden)** : Definiert die Häufigkeit der Integritätstests für Ihre Back-Ends bzw. die Intervalle, in denen die einzelnen Front Door-Umgebungen einen Test senden.

    >[!NOTE]
    >Für schnellere Failover sollten Sie das Intervall auf einen niedrigeren Wert festlegen. Je niedriger der Wert, desto größer ist jedoch das Integritätstestvolumen, das Ihre Back-Ends empfangen. Wenn beispielsweise bei 90 Front Door-Umgebungen bzw. globalen POPs das Intervall auf 30 Sekunden festgelegt ist, empfängt jedes Back-End etwa 3 bis 5 Testanforderungen pro Sekunde.

Weitere Informationen finden Sie unter [Integritätstests](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Einstellungen für den Lastenausgleich
Anhand der Einstellungen für den Lastenausgleich für den Back-End-Pool wird definiert, wie Integritätstests ausgewertet werden. Diese Einstellungen bestimmen, ob das Back-End fehlerfrei oder fehlerhaft ist. Überprüft wird auch, wie der Lastenausgleich für den Datenverkehr zwischen den verschiedenen Back-Ends im Back-End-Pool erfolgen soll. Die folgenden Einstellungen sind für die Konfiguration des Lastenausgleichs verfügbar:

- **Stichprobengröße**: Gibt an, wie viele Stichproben von Integritätstests bei der Auswertung der Integrität der Back-Ends zu berücksichtigen sind.

- **Erfolgreiche Stichprobengröße**: Definiert die zuvor erwähnte Stichprobengröße, d.h. die Anzahl der erfolgreichen Stichproben, die erforderlich sind, um das Back-End als fehlerfrei zu bezeichnen. Angenommen, das Intervall für Front Door-Integritätstests ist auf 30 Sekunden, die Stichprobengröße auf 5 und die erfolgreiche Stichprobengröße auf 3 festgelegt. Jedes Mal, wenn Integritätstests für Ihr Back-End ausgewertet werden, werden die letzten fünf Stichproben innerhalb von 150 Sekunden (5 x 30) betrachtet. Mindestens drei erfolgreiche Stichproben sind erforderlich, um Ihr Back-End als fehlerfrei zu deklarieren.

- **Wartezeitenaktivität (zusätzliche Wartezeit)** : Definiert, ob Front Door die Anforderung an Back-Ends innerhalb des Aktivitätsbereichs der Wartezeitmessung senden oder die Anforderung an das nächstgelegene Back-End weiterleiten soll.

Weitere Informationen finden Sie unter [Datenverkehrsrouting auf Grundlage der niedrigsten Latenzen](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Front Door-Instanz](quickstart-create-front-door.md)
- [Übersicht über die Routingarchitektur](front-door-routing-architecture.md)