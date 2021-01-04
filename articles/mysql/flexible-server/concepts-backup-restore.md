---
title: Sicherung und Wiederherstellung in Azure Database for MySQL Flexible Server
description: Erfahren Sie mehr über die Konzepte der Sicherung und Wiederherstellung mit Azure Database for MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240765"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Sicherung und Wiederherstellung in Azure Database for MySQL Flexible Server (Vorschau)

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server erstellt automatisch Serversicherungen und speichert diese sicher in lokalen redundanten Speichern innerhalb der Azure-Region. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

## <a name="backup-overview"></a>Übersicht über Azure Backup

Flexible Server erstellt Momentaufnahmensicherungen der Datendateien und speichert sie in einem lokalen redundanten Speicher. Der Server führt auch eine Sicherung der Transaktionsprotokolle durch und speichert diese ebenfalls in einem lokalen redundanten Speicher. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Sie können die Sicherung der Datenbank optional von 1 bis 35 Tagen konfigurieren. Alle Sicherungen werden mithilfe der AES 256-Bit-Verschlüsselung für die ruhenden Daten verschlüsselt.

Diese Sicherungsdateien können nicht exportiert werden. Die Sicherungen können nur für Wiederherstellungsvorgänge in Flexible Server verwendet werden. Sie können auch [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) von einem MySQL-Client aus verwenden, um eine Datenbank zu kopieren.

## <a name="backup-frequency"></a>Sicherungshäufigkeit

Sicherungen auf flexiblen Servern basieren auf Momentaufnahmen. Die erste vollständige Momentaufnahmensicherung wird unmittelbar nach der Erstellung des Servers eingeplant. Diese erste vollständige Momentaufnahmensicherung wird als Basissicherung des Servers beibehalten. Nachfolgende Momentaufnahmensicherungen sind nur differenzielle Sicherungen.

Differentielle Momentaufnahmesicherungen werden mindestens einmal täglich erstellt. Differenzielle Momentaufnahmensicherungen erfolgen nicht nach einem festgelegten Zeitplan. Differenzielle Momentaufnahmesicherungen werden alle 24 Stunden ausgeführt, sofern die binären Protokolle in MySQL seit der letzten differenziellen Sicherung die Größe von 50 GB nicht überschreiten. An einem Tag sind maximal sechs differenzielle Momentaufnahmen zulässig. Transaktionsprotokollsicherungen finden alle fünf Minuten statt.

## <a name="backup-retention"></a>Sicherungsaufbewahrung

Datenbanksicherungen werden in einem lokal redundanten Speicher gespeichert, der innerhalb einer Region in drei Kopien gespeichert wird. Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung auf dem Server beibehalten. Sie können einen Aufbewahrungszeitraum von 1 bis 35 Tagen auswählen, wobei die standardmäßige Beibehaltungsdauer sieben Tage beträgt. Sie können den Aufbewahrungszeitraum bei der Servererstellung oder später festlegen, indem Sie die Sicherungskonfiguration mithilfe des Azure-Portals aktualisieren.

Der Aufbewahrungszeitraum für Sicherungen legt fest, wie weit zurück in der Zeit ein Zeitpunktwiederherstellungsvorgang durchgeführt werden kann, da er auf verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher beibehalten. Wenn der Aufbewahrungszeitraum für Sicherungen z. B. auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster einer Dauer von sieben Tagen. In diesem Szenario bleiben alle Sicherungen erhalten, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. Bei einem Fenster für die Aufbewahrung von Sicherungen von sieben Tagen werden Datenbankmomentaufnahmen und Transaktionsprotokollsicherungen für die letzten acht Tage (ein Tag vor dem Fenster) gespeichert.

## <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitgestellt haben, verfügen Sie über 250 GB an Speicher, der ohne zusätzliche Kosten für Serversicherungen zur Verfügung steht. Wenn die tägliche Sicherheitsauslastung 25 GB beträgt, können Sie für bis zu 10 Tage über kostenlosen Sicherungsspeicher verfügen. Der für Sicherungen verwendete Speicher über 250 GB wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mysql/) abgerechnet.

Sie können die im Azure-Portal in Azure Monitor verfügbare Metrik für den [verwendeten Sicherungsspeicher](../concepts-monitoring.md) zum Überwachen des von einem Server genutzten Sicherungsspeichers verwenden. Die Metrik für den belegten **Sicherungsspeicher** stellt den gesamten Speicherplatz dar, der von allen Datenbank- und Protokollsicherungen beansprucht wurde, die auf Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden. Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt.

Das primäre Mittel zur Kostenkontrolle bei der Speicherung von Sicherungen ist die Festlegung eines angemessenen Aufbewahrungszeitraums für Sicherungen. Sie können einen Aufbewahrungszeitraum zwischen 1 und 35 Tagen auswählen.

> [!IMPORTANT]
> Sicherungen von einem Datenbankserver, der in einer zonenredundanten Hochverfügbarkeitskonfiguration konfiguriert ist, erfolgen vom primären Datenbankserver, da der Mehraufwand bei Momentaufnahmensicherungen minimal ist.

> [!IMPORTANT]
> Georedundante Sicherungen werden derzeit für Flexible Server nicht unterstützt.

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt

In Azure Database for MySQL Flexible Server wird bei der Zeitpunktwiederherstellung ein neuer Server aus den Sicherungen des flexiblen Servers in derselben Region wie Ihr Ausgangsserver erstellt. Bei der Erstellung werden die Konfiguration für die Computeebene, die Anzahl der virtuellen Kerne, die Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des ursprünglichen Servers verwendet. Außerdem werden Tags und Einstellungen wie „Virtuelles Netzwerk“ und „Firewall“ vom Quellserver übernommen. Die Compute- und Speicherebene, die Konfiguration und die Sicherheitseinstellungen des wiederhergestellten Servers können nach Abschluss der Wiederherstellung geändert werden.

> [!NOTE]
> Es gibt zwei Serverparameter, die nach dem Wiederherstellungsvorgang auf Standardwerte zurückgesetzt werden (und nicht vom primären Server übernommen werden)
> *   time_zone: Dieser Wert wird auf den DEFAULT-Wert SYSTEM festgelegt.
> *   event_scheduler: Dieser Wert wird auf dem wiederhergestellten Server auf OFF festgelegt.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Einige der üblichen Anwendungsfälle sind:
-   Wenn ein Benutzer versehentlich Daten in der Datenbank löscht
-   Der Benutzer löscht eine wichtige Tabelle oder Datenbank
-   Eine Benutzeranwendung überschreibt aufgrund eines Anwendungsfehlers versehentlich gültige Daten mit ungültigen Daten

Sie können über das [Azure-Portal](how-to-restore-server-portal.md) zwischen einem letzten Wiederherstellungspunkt und einem benutzerdefinierten Wiederherstellungspunkt auswählen.

-   **Neuester Wiederherstellungspunkt** : Der neueste Wiederherstellungspunkt hilft Ihnen, den Server auf die letzte auf dem Quellserver durchgeführte Sicherung zurückzusetzen. Der Zeitstempel für die Wiederherstellung wird ebenfalls im Portal angezeigt. Diese Option ist hilfreich, um den Server schnell auf den aktuellsten Stand zu bringen.
-   **Benutzerdefinierter Wiederherstellungspunkt** : Dadurch können Sie einen beliebigen Zeitpunkt innerhalb des für diesen flexiblen Server definierten Aufbewahrungszeitraums wählen. Diese Option ist nützlich, um den Server genau zu dem Zeitpunkt wiederherzustellen, zu dem ein Benutzerfehler aufgetreten ist.

Die geschätzte Wiederherstellungszeit hängt von mehreren Faktoren ab, einschließlich der Datenbankgrößen, der Größe der Sicherung des Transaktionsprotokolls, der Computegröße der SKU und auch der Zeit der Wiederherstellung. Die Wiederherstellung des Transaktionsprotokolls ist der zeitaufwendigste Teil des Wiederherstellungsprozesses. Wenn die Wiederherstellungszeit näher am Zeitplan der vollständigen oder differenziellen Momentaufnahmensicherung gewählt wird, erfolgen die Wiederherstellungen schneller, da die Anwendung des Transaktionsprotokolls minimal ist. Um die genaue Wiederherstellungszeit für Ihren Server zu schätzen, wird dringend empfohlen, ihn in Ihrer Umgebung zu testen, da er zu viele umgebungsspezifische Variablen aufweist.

> [!IMPORTANT]
> Wenn Sie einen flexiblen Server wiederherstellen, der mit zonenredundanter Hochverfügbarkeit konfiguriert ist, wird der wiederhergestellte Server in derselben Region und Zone wie Ihr primärer Server konfiguriert und als einzelner flexibler Server in einem Modus ohne Hochverfügbarkeit bereitgestellt. Weitere Informationen finden Sie unter [Zonenredundante Hochverfügbarkeit](concepts-high-availability.md) für flexible Server.

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) nutzen.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach beiden Wiederherstellungsverfahren ( **neuester Wiederherstellungspunkt** oder **Benutzerdefinierter Wiederherstellungspunkt** ) sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

-   Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.
-   Sicherstellen, dass geeignete Firewallregeln auf Serverebene und Regeln von virtuellen Netzwerken vorhanden sind, damit Benutzer eine Verbindung herstellen können.
-   Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.
-   Konfigurieren der erforderlichen Warnungen.

## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)
