---
title: Behandeln von Problemen beim Sichern von Azure-Dateifreigaben
description: Dieser Artikel enthält Informationen zum Behandeln von Problemen in Verbindung mit dem Schutz Ihrer Azure-Dateifreigaben.
services: backup
ms.service: backup
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: eec30ec8ff85f2d9a2ba78da2872b081e90c9e33
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240159"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Behandeln von Problemen beim Sichern von Azure-Dateifreigaben
Die folgende Tabelle enthält Problembehandlungsinformationen für Probleme und Fehler, die bei der Verwendung der Sicherung von Azure-Dateifreigaben auftreten können.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Einschränkungen beim Sichern von Azure-Dateifreigaben während der Vorschauphase
Die Sicherung für Azure-Dateifreigaben befindet sich in der Vorschauphase. Azure-Dateifreigaben in Speicherkonten vom Typ „Allgemein v1“ und „Allgemein v2“ werden unterstützt. Folgende Sicherungsszenarien werden für Azure-Dateifreigaben nicht unterstützt:
- Sie können Azure-Dateifreigaben in Speicherkonten mit aktivierten virtuellen Netzwerken oder aktivierter Firewall nicht schützen.
- Für den Schutz von Azure Files mit Azure Backup ist keine Befehlszeilenschnittstelle verfügbar.
- Die Anzahl geplanter Sicherungen ist auf eine Sicherung pro Tag begrenzt.
- Die Anzahl bedarfsgesteuerter Sicherungen ist auf vier Sicherungen pro Tag begrenzt.
- Verwenden Sie [Ressourcensperren](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) für das Speicherkonto, um das versehentliche Löschen von Sicherungen in Ihrem Recovery Services-Tresor zu verhindern.
- Löschen Sie keine Momentaufnahmen, die mit Azure Backup erstellt wurden. Das Löschen von Momentaufnahmen kann zum Verlust von Wiederherstellungspunkten bzw. zu Wiederherstellungsfehlern führen.
- Löschen Sie keine Dateifreigaben, die durch Azure Backup geschützt sind. In der aktuellen Lösung werden nach dem Löschen der Dateifreigabe alle von Azure Backup erstellten Momentaufnahmen gelöscht, sodass alle Wiederherstellungspunkte verloren gehen.

Die Sicherung für Azure-Dateifreigaben in Speicherkonten mit Replikation vom Typ [ZRS](../storage/common/storage-redundancy-zrs.md) (Zone Redundant Storage, zonenredundanter Speicher) steht momentan nur in den Regionen „USA, Mitte“ (CUS), „USA, Osten (EUS), „USA, Osten 2“ (EUS2), „Europa, Norden“ (NE), „Asien, Südosten“ (SEA), „Europa, Westen“ (WE) und „USA, Westen 2“ (WUS2) zur Verfügung.

## <a name="configuring-backup"></a>Konfigurieren der Sicherung
Die folgende Tabelle bezieht sich auf die Konfiguration der Sicherung:

| Fehlermeldungen | Problemumgehung oder Lösungstipps |
| ------------------ | ----------------------------- |
| Ich habe mein Speicherkonto zum Konfigurieren der Sicherung für die Azure-Dateifreigabe nicht gefunden. | <ul><li>Warten Sie, bis die Ermittlung abgeschlossen ist. <li>Überprüfen Sie, ob eine Dateifreigabe aus dem Speicherkonto bereits durch einen anderen Recovery Services-Tresor geschützt ist. **Hinweis**: Alle Dateifreigaben in einem Speicherkonto können nur unter einem einzelnen Recovery Services-Tresor geschützt werden. <li>Vergewissern Sie sich, dass sich die Dateifreigabe nicht in einem nicht unterstützten Speicherkonto befindet.|
| Im Portal tritt ein Fehler mit dem Hinweis auf, dass die Speicherkonten nicht erfolgreich erkannt werden konnten. | Wenn es sich bei Ihrem Abonnement um ein (CSP-fähiges) Partnerabonnement handelt, ignorieren Sie den Fehler. Falls Ihr Abonnement nicht CSP-fähig ist und Ihre Speicherkonten nicht erkannt werden, wenden Sie sich an den Support.|
| Die Überprüfung oder Registrierung des ausgewählten Speicherkontos war nicht erfolgreich.| Wiederholen Sie den Vorgang. Sollte das Problem weiterhin auftreten, wenden Sie sich an den Support.|
| Die Dateifreigaben im ausgewählten Speicherkonto konnten nicht aufgelistet werden oder wurden nicht gefunden. | <ul><li> Vergewissern Sie sich, dass das Speicherkonto in der Ressourcengruppe vorhanden ist und seit der letzten Überprüfung/Registrierung im Tresor nicht gelöscht oder verschoben wurde.<li>Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde. <li>Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird.<li>Überprüfen Sie, ob die Dateifreigabe bereits im gleichen Recovery Services-Tresor geschützt wird.|
| Bei der Konfiguration der Dateifreigabesicherung (oder der Schutzrichtlinie) tritt ein Fehler auf. | <ul><li>Wiederholen Sie den Vorgang, um zu prüfen, ob das Problem weiterhin besteht. <li> Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde. <li> Wenn Sie mehrere Dateifreigaben auf einmal schützen möchten und für einige der Dateifreigaben ein Fehler auftritt, konfigurieren Sie die Sicherung für die Dateifreigaben, bei denen ein Fehler aufgetreten ist, erneut. |
| Nach dem Aufheben des Schutzes einer Dateifreigabe kann der Recovery Services-Tresor nicht gelöscht werden. | Öffnen Sie im Azure-Portal Ihren Tresor, klicken Sie auf **Sicherungsinfrastruktur** > **Speicherkonten**, und klicken Sie anschließend auf **Registrierung aufheben**, um das Speicherkonto aus dem Recovery Services-Tresor zu entfernen.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Fehlermeldungen für nicht erfolgreiche Sicherungs- oder Wiederherstellungsaufträge

| Fehlermeldungen | Problemumgehung oder Lösungstipps |
| -------------- | ----------------------------- |
| Vorgangsfehler, weil die Dateifreigabe nicht gefunden wurde. | Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde.|
| Das Speicherkonto wurde nicht gefunden oder wird nicht unterstützt. | <ul><li>Vergewissern Sie sich, dass das Speicherkonto in der Ressourcengruppe vorhanden ist und seit der letzten Überprüfung nicht gelöscht oder aus der Ressourcengruppe entfernt wurde. <li> Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird.|
| Sie haben die maximale Anzahl von Momentaufnahmen für diese Dateifreigabe erreicht. Sie können weitere erstellen, sobald ältere abgelaufen sind. | <ul><li> Dieser Fehler kann auftreten, wenn Sie mehrere bedarfsgesteuerte Sicherungen für eine Datei erstellen. <li> Pro Dateifreigabe sind maximal 200 Momentaufnahmen zulässig – einschließlich der von Azure Backup erstellten Momentaufnahmen. Ältere geplante Sicherungen (oder Momentaufnahmen) werden automatisch bereinigt. Bedarfsgesteuerte Sicherungen (oder Momentaufnahmen) müssen gelöscht werden, wenn die maximal zulässige Anzahl erreicht wird.<li> Löschen Sie die bedarfsgesteuerten Sicherungen (Momentaufnahmen von Azure-Dateifreigaben) über das Azure Files-Portal. **Hinweis**: Das Löschen von Momentaufnahmen, die mit Azure Backup erstellt wurden, führt zum Verlust der Wiederherstellungspunkte. |
| Fehler bei der Dateifreigabesicherung/Dateifreigabewiederherstellung aufgrund der Speicherdienstdrosselung. Möglicherweise ist der Speicherdienst mit der Verarbeitung anderer Anforderungen für das angegebene Speicherkonto ausgelastet.| Wiederholen Sie den Vorgang nach einiger Zeit. |
| Fehler beim Wiederherstellen: Die Zieldateifreigabe wurde nicht gefunden. | <ul><li>Vergewissern Sie sich, dass das ausgewählte Speicherkonto vorhanden ist und die Zieldateifreigabe nicht gelöscht wurde. <li> Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird. |
| Azure Backup wird derzeit nicht für Azure-Dateifreigaben in Speicherkonten unterstützt, für die virtuelle Netzwerke aktiviert sind. | Deaktivieren Sie virtuelle Netzwerke für Ihr Speicherkonto, um erfolgreiche Sicherungs- und Wiederherstellungsvorgänge zu ermöglichen. |
| Fehler bei der Sicherung/Wiederherstellung, weil sich das Speicherkonto im gesperrten Zustand befindet. | Entfernen Sie die Sperre des Speicherkontos, oder verwenden Sie eine Löschsperre anstelle einer Lesesperre, und wiederholen Sie den Vorgang. |
| Fehler bei der Wiederherstellung, weil die Anzahl fehlerhafter Dateien den Schwellenwert übersteigt. | <ul><li> Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Fehler, und wiederholen Sie den Wiederherstellungsvorgang für die Dateien, bei denen ein Fehler aufgetreten ist. <li> Gängige Fehlerursachen beim Wiederherstellen von Dateien: <br/> - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet. <br/> - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Datei, bei der ein Fehler aufgetreten ist. |
| Fehler bei der Wiederherstellung, weil keine Datei wiederhergestellt werden konnte. | <ul><li> Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Fehler, und wiederholen Sie die Wiederherstellungsvorgänge für die Dateien, bei denen ein Fehler aufgetreten ist. <li> Gängige Fehlerursachen beim Wiederherstellen von Dateien: <br/> - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet. <br/> - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Datei, bei der ein Fehler aufgetreten ist. |
| Fehler beim Wiederherstellen: Eine der Dateien in der Quelle ist nicht vorhanden. | <ul><li> Die ausgewählten Elemente sind in den Wiederherstellungspunktdaten nicht vorhanden. Geben Sie die korrekte Dateiliste an, um die Dateien wiederherzustellen. <li> Die Dateifreigabemomentaufnahme für den Wiederherstellungspunkt wurde manuell gelöscht. Wiederholen Sie den Wiederherstellungsvorgang mit einem anderen Wiederherstellungspunkt. |
| Ein Wiederherstellungsauftrag wird zurzeit für dasselbe Ziel durchgeführt. | <ul><li>Die Dateifreigabesicherung unterstützt keine parallele Wiederherstellung in der gleichen Zieldateifreigabe. <li>Warten Sie, bis die aktuelle Wiederherstellung abgeschlossen ist, und wiederholen Sie den Vorgang. Sollten Sie im Recovery Services-Tresor keinen Wiederherstellungsauftrag finden, überprüfen Sie andere Recovery Services-Tresore im gleichen Abonnement. |
| Fehler beim Wiederherstellungsvorgang, weil die Zieldateifreigabe voll ist. | Erhöhen Sie das Größenkontingent der Zieldateifreigabe so, dass es für die Wiederherstellungsdaten ausreicht, und wiederholen Sie anschließend den Vorgang. |
| Der Wiederherstellungsvorgang war nicht erfolgreich: Beim Ausführen der Wiederherstellungsvorbereitung für mit der Zieldateifreigabe verknüpfte Ressourcen des Dateisynchronisierungsdiensts ist ein Fehler aufgetreten. | Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support. |
| Mindestens eine Datei konnte nicht erfolgreich wiederhergestellt werden. Weitere Informationen finden Sie in der Liste fehlerhafter Dateien im oben angegebenen Pfad. | <ul> <li> Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Ursachen, und wiederholen Sie den Wiederherstellungsvorgang für die Dateien, bei denen ein Fehler aufgetreten ist. <li> Häufige Fehlerursachen beim Wiederherstellen von Dateien: <br/> - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet. <br/> - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Dateien, bei denen ein Fehler aufgetreten ist. |


## <a name="modify-policy"></a>Richtlinie ändern
| Fehlermeldungen | Problemumgehung oder Lösungstipps |
| ------------------ | ----------------------------- |
| Für dieses Element wird derzeit ein anderer Vorgang zum Konfigurieren von Schutzeinstellungen ausgeführt. | Warten Sie, bis die vorhergehende Vorgang zum Ändern der Richtlinie abgeschlossen ist, und versuchen Sie es nach einiger Zeit noch mal.|
| Für das ausgewählte Element wird derzeit ein anderer Vorgang ausgeführt. | Warten Sie, bis dieser andere Vorgang abgeschlossen ist, und versuchen Sie es nach einiger Zeit noch mal. |


## <a name="see-also"></a>Siehe auch
Weitere Informationen zum Sichern von Azure-Dateifreigaben finden Sie in den folgenden Artikeln:
- [Sichern von Azure-Dateifreigaben](backup-azure-files.md)
- [Fragen zum Sichern von Azure Files](backup-azure-files-faq.md)
