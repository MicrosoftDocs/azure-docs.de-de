---
title: Konfigurieren eines Azure Cosmos DB-Kontos mit regelmäßiger Sicherung
description: In diesem Artikel wird beschrieben, wie Azure Cosmos DB-Konten mit regelmäßiger Sicherung und einem Sicherungsintervall konfiguriert werden. und Datenaufbewahrung. Außerdem wird erläutert, wie Sie den Support zum Wiederherstellen Ihrer Daten kontaktieren können.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 185320868c491d98df5fb6e31d9a627157431944
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527067"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Konfigurieren eines Azure Cosmos DB-Kontos mit regelmäßiger Sicherung
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Die automatischen Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit des Datenbankbetriebs. Alle Sicherungskopien werden in einem Speicherdienst getrennt gespeichert, und diese Sicherungen werden zum besseren Schutz vor regionalen Ausfällen global repliziert. Azure Cosmos DB sorgt nicht nur dafür, dass Ihre Daten, sondern auch die Sicherungen Ihrer Daten überaus redundant und gegen regionale Katastrophen geschützt sind. Die folgenden Schritte zeigen, wie Azure Cosmos DB eine Datensicherung ausführt:

* Azure Cosmos DB erstellt alle vier Stunden automatisch eine vollständige Sicherung Ihrer Datenbank, und es werden standardmäßig immer nur die neuesten beiden Sicherungen gespeichert. Wenn die Standardintervalle für Ihre Workloads nicht ausreichen, können Sie das Sicherungsintervall und den Aufbewahrungszeitraum im Azure-Portal ändern. Sie können die Konfiguration der Sicherung während oder nach der Erstellung des Azure Cosmos-Kontos ändern. Wenn der Container bzw. die Datenbank gelöscht wird, bewahrt Azure Cosmos DB die vorhandenen Momentaufnahmen des angegebenen Containers bzw. der Datenbank 30 Tage lang auf.

* Azure Cosmos DB speichert diese Sicherungen in Azure Blob-Speicher, während sich die eigentlichen Daten lokal in Azure Cosmos DB befinden.

* Um eine geringe Wartezeit zu gewährleisten, wird die Momentaufnahme Ihrer Sicherung in Azure Blob Storage in der Region gespeichert, in der sich auch die aktuelle Schreibregion befindet (bzw. bei einer Konfiguration für Schreibvorgänge in mehreren Regionen in **einer** der Schreibregionen). Zum besseren Schutz vor regionalen Katastrophen wird jede Momentaufnahme der Sicherungsdaten in Azure Blob Storage nochmals mithilfe von georedundantem Speicher (GRS) in eine andere Region repliziert. Die Region, in die die Sicherung repliziert wird, richtet sich nach Ihrer Quellregion und dem Regionspaar, das der Quellregion zugeordnet ist. Weitere Informationen finden Sie im Artikel mit der [Liste der georedundanten Azure-Regionspaare](../best-practices-availability-paired-regions.md). Sie können auf diese Sicherung nicht direkt zugreifen. Das Team von Azure Cosmos DB stellt Ihre Sicherung wieder her, wenn Sie dies über eine Supportanfrage anfordern.

  Die folgende Abbildung zeigt, wie ein Azure Cosmos DB-Container (mit allen drei primären physischen Partitionen in „USA, Westen“) in einem Azure Blob Storage-Remotekonto in „USA, Westen“ gesichert und anschließend nach „USA, Osten“ repliziert wird:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Regelmäßige vollständige Sicherungen aller Cosmos DB-Entitäten in georedundantem Azure Storage." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Die Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit Ihrer Anwendungen. Azure Cosmos DB erstellt die Datensicherung im Hintergrund, ohne zusätzlichen bereitgestellten Durchsatz (Anforderungseinheiten, RUs) zu beanspruchen oder die Leistung und Verfügbarkeit Ihrer Datenbank zu beeinträchtigen.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Ändern des Sicherungsintervalls und des Aufbewahrungszeitraums

Von Azure Cosmos DB wird alle vier Stunden automatisch eine vollständige Sicherung Ihrer Daten erstellt, und es sind immer die beiden neuesten Sicherungen gespeichert. Diese Konfiguration ist die Standardoption, die ohne Zusatzkosten angeboten wird. Sie können das Standardsicherungsintervall und den Aufbewahrungszeitraum während der Erstellung des Azure Cosmos-Kontos oder nach dem Erstellen des Kontos ändern. Die Sicherungskonfiguration wird auf der Ebene des Azure Cosmos-Kontos festgelegt, und Sie müssen sie für jedes Konto vornehmen. Nachdem Sie die Sicherungsoptionen für ein Konto konfiguriert haben, wird es auf alle Container innerhalb dieses Kontos angewandt. Derzeit können Sie die Sicherungsoptionen nur im Azure-Portal ändern.

Wenn Sie Ihre Daten versehentlich gelöscht oder beschädigt haben, stellen Sie **vor dem Erstellen einer Supportanfrage zum Wiederherstellen der Daten sicher, den Aufbewahrungszeitraum für Ihr Konto auf mindestens sieben Tage zu erhöhen. Es empfiehlt sich, den Aufbewahrungszeitraum innerhalb von 8 Stunden nach diesem Ereignis zu erhöhen.** Auf diese Weise hat das Azure Cosmos DB-Team genug Zeit, um Ihr Konto wiederherzustellen.

Gehen Sie anhand der folgenden Schritte vor, um die Standardsicherungsoptionen für ein vorhandenes Azure Cosmos-Konto zu ändern:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie den Bereich **Sichern und Wiederherstellen**. Aktualisieren Sie das Sicherungsintervall und den Aufbewahrungszeitraum für Sicherungen nach Bedarf.

   * **Sicherungsintervall:** das Intervall, in dem Azure Cosmos DB versucht, eine Sicherung der Daten zu erstellen. Die Sicherung nimmt immer eine gewisse Zeit in Anspruch und kann in einigen Fällen aufgrund von Downstreamabhängigkeiten möglicherweise zu einem Fehler führen. Azure Cosmos DB versucht, eine Sicherung im konfigurierten Intervall zu erstellen, es kann jedoch nicht garantiert werden, dass die Sicherung innerhalb dieses Zeitintervalls abgeschlossen wird. Sie können diesen Wert in Stunden oder Minuten konfigurieren. Das Sicherungsintervall darf nicht kürzer als 1 Stunde und nicht länger als 24 Stunden sein. Wenn Sie dieses Intervall ändern, wird das neue Intervall ab dem Zeitpunkt wirksam, zu dem die letzte Sicherung erstellt wurde.

   * **Beibehaltung der Sicherung:** Dies stellt den Aufbewahrungszeitraum für die einzelnen Sicherungen dar. Sie können die Zeit in Stunden oder Tagen konfigurieren. Der minimale Aufbewahrungszeitraum darf nicht kürzer als das Zweifache des Sicherungsintervalls (in Stunden) und nicht länger als 720 Stunden sein.

   * **Aufbewahrte Kopien der Daten:** Standardmäßig werden zwei Sicherungskopien Ihrer Daten kostenlos angeboten. Wenn Sie mehr als zwei Kopien benötigen, fallen zusätzliche Gebühren an. Im Abschnitt „Speichernutzung“ auf der Seite [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/) erfahren Sie den genauen Preis für zusätzliche Kopien.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Konfigurieren des Sicherungsintervalls und Aufbewahrungszeitraums für ein vorhandenes Azure Cosmos-Konto." border="true":::

Wenn Sie während der Kontoerstellung Sicherungsoptionen konfigurieren, können Sie die **Sicherungsrichtlinie** konfigurieren, die **regelmäßig** oder **fortlaufend** sein kann. Bei einer Richtlinie für regelmäßige Sicherungen können Sie das Sicherungsintervall und den Aufbewahrungszeitraum für Sicherungen konfigurieren. Die Richtlinie für fortlaufende Sicherungen ist zurzeit nur bei der Registrierung verfügbar. Das Team von Azure Cosmos DB bewertet Ihre Workload und genehmigt Ihre Anforderung.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Konfigurieren einer Richtlinie für regelmäßige oder fortlaufende Sicherungen für neue Azure Cosmos-Konten." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Anfordern der Datenwiederherstellung aus einer Sicherung

Falls Sie Ihre Datenbank oder einen Container versehentlich löschen, können Sie ein [Supportticket erstellen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oder [sich an den Azure Support wenden](https://azure.microsoft.com/support/options/), um die Daten aus automatischen Onlinesicherungen wiederherstellen zu lassen. Der Azure-Support steht nur in ausgewählten Tarifen wie **Standard** und **Developer** sowie in höheren Tarifen zur Verfügung. Für den **Basic**-Tarif ist kein Azure-Support verfügbar. Weitere Informationen zu anderen Supportplänen finden Sie auf der Seite [Azure-Supportpläne](https://azure.microsoft.com/support/plans/).

Für die Wiederherstellung einer bestimmten Momentaufnahme der Sicherung setzt Azure Cosmos DB voraus, dass die Daten während des Sicherungszyklus für diese Momentaufnahme zur Verfügung stehen.
Sie benötigen für das Anfordern einer Wiederherstellung die folgenden Informationen:

* Halten Sie Ihre Abonnement-ID bereit.

* Basierend darauf, wie Ihre Daten versehentlich gelöscht oder geändert wurden, sollten Sie ggf. weitere Informationen bereithalten. Es wird empfohlen, die Informationen vorab zusammenzustellen, um die Kommunikation zu minimieren, die sich bei zeitkritischen Fällen nachteilig auswirken kann.

* Wenn das gesamte Azure Cosmos DB-Konto gelöscht wurde, müssen Sie den Namen des gelöschten Kontos angeben. Wenn Sie ein anderes Konto mit dem gleichen Namen wie das gelöschte Konto erstellen, geben Sie dies dem Supportteam an, da es dabei hilft, das richtige Konto auszuwählen. Es wird empfohlen, für jedes gelöschte Konto unterschiedliche Supporttickets zu erstellen, weil dadurch Irritationen im Hinblick auf den Status der Wiederherstellung minimiert werden.

* Wenn eine oder mehrere Datenbanken gelöscht werden, sollten Sie das Azure Cosmos-Konto und die Namen der Azure Cosmos-Datenbanken angeben sowie darüber informieren, ob eine neue Datenbank mit demselben Namen vorhanden ist.

* Wenn Container gelöscht wurden, sollten Sie den Namen des Azure Cosmos-Kontos, die Datenbanknamen und die Containernamen bereitstellen. Geben Sie auch an, ob ein Container mit demselben Namen vorhanden ist.

* Wenn Sie Ihre Daten versehentlich gelöscht oder beschädigt haben, sollten Sie sich innerhalb von 8 Stunden an den [Azure-Support](https://azure.microsoft.com/support/options/) wenden, damit das Azure Cosmos DB-Team Sie beim Wiederherstellen der Daten aus den Sicherungen unterstützen kann. **Bevor Sie eine Supportanfrage zum Wiederherstellen der Daten erstellen, müssen Sie [die Sicherungsaufbewahrung](#configure-backup-interval-retention) für Ihr Konto auf mindestens sieben Tage erhöhen. Am besten ist es, den Aufbewahrungszeitraum innerhalb von 8 Stunden nach diesem Ereignis zu erhöhen.** Auf diese Weise hat das Supportteam von Azure Cosmos DB genug Zeit zum Wiederherstellen Ihres Kontos.

Zusätzlich zum Namen des Azure Cosmos-Kontos, den Datenbanknamen und den Containernamen sollten Sie den Zeitpunkt angeben, zu dem die Daten wiederhergestellt werden können. Es ist wichtig, dabei so präzise wie möglich zu sein, damit wir die besten verfügbaren Sicherungen für diesen Zeitpunkt bestimmen können. **Es ist auch wichtig, die Uhrzeit in UTC anzugeben.**

Der folgende Screenshot veranschaulicht das Erstellen einer Supportanfrage für einen Container (Sammlung/Graph/Tabelle) zum Wiederherstellen von Daten mithilfe des Azure-Portals. Geben Sie weitere Details wie z. B. den Datentyp, den Zweck der Wiederherstellung und den Zeitpunkt der Datenlöschung an, damit wir die Anforderung priorisieren können.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Erstellen einer Supportanforderung für Sicherung über das Azure-Portal." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Überlegungen zum Wiederherstellen der Daten aus einer Sicherung

In den folgenden Szenarien werden Ihre Daten möglicherweise versehentlich gelöscht oder geändert:  

* Das gesamte Azure Cosmos-Konto wird gelöscht.

* Azure Cosmos-Datenbanken werden gelöscht.

* Ein oder mehrere Azure Cosmos-Container werden gelöscht.

* Azure Cosmos-Elemente (z. B. Dokumente) in einem Container werden gelöscht oder geändert. Dieser spezifische Fall wird in der Regel als „Datenbeschädigung“ bezeichnet.

* Eine freigegebene Datenbank oder Container in einer freigegebenen Datenbank werden gelöscht oder beschädigt.

Azure Cosmos DB können die Daten in allen oben genannten Szenarien wiederherstellen. Beim Wiederherstellen wird immer ein neues Azure Cosmos-Konto zum Speichern der wiederhergestellten Daten erstellt. Wenn kein Name für das neue Konto angegeben wird, hat dieser das Format `<Azure_Cosmos_account_original_name>-restored1`. Die letzte Ziffer wird nach und nach erhöht, wenn mehrere Wiederherstellungen durchgeführt werden. Sie können keine Daten in einem vorab erstellten Azure Cosmos-Konto wiederherstellen.

Wenn Sie ein Azure Cosmos-Konto versehentlich löschen, können wir die Daten in einem neuen Konto mit demselben Namen wiederherstellen, sofern der Kontoname zurzeit nicht verwendet wird. Daher wird empfohlen, das Konto nach dem Löschen nicht neu zu erstellen. Damit wird nicht nur verhindert, dass die wiederhergestellten Daten denselben Namen erhalten, sondern es macht es auch schwieriger, das richtige Konto zum Wiederherstellen zu erkennen.

Wenn eine Azure Cosmos-Datenbank gelöscht wird, ist es möglich, die gesamte Datenbank oder eine Teilmenge der Container innerhalb der Datenbank wiederherzustellen. Es ist auch möglich, bestimmte Container aus mehreren Datenbanken auszuwählen und in einem neuen Azure Cosmos-Konto wiederherzustellen.

Wenn Elemente in einem Container versehentlich gelöscht oder geändert werden (der Fall der Datenbeschädigung), müssen Sie den Zeitpunkt angeben, der wiederhergestellt werden soll. Die Zeit ist bei einer Datenbeschädigung sehr wichtig. Da der Container aktiv ist, wird die Sicherung weiterhin ausgeführt. Wenn Sie also länger als den Aufbewahrungszeitraum warten (der Standardwert ist acht Stunden), werden die Sicherungen überschrieben. Um zu verhindern, dass die Sicherung überschrieben wird, erhöhen Sie den Aufbewahrungszeitraum der Sicherung für Ihr Konto auf mindestens sieben Tage. Es empfiehlt sich, den Aufbewahrungszeitraum innerhalb von acht Stunden nach der Datenbeschädigung zu erhöhen.

Wenn Sie Ihre Daten versehentlich gelöscht oder beschädigt haben, sollten Sie sich innerhalb von 8 Stunden an den [Azure-Support](https://azure.microsoft.com/support/options/) wenden, damit das Azure Cosmos DB-Team Sie beim Wiederherstellen der Daten aus den Sicherungen unterstützen kann. Auf diese Weise hat das Supportteam von Azure Cosmos DB genug Zeit, Ihr Konto wiederherzustellen.

> [!NOTE]
> Nachdem Sie die Daten wiederhergestellt haben, werden nicht alle Quellfunktionen oder -einstellungen auf das wiederhergestellte Konto übertragen. Die folgenden Einstellungen werden nicht auf das neue Konto übertragen:
> * VNET-Zugriffssteuerungslisten
> * Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen
> * Einstellungen für mehrere Regionen  

Wenn Sie den Durchsatz auf Datenbankebene bereitstellen, erfolgen die Sicherungs- und Wiederherstellungsprozesse auf der Ebene der gesamten Datenbank und nicht der einzelnen Container. In solchen Fällen können Sie keine Teilmenge der Container für die Wiederherstellung auswählen.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Erforderliche Berechtigungen zum Ändern der Aufbewahrung oder Wiederherstellung über das Portal
Prinzipale, die zur Rolle [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator) gehören, Besitzer oder Mitwirkende dürfen eine Wiederherstellung anfordern oder den Aufbewahrungszeitraum ändern.

## <a name="understanding-costs-of-extra-backups"></a>Grundlegendes zu den Kosten zusätzlicher Sicherungen
Zwei Sicherungen werden kostenlos bereitgestellt. Zusätzliche Sicherungen werden zu den regionsbasierten Preisen für den Sicherungsspeicher berechnet, die unter den [Preisen für Sicherungsspeicher](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/) beschrieben werden. Ein Beispiel: Die Aufbewahrungsdauer für Sicherungen ist auf 240 Stunden (10 Tage) und das Sicherungsintervall auf 24 Stunden festgelegt. Dies ergibt zehn Kopien der Sicherungsdaten. Bei 1 TB Daten in der Region „USA, Westen 2“ bedeutet das 1000 * 0,12 ~ 120 USD für den Sicherungsspeicher im Monat. 


## <a name="options-to-manage-your-own-backups"></a>Optionen für das Verwalten Ihrer eigenen Sicherungen

Mithilfe von Azure Cosmos DB-SQL-API-Konten können Sie auch Ihre eigenen Sicherungen verwalten. Nutzen Sie dazu eine der folgenden Vorgehensweisen:

* Verwenden Sie [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) zum Verschieben von Daten in einen Speicher Ihrer Wahl in regelmäßigen Abständen.

* Verwenden Sie den [Änderungsfeed](change-feed.md) von Azure Cosmos DB, um Daten regelmäßig für eine vollständige Sicherung oder für inkrementelle Änderungen zu lesen und in Ihrem eigenen Speicher zu speichern.

## <a name="post-restore-actions"></a>Aktionen nach der Wiederherstellung

Das Hauptziel der Datenwiederherstellung ist die Wiederherstellung der Daten, die Sie versehentlich gelöscht oder geändert haben. Es wird daher empfohlen, dass Sie zunächst den Inhalt der wiederhergestellten Daten untersuchen, um sicherzustellen, dass die gewünschten Daten enthalten sind. Wenn alles gut aussieht, können Sie die Daten zurück zum primären Konto migrieren. Es ist zwar möglich, das wiederhergestellte Konto als neues aktives Konto zu verwenden, dies wird für Produktionsworkloads jedoch nicht empfohlen. 

Nachdem die Daten wiederhergestellt wurden, erhalten Sie eine Benachrichtigung über den Namen des neuen Kontos (in der Regel im Format `<original-name>-restored1`) und der Uhrzeit, zu der das Konto wiederhergestellt wurde. Das wiederhergestellte Konto verfügt den gleichen bereitgestellten Durchsatz und dieselben Richtlinien für die Indizierung, und es befindet sich in derselben Region wie das ursprüngliche Konto. Ein Benutzer, der Abonnementadministrator oder Co-Administrator ist, kann das wiederhergestellte Konto anzeigen.

### <a name="migrate-data-to-the-original-account"></a>Migrieren von Daten zum ursprünglichen Konto

Im Folgenden werden verschiedene Möglichkeiten zum Migrieren von Daten zurück zum ursprünglichen Konto beschrieben:

* Mithilfe des [Azure Cosmos DB-Datenmigrationstools](import-data.md)
* Mithilfe von [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)
* Mithilfe des [Änderungsfeeds](change-feed.md) in Azure Cosmos DB
* Durch Schreiben von eigenem benutzerdefiniertem Code

Es wird empfohlen, den Container oder die Datenbank sofort nach der Migration zu löschen. Wenn Sie die wiederhergestellten Datenbanken oder Container nicht löschen, entstehen Kosten für Anforderungseinheiten, Speicherung und Erfassung.

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie eine Wiederherstellungsanforderung übermitteln möchten, kontaktieren Sie den Azure-Support, und [erstellen Sie im Azure-Portal ein Ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Konfigurieren und verwalten Sie die fortlaufende Sicherung über das [Azure-Portal](continuous-backup-restore-portal.md) oder aber mithilfe von [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.