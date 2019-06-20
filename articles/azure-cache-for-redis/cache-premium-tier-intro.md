---
title: Einführung in den Premium-Tarif von Azure Cache for Redis | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie für Ihre Azure Cache for Redis-Instanzen im Tarif „Premium“ Redis-Persistenz Redis-Clustering und VNET-Unterstützung erstellen und verwalten.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 6960c21091e0bc01c198e713c0c276984566ac41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786079"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Einführung in den Premium-Tarif von Azure Cache for Redis
Azure Cache for Redis ist ein verteilter, verwalteter Cache, mit dem Sie hoch skalierbare und reaktionsschnelle Anwendungen erstellen können, indem Sie äußerst schnellen Zugriff auf Daten bieten. 

Der neue Tarif „Premium“ ist speziell auf Unternehmen zugeschnitten. Er enthält alle Features des Tarifs „Standard“ und bietet darüber hinaus weitere Features, z.B. eine bessere Leistung, größere Workloads, Notfallwiederherstellung, Import/Export und verbesserte Sicherheit. Nachfolgend finden Sie weitere Informationen zu den zusätzlichen Features des Cachetarifs "Premium".

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bessere Leistung als in den Tarifen "Standard" und "Basic"
**Bessere Leistung in den Tarifen "Standard" und "Basic".** Caches im Tarif "Premium" werden auf Hardware mit schnelleren Prozessoren bereitgestellt, die im Vergleich zu den Tarifen "Basic" oder "Standard" eine bessere Leistung bieten. Caches im Tarif "Premium" erreichen einen höheren Durchsatz und geringere Wartezeiten. 

**Der Durchsatz für Cache derselben Größe ist im Tarif "Premium" im Vergleich zum Tarif "Standard" höher.** Ein P4-Cache (Premium) mit 53 GB erreicht beispielsweise einen Durchsatz von 250.000 Anforderungen pro Sekunde, während ein C6-Cache (Standard) einen Durchsatz von 150.000 erreicht.

Weitere Informationen zur Größe, zum Durchsatz und zur Bandbreite von Premium-Caches finden Sie in den [häufig gestellten Fragen zu Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="redis-data-persistence"></a>Redis-Datenpersistenz
Der Tarif "Premium" ermöglicht die Persistenz der Cachedaten in einem Azure Storage-Konto. In einem "Basic"-/"Standard"-Cache werden alle Daten nur im Arbeitsspeicher gespeichert. Bei Problemen mit der zugrunde liegenden Infrastruktur kann es zu Datenverlusten kommen. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Premium-Tarif zu verwenden, um den Schutz vor Datenverlusten zu erhöhen. Azure Cache for Redis bietet RDB- und AOF-Optionen (demnächst verfügbar) bei der [Redis-Persistenz](https://redis.io/topics/persistence). 

Informationen zum Konfigurieren von Persistenz finden Sie unter [Konfigurieren von Persistenz für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-Cluster
Wenn Sie Caches mit einer Größe über 53 GB erstellen oder Daten über mehrere Redis-Knoten horizontal partitionieren möchten, können Sie das im Tarif "Premium" verfügbare Redis-Clustering verwenden. Jeder Knoten besteht aus einem von Azure für Hochverfügbarkeit verwalteten Paar aus primärem Cache und Replikatcache. 

**Redis-Clustering bietet Ihnen maximale Skalierung und Durchsatz.** Der Durchsatz steigt linear, je mehr Shards (Knoten) Sie im Cluster verwenden. Beispiel: Wenn Sie einen P4-Cluster mit 10 Shards erstellen, beträgt der verfügbare Durchsatz 250.000 * 10 = 2,5 Millionen Anforderungen pro Sekunde. Ausführliche Informationen zu Größe, Durchsatz und Bandbreite von Premium-Caches finden Sie in den [häufig gestellten Fragen zu Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

Informationen zum Einstieg in die Arbeit mit Clustern finden Sie unter [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Erhöhte Sicherheit und Isolierung
Auf im Tarif "Basic" oder "Standard" erstellte Caches kann über das öffentliche Internet zugegriffen werden. Der Zugriff auf den Cache ist basierend auf dem Zugriffsschlüssel eingeschränkt. Im Tarif "Premium" können Sie darüber hinaus sicherstellen, dass nur Clients in einem bestimmten Netzwerk auf den Cache zugreifen können. Sie können Azure Cache for Redis in einem [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) bereitstellen. Sie können alle Features von VNet, z. B. Subnetze, Richtlinien für die Zugriffssteuerung und andere Features, verwenden, um den Zugriff auf Redis weiter einzuschränken.

Weitere Informationen finden Sie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Export ist ein Vorgang der Azure Cache for Redis-Datenverwaltung, bei dem Sie Daten in Azure Cache for Redis importieren oder aus Azure Cache for Redis exportieren können. Hierzu importieren bzw. exportieren Sie eine Momentaufnahme der Azure Cache for Redis-Datenbank (RDB) aus einem Premium-Cache in ein Seitenblob in einem Azure Storage-Konto. So können Sie zwischen verschiedenen Azure Cache for Redis-Instanzen migrieren oder den Cache vor der Nutzung mit Daten auffüllen.

Die Importfunktion kann verwendet werden, um Redis-kompatible RDB-Dateien von beliebigen Redis-Servern zu importieren, die in einer Cloud oder Umgebung ausgeführt werden, z.B. Redis unter Linux oder Windows oder bei einem Cloudanbieter wie Amazon Web Services und anderen. Das Importieren von Daten ist eine einfache Möglichkeit zum Erstellen eines Cache mit vorab aufgefüllten Daten. Während des Importvorgangs lädt Azure Cache for Redis die RDB-Dateien aus Azure Storage in den Arbeitsspeicher und fügt die Schlüssel anschließend in den Cache ein.

Mit der Exportfunktion können Sie die in Azure Cache for Redis gespeicherten Daten als Redis-kompatible RDB-Dateien exportieren. Sie können diese Funktion nutzen, um Daten von einer Azure Cache for Redis-Instanz zur anderen oder auf einen anderen Redis-Server zu verschieben. Während des Exportvorgangs wird auf dem virtuellen Computer, der die Azure Cache for Redis-Serverinstanz hostet, eine temporäre Datei erstellt, die dann in das angegebene Speicherkonto hochgeladen wird. Nachdem der Exportvorgang mit dem Status „Erfolg“ oder „Fehler“ abgeschlossen wurde, wird die temporäre Datei gelöscht.

Weitere Informationen finden Sie unter [Gewusst wie: Importieren und Exportieren von Daten in Azure Cache for Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reboot
Im Premium-Tarif können Sie einen oder mehrere Knoten Ihres Caches bei Bedarf neu starten. Dies ermöglicht das Testen Ihrer Anwendung auf Stabilität im Fall eines Ausfalls. Sie können die folgenden Knoten neu starten.

* Masterknoten des Caches
* Sekundärer Knoten Ihres Caches
* Beide Knoten, primär und sekundär, Ihres Caches
* Wenn Sie einen Premium-Cache mit Clustering verwenden, können Sie den primären, den sekundären oder beide Knoten für einzelne Shards im Cache neu starten.

Weitere Informationen finden Sie unter [Neustart](cache-administration.md#reboot) und [Häufig gestellte Fragen zum Neustart](cache-administration.md#reboot-faq).

>[!NOTE]
>Die Neustartfunktionalität ist nun für alle Azure Cache for Redis-Tarife aktiviert.
>
>

## <a name="schedule-updates"></a>Planen von Updates
Mit dem Feature zum Planen von Updates können Sie ein Wartungsfenster für Ihren Cache bestimmen. Wenn das Wartungsfenster angegeben ist, erfolgen alle Redis-Serverupdates in diesem Zeitfenster. Um ein Wartungsfenster anzugeben, wählen Sie die gewünschten Tage aus. Geben Sie die Startzeit des Wartungsfensters für jeden Tag an. Beachten Sie, dass die Zeit im Wartungsfenster als UTC angegeben ist. 

Weitere Informationen finden Sie unter [Planen von Updates](cache-administration.md#schedule-updates) und [Häufig gestellte Fragen zum Planen von Updates](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Nur Updates des Redis-Servers erfolgen während des geplanten Wartungsfensters. Das Wartungsfenster gilt nicht für Azure-Updates oder Updates des Betriebssystems virtueller Computer.
> 
> 

## <a name="geo-replication"></a>Georeplikation

**Georeplikation** bietet einen Mechanismus zum Verknüpfen von zwei Azure Cache for Redis-Instanzen im Premium-Tarif. Ein Cache wird als primärer verknüpfter Cache festgelegt und der andere als sekundärer verknüpfter Cache. Der sekundäre verknüpfte Cache ist schreibgeschützt. Und die in den primären Cache geschriebenen Daten werden im sekundären verknüpften Cache repliziert. Über diese Funktion kann ein Cache über verschiedene Azure-Regionen hinweg repliziert werden.

Weitere Informationen finden Sie unter [Konfigurieren der Georeplikation für Azure Cache for Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>So wechseln Sie zum Premium-Tarif
Um zum Premium-Tarif zu wechseln, wählen Sie einfach auf dem Blatt **Tarif ändern** einen der Premium-Tarife aus. Sie können für Ihren Cache auch mithilfe von PowerShell und CLI zum Premium-Tarif wechseln. Ausführliche Anweisungen finden Sie unter [Skalieren von Azure Cache for Redis](cache-how-to-scale.md) und [Automatisieren eines Skalierungsvorgangs](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nächste Schritte
Erstellen Sie einen Cache, und untersuchen Sie die neuen Features des "Premium"-Tarifs.

* [Konfigurieren von Persistenz für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-persistence.md)
* [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-vnet.md)
* [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md)
* [Importieren und Exportieren von Daten in Azure Cache for Redis](cache-how-to-import-export-data.md)
* [Verwalten von Azure Cache for Redis](cache-administration.md)

