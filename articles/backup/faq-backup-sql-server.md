---
title: 'Häufig gestellte Fragen: Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern'
description: Enthält Antworten auf häufig gestellte Fragen zur Sicherung von SQL Server-Datenbanken auf Azure-VMs mit Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 89316770dc137bff031e6268db5ece156edd4f25
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172378"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Häufig gestellte Fragen zu SQL Server-Datenbanken, die auf einer Azure VM-Sicherungsinstanz ausgeführt werden

In diesem Artikel werden häufige Fragen zum Sichern von SQL Server-Datenbanken beantwortet, die auf virtuellen Azure-Computern (VMs) ausgeführt werden und für die der Dienst [Azure Backup](backup-overview.md) genutzt wird.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Kann ich Azure Backup für IaaS-VMs und SQL Server auf demselben Computer verwenden?

Ja – sowohl die VM-Sicherung als auch die SQL-Sicherung können sich auf demselben virtuellen Computer befinden. In diesem Fall lösen wir auf dem virtuellen Computer intern eine vollständige Kopiesicherung aus, damit die Protokolle nicht abgeschnitten werden.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Wird der Sicherungsvorgang bei Problemen von der Lösung wiederholt bzw. werden Sicherungen automatisch repariert?

Unter bestimmten Umständen löst der Azure Backup-Dienst korrigierende Sicherungen aus. Die automatische Korrektur kann in den sechs Fällen erfolgen, die unten aufgeführt sind:

- Wenn Protokoll- oder differenzielle Sicherungen aufgrund eines LSN-Überprüfungsfehlers nicht erfolgreich sind, wird die nächste Protokoll- bzw. differenzielle Sicherung stattdessen in eine vollständige Sicherung konvertiert.
- Falls vor einer Protokoll- bzw. differenziellen Sicherung keine vollständige Sicherung durchgeführt wurde, wird die entsprechende Sicherung in eine vollständige Sicherung konvertiert.
- Wenn der Zeitpunkt der letzten vollständigen Sicherung länger als 15 Tage zurückliegt, wird die nächste Protokoll- bzw. differenzielle Sicherung in eine vollständige Sicherung konvertiert.
- Alle Sicherungsaufträge, die aufgrund eines Upgrades der Erweiterung abgebrochen werden, werden nach Abschluss des Upgrades und dem Starten der Erweiterung erneut ausgelöst.
- Wenn Sie sich für das Überschreiben der Datenbank während der Wiederherstellung entscheiden, tritt bei der nächsten Protokoll- bzw. differenziellen Sicherung ein Fehler auf, und stattdessen wird eine vollständige Sicherung ausgelöst.
- In Fällen, in denen aufgrund einer Änderung des Datenbank-Wiederherstellungsmodells für eine vollständige Sicherung das Zurücksetzen der Protokollketten erforderlich ist, wird beim nächsten geplanten Zeitpunkt automatisch eine vollständige Sicherung ausgelöst.

Die Funktion der automatischen Reparatur ist standardmäßig für alle Benutzer aktiviert. Wenn Sie die Funktion deaktivieren möchten, führen Sie die folgenden Schritte aus:

- Erstellen Sie auf der SQL Server-Instanz im Ordner *C:\Programme\Azure Workload Backup\bin* die Datei **ExtensionSettingsOverrides.json** (bzw. bearbeiten Sie sie).
- Legen Sie in **ExtensionSettingsOverrides.json** Folgendes fest: *{"EnableAutoHealer": false}* .
- Speichern Sie Ihre Änderungen, und schließen Sie die Datei.
- Öffnen Sie auf der SQL Server-Instanz den **Task-Manager** , und starten Sie dann den Dienst **AzureWLBackupCoordinatorSvc** neu.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Kann ich steuern, wie viele gleichzeitige Sicherungen für die SQL Server-Instanz ausgeführt werden?

Ja. Sie können die Rate verringern, mit der die Sicherungsrichtlinie ausgeführt wird, um die Auswirkungen auf eine SQL Server-Instanz zu minimieren. So ändern Sie die Einstellung:

1. Erstellen Sie auf der SQL Server-Instanz im Ordner *C:\Programme\Azure Workload Backup\bin* die Datei *ExtensionSettingsOverrides.json* .
2. Ändern Sie in der Datei *ExtensionSettingsOverrides.json* die Einstellung **DefaultBackupTasksThreshold** in einen niedrigeren Wert (z. B. „5“). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Der Standardwert von „DefaultBackupTasksThreshold“ ist **20** .

3. Speichern Sie Ihre Änderungen, und schließen Sie die Datei.
4. Öffnen Sie auf der SQL Server-Instanz **Task-Manager** . Starten Sie den Dienst **AzureWLBackupCoordinatorSvc** neu.<br/> <br/>
 Zwar ist diese Methode hilfreich, wenn die Sicherungsanwendung viele Ressourcen verbraucht, der [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) von SQL Server stellt jedoch eine allgemeinere Möglichkeit zur Angabe von Grenzwerten für die Menge an CPU, physischer E/A und Speicher dar, den eingehende Anwendungsanforderungen nutzen können.

> [!NOTE]
> Auf der Benutzeroberfläche können Sie weiterhin jederzeit eine beliebige Anzahl von Sicherungen planen. Diese werden allerdings in einem gleitenden Fenster von beispielsweise jeweils 5 verarbeitet, wie im obigen Beispiel gezeigt.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kann ich eine vollständige Sicherung aus einem sekundären Replikat ausführen?

Gemäß den SQL-Einschränkungen können Sie für das sekundäre Replikat eine Sicherung vom Typ „Nur vollständig kopieren“ ausführen. Eine vollständige Sicherung ist jedoch nicht zulässig.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kann ich Verfügbarkeitsgruppen lokal schützen?

Nein. Azure Backup schützt SQL Server-Datenbanken, die in Azure ausgeführt werden. Wenn eine Verfügbarkeitsgruppe auf Azure und lokale Computer verteilt ist, kann die Verfügbarkeitsgruppe nur geschützt werden, sofern das primäre Replikat in Azure ausgeführt wird. Außerdem werden mit Azure Backup nur die Knoten geschützt, die in derselben Azure-Region wie der Recovery Services-Tresor ausgeführt werden.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kann ich Verfügbarkeitsgruppen regionsübergreifend schützen?

Der Recovery Services-Tresor von Azure Backup kann alle Knoten erkennen und schützen, die sich in derselben Region wie der Tresor befinden. Wenn sich Ihre SQL Server Always On-Verfügbarkeitsgruppe über mehrere Azure-Regionen erstreckt, richten Sie die Sicherung über die Region ein, die über den primären Knoten verfügt. Azure Backup kann alle Datenbanken in der Verfügbarkeitsgruppe gemäß Ihrer Sicherungseinstellung erkennen und schützen. Wenn Ihre Sicherungseinstellung nicht erfüllt wird, tritt für Sicherungen ein Fehler auf, und die entsprechende Warnung wird angezeigt.

## <a name="do-successful-backup-jobs-create-alerts"></a>Erstellen erfolgreiche Sicherungsaufträge Warnungen?

Nein. Erfolgreiche Sicherungsaufträge generieren keine Warnungen. Warnungen werden nur für Sicherungsaufträge gesendet, bei denen ein Fehler aufgetreten ist. Ausführliche Informationen zum Verhalten von Portalwarnungen finden Sie [hier](backup-azure-monitoring-built-in-monitor.md). Falls Sie aber daran interessiert sind, Benachrichtigungen auch für erfolgreiche Aufträge zu erhalten, können Sie Sicherungen [über Azure Monitor überwachen](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Werden geplante Sicherungsaufträge im Menü „Sicherungsaufträge“ angezeigt?

Im Menü **Sicherungsauftrag** werden alle geplanten und bedarfsgesteuerten Vorgänge angezeigt – mit Ausnahme der geplanten Protokollsicherungen, da diese möglicherweise sehr häufig ausgeführt werden. Verwenden Sie für geplante Protokollaufträge [Überwachen mithilfe von Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Werden zukünftige Datenbanken für die Durchführung von Sicherungen automatisch hinzugefügt?

Ja. Sie können diese Funktion per [automatischem Schutz](backup-sql-server-database-azure-vms.md#enable-auto-protection) erzielen.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Was passiert mit den Sicherungen, wenn ich eine Datenbank von einer automatisch geschützten Instanz lösche?

Wenn eine Datenbank aus einer automatisch geschützten Instanz gelöscht wird, wird weiterhin versucht, Sicherungen der Datenbank durchzuführen. Dies bedeutet auch, dass die gelöschte Datenbank unter **Sicherungselemente** als fehlerhaft angezeigt wird und weiterhin geschützt ist.

Die richtige Vorgehensweise zum Beenden des Schutzes dieser Datenbank besteht darin, hierfür die Option **Sicherung beenden** mit **Daten löschen** zu verwenden.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Welches Verhalten ergibt sich, wenn ich den Vorgang „Sicherung beenden“ für eine automatisch geschützte Datenbank durchführe?

Wenn Sie **Sicherung beenden mit „Daten beibehalten“** durchführen, werden keine weiteren Sicherungen erstellt, und die vorhandenen Wiederherstellungspunkte bleiben intakt. Die Datenbank wird trotzdem weiter als geschützt angesehen und unter **Sicherungselemente** angezeigt.

Wenn Sie **Sicherung beenden mit „Daten löschen“** durchführen, werden keine weiteren Sicherungen erstellt, und die vorhandenen Wiederherstellungspunkte werden ebenfalls gelöscht. Die Datenbank wird als nicht geschützt angesehen und unter der Instanz im Bereich „Sicherung konfigurieren“ angezeigt. Im Gegensatz zu anderen ungeschützten Datenbanken, die manuell ausgewählt oder automatisch geschützt werden können, wird diese Datenbank ausgegraut angezeigt und kann nicht ausgewählt werden. Die einzige Möglichkeit, diese Datenbank wieder mit Schutz zu versehen, ist das Deaktivieren des automatischen Schutzes für die Instanz. Sie können diese Datenbank jetzt auswählen und den Schutz dafür konfigurieren oder den automatischen Schutz auf der Instanz wieder aktivieren.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Welches Verhalten ergibt sich, wenn ich den Namen der Datenbank ändere, nachdem sie geschützt wurde?

Eine umbenannte Datenbank wird wie eine neue Datenbank behandelt. Der Dienst verarbeitet diese Situation daher so, als ob die Datenbank nicht gefunden wurde: die Sicherungen können nicht ausgeführt werden.

Sie können die umbenannte Datenbank auswählen und den Schutz dafür konfigurieren. Wenn in der Instanz der automatische Schutz aktiviert ist, wird die umbenannte Datenbank automatisch erkannt und geschützt.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Warum kann ich eine hinzugefügte Datenbank für eine Instanz mit automatischem Schutz nicht sehen?

Eine Datenbank, die Sie [einer automatisch geschützten Instanz hinzufügen](backup-sql-server-database-azure-vms.md#enable-auto-protection), wird ggf. nicht sofort unter den geschützten Elementen angezeigt. Dies liegt daran, dass die Ermittlung in der Regel alle 8 Stunden ausgeführt wird. Sie können neue Datenbanken aber sofort erkennen und schützen, wenn Sie manuell eine Ermittlung durchführen, indem Sie **Datenbanken neu ermitteln**  auswählen. Dies ist in der folgenden Abbildung dargestellt:

  ![Manuelles Ermitteln einer neu hinzugefügten Datenbank](./media/backup-azure-sql-database/view-newly-added-database.png)
  
## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Kann ich Datenbanken schützen, für die TDE (Transparent Data Encryption) aktiviert ist, und bleibt die Datenbank während des gesamten Sicherungsprozesses verschlüsselt?

Ja, Azure Backup unterstützt die Sicherung von Datenbanken und Servern in SQL Server mit aktivierter TDE. Backup unterstützt [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) mit Schlüsseln, die von Azure verwaltet werden, oder mit kundenseitig verwalteten Schlüssen (Bring Your Own Key, BYOK).  Backup führt im Rahmen des Sicherungsprozesses keine SQL-Verschlüsselung durch, daher bleibt die Datenbank bei der Sicherung verschlüsselt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie eine [SQL Server-Datenbank sichern](backup-azure-sql-database.md), die auf einer Azure-VM ausgeführt wird.