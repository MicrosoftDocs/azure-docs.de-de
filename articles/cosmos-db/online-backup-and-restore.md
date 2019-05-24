---
title: Automatische Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB
description: In diesem Artikel wird beschrieben, wie die automatische Onlinesicherung und die bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB funktionieren.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 066549f1343eaceb9a47fccc3b5d4508f226a89b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967478"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB

Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Die automatischen Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit des Datenbankbetriebs. Alle Sicherungskopien werden in einem Speicherdienst getrennt gespeichert, und diese Sicherungen werden zum besseren Schutz vor regionalen Ausfällen global repliziert. Automatische Sicherungen sind in Situationen hilfreich, in denen Sie versehentlich Ihr Azure Cosmos-Konto, die Datenbank oder einen Container löschen oder aktualisieren und später eine Datenwiederherstellung durchführen möchten.

## <a name="automatic-and-online-backups"></a>Automatische Onlinesicherungen

Azure Cosmos DB sorgt nicht nur dafür, dass Ihre Daten, sondern auch die Sicherungen Ihrer Daten überaus redundant und gegen regionale Katastrophen geschützt sind. Die folgenden Schritte zeigen, wie Azure Cosmos DB eine Datensicherung ausführt:

* Azure Cosmos DB erstellt alle 4 Stunden automatisch eine Sicherung Ihrer Datenbank, und es werden immer nur die neuesten 2 Sicherungen gespeichert. Aber wenn der Container bzw. die Datenbank gelöscht wird, bewahrt Azure Cosmos DB die vorhandenen Momentaufnahmen des angegebenen Containers bzw. der Datenbank 30 Tage lang auf.

* Azure Cosmos DB speichert diese Sicherungen in Azure Blob-Speicher, während sich die eigentlichen Daten lokal in Azure Cosmos DB befinden.

*  Um niedrige Latenz zu gewährleisten, wird die Momentaufnahme Ihrer Sicherung in Azure Blob Storage in der Region gespeichert, in der sich auch die aktuelle Schreibregion Ihres Azure Cosmos-Datenbankkontos befindet (bzw. bei einer Multimaster-Konfiguration in einer der Schreibregionen). Zum besseren Schutz vor regionalen Katastrophen wird jede Momentaufnahme der Sicherungsdaten in Azure Blob Storage nochmals mithilfe von georedundantem Speicher (GRS) in eine andere Region repliziert. Die Region, in die die Sicherung repliziert wird, richtet sich nach Ihrer Quellregion und dem Regionspaar, das der Quellregion zugeordnet ist. Weitere Informationen finden Sie im Artikel mit der [Liste der georedundanten Azure-Regionspaare](../best-practices-availability-paired-regions.md). Sie können auf diese Sicherung nicht direkt zugreifen. Azure Cosmos DB verwendet diese Sicherung nur, wenn die Wiederherstellung einer Sicherung initiiert wird.

* Die Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit Ihrer Anwendungen. Azure Cosmos DB erstellt die Datensicherung im Hintergrund, ohne Ihren bereitgestellten Durchsatz (Anforderungseinheiten, RUs) zu beanspruchen bzw. die Leistung oder Verfügbarkeit Ihrer Datenbank zu beeinträchtigen.

* Wenn Sie Ihre Daten versehentlich gelöscht oder beschädigt haben, sollten Sie sich innerhalb von 8 Stunden an den [Azure-Support](https://azure.microsoft.com/support/options/) wenden, damit das Azure Cosmos DB-Team Sie beim Wiederherstellen der Daten aus den Sicherungen unterstützen kann.

Die folgende Abbildung zeigt, wie ein Azure Cosmos DB-Container (mit allen drei primären physischen Partitionen in „USA, Westen“) in einem Azure Blob Storage-Remotekonto in „USA, Westen“ gesichert und anschließend nach „USA, Osten“ repliziert wird:

![Regelmäßige vollständige Sicherungen aller Cosmos DB-Entitäten in georedundantem Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Optionen für das Verwalten Ihrer eigenen Sicherungen

Mithilfe von Azure Cosmos DB-SQL-API-Konten können Sie auch Ihre eigenen Sicherungen verwalten. Nutzen Sie dazu eine der folgenden Vorgehensweisen:

* Verwenden Sie [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) zum Verschieben von Daten in einen Speicher Ihrer Wahl in regelmäßigen Abständen.

* Verwenden Sie den [Änderungsfeed](change-feed.md) von Azure Cosmos DB, um Daten regelmäßig für eine vollständige Sicherung sowie für inkrementelle Änderungen zu lesen und in Ihrem eigenen Speicher zu speichern.

## <a name="backup-retention-period"></a>Aufbewahrungszeitraum der Sicherung

Azure Cosmos DB erstellt alle vier Stunden Momentaufnahmen Ihrer Daten. Es werden jeweils nur die letzten zwei Momentaufnahmen aufbewahrt. Aber wenn der Container bzw. die Datenbank gelöscht wird, bewahrt Azure Cosmos DB die vorhandenen Momentaufnahmen des angegebenen Containers bzw. der Datenbank 30 Tage lang auf.

## <a name="restoring-data-from-online-backups"></a>Wiederherstellen von Daten aus Onlinesicherungen

Das versehentliche Löschen oder Ändern von Daten kann in einem der folgenden Szenarien auftreten:  

* Das gesamte Azure Cosmos-Konto wird gelöscht.

* Eine oder mehrere Azure Cosmos-Datenbanken werden gelöscht.

* Ein oder mehrere Azure Cosmos-Container werden gelöscht.

* Azure Cosmos-Elemente (z.B. Dokumente) in einem Container werden gelöscht oder geändert. Dieser spezifische Fall wird in der Regel als „Datenbeschädigung“ bezeichnet.

* Eine freigegebene Datenbank oder Container in einer freigegebenen Datenbank werden gelöscht oder beschädigt.

Azure Cosmos DB können die Daten in allen oben genannten Szenarien wiederherstellen. Beim Wiederherstellungsprozess wird immer ein neues Azure Cosmos-Konto zum Speichern der wiederhergestellten Daten erstellt. Wenn kein Name für das neue Konto angegeben wird, hat dieser das Format `<Azure_Cosmos_account_original_name>-restored1`. Die letzte Ziffer wird erhöht, wenn mehrere Wiederherstellungen durchgeführt werden. Sie können keine Daten in einem vorab erstellten Azure Cosmos-Konto wiederherstellen.

Wenn ein Azure Cosmos-Konto gelöscht wird, können wir die Daten in einem Konto mit dem gleichen Namen wiederherstellen, vorausgesetzt, der Kontoname wird zurzeit nicht verwendet. In solchen Fällen wird empfohlen, das Konto nach dem Löschen nicht neu zu erstellen, da damit nicht nur verhindert wird, dass die wiederhergestellten Daten denselben Namen verwenden, sondern dies macht es auch schwieriger, das richtige Konto zum Wiederherstellen zu erkennen. 

Wenn eine Azure Cosmos-Datenbank gelöscht wird, ist es möglich, die gesamte Datenbank oder eine Teilmenge der Container innerhalb der Datenbank wiederherzustellen. Es ist auch möglich, Container aus mehreren Datenbanken auszuwählen und wiederherzustellen. Alle wiederhergestellten Daten werden dann in einem neuen Azure Cosmos-Konto gespeichert.

Wenn ein oder mehrere Elemente in einem Container versehentlich gelöscht oder geändert werden (der Fall der Datenbeschädigung), müssen Sie den Zeitpunkt angeben, der wiederhergestellt werden soll. Bei diesem Szenario ist Zeit der entscheidende Faktor. Da der Container aktiv ist, wird die Sicherung weiterhin ausgeführt. Wenn Sie also länger als für den Aufbewahrungszeitraum warten (der Standardwert ist acht Stunden), werden die Sicherungen überschrieben. Im Fall von Löschungen werden Ihre Daten nicht mehr gespeichert, da sie vom Sicherungszyklus nicht überschrieben werden. Sicherungen von gelöschten Datenbanken oder Containern werden 30 Tage lang gespeichert.

Wenn Sie den Durchsatz auf Datenbankebene bereitstellen (d.h., eine Gruppe von Containern teilt sich den bereitgestellten Durchsatz), erfolgen die Sicherungs- und Wiederherstellungsprozesse auf Ebene der gesamten Datenbank und nicht auf Ebene der einzelnen Container. In solchen Fällen ist das Auswählen einer Teilmenge der Containern für die Wiederherstellung nicht möglich.

## <a name="migrating-data-to-the-original-account"></a>Migrieren von Daten zum ursprünglichen Konto

Das Hauptziel der Datenwiederherstellung ist die Möglichkeit zum Wiederherstellen aller Daten, die Sie versehentlich löschen oder ändern. Es wird daher empfohlen, dass Sie zunächst den Inhalt der wiederhergestellten Daten untersuchen, um sicherzustellen, dass die gewünschten Daten enthalten sind. Führen Sie dann die Migration der Daten zum primären Konto durch. Es ist zwar möglich, das wiederhergestellte Konto als aktives Konto zu verwenden, dies wird für Produktionsworkloads jedoch nicht empfohlen.  

Im Folgenden werden verschiedene Möglichkeiten zum Migrieren von Daten zurück zum ursprünglichen Azure Cosmos-Konto beschrieben:

* Verwenden des [Datenmigrationstools von Cosmos DB](import-data.md)
* Verwenden von [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Verwenden des [Änderungsfeeds](change-feed.md) in Azure Cosmos DB 
* Schreiben von benutzerdefiniertem Code

Löschen Sie die wiederhergestellten Konten, sobald Sie mit der Migration fertig sind, da für diese laufende Gebühren anfallen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich als Nächstes darüber, wie Sie Daten aus einem Azure Cosmos-Konto wiederherstellen oder Daten zu einem Azure Cosmos-Konto migrieren.

* Wenn Sie eine Wiederherstellungsanforderung übermitteln möchten, kontaktieren Sie den Azure-Support, und [erstellen Sie im Azure-Portal ein Ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* [Wiederherstellung von Daten aus einem Azure Cosmos-Konto](how-to-backup-and-restore.md)
* [Verwenden des Änderungsfeeds von Cosmos DB](change-feed.md) zum Verschieben von Daten in Azure Cosmos DB
* [Verwenden von Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) zum Verschieben von Daten in Azure Cosmos DB

