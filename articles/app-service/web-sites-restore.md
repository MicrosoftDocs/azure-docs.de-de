---
title: Wiederherstellen einer App aus einer Sicherung
description: Erfahren Sie, wie Sie Ihre App aus einer Sicherung wiederherstellen. Bestimmte verknüpfte Datenbanken können zusammen mit der App in einem Vorgang wiederhergestellt werden.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: c1b0ce62905424032c2100a1a032fa43ba97578f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999398"
---
# <a name="restore-an-app-in-azure"></a>Wiederherstellen einer App in Azure
In diesem Artikel erfahren Sie, wie Sie eine App in [Azure App Service](../app-service/overview.md) wiederherstellen, die zuvor gesichert wurde (siehe [Sichern einer App in Azure](manage-backup.md)). Sie können Ihre App mit den zugehörigen verknüpften Datenbanken bei Bedarf in einem vorherigen Zustand wiederherstellen oder basierend auf den Sicherungen der ursprünglichen App eine neue App erstellen. Azure App Service unterstützt die folgenden Datenbanken für Sicherung und Wiederherstellung:
- [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure-Datenbank für PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL In-App](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Das Wiederherstellen aus Sicherungen ist für Apps verfügbar, die in den Tarifen **Standard** und **Premium** ausgeführt werden. Informationen zum Hochskalieren der App finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md). Im Tarif **Premium** ist eine größere Anzahl täglicher Sicherungen zulässig ist als im Tarif **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Wiederherstellen einer App aus einer vorhandenen Sicherung
1. Klicken Sie im Azure-Portal auf der Seite **Einstellungen** Ihrer App auf **Sicherungen**, um die Seite **Sicherungen** anzuzeigen. Klicken Sie auf **Wiederherstellen**.
   
    !["Jetzt wiederherstellen" auswählen][ChooseRestoreNow]
2. Wählen Sie auf der Seite **Wiederherstellen** zuerst die Sicherungsquelle aus.
   
    ![Screenshot mit Anzeige der Position zur Auswahl der Sicherungsquelle](./media/web-sites-restore/021ChooseSource1.png)
   
    Die Option **App-Sicherung** zeigt alle vorhandenen Sicherungen der aktuellen App, aus denen Sie eine auswählen können.
    Die Option **Speicher** ermöglicht Ihnen in einem in Ihrem Abonnement vorhandenen Azure Storage-Konto und Container das Auswählen einer Sicherungsdatei im ZIP-Format.
    Wenn Sie versuchen, eine Sicherung einer anderen Anwendung wiederherzustellen, verwenden Sie die Option **Speicher** .
3. Geben Sie anschließend das Ziel für die App-Wiederherstellung unter **Wiederherstellungsziel** an.
   
    ![Screenshot mit Anzeige der Position zur Festlegung des Ziels für die App-Wiederherstellung](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Wenn Sie **Überschreiben** wählen, werden alle Daten in Ihrer vorhandenen App gelöscht und überschrieben. Bevor Sie auf **OK** klicken, stellen Sie sicher, dass alles genau Ihren Vorstellungen entspricht.
   > 
   > 
   
   > [!WARNING]
   > Wenn der App Service Daten in die Datenbank schreibt, während Sie sie wiederherstellen, kann es zu Symptomen wie die Verletzung des PRIMARY KEY und Datenverlust kommen. Es wird empfohlen, den App Service zuerst zu beenden, bevor Sie mit der Wiederherstellung der Datenbank beginnen.
   > 
   > 
   
    Sie können **Vorhandene App** auswählen, um die App-Sicherung in einer anderen App in derselben Ressourcengruppe wiederherzustellen. Bevor Sie diese Option verwenden, sollten Sie bereits eine andere App in der Ressourcengruppe erstellt haben, deren Datenbankkonfiguration derjenigen entspricht, die in der App-Sicherung definiert ist. Sie können auch eine **neue** App erstellen, auf der Ihr Inhalt wiederhergestellt wird.

4. Klicken Sie auf **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Herunterladen oder Löschen einer Sicherung aus einem Speicherkonto
1. Wählen Sie auf der Hauptseite **Durchsuchen** des Azure-Portals die Option **Speicherkonten** aus. Eine Liste Ihrer vorhandenen Speicherkonten wird angezeigt.
2. Wählen Sie das Speicherkonto aus, das die herunterzuladende oder zu löschende Sicherung enthält. Die Seite für das Speicherkonto wird angezeigt.
3. Wählen Sie auf der Seite des Speicherkontos den gewünschten Container aus.
   
    ![Container anzeigen][ViewContainers]
4. Wählen Sie die Sicherungsdatei aus, die Sie herunterladen oder löschen möchten.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klicken Sie je nachdem, was Sie tun möchten, auf **Herunterladen** oder **Löschen**.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Überwachen eines Wiederherstellungsvorgangs
Um Details über den Erfolg oder Misserfolg des Wiederherstellungsvorgangs für die App anzuzeigen, wechseln Sie im Azure-Portal zur Seite **Aktivitätsprotokoll**.  
 

Scrollen Sie nach unten zum gewünschten Wiederherstellungsvorgang, und klicken Sie darauf, um ihn auszuwählen.

Auf der Seite „Details“ werden die verfügbaren Informationen im Zusammenhang mit dem Wiederherstellungsvorgang angezeigt.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Mit [Azure CLI](/cli/azure/install-azure-cli) oder mit [Azure PowerShell](/powershell/azure/) können Sie mithilfe von Skripts die Sicherungsverwaltung automatisieren.

Beispiele finden Sie unter:

- [Azure CLI-Beispiele](samples-cli.md)
- [Azure PowerShell-Beispiele](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
