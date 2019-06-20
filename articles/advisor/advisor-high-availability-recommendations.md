---
title: Verbessern der Verfügbarkeit Ihrer Anwendung mit Azure Advisor | Microsoft-Dokumentation
description: Mithilfe von Azure Advisor können Sie die Hochverfügbarkeit Ihrer Azure-Bereitstellungen sicherstellen.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdba3f135f852312af1692f77643095d865f1d06
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254666"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Verbessern der Verfügbarkeit Ihrer Anwendung mit Azure Advisor

Der Azure Advisor hilft Ihnen, die ununterbrochene Verfügbarkeit Ihrer unternehmenskritischen Anwendungen zu gewährleisten und zu verbessern. Empfehlungen für Hochverfügbarkeit von Advisor erhalten Sie auf dem Advisor-Dashboard auf der Registerkarte **Hochverfügbarkeit**.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Sicherstellen von Fehlertoleranz für virtuelle Computer

Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Advisor identifiziert virtuelle Computer, die nicht zu einer Verfügbarkeitsgruppe gehören und empfiehlt, diese in eine Verfügbarkeitsgruppe zu verschieben. Durch diese Konfiguration wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die Azure-SLA für virtuelle Computer eingehalten wird. Sie können eine Verfügbarkeitsgruppe für den virtuellen Computer erstellen oder diesen einer vorhandenen Verfügbarkeitsgruppe hinzufügen.

> [!NOTE]
> Wenn Sie eine Verfügbarkeitsgruppe erstellen möchten, müssen Sie ihr mindestens einen weiteren virtuellen Computer hinzufügen. Wir empfehlen das Gruppieren von mindestens zwei virtuellen Computern in einer Verfügbarkeitsgruppe, um sicherzustellen, dass mindestens ein Computer bei einem Ausfall verfügbar ist.

## <a name="ensure-availability-set-fault-tolerance"></a>Sicherstellen von Fehlertoleranz für Verfügbarkeitsgruppen

Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Der Advisor ermittelt Verfügbarkeitsgruppen mit nur einem virtuellen Computer und empfiehlt, dieser weitere hinzuzufügen. Durch diese Konfiguration wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die Azure-SLA für virtuelle Computer eingehalten wird. Sie können einen virtuellen Computer erstellen oder der Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzufügen.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Verwenden von Managed Disks zur Erhöhung der Datenzuverlässigkeit

Virtuelle Computer, die sich in einer Verfügbarkeitsgruppe mit Datenträgern befinden, die entweder Speicherkonten oder Speicherskalierungseinheiten gemeinsam nutzen, sind nicht widerstandsfähig gegen Ausfälle einzelner Speicherskalierungseinheiten während eines Ausfalls. Advisor identifiziert diese Verfügbarkeitsgruppen und empfehlen die Migration zu Azure Managed Disks. Dadurch wird sichergestellt, dass die Datenträger der verschiedenen virtuellen Computer in der Verfügbarkeitsgruppe ausreichend isoliert sind, um einen Single Point of Failure zu vermeiden. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Sicherstellen von Fehlertoleranz für Anwendungsgateways

Bei Einhaltung dieser Empfehlung wird die Geschäftskontinuität unternehmenskritischer Anwendungen sichergestellt, die von Anwendungsgateways unterstützt werden. Advisor identifiziert die Anwendungsgatewayinstanzen, die nicht für die Fehlertoleranz konfiguriert sind, und schlägt Aktionen vor, die Sie zur Problembehebung ausführen können. Der Advisor identifiziert mittelgroße oder große Einzelinstanz-Anwendungsgateways und empfiehlt, mindestens eine weitere Instanz hinzufügen. Identifiziert außerdem kleine Einzel- und Mehrinstanz-Anwendungsgateways und empfiehlt die Migration zu mittelgroßen oder großen SKUs. Der Advisor empfiehlt diese Aktionen, um sicherzustellen, dass Ihre Anwendungsgatewayinstanzen so konfiguriert sind, dass sie die aktuellen SLA-Anforderungen für diese Ressourcen erfüllen.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Schützen Ihrer VM-Daten vor zufälligem Löschen

Durch das Einrichten der Sicherung eines virtuellen Computers wird die Verfügbarkeit Ihrer geschäftskritischen Daten sichergestellt, und Ihre Daten werden vor versehentlichem Löschen oder vor Beschädigung geschützt. Der Advisor identifiziert virtuelle Computer, auf denen die Sicherung nicht aktiviert ist, und empfiehlt das Aktivieren der Sicherung. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Sicherstellen, dass Sie bei Bedarf Zugang zu Azure-Cloudexperten haben

Bei unternehmenskritischen Workloads ist es wichtig, im Bedarfsfall Zugang zu technischem Support zu haben. Der Advisor ermittelt potenziell unternehmenskritische Abonnements, deren Supportplan keinen technischen Support enthält, und empfiehlt ein Upgrade auf eine Option mit technischem Support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Erstellen von Azure Service Health-Warnungen für Benachrichtigungen bei Azure-Dienstproblemen

Es wird empfohlen, Azure Service Health-Warnungen einzurichten, um über Azure-Dienstprobleme benachrichtigt zu werden. [Azure Service Health](https://azure.microsoft.com/features/service-health/) ist ein kostenloser Dienst, der personalisierte Anleitungen und Unterstützung bietet, wenn Sie von einem Azure-Dienstproblem betroffen sind. Advisor ermittelt Abonnements, für die keine Warnungen konfiguriert sind, und empfiehlt, solche zu erstellen.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurieren von Traffic Manager-Endpunkten für Resilienz

Traffic Manager-Profile mit mehr als einem Endpunkt haben eine höhere Verfügbarkeit, wenn ein bestimmter Endpunkt ausfällt. Das Platzieren von Endpunkten in verschiedenen Regionen verbessert die Dienstzuverlässigkeit noch mehr. Advisor ermittelt Traffic Manager-Profile mit nur einem Endpunkt und empfiehlt, mindestens einen weiteren Endpunkt in einer anderen Region hinzuzufügen.

Wenn sich alle Endpunkte in einem Traffic Manager-Profil, das für das Näherungsrouting konfiguriert ist, in derselben Region befinden, kann es bei Benutzern in anderen Regionen zu Verbindungsverzögerungen kommen. Das Hinzufügen oder Verschieben eines Endpunkts in eine andere Region verbessert die Gesamtleistung und bietet eine höhere Verfügbarkeit, wenn alle Endpunkte in einer Region ausfallen. Advisor identifiziert für das Näherungsrouting konfigurierte Traffic Manager-Profile, bei denen sich alle Endpunkte in derselben Region befinden. Advisor empfiehlt das Hinzufügen oder Verschieben eines Endpunkts in eine andere Azure-Region.

Wenn ein Traffic Manager-Profil für das geografische Routing konfiguriert ist, wird der Datenverkehr basierend auf definierten Regionen an die Endpunkte weitergeleitet. Wenn eine Region ausfällt, ist kein vordefiniertes Failover verfügbar. Durch das Konfigurieren eines Endpunkts mit der regionalen Gruppierung „Alle (Welt)“ wird das Verwerfen von Datenverkehr vermieden und die Dienstverfügbarkeit verbessert. Advisor identifiziert Traffic Manager-Profile, die für das geografische Routing konfiguriert sind und keinen Endpunkt haben, der mit der regionalen Gruppierung „Alle (Welt)“ konfiguriert ist. Advisor empfiehlt, die Konfiguration zu ändern, sodass ein Endpunkt mit „Alle (Welt)“ konfiguriert ist.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Speichern und Wiederherstellen von Daten nach versehentlichem Überschreiben oder Löschen durch vorläufiges Löschen in Ihrem Azure Storage-Konto

Aktivieren Sie [vorläufiges Löschen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) in Ihrem Speicherkonto, sodass gelöschte Blobs in einen vorläufig gelöschten Zustand übergehen, anstatt endgültig gelöscht zu werden. Wenn Daten überschrieben werden, wird eine vorläufig gelöschte Momentaufnahme generiert, um den Zustand der überschriebenen Daten zu speichern. Durch vorläufiges Löschen können Sie Daten wiederherstellen, wenn diese versehentlich gelöscht oder überschrieben wurden. Advisor identifiziert Azure Storage-Konten, bei denen das vorläufige Löschen deaktiviert ist, und schlägt vor, es zu aktivieren.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurieren des VPN-Gateway zu aktiv-aktiv für Verbindungsresilienz

In der Aktiv-Aktiv-Konfiguration richten beide Instanzen eines VPN-Gateways S2S-VPN-Tunnel zu Ihrem lokalen VPN-Gerät ein. Wenn ein geplantes Wartungsereignis oder ein ungeplantes Ereignis mit einer Gateway-Instanz eintritt, wird der Datenverkehr automatisch auf den anderen aktiven IPsec-Tunnel umgeschaltet. Azure Advisor identifiziert VPN-Gateways, die nicht als aktiv-aktiv konfiguriert sind, und schlägt vor, dass Sie sie für Hochverfügbarkeit konfigurieren.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Verwenden Sie zum Ausführen Ihrer Produktionsworkloads Produktions-VPN-Gateways.

Azure Advisor überprüft alle VPN-Gateways, die eine Basic-SKU sind, und empfiehlt, stattdessen eine Produktions-SKU zu verwenden. Die Basic-SKU kann zu Entwicklungs- und Testzwecken eingesetzt werden. Produktions-SKUs bieten eine höhere Anzahl von Tunneln, BGP-Unterstützung, Aktiv-Aktiv-Konfigurationsoptionen, kundenspezifische Ipsec-/IKE-Richtlinien sowie höhere Stabilität und Verfügbarkeit.

## <a name="repair-invalid-log-alert-rules"></a>Reparieren ungültiger Protokollwarnungsregeln

Azure Advisor erkennt Warnungsregeln mit ungültigen Abfragen im Bedingungsabschnitt. Protokollwarnungsregeln werden in Azure Monitor erstellt und verwendet, um in regelmäßigen Abständen Analytics-Abfragen durchzuführen. Anhand der Ergebnisse der Abfrage wird ermittelt, ob eine Warnung ausgelöst werden muss. Es kann vorkommen, dass Analytics-Abfragen aufgrund von Änderungen an referenzierten Ressourcen, Tabellen oder Befehlen im Laufe der Zeit ungültig werden. Von Advisor wird empfohlen, die Abfrage in der Warnungsregel zu korrigieren, um eine automatische Deaktivierung zu verhindern und die Überwachung Ihrer Ressourcen in Azure sicherzustellen. Weitere Informationen zur Behandlung von Problemen mit Warnungsregeln finden Sie [hier](https://aka.ms/aa_logalerts_queryrepair).

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurieren eines konsistenten Indizierungsmodus für Ihre Cosmos DB-Sammlung

Azure Cosmos DB-Container, die mit dem Indizierungsmodus „Verzögert“ konfiguriert sind, beeinträchtigen unter Umständen die Aktualität der Abfrageergebnisse. Advisor erkennt Container mit dieser Konfiguration und empfiehlt, den Modus in „Konsistent“ zu ändern. Weitere Informationen zu Indizierungsrichtlinien in Cosmos DB finden Sie [hier](https://aka.ms/cosmosdb/how-to-manage-indexing-policy).

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurieren Ihres Azure Cosmos DB-Containers mit einem Partitionsschlüssel

Azure Advisor erkennt nicht partitionierte Azure Cosmos DB-Sammlungen, deren bereitgestelltes Speicherkontingent nahezu erschöpft ist. Es wird empfohlen, diese Sammlungen zu neuen Sammlungen mit einer Partitionsschlüsseldefinition zu migrieren, um die automatische Erweiterung durch den Dienst zu ermöglichen. Weitere Informationen zur Wahl eines Partitionsschlüssels finden Sie [hier](https://aka.ms/cosmosdb/choose-partitionkey).

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgraden Ihres Azure Cosmos DB .NET SDK auf die aktuelle Version über NuGet

Azure Advisor erkennt Azure Cosmos DB-Konten mit alten Versionen des .NET SDK und empfiehlt, über NuGet ein Upgrade auf die aktuelle Version auszuführen, um die neuesten Korrekturen, Leistungsverbesserungen und Features zu erhalten. Weitere Informationen zum Cosmos DB .NET SDK finden Sie [hier](https://aka.ms/cosmosdb/sql-api-sdk-dotnet).

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgraden Ihres Azure Cosmos DB Java SDK auf die aktuelle Version über Maven

Azure Advisor erkennt Azure Cosmos DB-Konten mit alten Versionen des Java SDK und empfiehlt, über Maven ein Upgrade auf die aktuelle Version auszuführen, um die neuesten Korrekturen, Leistungsverbesserungen und Features zu erhalten. Weitere Informationen zum Cosmos DB Java SDK finden Sie [hier](https://aka.ms/cosmosdb/sql-api-sdk-dotnet).

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Upgraden Ihres Azure Cosmos DB-Spark-Connectors auf die aktuelle Version über Maven

Azure Advisor erkennt Azure Cosmos DB-Konten mit alten Versionen des Cosmos DB-Spark-Connectors und empfiehlt, über Maven ein Upgrade auf die aktuelle Version auszuführen, um die neuesten Korrekturen, Leistungsverbesserungen und Features zu erhalten. Weitere Informationen zum Cosmos DB-Spark-Connector finden Sie [hier](https://aka.ms/cosmosdb/spark-connector).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Zugreifen auf Advisor-Empfehlungen zu Hochverfügbarkeit

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicken Sie auf dem Advisor-Dashboard auf die Registerkarte **Hochverfügbarkeit**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Advisor-Empfehlungen finden Sie unter:
* [Einführung in Azure Advisor](advisor-overview.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Empfehlungen zu Kosten](advisor-cost-recommendations.md)
* [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md)
* [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md)

