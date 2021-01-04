---
title: 'Häufig gestellte Fragen: Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern'
description: In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zum Sichern von SAP HANA-Datenbanken mit dem Azure Backup-Dienst.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400206"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Häufig gestellte Fragen: Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern

In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zum Sichern von SAP HANA-Datenbanken mit dem Azure Backup-Dienst.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Wie viele vollständige Sicherungen werden pro Tag unterstützt?

Wir unterstützen nur eine vollständige Sicherung pro Tag. Es ist nicht möglich, am selben Tag eine differenzielle Sicherung und eine vollständige Sicherung auszulösen.

### <a name="do-successful-backup-jobs-create-alerts"></a>Erstellen erfolgreiche Sicherungsaufträge Warnungen?

Nein. Erfolgreiche Sicherungsaufträge generieren keine Warnungen. Warnungen werden nur für Sicherungsaufträge gesendet, bei denen ein Fehler aufgetreten ist. Ausführliche Informationen zum Verhalten von Portalwarnungen finden Sie [hier](./backup-azure-monitoring-built-in-monitor.md). Falls Sie aber daran interessiert sind, Benachrichtigungen auch für erfolgreiche Aufträge zu erhalten, können Sie [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md) verwenden.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Werden geplante Sicherungsaufträge im Menü „Sicherungsaufträge“ angezeigt?

Im Menü Sicherungsauftrag werden nur bedarfsgesteuerte Sicherungsaufträge angezeigt. Verwenden Sie für geplante Aufträge [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>Werden zukünftige Datenbanken für die Durchführung von Sicherungen automatisch hinzugefügt?

Nein. Dies wird derzeit nicht unterstützt.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Was passiert mit den Sicherungen, wenn ich eine Datenbank aus einer Instanz lösche?

Wenn eine Datenbank aus einer SAP HANA-Instanz gelöscht wird, wird weiterhin versucht, Sicherungen der Datenbank durchzuführen. Dies bedeutet auch, dass die gelöschte Datenbank unter **Sicherungselemente** als fehlerhaft angezeigt wird und weiterhin geschützt ist.
Die richtige Vorgehensweise zum Beenden des Schutzes dieser Datenbank besteht darin, hierfür die Option zum **Beenden der Sicherung bei gelöschten Daten** zu verwenden.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Welches Verhalten ergibt sich, wenn ich den Namen der Datenbank ändere, nachdem sie geschützt wurde?

Eine umbenannte Datenbank wird wie eine neue Datenbank behandelt. Diese Situation wird daher vom Dienst so behandelt, als wäre die Datenbank nicht gefunden worden, und für die Sicherungen tritt ein Fehler auf. Die umbenannte Datenbank wird als neue Datenbank angezeigt und muss für Schutz konfiguriert werden.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Was sind die Voraussetzungen für die Sicherung von SAP HANA-Datenbanken auf einer Azure-VM?

Informationen finden Sie in den Abschnitten zu den [Voraussetzungen](tutorial-backup-sap-hana-db.md#prerequisites) und den [Aufgaben des Vorregistrierungsskripts](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Welche Berechtigungen sollten festgelegt werden, damit SAP HANA-Datenbanken von Azure gesichert werden können?

Wenn Sie das Skript vor der Registrierung ausführen, werden die erforderlichen Berechtigungen festgelegt, damit Azure SAP HANA-Datenbanken sichern kann. Weitere Informationen zu den Aufgaben des Vorregistrierungsskripts finden Sie [hier](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Funktionieren Sicherungen nach der Migration von SAP HANA von SDC auf MDC?

Weitere Informationen finden Sie in [diesem Abschnitt](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) des Leitfadens zur Problembehandlung.

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>Was soll beim Upgrade innerhalb der gleichen Version durchgeführt werden?

Weitere Informationen finden Sie in [diesem Abschnitt](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) des Leitfadens zur Problembehandlung.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Kann die HANA-Sicherung in Azure für eine virtuelle IP-Adresse (Lastenausgleich) und nicht einen virtuellen Computer eingerichtet werden?

Derzeit besteht keine Möglichkeit, die Lösung nur für eine virtuelle IP-Adresse einzurichten. Es wird ein virtueller Computer zum Ausführen der Lösung benötigt.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Wie kann ich eine bedarfsgesteuerte Sicherung in das lokale Dateisystem anstatt in den Azure-Tresor verschieben?

1. Warten Sie, bis die aktuell ausgeführte Sicherung für die gewünschte Datenbank abgeschlossen ist (überprüfen Sie in Studio, ob der Vorgang abgeschlossen ist).
1. Deaktivieren Sie Protokollsicherungen, und legen Sie die Katalogsicherung mithilfe der folgenden Schritte auf **Dateisystem** für die gewünschte Datenbank fest:
1. Doppelklicken Sie auf **SYSTEMDB** -> **Konfiguration** -> **Datenbank auswählen** -> **Filter (Protokoll)** .
    1. Legen Sie „enable_auto_log_backup“ auf **no** fest.
    1. Legen Sie „catalog_backup_using_backint“ auf **false** fest.
1. Erstellen Sie eine bedarfsgesteuerte Sicherung (vollständig/differenziell/inkrementell) für die gewünschte Datenbank, und warten Sie, bis die Vorgänge für die Sicherung und die Katalogsicherung abgeschlossen sind.
1. Sollen auch die Protokollsicherungen in das Dateisystem verschoben werden, legen Sie „enable_auto_log_backup“ auf **yes** fest.
1. Kehren Sie zu den vorherigen Einstellungen zurück, damit Sicherungen in den Azure-Tresor übertragen werden können:
    1. Legen Sie „enable_auto_log_backup“ auf **yes** fest.
    1. Legen Sie „catalog_backup_using_backint“ auf **true** fest.

>[!NOTE]
>Beim Verschieben von Sicherungen in das lokale Dateisystem und Zurückwechseln zum Azure-Tresor kann es zu einer Unterbrechung der Protokollkette im Tresor kommen. Hierdurch wird eine vollständige Sicherung ausgelöst, nach deren erfolgreichem Abschluss mit dem Sichern der Protokolle begonnen wird.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Wie kann ich die SAP HANA-Sicherung mit meiner Einrichtung der HANA-Replikation nutzen?

Derzeit ist Azure Backup nicht in der Lage, eine HSR-Einrichtung zu verstehen. Dies bedeutet, dass der primäre und sekundäre Knoten der HSR als zwei einzelne VMs behandelt werden, die nicht in Verbindung stehen. Zuerst müssen Sie die Sicherung auf dem primären Knoten konfigurieren. Wenn ein Failover ausgeführt wird, muss die Sicherung auf dem sekundären Knoten konfiguriert werden (der nun zum primären Knoten wird). Es erfolgt kein automatisches Failover der Sicherung auf den anderen Knoten.

Wenn Sie zu einem beliebigen Zeitpunkt Daten vom aktiven (primären) Knoten sichern möchten, können Sie einen **Wechsel des Schutzes** auf den sekundären Knoten durchführen, der nach dem Failover zum primären Knoten geworden ist.

Führen Sie diese Schritte aus, um diesen **Wechsel des Schutzes** durchzuführen:

- [Beenden des Schutzes](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (mit Beibehaltung der Daten) auf dem primären Knoten
- Ausführen des [Vorregistrierungsskripts](https://aka.ms/scriptforpermsonhana) auf dem sekundären Knoten
- [Ermitteln der Datenbanken](tutorial-backup-sap-hana-db.md#discover-the-databases) auf dem sekundären Knoten und [Konfigurieren der entsprechenden Sicherungen](tutorial-backup-sap-hana-db.md#configure-backup)

Diese Schritte müssen nach jedem Failover manuell ausgeführt werden. Sie können diese Schritte nicht nur im Azure-Portal ausführen, sondern auch per Befehlszeile/HTTP REST. Für die Automatisierung dieser Schritte können Sie ein Azure-Runbook verwenden.

Hier ist ein ausführliches Beispiel für den **Wechsel des Schutzes** angegeben:

In diesem Beispiel verfügen Sie über zwei Knoten: Knoten 1 (primär) und Knoten 2 (sekundär) in der HSR-Einrichtung.  Sicherungen werden auf Knoten 1 konfiguriert. Wie bereits erwähnt, sollten Sie noch nicht versuchen, Sicherungen auf Knoten 2 zu konfigurieren.

Wenn das erste Failover ausgeführt wird, wird Knoten 2 zum primären Knoten. Dies ergibt folgende Szenarien:

1. Beenden Sie den Schutz auf Knoten 1 (vorheriger primärer Knoten) mit der Option zur Beibehaltung der Daten.
1. Führen Sie das Vorregistrierungsskript auf Knoten 2 (nun der primäre Knoten) aus.
1. Ermitteln Sie die Datenbanken auf Knoten 2, weisen Sie eine Sicherungsrichtlinie zu, und konfigurieren Sie die Sicherungen.

Anschließend wird auf Knoten 2 eine erste vollständige Sicherung ausgelöst, und nach deren Abschluss beginnen die Protokollsicherungen.

Beim nächsten Failover wird Knoten 1 wieder zum primären Knoten und Knoten 2 zum sekundären Knoten. Wiederholen Sie nun den Vorgang:

1. Beenden Sie den Schutz von Knoten 2 mit der Option zur Beibehaltung der Daten.
1. Führen Sie das Vorregistrierungsskript auf Knoten 1 (der wieder zum primären Knoten geworden ist) aus.
1. [Setzen Sie dann die Sicherungen auf Knoten 1 fort](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database), indem Sie die erforderliche Richtlinie nutzen (da die Sicherungsvorgänge zuvor für Knoten 1 beendet wurden).

Anschließend wird auf Knoten 1 erneut eine vollständige Sicherung ausgelöst, und nach deren Abschluss beginnen die Protokollsicherungen.

## <a name="restore"></a>Restore

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Warum kann ich das HANA-System nicht sehen, in dem meine Datenbank wieder hergestellt werden soll?

Überprüfen Sie, ob alle Voraussetzungen für die Wiederherstellung in der SAP HANA-Zielinstanz erfüllt sind. Weitere Informationen finden Sie unter [Voraussetzungen: Wiederherstellen von SAP HANA-Datenbanken in einer Azure-VM](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Warum schlägt die Wiederherstellungsoption zum Überschreiben der Datenbank für meine Datenbank fehl?

Stellen Sie sicher, dass die Option **Überschreiben erzwingen** beim Wiederherstellen ausgewählt ist.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Warum wird der Fehler „Quell- und Zielsystem für die Wiederherstellung sind inkompatibel“ angezeigt?

Lesen Sie den SAP HANA-Hinweis [1642148](https://launchpad.support.sap.com/#/notes/1642148), um zu ermitteln, welche Wiederherstellungstypen derzeit unterstützt werden.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Kann ich eine Sicherung einer laufenden Datenbank auf SLES verwenden, um sie in einem RHEL HANA-System wiederherzustellen (oder umgekehrt)?

Ja. Sie können Streamingsicherungen, die auf einer auf SLES laufenden HANA-Datenbank ausgelöst wurden, verwenden, um sie auf einem RHEL HANA-System wiederherzustellen (und umgekehrt). Das heißt, dass die betriebssystemübergreifende Wiederherstellung mithilfe von Streamingsicherungen möglich ist. Sie müssen aber sicherstellen, dass sowohl das HANA-System, auf dem Sie die Wiederherstellung durchführen möchten, als auch das HANA-System, das für die Wiederherstellung verwendet wird, gemäß SAP für die Wiederherstellung kompatibel sind. Lesen Sie SAP HANA-Hinweis [1642148](https://launchpad.support.sap.com/#/notes/1642148), um zu erfahren, welche Wiederherstellungstypen kompatibel sind.

## <a name="policy"></a>Policy

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Verschiedene verfügbare Optionen bei der Erstellung einer neuen Richtlinie für SAP HANA-Sicherungen

Bevor Sie eine Richtlinie erstellen, sollten Sie sich mit den Anforderungen in puncto RPO und RTO sowie mit den entsprechenden Auswirkungen auf die Kosten auseinandersetzen.

RPO (Recovery Point Objective) gibt den akzeptablen Datenverlust für den Benutzer/Kunden an. Dies wird durch das Protokollsicherungsintervall bestimmt. Häufigere Protokollsicherungen bedeuten einen niedrigeren RPO-Wert. Der kleinste vom Azure Backup-Dienst unterstützte Wert beträgt 15 Minuten. Das Protokollsicherungsintervall kann also 15 Minuten oder mehr betragen.

RTO (Recovery Time Objective) gibt an, wie schnell die Daten nach einem Datenverlust auf den letzten verfügbaren Zeitpunkt wiederhergestellt werden sollen. Dies hängt davon ab, welche Wiederherstellungsstrategie von HANA genutzt wird (was wiederum mit der Anzahl der für die Wiederherstellung erforderlichen Dateien zusammenhängt). Dadurch ergeben sich auch Auswirkungen auf die Kosten. Die folgende Tabelle gibt einen besseren Überblick über alle Szenarien und deren Auswirkungen:

|Sicherungsrichtlinie  |RTO  |Kosten  |
|---------|---------|---------|
|Täglich vollständig + Protokolle     |   Schnellste Option, da für die Point-in-Time-Wiederherstellung lediglich eine vollständige Kopie und die erforderlichen Protokolle benötigt werden.      |    Teuerste Option, da täglich eine vollständige Kopie erstellt wird, wodurch sich im Back-End bis zum Erreichen der Aufbewahrungsdauer mehr und mehr Daten ansammeln.   |
|Wöchentlich vollständig + täglich differenziell + Protokolle     |   Langsamer als die vorherige Option, aber schneller als die nächste Option, da für die Point-in-Time-Wiederherstellung eine vollständige Kopie und eine differenzielle Kopie sowie Protokolle benötigt werden.      |    Kostengünstigere Option, da die tägliche differenzielle Sicherung in der Regel kleiner als eine vollständige Sicherung ist und nur einmal pro Woche eine vollständige Kopie erstellt wird.      |
|Wöchentlich vollständig + täglich inkrementell + Protokolle     |  Langsamste Option, da für die Point-in-Time-Wiederherstellung eine vollständige Kopie und n inkrementelle Sicherungen sowie Protokolle benötigt werden.       |     Kostengünstigste Option, da die tägliche inkrementelle Sicherung kleiner als eine differenzielle Sicherung ist und eine vollständige Kopie nur wöchentlich erstellt wird.    |

> [!NOTE]
> Die obigen Optionen sind die gängigsten, aber nicht die einzigen Optionen. Sie können beispielsweise auch eine Konfiguration verwenden, die sich aus einer vollständigen Sicherung und zwei differenziellen Sicherungen pro Woche sowie Protokollen zusammensetzt.

Die Richtlinienvariante kann daher auf der Grundlage von RPO-, RTO- und Kostenaspekten gewählt werden.

### <a name="impact-of-modifying-a-policy"></a>Auswirkungen von Richtlinienänderungen

Bei der Ermittlung, welche Auswirkungen die Umstellung der Richtlinie eines Sicherungselements von Richtlinie 1 (Policy 1, P1) auf Richtlinie 2 (Policy 2, P2) oder die Bearbeitung der Richtlinie 1 (Policy 1, P1) hat, sind ein paar Prinzipien zu beachten.

- Alle Änderungen werden auch rückwirkend angewendet. Die neueste Sicherungsrichtlinie wird auch auf zuvor erstellte Wiederherstellungspunkte angewendet. Ein Beispiel: Angenommen, die Aufbewahrungsdauer für tägliche vollständige Sicherungen beträgt 30 Tage, und auf der Grundlage der derzeit aktiven Richtlinie wurden zehn Wiederherstellungspunkte erstellt. Wenn nun die Aufbewahrungsdauer für tägliche vollständige Sicherungen auf zehn Tage verkürzt wird, wird auch die Ablaufzeit der vorherigen Punkte nach der Formel „Startzeit + zehn Tage“ neu berechnet, und abgelaufene Punkte werden gelöscht.
- Zum Änderungsumfang zählen auch der Tag der Sicherung, die Art der Sicherung und die Aufbewahrungsdauer. Beispiel: Wenn eine Richtlinie von täglichen vollständigen Sicherungen in wöchentliche vollständige Sicherungen am Sonntag geändert wird, werden alle vorherigen vollständigen Sicherungen, die nicht an einem Sonntag erstellt wurden, zum Löschen markiert.
- Ein übergeordnetes Element wird erst gelöscht, wenn das untergeordnete Element aktiv/nicht abgelaufen ist. Jeder Sicherungstyp verfügt über eine gemäß der aktuell aktiven Richtlinie festgelegte Ablaufzeit. Vollständige Sicherungen werden jedoch als übergeordnete Elemente für nachfolgende differenzielle Sicherungen, inkrementelle Sicherungen und Protokollsicherungen betrachtet. Eine differenzielle Sicherung und ein Protokoll sind niemals übergeordnete Elemente. Eine inkrementelle Sicherung kann ein übergeordnetes Element einer nachfolgenden inkrementellen Sicherung sein. Ein übergeordnetes Element, das zum Löschen markiert ist, wird nicht gelöscht, wenn die untergeordneten differenziellen Sicherungen oder Protokollsicherungen nicht abgelaufen sind. Wenn eine Richtlinie beispielsweise von täglichen vollständigen Sicherungen in wöchentliche vollständige Sicherungen am Sonntag geändert wird, werden alle vorherigen vollständigen Sicherungen, die nicht an einem Sonntag erstellt wurden, zum Löschen markiert. Sie werden jedoch erst wirklich gelöscht, wenn die zuvor täglich erstellten Protokolle abgelaufen sind. Anders ausgedrückt: Sie werden gemäß der aktuellen Protokolldauer aufbewahrt. Nach Ablauf der Protokolle werden sowohl die Protokolle als auch diese vollständigen Sicherungen gelöscht.

Mit diesen Prinzipien im Hinterkopf können Sie die folgende Tabelle lesen und die Auswirkungen einer Richtlinienänderung nachvollziehen.

|Alte Richtlinie/neue Richtlinie  |Täglich vollständig + Protokolle  | Wöchentlich vollständig + täglich differenziell + Protokolle  |Wöchentlich vollständig + täglich inkrementell + Protokolle  |
|---------|---------|---------|---------|
|Täglich vollständig + Protokolle     |   -      |    Die vorherigen vollständigen Sicherungen, die nicht am gleichen Wochentag erstellt wurden, werden zum Löschen markiert, aber für die Dauer des Protokollaufbewahrungszeitraums aufbewahrt.     |    Die vorherigen vollständigen Sicherungen, die nicht am gleichen Wochentag erstellt wurden, werden zum Löschen markiert, aber für die Dauer des Protokollaufbewahrungszeitraums aufbewahrt.     |
|Wöchentlich vollständig + täglich differenziell + Protokolle     |   Die Aufbewahrung der vorherigen wöchentlichen vollständigen Sicherungen wird gemäß der aktuellen Richtlinie neu berechnet. Die vorherigen differenziellen Sicherungen werden sofort gelöscht.      |    -     |    Die vorherigen differenziellen Sicherungen werden sofort gelöscht.     |
|Wöchentlich vollständig + täglich inkrementell + Protokolle     |     Die Aufbewahrung der vorherigen wöchentlichen vollständigen Sicherungen wird gemäß der aktuellen Richtlinie neu berechnet. Die vorherigen inkrementellen Sicherungen werden sofort gelöscht.    |     Die vorherigen inkrementellen Sicherungen werden sofort gelöscht.    |    -     |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie [SAP HANA-Datenbanken auf virtuellen Azure-Computern gesichert werden](./backup-azure-sap-hana-database.md).
