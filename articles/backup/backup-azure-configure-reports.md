---
title: Konfigurieren von Berichten für Azure Backup
description: Erfahren Sie, wie Sie Power BI-Berichte für Azure Backup mit einem Recovery Services-Tresor konfigurieren.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: adigan
ms.openlocfilehash: e3004a44958d75d18d608a2fbed7ccc44a00dc93
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60642677"
---
# <a name="configure-azure-backup-reports"></a>Konfigurieren von Azure Backup-Berichten
Dieser Artikel erläutert die Schritte zum Konfigurieren von Berichten für Azure Backup mit dem Recovery Services-Tresor. Außerdem erfahren Sie, wie Sie mit Power BI auf Berichte zugreifen. Nachdem Sie diese Schritte abgeschlossen haben, können Sie direkt Power BI aufrufen, um Berichte anzuzeigen, anzupassen und zu erstellen.

> [!IMPORTANT]
> Ab dem 1. November 2018 kann es bei einigen Kunden in Power BI zu Problemen beim Laden der Daten in die Azure Backup-App mit folgendem Fehler kommen: „Am Ende der JSON-Eingabe wurden überzählige Zeichen ermittelt. Die Ausnahme wurde von der IDataReader-Schnittstelle ausgelöst.“
Dies liegt an einer Änderung des Formats, in dem Daten in das Speicherkonto geladen werden.
Laden Sie die aktuelle App (Version 1.8) herunter, um dieses Problem zu vermeiden.
>
>

## <a name="supported-scenarios"></a>Unterstützte Szenarien
- Azure Backup-Berichte werden für die Sicherung von Azure-VMs und Dateien/Ordnern in der Cloud mit dem Azure Recovery Services-Agent unterstützt.
- Berichte für Azure SQL-Datenbank, Azure-Dateifreigaben, Data Protection Manager und Azure Backup Server werden aktuell nicht unterstützt.
- Sie können die Berichte für verschiedene Tresore und Abonnements anzeigen, sofern für alle Tresore dasselbe Speicherkonto konfiguriert ist. Das ausgewählte Speicherkonto muss sich in der gleichen Region wie der Recovery Services-Tresor befinden.
- Die Häufigkeit der geplanten Aktualisierungen für die Berichte beträgt in Power BI 24 Stunden. Sie können auch in Power BI eine Ad-hoc-Aktualisierung der Berichte ausführen. In diesem Fall werden die neuesten Daten im Kundenspeicherkonto zum Rendern von Berichten verwendet.

## <a name="prerequisites"></a>Voraussetzungen
- Erstellen Sie ein [Azure-Speicherkonto](../storage/common/storage-quickstart-create-account.md), um Berichte zu konfigurieren. Dieses Speicherkonto wird zum Speichern von berichtsbezogenen Daten verwendet.
- [Erstellen Sie ein Power BI-Konto](https://powerbi.microsoft.com/landing/signin/), um im Power BI-Portal Ihre eigenen Berichte anzuzeigen, anzupassen und zu erstellen.
- Registrieren Sie den Ressourcenanbieter **Microsoft.insights**, falls er noch nicht registriert ist. Verwenden Sie die Abonnements für das Speicherkonto und den Recovery Services-Tresor, damit die Berichtsdaten an das Speicherkonto übermittelt werden können. Öffnen Sie zum Registrieren das Azure-Portal, wählen Sie **Abonnement** > **Ressourcenanbieter** aus, und markieren Sie den gewünschten Anbieter.

## <a name="configure-storage-account-for-reports"></a>Konfigurieren des Speicherkontos für Berichte
Diese Schritte zeigen, wie Sie das Speicherkonto für einen Recovery Services-Tresor über das Azure-Portal konfigurieren. Diese Konfiguration wird einmalig ausgeführt. Wenn das Speicherkonto konfiguriert wurde, können Sie direkt Power BI öffnen, um das Inhaltspaket anzuzeigen und Berichte zu verwenden.

1. Falls Sie bereits über einen geöffneten Recovery Services-Tresor verfügen, fahren Sie mit dem nächsten Schritt fort. Wenn Sie keinen Recovery Services-Tresor geöffnet haben, wählen Sie im Azure-Portal **Alle Dienste** aus.

   * Geben Sie in der Liste mit den Ressourcen **Recovery Services**ein.
   * Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Recovery Services-Tresore**, wenn der Eintrag angezeigt wird.
   * Die Liste mit den Recovery Services-Tresoren wird angezeigt. Wählen Sie in der Liste mit den Recovery Services-Tresoren einen Tresor aus.

     Das ausgewählte Tresor-Dashboard wird geöffnet.
2. Wählen Sie in der Liste der Elemente, die unter dem Tresor angezeigt wird, im Abschnitt **Überwachung und Berichte** **Sicherungsberichte** aus, um das Speicherkonto für Berichte zu konfigurieren.

      ![Wählen Sie in „Sicherungsberichte“ das Menüelement „Schritt 2“](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Wählen Sie auf dem Blatt **Sicherungsberichte** den Link **Diagnoseeinstellungen** aus. Dadurch wird die Benutzeroberfläche **Diagnoseeinstellungen** geöffnet, die zum Übertragen der Daten mithilfe von Push an das Speicherkonto des Kunden verwendet wird.

      ![Schritt 3: Aktivieren der Diagnose](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Wählen Sie **Diagnose aktivieren** aus, um eine Benutzeroberflächen zum Konfigurieren eines Speicherkontos zu öffnen.

      ![Schritt 4: Aktivieren der Diagnose](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Geben Sie im Feld **Name** einen Einstellungsnamen ein. Aktivieren Sie das Kontrollkästchen **In einem Speicherkonto archivieren**, damit die Berichtsdaten an das Speicherkonto übermittelt werden können.

      ![Schritt 5: Aktivieren der Diagnose](./media/backup-azure-configure-reports/select-setting-name.png)
6. Wählen Sie **Speicherkonto**, in der Liste zum Speichern der Berichtsdaten das entsprechende Abonnement und Speicherkonto und dann **OK** aus.

      ![Schritt 6: Auswählen des Speicherkontos](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Aktivieren Sie im Abschnitt **Protokoll** das Kontrollkästchen **AzureBackupReport**. Verwenden Sie den Schieberegler, um einen Aufbewahrungszeitraum für diese Berichtsdaten auszuwählen. Die Berichtsdaten werden für den mit dem Schieberegler ausgewählten Zeitraum im Speicherkonto aufbewahrt.

      ![Schritt 7: Speichern des Speicherkontos](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Überprüfen Sie alle Änderungen, und wählen Sie **Speichern** aus. Dadurch wird sichergestellt, dass alle Änderungen gespeichert werden und das Speicherkonto nun für das Speichern von Berichtsdaten konfiguriert ist.

9. Die Tabelle **Diagnoseeinstellungen** zeigt nun die neue, aktivierte Einstellung für den Tresor an. Falls sie nicht sichtbar ist, aktualisieren Sie die Tabelle, um die aktualisierte Einstellung anzuzeigen.

      ![Schritt 9: Anzeigen der Diagnoseeinstellung](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Nachdem Sie die Berichte durch Speichern des Speicherkontos konfiguriert haben, warten Sie *24 Stunden*, bis der erste Datenpush abgeschlossen ist. Importieren Sie die Azure Backup-App erst danach in Power BI. Weitere Informationen finden Sie im Bereich [Häufig gestellte Fragen](#frequently-asked-questions).
>
>

## <a name="view-reports-in-power-bi"></a>Anzeigen von Berichten in Power BI
Nachdem das Speicherkonto mit dem Recovery Services-Tresor für Berichte konfiguriert wurde, dauert es etwa 24 Stunden, bis die ersten Daten an das Speicherkonto übermittelt werden. 24 Stunden nach Einrichten des Speicherkontos können Sie anhand der folgenden Schritte Berichte in Power BI anzeigen.
Wenn Sie den Bericht anpassen und freigeben möchten, erstellen Sie einen Arbeitsbereich, und führen Sie die folgenden Schritte aus.

1. Melden Sie sich bei Power BI [an](https://powerbi.microsoft.com/landing/signin/).
2. Wählen Sie **Daten abrufen** aus. Wählen Sie unter **Weitere Möglichkeiten zum Erstellen eigener Inhalte** die Option **Dienstinhaltspakete** aus. Befolgen Sie die Schritte in der [Power BI-Dokumentation zur Verbindungsherstellung mit einem Dienst](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Geben Sie in der Leiste **Suche** **Azure Backup** ein, und wählen Sie **Jetzt holen** aus.

      ![Abrufen eines Inhaltspakets](./media/backup-azure-configure-reports/content-pack-get.png)
4. Geben Sie den Namen des Speicherkonto ein, das in Schritt 5 konfiguriert wurde, und wählen Sie **Weiter** aus.

    ![Den Namen des Speicherkontos erfassen](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Geben Sie bei Verwendung der Authentifizierungsmethode „Schlüssel“ den Speicherkontoschlüssel für dieses Speicherkonto ein. Zum [Anzeigen und Kopieren der Speicherzugriffsschlüssel](../storage/common/storage-account-manage.md#access-keys) navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.

     ![Speicherkonto erfassen](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Wählen Sie **Anmelden**. Nach der erfolgreichen Anmeldung wird eine Benachrichtigung über den Datenimport angezeigt.

    ![Importieren eines Inhaltspakets](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Nach Abschluss des Imports erscheint die Benachrichtigung **Erfolgreich**. Wenn viele Daten im Speicherkonto vorhanden sind, kann der Import des Inhaltspakets einige Zeit dauern.

    ![Erfolgreicher Import des Inhaltspakets](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Wenn die Daten erfolgreich importiert wurden, wird das **Azure Backup**-Inhaltspaket im Navigationsbereich in **Apps** angezeigt. Unter **Dashboards**, **Berichte** und **Datasets** wird jetzt Azure Backup in der Liste angezeigt.

8. Wählen Sie unter **Dashboards** **Azure Backup** aus. Daraufhin wird eine Reihe angehefteter Schlüsselberichte angezeigt.

      ![Azure Backup-Dashboard](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Um alle Berichte anzuzeigen, wählen Sie einen beliebigen Bericht im Dashboard aus.

      ![Azure Backup-Auftragsintegrität](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Wählen Sie jede Registerkarte in den Berichten aus, um die Berichte in diesem Bereich anzuzeigen.

      ![Azure Backup-Berichtsregisterkarten](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Wie überprüfe ich, ob bereits Berichtsdaten an ein Speicherkonto übermittelt werden?
Öffnen Sie das konfigurierte Speicherkonto, und wählen Sie Container aus. Wenn der Container einen Eintrag für „insights-logs-azurebackupreport“ aufweist, bedeutet dies, dass Berichtsdaten übermittelt werden.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Wie oft werden Daten an das Speicherkonto und das Azure Backup-Inhaltspaket in Power BI mithilfe von Push übertragen?
  Nach dem Aktivieren dauert es etwa 24 Stunden, bis Daten mithilfe von Push an ein Speicherkonto übertragen werden. Nach dem ersten Push gelten für die Aktualisierungshäufigkeit der Daten die Angaben in der folgenden Abbildung.

  * Daten zu **Aufträgen**, **Warnungen**, **Sicherungselementen**, **Tresoren**, **geschützten Servern** und **Richtlinien** werden beim Protokollieren mithilfe von Push an das Speicherkonto des Kunden übertragen.

  * Daten bezüglich des **Speichers** werden alle 24 Stunden mithilfe von Push an das Speicherkonto des Kunden übertragen.

       ![Übermittlungshäufigkeit von Azure Backup-Berichtsdaten](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI verfügt über eine [täglich geplante Aktualisierung](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Sie können die Daten in Power BI für das Inhaltspaket manuell aktualisieren.

### <a name="how-long-can-i-retain-reports"></a>Wie lange kann ich Berichte aufbewahren?
Beim Einrichten eines Speicherkontos können Sie einen Aufbewahrungszeitraum für Berichtsdaten auswählen. Führen Sie Schritt 6 im Abschnitt [Konfigurieren des Speicherkontos für Berichte](backup-azure-configure-reports.md#configure-storage-account-for-reports) aus. Außerdem können Sie [Berichte in Excel analysieren](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) und sie Ihren Anforderungen entsprechend für einen längeren Zeitraum aufbewahren.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Werden alle meine Daten in Berichten nach dem Konfigurieren des Speicherkontos angezeigt?
 Alle Daten, die nach der Konfiguration des Speicherkontos generiert werden, werden mithilfe von Push an das Speicherkonto übertragen und stehen in den Berichten zur Verfügung. Aufträge, die sich in Bearbeitung befinden, werden nicht mithilfe von Push für die Berichterstellung übertragen. Wenn ein Auftrag abgeschlossen wird oder fehlschlägt, wird er an die Berichte gesendet.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Ich habe das Speicherkonto bereits so konfiguriert, dass Berichte angezeigt werden. Kann ich die Konfiguration ändern, um ein anderes Speicherkonto zu verwenden?
Ja, Sie können die Konfiguration ändern und auf ein anderes Speicherkonto zeigen. Verwenden Sie beim Verbinden mit dem Azure Backup-Inhaltspaket das neu konfigurierte Speicherkonto. Sobald ein anderes Speicherkonto konfiguriert ist, werden neue Daten an dieses Speicherkonto übermittelt. Daten, die vor der Konfigurationsänderung stammen, verbleiben im älteren Speicherkonto.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kann ich Berichte für verschiedene Tresore und Abonnements anzeigen?
Ja, Sie können dasselbe Speicherkonto für verschiedene Tresore konfigurieren, um tresorübergreifende Berichte anzuzeigen. Sie können auch dasselbe Speicherkonto für Tresore in verschiedenen Abonnements verwenden. Dieses Speicherkonto lässt sich dann beim Verbinden mit dem Azure Backup-Inhaltspaket in Power BI verwenden, um die Berichte anzuzeigen. Das ausgewählte Speicherkonto muss sich in der gleichen Region wie der Recovery Services-Tresor befinden.

## <a name="troubleshooting-errors"></a>Problembehandlung
| Fehlerdetails | Lösung |
| --- | --- |
| Nach dem Einrichten des Speicherkontos für Backup-Berichte wird unter **Speicherkonto** weiterhin **Nicht konfiguriert** angezeigt. | Wenn das Speicherkonto erfolgreich konfiguriert wurde, werden die Berichtsdaten trotz dieses Problems übertragen. Um dieses Problem zu beheben, öffnen Sie das Azure-Portal, und wählen Sie **Alle Dienste** > **Diagnoseeinstellungen** > **Recovery Services-Tresor** > **Einstellung bearbeiten** aus. Löschen Sie die zuvor konfigurierte Einstellung, und erstellen Sie auf demselben Blatt eine neue Einstellung. Wählen Sie diesmal im Feld **Name** die Option **Dienst** aus. Nun wird das konfigurierte Speicherkonto angezeigt. |
|Nach dem Import des Azure Backup-Inhaltspakets in Power BI wird die Fehlermeldung „404: Container wurde nicht gefunden.“ angezeigt. | Wie bereits erwähnt, werden Berichte erst 24 Stunden nach ihrer Konfiguration im Recovery Services-Tresor korrekt in Power BI angezeigt. Wenn Sie vor Ablauf dieser 24 Stunden versuchen, auf die Berichte zuzugreifen, wird diese Fehlermeldung angezeigt, weil noch nicht alle Daten zum Anzeigen gültiger Berichte vorhanden sind. |

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das Speicherkonto konfiguriert und das Azure Backup-Inhaltspaket importiert haben, müssen Sie die Berichte anpassen und Berichte mithilfe eines Datenmodells zur Berichterstellung erstellen. Weitere Informationen finden Sie in den folgenden Artikeln.

* [Verwenden eines Datenmodells für die Azure Backup-Berichterstellung](backup-azure-reports-data-model.md)
* [Filtern von Berichten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Erstellen von Berichten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
