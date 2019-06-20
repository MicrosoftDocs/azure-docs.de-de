---
title: Unterschiedliche Methoden des Datenverkehrsroutings in Azure Front Door Service | Microsoft-Dokumentation
description: In diesem Artikel werden die verschiedenen Methoden des Datenverkehrsroutings beschrieben, die von Azure Front Door Service verwendet werden.
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
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736223"
---
# <a name="front-door-routing-methods"></a>Datenverkehrsrouting in Azure Front Door Service

Azure Front Door Service unterstützt verschiedene Datenverkehrsrouting-Methoden, um zu ermitteln, wie HTTP/HTTPS-Datenverkehr an verschiedene Dienstendpunkte weitergeleitet werden soll. Auf alle Clientanforderungen, die bei Azure Front Door Service eingehen, wird die konfigurierte Datenverkehrsrouting-Methode angewendet, um sicherzustellen, dass die Anforderungen an die Back-End-Instanz weitergeleitet werden, die am ehesten geeignet ist. 

In Azure Front Door Service stehen vier Hauptkonzepte für das Datenverkehrsrouting zur Verfügung:

* **[Wartezeit](#latency):** Mit dem latenzbasierten Routing wird sichergestellt, dass Anforderungen an diejenigen Back-Ends gesendet werden, die innerhalb eines akzeptablen Empfindlichkeitsbereichs die niedrigste Latenz aufweisen. Benutzeranforderungen werden auf diese Weise unter Berücksichtigung der Netzwerklatenz an die nächstgelegenen Back-Ends gesendet.
* **[Priorität:](#priority)** Sie können Ihren verschiedenen Back-Ends Prioritäten zuweisen, wenn Sie ein primäres Dienst-Back-End für den gesamten Datenverkehr verwenden. Außerdem können Sie zusätzliche Back-Ends bereitstellen, falls das primäre Back-End oder die Ersatz-Back-Ends nicht verfügbar sind.
* **[Gewichtet:](#weighted)** Sie können Ihren verschiedenen Back-Ends Gewichtungen zuweisen, wenn Sie Datenverkehr entweder gleichmäßig oder gemäß Gewichtungskoeffizienten auf eine Gruppe von Back-Ends verteilen möchten.
* **Sitzungsaffinität:** Sie können für Ihre Front-End-Hosts oder -Domänen Sitzungsaffinität konfigurieren, wenn nachfolgende Anforderungen eines Benutzers an dasselbe Back-End gesendet werden sollen, solange die Benutzersitzung aktiv ist und die Back-End-Instanz nach Integritätstests weiterhin als fehlerfrei bewertet wird. 

Alle Azure Front Door Service-Konfigurationen schließen die Überwachung der Back-End-Integrität und ein automatisiertes, sofortiges und globales Failover ein. Weitere Informationen finden Sie unter [Überwachen von Azure Front Door Service-Back-Ends](front-door-health-probes.md). Azure Front Door Service kann so konfiguriert werden, dass eine einzelne oder bei entsprechenden Anwendungsanforderungen mehrere Routingmethoden in Kombination verwendet werden, um eine optimale Routingtopologie zu erstellen.

## <a name = "latency"></a>Datenverkehrsrouting auf Grundlage der niedrigsten Latenzen

Die Reaktionsfähigkeit vieler Anwendungen kann verbessert werden, indem Sie Back-Ends an mindestens zwei Standorten auf der Welt bereitstellen und Datenverkehr an den Standort weiterleiten, der den Endbenutzern am nächsten liegt. Mit der Standardmethode für das Routing von Datenverkehr für Ihre Azure Front Door Service-Konfiguration werden Anforderungen an die Endbenutzer weitergeleitet, die der Azure Front Door Service-Umgebung, die die Anforderung empfangen hat, am nächstgelegenen sind. In Kombination mit der Anycastarchitektur von Azure Front Door Service wird durch diesen Ansatz gewährleistet, dass Ihre Endbenutzer je nach Standort von maximaler Leistung profitieren.

Das nächstgelegene Back-End ist nicht unbedingt dasjenige, das geografisch am nächsten liegt. Azure Front Door Service ermittelt das nächstgelegene Back-End durch die Messung der Netzwerklatenz. Weitere Informationen finden Sie unter [Routingarchitektur von Azure Front Door Service](front-door-routing-architecture.md). 

Im Folgenden wird der allgemeine Ablauf zur Ermittlung der Back-Ends dargestellt:

| Verfügbare Back-Ends | Priorität | Latenzsignal (basierend auf Integritätstests) | Weights |
|-------------| ----------- | ----------- | ----------- |
| Zunächst werden alle aktivierten Back-Ends ausgewählt, die bei einem Integritätstest den Statuscode „200 OK“ zurückgeben und daher als fehlerfrei beurteilt werden. Angenommen, von den sechs Back-Ends A, B, C, D, E und F ist C fehlerhaft und E deaktiviert. Die Liste der verfügbaren Back-Ends umfasst damit A, B, D und F.  | Als Nächstes werden von den verfügbaren Back-Ends diejenigen mit der höchsten Priorität ausgewählt. Angenommen, den Back-Ends A, B und D ist als Priorität 1 und dem Back-End F die Priorität 2 zugeordnet. Als Back-Ends werden daher A, B und D ausgewählt.| Alle Back-Ends innerhalb des Latenzbereichs (niedrigste Latenz und Latenzempfindlichkeit werden in ms angegeben) werden ausgewählt. Angenommen, die Latenzzeit für Übertragungen zwischen der Azure Front Door Service-Umgebung, in der die Anforderung eingetroffen ist, und den Back-Ends A, B und D beträgt jeweils 15 ms, 30 ms und 60 ms. Die Latenzempfindlichkeit beträgt 30 ms. Der Pool für Back-Ends mit der niedrigsten Latenz umfasst dann A und B, da D mehr als 30 ms vom nächstgelegenen Back-End, nämlich A, entfernt ist. | Abschließend führt Azure Front Door Service für den Datenverkehr der zuletzt ausgewählten Back-Ends einen Roundrobin entsprechend der festgelegten Gewichtungen durch. Angenommen, Back-End A wird eine Gewichtung von 5 und Back-End B eine Gewichtung von 8 zugeordnet. Der Datenverkehr wird dann im Verhältnis 5:8 auf die Back-Ends A und B aufgeteilt. |

>[!NOTE]
> Standardmäßig wird für die Eigenschaft der Latenzempfindlichkeit 0 ms festgelegt. Dadurch wird die Anforderung immer an das am schnellsten verfügbare Back-End weitergeleitet.


## <a name = "priority"></a>Prioritätsbasiertes Datenverkehrsrouting

Wenn Organisationen die Zuverlässigkeit ihrer Dienste gewährleisten möchten, stellen sie häufig einen oder mehrere Sicherungsdienste bereit, für den Fall, dass der primäre Dienst ausfällt. Diese Topologie wird branchenweit als Aktiv/Standby- oder Aktiv/Passiv-Topologie bezeichnet. Mithilfe der prioritätsbasierten Methode für das Datenverkehrsrouting können Azure-Kunden dieses Failovermuster problemlos implementieren.

Die Standardkonfiguration von Azure Front Door Service enthält eine Liste mit Back-Ends, denen dieselbe Priorität zugeordnet ist. Azure Front Door Service leitet dadurch Datenverkehr ausschließlich an die Back-Ends mit der höchsten Priorität (dem niedrigsten Prioritätswert) und damit an die primären Back-Ends weiter. Wenn diese nicht verfügbar sind, wird der Datenverkehr an die sekundären Back-Ends (diejenigen, denen der zweitniedrigste Prioritätswert zugeordnet wurde) weitergeleitet. Wenn weder die primärem noch sekundären Back-Ends verfügbar sind, wird der Datenverkehr an das dritte Back-End gesendet usw. Die Verfügbarkeit des Back-Ends basiert auf dem konfigurierten Status (aktiviert oder deaktiviert) und dem Back-End-Integritätsstatus, der regelmäßig durch Integritätstests ermittelt wird.

### <a name="configuring-priority-for-backends"></a>Konfigurieren der Priorität für Back-Ends

Jedes Back-End Ihres Back-End-Pools in der Azure Front Door Service-Konfiguration verfügt über die Eigenschaft „Priority“ (Priorität), die eine Zahl zwischen 1 und 5 sein kann. Sie können die Priorität explizit für jedes Back-End mithilfe dieser Eigenschaft festlegen. Niedrigere Werte stellen eine höhere Priorität dar. Ein Prioritätswert kann für mehrere Back-Ends verwendet werden.

## <a name = "weighted"></a>Gewichtete Methode für das Datenverkehrsrouting
Bei der gewichteten Methode für das Datenverkehrsrouting wird der Datenverkehr gleichmäßig verteilt oder eine vordefinierte Gewichtung verwendet.

Bei der Methode für Datenverkehrsrouting auf Grundlage von Gewichtungen weisen Sie in der Azure Front Door Service-Konfiguration Ihres Back-End-Pools jedem Back-End eine Gewichtung zu. Die Gewichtung ist eine Ganzzahl zwischen 1 und 1000. Als Standardgewichtung für den Parameter wird 50 verwendet.

Für Back-Ends, die sich innerhalb des festgelegten zulässigen Empfindlichkeitsbereichs für die Latenz befinden und daher verfügbar sind, wird der Datenverkehr mit dem Roundrobinverfahren entsprechend den angegebenen Gewichtungen verteilt. Wenn für die Latenzempfindlichkeit ein Wert von 0 Millisekunden festgelegt ist, wird diese Eigenschaft nur dann berücksichtigt, wenn die Netzwerklatenz für zwei Back-Ends gleich ist. 

Durch die gewichtete Methode werden einige nützliche Szenarios ermöglicht:

* **Allmähliches Anwendungsupgrade**: Legen Sie für den Datenverkehr einen Prozentwert fest, damit nur ein Teil des Datenverkehrs an ein neues Back-End weitergeleitet wird, und erhöhen Sie den Datenverkehr dann im Lauf der Zeit allmählich, bis dessen Umfang dem anderer Back-Ends entspricht.
* **Anwendungsmigration zu Azure**: Erstellen Sie einen Back-End-Pool mit Azure-Endpunkten und externen Endpunkten. Passen Sie dabei die Gewichtung der Back-Ends so an, dass neue Back-Ends priorisiert werden. Sie können diese Änderungen schrittweise einführen, in dem Sie zuerst neue Back-Ends deaktivieren, ihnen anschließend die niedrigsten Gewichtungen zuweisen und die Werte dann langsam erhöhen, bis die Back-Ends den größten Teil des Datenverkehrs übernehmen. Abschließend deaktivieren Sie die weniger bevorzugten Back-Ends und entfernen diese aus dem Pool.  
* **Cloudbursting für zusätzliche Kapazität**: Erweitern Sie eine lokale Bereitstellung schnell in die Cloud, indem Sie sie hinter Front Door zur Verfügung stellen. Wenn Sie zusätzliche Kapazität in der Cloud benötigen, können Sie weitere Back-Ends hinzufügen oder aktivieren und dabei angeben, welcher Teil des Datenverkehrs an das jeweilige Back-End gesendet wird.

## <a name = "affinity"></a>Sitzungsaffinität
Ohne Sitzungsaffinität leitet Azure Front Door Service auf Grundlage der Lastenausgleichskonfiguration Anforderungen eines Clients an unterschiedliche Back-Ends weiter. Dies gilt insbesondere dann, wenn sich die Latenzen für unterschiedliche Back-Ends ändern oder unterschiedliche Anforderungen eines Benutzers bei einer anderen Azure Front Door Service-Umgebung eintreffen. In einigen zustandsbehafteten Anwendungen oder in bestimmten anderen Szenarios wird jedoch bevorzugt, dass nachfolgende Anforderungen eines Benutzers immer an demselben Back-End eintreffen, das die ursprüngliche Anforderung verarbeitet hat. Die cookiebasierte Sitzungsaffinität ist ein hilfreiches Feature, mit dem eine Benutzersitzung auf demselben Back-End fortgesetzt werden kann. Mithilfe von Cookies, die von Azure Front Door Service verwaltet werden, kann nachfolgender Datenverkehr einer Benutzersitzung an dasselbe Back-End weitergeleitet und verarbeitet werden, solange die Integrität des Back-Ends gewährleistet und die Benutzersitzung aktiv ist. 

Sitzungsaffinität kann auf einer Front-End-Hostebene für alle konfigurierten Domänen (oder untergeordneten Domänen) festgelegt werden. Nach der Aktivierung fügt Azure Front Door Service der Sitzung des Benutzers ein Cookie hinzu. Mithilfe der cookiebasierten Sitzungsaffinität kann Azure Front Door Service verschiedene Benutzer auch dann identifizieren, wenn für diese die gleiche IP-Adresse verwendet wird. Dadurch kann der Datenverkehr wiederum gleichmäßiger zwischen Ihren unterschiedlichen Back-Ends verteilt werden.

Die Lebensdauer des Cookies ist identisch mit derjenigen der Benutzersitzung, da Azure Front Door Service derzeit nur Sitzungscookies unterstützt. 

> [!NOTE]
> Öffentlichen Proxys können die Sitzungsaffinität beeinträchtigen. Grund dafür ist, dass Front Door zum Einrichten einer Sitzung ein Sitzungsaffinitätscookie zur Antwort hinzufügen muss. Dies ist jedoch nicht möglich, wenn die Antwort zwischengespeichert werden kann, da dies die Cookies anderer Clients, die die gleiche Ressource anfordern, beeinträchtigen würde. Um dies zu verhindern, wird die Sitzungsaffinität **nicht** hergestellt, wenn das Back-End bei einem Versuch eine zwischenspeicherbare Antwort sendet. Wenn die Sitzung bereits eingerichtet ist, spielt es keine Rolle, ob die Antwort aus dem Back-End zwischengespeichert werden kann.
> Die Sitzungsaffinität wird unter den folgenden Umständen hergestellt, **sofern** die Antwort einen HTTP 304-Statuscode enthält:
> - Die Antwort hat spezifische Werte für den ```Cache-Control```-Header, die ein Zwischenspeichern verhindern, wie z.B. „private“ oder „no-store“.
> - Die Antwort enthält einen noch nicht abgelaufenen ```Authorization```-Header.
> - Die Antwort enthält einen HTTP 302-Statuscode.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Azure Front Door Service-Konfiguration](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
