---
title: Hochverfügbarkeit und Notfallwiederherstellung für Azure IoT Hub | Microsoft-Dokumentation
description: Informationen zu den Azure- und IoT Hub-Features zum Erstellen von Azure IoT-Lösungen mit hoher Verfügbarkeit und Notfallwiederherstellung.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: 7479d9a230bd28c2ed2e4c8c79ba9301028af36c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60779371"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung von IoT Hub

Als ersten Schritt zur Implementierung einer belastbaren IoT-Lösung müssen Architekten, Entwickler und Geschäftsinhaber die Betriebszeitziele für die von ihnen erstellten Lösungen definieren. Diese Ziele können in erster Linie auf Grundlage bestimmter Geschäftsziele für jedes Szenario definiert werden. In diesem Zusammenhang bietet der Artikel [Geschäftskontinuität mit Azure – technische Dokumentation](https://docs.microsoft.com/azure/architecture/resiliency/) einen allgemeinen Überblick über Geschäftskontinuität und Notfallwiederherstellung. Das Dokument [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen](https://msdn.microsoft.com/library/dn251004.aspx) enthält Architekturanleitungen zu Strategien für Azure-Anwendungen in Bezug auf Notfallwiederherstellung und Hochverfügbarkeit.

Dieser Artikel beschreibt die Funktionen für Hochverfügbarkeit und Notfallwiederherstellung, die der Dienst IoT Hub bietet. In diesem Artikel werden schwerpunktmäßig die folgenden Themen beschrieben:

- Regionale Hochverfügbarkeit
- Regionenübergreifende Notfallwiederherstellung
- Erzielen regionenübergreifender Hochverfügbarkeit

Abhängig von den Betriebszeitzielen, die Sie für Ihre IoT-Lösungen definieren, sollten Sie festlegen, welche der unten aufgeführten Optionen am besten zu Ihren Geschäftszielen passt. Die Integration einer dieser Hochverfügbarkeits-/Notfallwiederherstellungsalternativen in Ihre IoT-Lösung erfordert eine sorgfältige Bewertung der folgenden Aspekte und ihrer Wichtigkeit:

- Erforderliche Resilienz 
- Implementierungs- und Wartungsaufwand
- Wirkung des Wareneinsatzes

## <a name="intra-region-ha"></a>Regionale Hochverfügbarkeit

Der Dienst IoT Hub bietet regionale Hochverfügbarkeit, indem in fast allen Dienstschichten Redundanzen implementiert werden. Das [von IoT Hub veröffentlichte SLA](https://azure.microsoft.com/support/legal/sla/iot-hub) wird mithilfe dieser Redundanzen eingehalten. Für die Entwickler einer IoT-Lösung entsteht kein zusätzlicher Aufwand, um die Vorteile dieser Hochverfügbarkeitsfunktionen zu nutzen. Obwohl IoT Hub eine relativ hohe Betriebszeitgarantie bietet, können hier wie bei jeder verteilten Verarbeitungsplattform auch vorübergehende Fehler auftreten. Wenn Sie gerade erst mit der Migration Ihrer Lösungen von einer lokalen Lösung zur Cloud beginnen, müssen Sie sich statt der Optimierung der „durchschnittlichen Zeit zwischen Ausfällen“ auf die Optimierung der „durchschnittlichen Zeit bis zur Wiederherstellung“ konzentrieren. Mit anderen Worten, vorübergehende Fehler gelten als normal bei einem hybriden Cloudeinsatz. Entsprechende [Wiederholungsrichtlinien](iot-hub-reliability-features-in-sdks.md) müssen in die Komponenten integriert werden, die mit einer Cloudanwendung interagieren, um vorübergehende Fehler zu behandeln.

> [!NOTE]
> Einige Azure-Dienste bieten auch zusätzliche Verfügbarkeitsschichten innerhalb einer Region durch die Integration von [Verfügbarkeitszonen](../availability-zones/az-overview.md). Verfügbarkeitszonen werden derzeit von IoT Hub nicht unterstützt.

## <a name="cross-region-dr"></a>Regionenübergreifende Notfallwiederherstellung

Es kann in einigen seltenen Fällen dazu kommen, dass ein Rechenzentrum aufgrund von Stromausfällen oder anderen Fehlern beim Inventar längere Zeit ausfällt. Solche Ereignisse sind selten, bei denen die oben beschriebene regionale Hochverfügbarkeit nicht immer hilfreich ist. IoT Hub bietet mehrere Lösungen für die Wiederherstellung nach solchen längeren Ausfallzeiten. 

Die Kunden können dabei zwischen zwei Wiederherstellungsoptionen wählen: „Von Microsoft initiiertes Failover“ und „manuelles Failover“. Der grundlegende Unterschied zwischen den beiden Möglichkeiten ist, dass im ersten Fall Microsoft der Initiator ist und im zweiten der Benutzer. Das manuelle Failover bietet außerdem eine schnellere RTO (Recovery Time Objective) als das von Microsoft initiierte Failover. Die spezifische RTO der jeweiligen Option werden in den folgenden Abschnitten erläutert. Wenn eine dieser Optionen zum Ausführen eines Failovers einer IoT Hub-Instanz aus der primären Region ausgeübt wird, wird der Hub in der entsprechenden [geografisch gekoppelten Azure-Region](../best-practices-availability-paired-regions.md) voll funktionsfähig.

Beide Failoveroptionen bieten die folgende Recovery Point Objectiv (RPO):

| Datentyp | Recovery Point Objective (RPO) |
| --- | --- |
| Identitätsregistrierung |0-5 Minuten Datenverlust |
| Daten des Gerätezwillings |0-5 Minuten Datenverlust |
| C2D-Nachrichten (Cloud-to-Device)<sup>1</sup> |0-5 Minuten Datenverlust |
| Übergeordnete<sup>1</sup> und Geräteaufträge |0-5 Minuten Datenverlust |
| D2C-Nachrichten |Alle ungelesenen Nachrichten gehen verloren |
| Vorgangsüberwachungsnachrichten |Alle ungelesenen Nachrichten gehen verloren |
| Cloud-zu-Gerät-Feedbacknachrichten |Alle ungelesenen Nachrichten gehen verloren |

<sup>1</sup>Cloud-zu-Gerät-Nachrichten und übergeordnete Aufträge werden in der Vorschauversion dieser Funktion im Rahmen des manuellen Failovers nicht wiederhergestellt.

Sobald der Failovervorgang für die IoT Hub-Instanz abgeschlossen ist, wird erwartet, dass alle Vorgänge des Geräts und der Back-End-Anwendungen ohne manuellen Eingriff fortgesetzt werden.

> [!CAUTION]
> - Der Event Hub-kompatible Namen und Endpunkt des im IoT Hub integrierten Endpunkts ändern sich nach dem Failover. Wenn Sie Telemetrienachrichten vom integrierten Endpunkt über den Event Hub-Client oder den Ereignisprozessorhost empfangen, [ verwenden Sie die IoT Hub-Verbindungszeichenfolge](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint), um die Verbindung herzustellen. So wird sichergestellt, dass Ihre Back-End-Anwendungen weiterhin ausgeführt werden, ohne dass ein manueller Eingriff nach dem Failover erforderlich ist. Wenn Sie den Event Hub-kompatiblen Namen und Endpunkt direkt in Ihrer Back-End-Anwendung verwenden, müssen Sie Ihre Anwendung neu konfigurieren, indem Sie den [neuen Event Hub-kompatiblen Namen und Endpunkt](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) nach dem Failover abrufen, um die Vorgänge fortzusetzen.
>
> - Nach dem Failover können die über Event Grid gesendeten Ereignisse über dieselben Abonnements verarbeitet werden, die zuvor konfiguriert wurden, solange diese Event Grid-Abonnements verfügbar sind.
>
> - Beim Weiterleiten an Blobspeicher sollten die Blobs eingetragen und dann durchlaufen werden, um sicherzustellen, dass alle Container gelesen werden, ohne Annahmen zu Partitionen anzustellen. Der Partitionsbereich könnte sich möglicherweise bei einem von Microsoft initiierten Failover oder einem manuellen Failover ändern. Informationen zur Aufzählung der Listen von Blobs finden Sie unter [Azure Blob Storage](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

### <a name="microsoft-initiated-failover"></a>Von Microsoft initiiertes Failover

Das von Microsoft initiierte Failover wendet Microsoft in seltenen Fällen an, um ein Failover für alle IoT Hubs einer betroffenen Region in die entsprechende geografisch gekoppelte Region auszuführen. Dieser Prozess ist eine Standardoption (Benutzer können sie also nicht abwählen), für die kein Eingriff des Benutzers erforderlich ist. Microsoft behält sich das Recht vor, zu bestimmen, wann diese Option angewendet wird. Dieser Mechanismus bedarf nicht der Zustimmung des Benutzers, bevor ein Failover für den Benutzerhub ausgeführt wird. Das von Microsoft initiierte Failover weist eine RTO von 2 bis 26 Stunden auf. 

Die große RTO-Zeitspanne ist dadurch bedingt, dass Microsoft das Failover für alle betroffenen Kunden in der entsprechenden Region ausführt. Wenn Sie eine weniger wichtige IoT-Lösung ausführen, die eine Downtime von etwa einem Tag toleriert, ist es für Sie in Ordnung, eine Abhängigkeit von dieser Option einzugehen, um die allgemeinen Notfallwiederherstellungsziele für Ihre IoT-Lösung zu erfüllen. Die Gesamtzeit, nach der Laufzeitvorgänge nach Auslösung dieses Prozesses wieder vollständig funktionsfähig sind, wird im Abschnitt „Zeit bis zur Wiederherstellung“ beschrieben.

### <a name="manual-failover-preview"></a>Manuelles Failover (Vorschauversion)

Wenn Ihre geschäftlichen Betriebszeitziele durch das von Microsoft initiierte Failover nicht erfüllt werden, können Sie das manuelle Failover nutzen, um den Failoverprozess selbst auszulösen. Die RTO beträgt für diese Option 10 Minuten bis ein paar Stunden. Die RTO ist derzeit eine Funktion der Geräte, die für die IoT Hub-Instanz registriert sind und für die ein Failover ausgeführt wird. Für einen Hub, der etwa 100.000 Geräte hostet, beträgt die RTO ca. 15 Minuten. Die Gesamtzeit, nach der Laufzeitvorgänge nach Auslösung dieses Prozesses wieder vollständig funktionsfähig sind, wird im Abschnitt „Zeit bis zur Wiederherstellung“ beschrieben.

Die manuelle Failoveroption ist immer verfügbar, unabhängig davon, ob in der primären Region Downtime auftritt. Daher lässt sich diese Option für ein geplantes Failover nutzen. Ein Beispiel für die Verwendung geplanter Failover ist die Durchführung regelmäßiger Failoverdrills. Beachten Sie jedoch, dass ein geplantes Failover zu einer Hubdowntime für die von der RTO für diese Option definierte Zeitdauer und zu Datenverlust führt, wie in der obigen RPO-Tabelle definiert. Sie könnten eine IoT Hub-Testinstanz einrichten, um die geplante Failoveroption regelmäßig auszuführen, damit Sie sich darauf verlassen können, dass Ihre End-to-End-Lösungen bei einem echten Notfall einsatzbereit sind.

> [!IMPORTANT]
> - Testdrills sollten nicht in IoT Hub-Instanzen ausgeführt werden, die in Ihrer Produktionsumgebung verwendet werden.
>
> - Das manuelle Failover sollte nicht als Mechanismus verwendet werden, um Ihren Hub dauerhaft zwischen den geografisch gekoppelten Azure-Regionen zu migrieren. Die Folge wäre eine erhöhte Latenz bei den Vorgängen, die von Geräten in der alten primären Region für den Hub ausgeführt werden.

### <a name="failback"></a>Failback

Ein Failback auf die alte primäre Region kann erreicht werden, indem das Failover erneut ausgelöst wird. Wenn das ursprüngliche Failover für eine Wiederherstellung nach einem längeren Ausfall in der ursprünglichen primären Region ausgeführt wurde, wird empfohlen, dass für den Hub ein Failback auf den ursprünglichen Standort ausgeführt wird, sobald dieser sich vom Ausfall erholt hat.

> [!IMPORTANT]
> - Benutzer dürfen nur zwei erfolgreiche Failover und Failbacks pro Tag ausführen.
>
> - Direkt aufeinanderfolgende Failover-/Failbackvorgänge sind nicht zulässig. Die Benutzer müssen zwischen diesen Vorgängen 1 Stunde warten.

### <a name="time-to-recover"></a>Zeit bis zur Wiederherstellung

Während der FQDN (und damit die Verbindungszeichenfolge) der IoT Hub-Instanz nach dem Failover identisch bleibt, ändert sich die zugrunde liegende IP-Adresse. Daher kann die Gesamtzeit, nach der Laufzeitvorgänge für Ihre IoT Hub-Instanz nach dem Auslösen des Failovers vollständig funktionsfähig sind, mit der folgenden Funktion ausgedrückt werden.

Zeit bis zur Wiederherstellung = RTO [10 Minuten–2 Stunden für manuelles Failover | 2–26 Stunden für von Microsoft initiiertes Failover] + Verzögerung bei DNS-Verteilung + von Clientanwendung benötigte Zeit zur Aktualisierung zwischengespeicherter IoT Hub-IP-Adressen.

> [!IMPORTANT]
> Die IoT-SDKs speichern nicht die IP-Adresse der IoT Hub-Instanz. Es wird empfohlen, die IP-Adresse der IoT Hub-Instanz nicht mit Benutzercodes mit einer SDK-Schnittstelle zwischenzuspeichern.

## <a name="achieve-cross-region-ha"></a>Erzielen regionenübergreifender Hochverfügbarkeit

Wenn das von Microsoft initiierte Failover oder das manuelle Failover Ihre geschäftlichen Betriebszeitziele nicht erfüllt, sollten Sie einen automatischen und regionsübergreifenden Failovermechanismus pro Gerät implementieren.
Eine ausführliche Erläuterung von Bereitstellungstopologien in IoT-Lösungen würde den Rahmen dieses Artikels sprengen. In diesem Artikel wird das Bereitstellungsmodell für das *regionale Failover* zur Erzielung von Notfallwiederherstellung und Hochverfügbarkeit behandelt.

In einem regionalen Failovermodell wird das Lösungs-Back-End in erster Linie an einem Rechenzentrumsstandort ausgeführt. Eine sekundäre IoT Hub-Einheit und ein Back-End werden an einem anderen Rechenzentrumsstandort bereitgestellt. Wenn die IoT Hub-Instanz in der primären Region ausfällt oder die Netzwerkverbindung des Geräts mit der primären Region unterbrochen wird, verwenden Geräte einen sekundären Dienstendpunkt. Sie können die Verfügbarkeit der Lösung durch die Implementierung eines regionsübergreifenden Failovermodells verbessern, statt innerhalb einer Region zu bleiben. 

Beachten Sie die folgenden Punkte, wenn Sie ein Modell für regionales Failover mit IoT Hub implementieren möchten:

* **Eine sekundäre Routinglogik für IoT Hub und Geräte**: Bei einer Dienstunterbrechung in der primären Region müssen Geräte eine Verbindung mit der sekundären Region herstellen. Da die meisten beteiligten Dienste zustandsorientiert sind, wird der Failoverprozess zwischen Regionen häufig von Lösungsadministratoren ausgelöst. Die beste Möglichkeit, Geräte über den neuen Endpunkt zu informieren und gleichzeitig die Kontrolle über den Prozess zu behalten, besteht darin, für die Geräte eine regelmäßige Prüfung eines *Concierge*-Diensts auf den derzeit aktiven Endpunkt durchführen zu lassen. Der Concierge-Dienst kann eine Webanwendung sein, die repliziert und deren Erreichbarkeit mithilfe von DNS-Umleitungsverfahren (z.B. per [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)) gewährleistet wird.

   > [!NOTE]
   > Der IoT Hub-Dienst ist kein unterstützter Endpunkttyp in Azure Traffic Manager. Es wird empfohlen, den vorgeschlagenen Concierge-Dienst mit Azure Traffic Manager zu integrieren, indem Sie die Endpunktintegritätstest-API implementieren.

* **Replikation der Identitätsregistrierung**: Um verwendet werden zu können, muss die sekundäre IoT Hub-Einheit alle Geräteidentitäten enthalten, die eine Verbindung mit der Lösung herstellen können. Für die Lösung sollten georeplizierte Backups von Geräteidentitäten vorgehalten und auf die sekundäre IoT Hub-Einheit hochgeladen werden, bevor der aktive Endpunkt für die Geräte gewechselt wird. Die Funktionen zum Exportieren der Geräteidentität von IoT Hub sind in diesem Zusammenhang praktisch. Weitere Informationen finden Sie unter [IoT Hub-Entwicklerhandbuch – Identitätsregistrierung](iot-hub-devguide-identity-registry.md).

* **Zusammenführungslogik**: Wenn die primäre Region wieder verfügbar ist, müssen die Status und Daten, die am sekundären Standort erstellt wurden, zurück zur primären Region migriert werden. Dieser Status und die Daten beziehen sich hauptsächlich auf Geräteidentitäten und Anwendungsmetadaten, die mit der primären IoT Hub-Einheit und etwaigen anderen anwendungsspezifischen Datenspeichern in der primären Region zusammengeführt werden müssen. 

Zur Vereinfachung dieses Schritts empfiehlt sich die Verwendung idempotenter Vorgänge. Idempotente Vorgänge verringern die Nebeneffekte für die letztendliche konsistente Verteilung von Ereignissen sowie für Duplikate oder die außerordentliche Bereitstellung von Ereignissen. Außerdem sollte die Anwendungslogik so entworfen werden, dass potenzielle Inkonsistenzen oder ein „geringfügig“ veralteter Zustand toleriert werden. Dieser Fall kann aufgrund der zusätzlichen Zeit eintreten, die das System für die Wiederherstellung basierend auf der RPO benötigt.

## <a name="choose-the-right-hadr-option"></a>Auswählen der geeigneten Option für Hochverfügbarkeit/Notfallwiederherstellung

Diese Zusammenfassung der in diesem Artikel vorgestellten Optionen für Hochverfügbarkeit und Notfallwiederherstellung dient als Referenz für die Auswahl der für Ihre Lösung geeigneten Option.

| Option für Hochverfügbarkeit/Notfallwiederherstellung | RTO | RPO | Manueller Eingriff | Implementierungskomplexität | Zusätzliche Kosten|
| --- | --- | --- | --- | --- | --- |
| Von Microsoft initiiertes Failover |2–26 Stunden|Siehe RPO-Tabelle oben|Nein|Keine|Keine|
| Manuelles Failover |10 Minuten–2 Stunden|Siehe RPO-Tabelle oben|Ja|Sehr gering. Sie müssen diesen Vorgang lediglich über das Portal auslösen.|Keine|
| Regionenübergreifende Hochverfügbarkeit |< 1 Minute|Abhängig von der Replikationsrate Ihrer benutzerdefinierten Hochverfügbarkeitslösung|Nein|Hoch|> 1-malig anfallende Kosten für 1 IoT Hub-Instanz|

## <a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

* [Erste Schritte mit IoT Hubs (Schnellstart)](quickstart-send-telemetry-dotnet.md)
* [Was ist Azure IoT Hub?](about-iot-hub.md)