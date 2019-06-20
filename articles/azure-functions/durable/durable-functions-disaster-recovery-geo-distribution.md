---
title: Notfallwiederherstellung und geografische Verteilung in Durable Functions – Azure
description: Hier erfahren Sie mehr zur Notfallwiederherstellung und zur geografischen Verteilung in Durable Functions.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60741345"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Notfallwiederherstellung und geografische Verteilung

## <a name="overview"></a>Übersicht

In Durable Functions werden sämtliche Zustände in Azure Storage persistent gespeichert. Ein [Aufgabenhub](durable-functions-task-hubs.md) ist ein logischer Container für Azure Storage-Ressourcen, die zur Orchestrierung verwendet werden. Orchestrator- und Aktivitätsfunktionen können nur miteinander interagieren, wenn sie zum selben Aufgabenhub gehören.
In den beschriebenen Szenarien werden Bereitstellungsoptionen empfohlen, um bei Notfallwiederherstellungsaktivitäten die Verfügbarkeit zu erhöhen und Ausfallzeiten zu minimieren.

Dabei ist zu beachten, dass diese Szenarien auf Aktiv/Passiv-Konfigurationen basieren, da sie von der Verwendung von Azure Storage gesteuert werden. Dieses Muster besteht aus der Bereitstellung einer (passiven) Sicherungsfunktionen-App in einer anderen Region. Der Traffic Manager überwacht die primäre (aktive) Funktionen-App zur Sicherstellung der Verfügbarkeit. Fällt die primäre App aus, wird ein Failover zur Sicherungsfunktionen-App durchgeführt. Weitere Informationen finden Sie im Artikel zur [Prioritätsmethode für das Datenverkehrsrouting](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) des [Traffic Managers](https://azure.microsoft.com/services/traffic-manager/).

>[!NOTE]
>
> - Die vorgeschlagene Aktiv/Passiv-Konfiguration stellt sicher, dass ein Client stets neue Orchestrierungen über HTTP auslösen kann. Da zwei Funktionen-Apps verwendet werden, die den gleichen Speicher nutzen, wird die Hintergrundverarbeitung zwischen diesen aufgeteilt, sodass sie um Nachrichten in der gleichen Warteschlange konkurrieren. Bei dieser Konfiguration fallen Kosten für zusätzlichen ausgehenden Datenverkehr bei der sekundären Funktionen-App an.
> - Das zugrunde liegende Speicherkonto und der Aufgabenhub werden in der primären Region erstellt und für beide Funktionen-Apps freigegeben.
> - Alle Funktionen-Apps, die redundant bereitgestellt werden, müssen die gleichen Funktionszugriffsschlüssel verwenden, für den Fall, dass sie über HTTP aktiviert werden. Die Functions Runtime macht eine [Verwaltungs-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) verfügbar, die Consumern das programmgesteuerte Hinzufügen, Löschen und Aktualisieren von Funktionstasten ermöglicht.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Szenario 1 – Lastenausgleich von Computekapazitäten mit freigegebenem Speicher

Fällt die Computeinfrastruktur in Azure aus, ist die Funktionen-App eventuell nicht verfügbar. Um die Wahrscheinlichkeit solcher Ausfallzeiten zu minimieren, werden in diesem Szenario zwei Funktionen-Apps verwendet, die in unterschiedlichen Regionen bereitgestellt werden.
Der Traffic Manager ist für die Erkennung von Problemen in der primären Funktionen-App konfiguriert und leitet Datenverkehr automatisch an die Funktionen-App in der sekundären Region um. Diese Funktionen-App verwendet das gleiche Azure Storage-Konto und den gleichen Aufgabenhub. Daher geht der Zustand der Funktionen-Apps nicht verloren und die Ausführung kann wie gewohnt fortgesetzt werden. Sobald die Integrität in der primären Region wiederhergestellt wurde, startet der Azure Traffic Manager automatisch die Weiterleitung von Anforderungen an diese Funktionen-App.

![Abbildung zu Szenario 1](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Dieses Bereitstellungsszenario bietet mehrere Vorteile:

- Fällt die Computeinfrastruktur aus, kann der Betrieb in der Failoverregion ohne Verlust des Zustands fortgesetzt werden.
- Der Traffic Manager übernimmt automatisch das Failover für die fehlerfreie Funktionen-App.
- Der Traffic Manager leitet automatisch wieder Datenverkehr an die primäre Funktionen-App weiter, nachdem der Ausfall behoben wurde.

Bei diesem Szenario sollten Sie jedoch Folgendes berücksichtigen:

- Wenn die Funktionen-App mit einem dedizierten App Service-Plan bereitgestellt wird, treibt eine Replikation der Computeinfrastruktur im Rechenzentrum, für das das Failover durchgeführt wird, die Kosten in die Höhe.
- Dieses Szenario deckt zwar Ausfälle in der Computeinfrastruktur ab, doch das Speicherkonto ist weiterhin der Single Point of Failure für die Funktionen-App. Ein Ausfall von Storage zieht einen Ausfall der Anwendung nach sich.
- Wenn ein Failover für die Funktionen-App ausgeführt wird, kommt es zu längeren Latenzen, da diese auf das Speicherkonto in verschiedenen Regionen zugreift.
- Der Zugriff auf den Speicherdienst von einer anderen Region als der Region, in der sich der Dienst befindet, führt aufgrund des ausgehenden Netzwerkdatenverkehrs zu erhöhten Kosten.
- Dieses Szenario basiert auf dem Traffic Manager. Angesichts der [Funktionsweise des Traffic Managers](../../traffic-manager/traffic-manager-how-it-works.md) kann es eine Weile dauern, bis eine Clientanwendung, die Durable Functions nutzt, erneut die Adresse der Funktionen-App des Traffic Managers abrufen muss.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Szenario 2 – Lastenausgleich von Computekapazitäten mit regionalem Speicher

In dem oben beschriebenen Szenario wird lediglich ein Ausfall in der Computeinfrastruktur behandelt. Fällt der Speicherdienst aus, so hat dies den Ausfall der Funktionen-App zur Folge.
Um einen kontinuierlichen Betrieb von Durable Functions sicherzustellen, wird in diesem Szenario ein lokales Speicherkonto für jede Region verwendet, in der die Funktionen-Apps bereitgestellt werden.

![Abbildung zu Szenario 2](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Bei dieser Vorgehensweise werden Verbesserungen gegenüber dem vorherigen Szenario vorgenommen:

- Fällt die Funktionen-App aus, übernimmt der Traffic Manager das Failover zur sekundären Region. Da die Funktionen-App jedoch ein eigenes Speicherkonto benötigt, wird weiterhin Durable Functions ausgeführt.
- Bei einem Failover kommt es nicht zu weiteren Latenzen in der Failoverregion, da sich die Funktionen-App und das Speicherkonto in derselben Region befinden.
- Ausfälle auf Speicherebene führen zu Ausfällen von Durable Functions, wodurch wiederum eine Umleitung zur Failoverregion ausgelöst wird. Da die Funktionen-App und der Speicher nach Region isoliert sind, ist Durable Functions weiterhin funktionsfähig.

Wichtige Hinweise zu diesem Szenario:

- Wenn die Funktionen-App mit einem dedizierten App Service-Plan bereitgestellt wird, treibt eine Replikation der Computeinfrastruktur im Rechenzentrum, für das das Failover durchgeführt wird, die Kosten in die Höhe.
- Der aktuelle Zustand wird beim Failover nicht übertragen, was bedeutet, dass bei der Ausführung von Funktionen und bei Prüfpunkten Fehler auftreten. Das Wiederholen bzw. das Neustarten der Aufgaben obliegt der Clientanwendung.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Szenario 3 – Lastenausgleich von Computekapazitäten mit freigegebenem GRS

Dieses Szenario stellt eine Abwandlung des ersten Szenarios dar, bei der ein freigegebenes Speicherkonto implementiert wird. Der wesentliche Unterschied ist, dass das Speicherkonto mit aktivierter Georeplikation erstellt wird.
Funktionell bietet dieses Szenario dieselben Vorteile wie Szenario 1, jedoch darüber hinaus zusätzlich Vorteile im Hinblick auf die Datenwiederherstellung:

- Georedundante Speicher (GRS) und georedundante Speicher mit Lesezugriff (RA-GRS) maximieren die Verfügbarkeit Ihres Speicherkontos.
- Bei einem regionalen Ausfall des Speicherdiensts ist eine der Möglichkeiten, wenn der Rechenzentrumsbetrieb zu dem Schluss kommt, das ein Failover zur sekundären Region erforderlich ist. In diesem Fall wird der Speicherkontozugriff ohne Eingreifen des Benutzers transparent zur geografisch replizierten Kopie des Speicherkontos umgeleitet.
- In diesem Fall wird der Zustand von Durable Functions bis zur letzten Replikation des Speicherkontos persistent gespeichert, und zwar alle paar Minuten.

Wie bei den anderen Szenarien sind auch hier wichtige Aspekte zu berücksichtigen:

- Ein Failover zum Replikat wird von Rechenzentrumsbetreibern durchgeführt und kann einige Zeit in Anspruch nehmen. Derweil ist die Funktionen-App nicht verfügbar.
- Der Einsatz von geografisch replizierten Speicherkonten führt zum Kostenanstieg.
- Die Replikation von GRS erfolgt asynchron. Einige der letzten Transaktionen könnten aufgrund von Latenzen seitens des Replikationsvorgangs verloren gehen.

![Abbildung zu Szenario 3](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md).
