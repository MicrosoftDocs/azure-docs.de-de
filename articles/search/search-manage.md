---
title: Dienstverwaltung für Azure Search im Portal – Azure Search
description: Verwalten eines Azure Search-Diensts, ein gehosteter Cloudsuchdienst in Microsoft Azure, mit dem Azure-Portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565434"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Dienstverwaltung für Azure Search im Azure-Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search ist ein vollständig verwalteter cloudbasierter Suchdienst zum Erstellen einer umfassenden Suchoberfläche für benutzerdefinierte Apps. In diesem Artikel werden die Dienstverwaltungsaufgaben beschrieben, die Sie im [Azure-Portal](https://portal.azure.com) für einen zuvor bereitgestellten Suchdienst ausführen können. Die Dienstverwaltung ist nicht sehr umfangreich und auf die folgenden Aufgaben beschränkt:

> [!div class="checklist"]
> * Verwalten des Zugriffs auf die *API-Schlüssel*, die für den Lese- oder Schreibzugriff auf den Dienst verwendet werden
> * Anpassen der Dienstkapazität, indem Sie die Zuordnung von Partitionen und Replikaten ändern
> * Überwachen der Ressourcennutzung relativ zu den Grenzwerten Ihrer Dienstebene

Beachten Sie, dass *Upgrade* nicht als administrativer Task aufgeführt ist. Da beim Bereitstellen des Diensts Ressourcen zugewiesen werden, erfordert das Verschieben zu einer anderen Preisstufe einen neuen Dienst. Weitere Informationen finden Sie unter [Erstellen eines Azure Search-Diensts](search-create-service-portal.md).

> [!Tip]
> Suchen Sie Hilfe zur Analyse von Suchdatenverkehr oder Abfrageleistung? Sie können Folgendes überwachen: das Abfragevolumen, die Begriffe, nach denen Personen suchen, und wie erfolgreich Suchergebnisse Kunden zu bestimmten Dokumenten in Ihrem Index leiten. Weitere Informationen finden Sie unter [Durchsuchen der Datenverkehrsanalyse für Azure Search](search-traffic-analytics.md), [Überwachen der Nutzung und Statistiken](search-monitor-usage.md) und [Leistung und Optimierung](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Administratorrechte
Die Bereitstellung oder Außerbetriebnahme des eigentlichen Diensts kann von einem Administrator oder Co-Administrator des Azure-Abonnements durchgeführt werden.

Innerhalb eines Diensts hat jede Person mit Zugriff auf die Dienst-URL und einem Admin-API-Schlüssel Lese-/Schreibzugriff auf den Dienst. Der Lese-/Schreibzugriff ermöglicht das Hinzufügen, Löschen oder Ändern von Serverobjekten, einschließlich der API-Schlüssel, Indizes, Indexer, Datenquellen, Zeitpläne und Rollenzuweisungen, die über [per RBAC-definierte Rollen](search-security-rbac.md) implementiert wurden.

Alle Benutzerinteraktionen mit Azure Search fallen unter einen dieser beiden Modi: Lese-/Schreibzugriff auf den Dienst (Administratorrechte) oder Lesezugriff auf den Dienst (Abfragerechte). Weitere Informationen finden Sie unter [Verwalten der API-Schlüssel](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Protokollierungs- und Systeminformationen
In Azure Search werden keine Protokolldateien für einen einzelnen Dienst über das Portal oder programmgesteuerte Schnittstellen verfügbar gemacht. Im Basic-Tarif und höher überwacht Microsoft alle Azure Search-Dienste auf eine Verfügbarkeit von 99,9% gemäß der Vereinbarung zum Servicelevel (SLA). Wenn der Dienst langsam ist oder der Anforderungsdurchsatz unter die SLA-Schwellenwerte fällt, überprüfen Supportteams die verfügbaren Protokolldateien und kümmern sich um die Lösung des Problems.

Allgemeine Informationen zu Ihrem Dienst erhalten Sie wie folgt:

* Im Portal über das Dashboard des Diensts in Form von Benachrichtigungen, Eigenschaften und Statusmeldungen.
* Über [PowerShell](search-manage-powershell.md) oder die [Verwaltungs-REST-API](https://docs.microsoft.com/rest/api/searchmanagement/), um [Diensteigenschaften abzurufen](https://docs.microsoft.com/rest/api/searchmanagement/services) oder den Status der Indexressourcennutzung zu ermitteln.
* Über [Datenverkehrsanalyse durchsuchen](search-traffic-analytics.md), wie bereits erwähnt.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Überwachen der Ressourcenauslastung
Die Ressourcenüberwachung im Dashboard ist auf die Informationen im Dienst-Dashboard und einige weitere Metriken beschränkt, die Sie durch Abfragen an den Dienst erhalten. Im Bereich Nutzung im Dienst-Dashboard können Sie direkt ablesen, ob die Partitionsressourcen für Ihre Anwendung angemessen sind. Sie können externe Ressourcen, wie z.B. die Azure-Überwachung, bereitstellen, wenn Sie protokollierte Ereignisse erfassen und aufzeichnen möchten. Weitere Informationen finden Sie unter [Überwachung von Azure Search](search-monitor-usage.md).

Mit der Suchdienst-REST API können Sie Gesamtzahlen für Dokumente und Indizes programmgesteuert abrufen: 

* [Indexstatistiken abrufen](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Dokumentenanzahl](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Notfallwiederherstellung und Dienstausfälle

Obwohl wir Ihre Daten retten können, bietet Azure Search kein sofortiges Failover des Diensts, wenn ein Ausfall auf Cluster- oder Rechenzentrumsebene auftritt. Wenn ein Cluster im Rechenzentrum fehlschlägt, wird das Betriebsteam dieses ermitteln und daran arbeiten, den Dienst wiederherzustellen. Während der Dienstwiederherstellung kommt es zu Ausfallzeiten, Sie können jedoch laut [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Dienstguthaben anfordern, um damit die Nichtverfügbarkeit des Diensts kompensieren zu können. 

Sollte ein unterbrechungsfreier Dienst im Falle schwerwiegender Fehler, die nicht der Kontrolle von Microsoft unterliegen, benötigt werden, können Sie [einen zusätzlichen Dienst](search-create-service-portal.md) in einer anderen Region bereitstellen und eine Georeplikationsstrategie implementieren, um sicherzustellen, dass Indizes für alle Dienste vollständig redundant sind.

Kunden, die [Indexer](search-indexer-overview.md) zum Auffüllen und Aktualisieren von Indizes verwenden, können die Notfallwiederherstellung über geospezifische Indizes ausführen, welche die gleiche Datenquelle verwenden. Zwei Dienste in unterschiedlichen Regionen, die jeweils einen Indexer ausführen, könnten die gleiche Datenquelle indizieren, um Georedundanz zu erzielen. Wenn Sie Daten aus Datenquellen indizieren, die auch georedundant sind, sollten Sie darauf achten, dass Azure Search-Indexer eine inkrementelle Indizierung nur aus primären Replikaten ausführen können. Achten Sie im Falle eines Failoverereignisses darauf, den Indexer erneut auf das neue primäre Replikat auszurichten. 

Wenn Sie keine Indexer verwenden, können Sie Ihren Anwendungscode verwenden, um Objekte und Daten gleichzeitig per Pushvorgang an verschiedene Dienste zu übermitteln. Weitere Informationen finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Da Azure Search keine primäre Datenspeicherlösung ist, wird kein formales Verfahren für Self-Service-Sicherung und -Wiederherstellung bereitgestellt. Ihr Anwendungscode, der zum Erstellen und Auffüllung eines Index verwendet wird, ist de facto die Wiederherstellungsoption, wenn Sie aus Versehen einen Index löschen. 

Um einen Index neu zu erstellen, würden Sie ihn (sofern vorhanden) löschen, den Index im Dienst wiederherstellen und ihn erneut laden, indem Sie Daten aus Ihrem primären Datenspeicher abrufen.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Herauf- oder Herunterskalieren
Jeder Suchdienst enthält zunächst ein Minimum von einem Replikat und einer Partition. Wenn Sie sich für eine [Preisstufe angemeldet haben, die dedizierte Ressourcen bietet](search-limits-quotas-capacity.md), klicken Sie im Servicedashboard auf die Kachel **Skalierung**, um die Ressourcenverwendung anzupassen.

Wenn Sie zusätzliche Kapazität über eine dieser Ressourcen hinzufügen, wird diese vom Dienst automatisch verwendet. Sie müssen nichts weiter tun. Allerdings tritt eine kleine Verzögerung auf, bevor die Auswirkungen der neuen Ressource spürbar sind. Die Bereitstellung zusätzlicher Ressourcen kann 15 Minuten oder mehr in Anspruch nehmen.

 ![][10]

### <a name="add-replicas"></a>Hinzufügen von Replikaten
Fügen Sie Replikate hinzu, um die Anzahl der Abfragen pro Sekunde (Queries per Second QPS) zu steigern oder Hochverfügbarkeit einzurichten. Jedes Replikat enthält eine Kopie eines Index. Jedes zusätzliche Replikat führt also zu einem zusätzlichen Index, der zur Behandlung von Abfragen verwendet werden kann. Für die Hochverfügbarkeit sind mindestens drei Replikate erforderlich (Details unter [Kapazitätsplanung](search-capacity-planning.md)).

Suchdienste mit mehr Replikaten können Abfragen per Lastenausgleich auf eine größere Anzahl von Indizes verteilen. Für ein gegebenes Abfragevolumen ist ein höherer Durchsatz an Abfragen möglich, wenn mehr Kopien des Index zur Beantwortung bereitstehen. Wenn Sie eine hohe Latenz in Ihren Abfragen bemerken, können Sie eine positive Auswirkung auf die Leistung erwarten, sobald die zusätzlichen Replikate online sind.

Auch wenn der Abfragedurchsatz beim Hinzufügen von Replikaten ansteigt, wird dieser mit zusätzlichen Replikaten nicht exakt verdoppelt, verdreifacht usw. Alle Suchanwendungen unterliegen externen Faktoren, die die Abfrageleistung beeinträchtigen können. Komplexe Abfragen und Netzwerklatenz sind zwei Faktoren, die die Antwortzeiten von Abfragen beeinflussen.

### <a name="add-partitions"></a>Hinzufügen von Partitionen
Bei den meisten Dienstanwendungen sind anstelle von Partitionen eher mehr Replikate erforderlich. In Fällen, in denen Sie eine zusätzliche Anzahl von Dokumenten benötigen, können Sie Partitionen hinzufügen, falls Sie für den Standarddienst registriert sind. In der Preisstufe Basic stehen keine zusätzlichen Partitionen zur Verfügung.

In der Preisstufe Standard werden Partitionen jeweils in Teilern von 12 hinzugefügt (1, 2, 3, 4, 6 oder 12). Dies ist ein Artefakt für das Sharding (Horizontales Partitionieren). Indizes werden in 12 Shards erstellt, die entweder in 1 Partition gespeichert oder in gleichen Teilen auf 2, 3, 4, 6 oder 12 Partitionen (1 Shard pro Partition) verteilt werden können.

### <a name="remove-replicas"></a>Entfernen von Replikaten
Nach Zeiten mit hohem Abfragevolumen können Sie mit dem Schieberegler die Replikate reduzieren, wenn sich die Suchabfragelast wieder normalisiert hat (z. B. nach Abschluss des Weihnachtsgeschäfts). Keine weiteren Schritte sind erforderlich. Beim Senken der Replikatanzahl werden virtuelle Computer im Rechenzentrum freigegeben. Ihre Abfrage- und Datenerfassungsoperationen werden nun auf weniger VMs ausgeführt als zuvor. Als Mindestanforderung muss ein Replikat vorhanden sein.

### <a name="remove-partitions"></a>Entfernen von Partitionen
Im Gegensatz zum Entfernen von Replikaten, bei dem Sie keine weiteren Schritte ausführen müssen, erfordert die Senkung des verbrauchten Speicherplatzes unter Umständen einigen Arbeitsaufwand. Wenn Sie in der Lösung beispielsweise drei Partitionen verwenden, wird bei der Reduzierung auf ein oder zwei Partitionen ein Fehler generiert, falls der neue Speicherplatz geringer ist als für das Hosten des Indexes erforderlich. Wie Sie sicherlich schon vermuten, können Sie entweder Indizes oder Dokumente innerhalb eines Index löschen, um Speicherplatz freizugeben, oder die aktuelle Konfiguration beibehalten.

Es ist nicht möglich, herauszufinden, welche Index-Shards auf welcher Partition gespeichert sind. Jede Partition bietet ca. 25 GB an Speicherplatz an. Sie müssen Ihren Speicherverbrauch also auf einen Wert senken, der von Ihren konfigurierten Partitionen unterstützt werden kann. Wenn Sie auf eine Partition zurückschalten, müssen alle 12 Shards in diese Partition passen.

Für die zukünftige Planung können Sie den Speicherverbrauch prüfen (siehe [Abrufen der Index-Statistiken](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)), um herauszufinden, wie viel Speicher Sie tatsächlich nutzen. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Bewährte Methoden zur Skalierung und Bereitstellung
In diesem 30-minütigen Video werden die bewährten Methoden für erweiterte Bereitstellungsszenarien vorgestellt, z.B. geografisch verteilte Workloads. Unter [Leistung und Optimierung von Azure Search](search-performance-optimization.md) finden Sie auch Hilfeseiten zu denselben Punkten.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Konzepten der Dienstverwaltung vertraut gemacht haben, können Sie [PowerShell](search-manage-powershell.md) verwenden, um Aufgaben zu automatisieren.

Außerdem wird empfohlen, dass Sie sich den [Artikel zu Leistung und Optimierung](search-performance-optimization.md) ansehen.

Es wird ebenfalls empfohlen, sich das im vorherigen Abschnitt erwähnte Video anzusehen. Er bietet umfangreichere Informationen zu den in diesem Abschnitt erwähnten Verfahren.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



