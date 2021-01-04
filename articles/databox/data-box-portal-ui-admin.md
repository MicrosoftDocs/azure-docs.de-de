---
title: Administratorhandbuch für das Azure Data Box Disk-Portal | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Verwalten der Data Box im Azure-Portal. Verwalten von Bestellungen und Datenträgern sowie Nachverfolgen eines Auftragsstatus im Verlauf.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 538a650c6063422f89c8ed3d1753981a293693b7
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338085"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>Verwalten von Data Box Disk über das Azure-Portal

Die Tutorials in diesem Artikel beziehen sich auf Microsoft Azure Data Box Disk während der Vorschauphase. Dieser Artikel beschreibt einige der komplexen Workflows und Verwaltungsaufgaben, die für die Data Box Disk-Instanz ausgeführt werden können. 

Die Data Box Disk-Instanz kann über das Azure-Portal verwaltet werden. Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe des Azure-Portals ausgeführt werden können. Verwenden Sie das Azure-Portal, um Aufträge und Datenträger zu verwalten und den Status des Auftrags bis zur Auslieferung nachzuverfolgen.

## <a name="cancel-an-order"></a>Abbrechen eines Auftrags

Das Abbrechen eines erteilten Auftrags kann aus verschiedenen Gründen erforderlich sein. Der Auftrag kann nur bis zum Beginn der Datenträgervorbereitung abgebrochen werden. Wenn die Datenträger vorbereitet wurden und der Auftrag verarbeitet wurde, kann er nicht mehr abgebrochen werden. 

Führen Sie die folgenden Schritte aus, um einen Auftrag zu stornieren.

1.  Navigieren Sie zu **Übersicht > Abbrechen**. 

    ![Befehl „Abbrechen“ auf der Registerkarte „Übersicht“ für einen Auftrag](media/data-box-portal-ui-admin/portal-ui-admin-cancel-command.png)

2.  Geben Sie einen Grund für den Abbruch an.  

    ![Grund für die Stornierung des Auftrags](media/data-box-portal-ui-admin/portal-ui-admin-cancel-order-reason.png)

3.  Nach Abbruch des Auftrags aktualisiert das Portal den Status des Auftrags und zeigt **Abgebrochen** an.

    ![Stornierter Auftrag](media/data-box-portal-ui-admin/portal-ui-admin-canceled-order.png)

Sie erhalten keine E-Mail-Benachrichtigung, wenn der Auftrag abgebrochen wird.

## <a name="clone-an-order"></a>Klonen eines Auftrags

Klonen kann in bestimmten Situationen hilfreich sein. Ein Beispiel: Angenommen, ein Benutzer hat mithilfe von Data Box Disk einige Daten übertragen. Bei zunehmender Datenmenge sind weitere Datenträger erforderlich, um die Daten an Azure zu übertragen. In diesem Fall kann der gleiche Auftrag einfach geklont werden.

Führen Sie die folgenden Schritte aus, um einen Auftrag zu klonen.

1.  Navigieren Sie zu **Übersicht > Klonen**. 

    ![Befehl „Klonen“ auf der Registerkarte „Übersicht“ für einen Auftrag](media/data-box-portal-ui-admin/portal-ui-admin-clone-command.png)

2.  Die Auftragsdetails bleiben unverändert. Als Auftragsname wird der Name des ursprünglichen Auftrags mit dem Zusatz *-Clone* verwendet. Aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie die Datenschutzinformationen gelesen haben. Klicken Sie auf **Erstellen**.    

Der Klon wird in wenigen Minuten erstellt und im Portal angezeigt.

[![Geklonter Auftrag](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png)](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png#lightbox) 

## <a name="delete-order"></a>Löschen eines Auftrags

Abgeschlossene Aufträge können bei Bedarf gelöscht werden. Der Auftrag enthält Ihre persönlichen Informationen wie Name, Adresse und Kontaktinformationen. Diese persönlichen Informationen werden zusammen mit dem Auftrag gelöscht.

Sie können nur Aufträge löschen, die abgeschlossen sind oder abgebrochen wurden. Führen Sie die folgenden Schritte aus, um einen Auftrag zu löschen.

1. Wechseln Sie zu **All resources** (Alle Ressourcen). Suchen Sie nach Ihrem Auftrag.

    ![Suchen von Aufträgen](media/data-box-portal-ui-admin/portal-ui-admin-search-data-box-disk-orders.png)

2. Klicken Sie auf den Auftrag, den Sie löschen möchten, und navigieren zu **Übersicht**. Klicken Sie auf der Befehlsleiste auf **Löschen**.

    ![Löschen einer Bestellung](media/data-box-portal-ui-admin/portal-ui-admin-delete-command.png)

3. Geben Sie bei entsprechender Aufforderung den Namen des Auftrags ein, um die Auftragslöschung zu bestätigen. Klicken Sie auf **Löschen**.

     ![Bestätigen der Löschung des Auftrags](media/data-box-portal-ui-admin/portal-ui-admin-confirm-deletion.png)


## <a name="download-shipping-label"></a>Versandetikett herunterladen

Das Adressetikett muss unter Umständen heruntergeladen werden, wenn Sie das Rücksendeetikett, das Ihren Datenträgern beilag, verlegt oder verloren haben. 

Führen Sie die folgenden Schritte aus, um ein Adressetikett herunterzuladen.
1.  Navigieren Sie zu **Übersicht > Versandetikett herunterladen**. Diese Option steht erst zur Verfügung, wenn der Datenträger versendet wurde. 

    ![Versandetikett herunterladen](media/data-box-portal-ui-admin/portal-ui-admin-download-shipping-label.png)

2.  Dadurch wird das folgende Rücksendeetikett heruntergeladen. Speichern Sie das Etikett, drucken Sie es aus, und bringen Sie es an der Rücklieferung an.

    ![Beispiel für Versandetikett](media/data-box-portal-ui-admin/portal-ui-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Bearbeiten der Lieferadresse

Unter Umständen müssen Sie nach Erteilung des Auftrags die Lieferadresse bearbeiten. Diese Option ist nur verfügbar, solange der Datenträger noch nicht versandt wurde. Nach dem Versand des Datenträgers ist diese Option nicht mehr verfügbar.

Führen Sie die folgenden Schritte aus, um den Auftrag zu bearbeiten.

1. Navigieren Sie zu **Auftragsdetails > Lieferadresse bearbeiten**.

    ![Befehl „Lieferadresse bearbeiten“ in „Auftragsdetails“](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-command.png)

2. Nun können Sie die Lieferadresse bearbeiten und die Änderungen speichern.

    ![Dialogfeld „Lieferadresse bearbeiten“](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Bearbeiten der Benachrichtigungsdetails

Unter Umständen müssen Sie die Benutzer ändern, die die E-Mails zum Auftragsstatus erhalten sollen. So kann es beispielsweise erforderlich sein, dass ein Benutzer informiert wird, wann der Datenträger geliefert oder abgeholt wird. Ein anderer Benutzer muss ggf. informiert werden, wenn der Datenkopiervorgang abgeschlossen ist, damit er sich vor dem Löschen aus der Quelle vergewissern kann, dass sich die Daten im Azure-Speicherkonto befinden. In diesen Fällen können Sie die Benachrichtigungsdetails bearbeiten.

Führen Sie die folgenden Schritte aus, um die Benachrichtigungsdetails zu bearbeiten.

1. Navigieren Sie zu **Auftragsdetails > Benachrichtigungsdetails bearbeiten**.

    ![Befehl „Benachrichtigungsdetails bearbeiten“ in „Auftragsdetails“](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-command.png)

2. Nun können Sie die Benachrichtigungsdetails bearbeiten und die Änderungen speichern.
 
    ![Dialogfeld „Benachrichtigungsdetails bearbeiten“](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-dbox.png)

## <a name="view-order-status"></a>Anzeigen des Auftragsstatus

|Auftragsstatus |BESCHREIBUNG |
|---------|---------|
|Bestellt     | Der Auftrag wurde erfolgreich erteilt. <br> Sollten die Datenträger nicht verfügbar sein, erhalten Sie eine entsprechende Benachrichtigung. <br>Sind die Datenträger verfügbar, identifiziert Microsoft einen Datenträger für den Versand und bereitet das Datenträgerpaket vor.        |
|Verarbeitet     | Die Auftragsverarbeitung ist abgeschlossen. <br> Während der Auftragsverarbeitung werden folgende Aktionen ausgeführt:<li>Die Datenträger werden mithilfe von AES-128-BitLocker-Verschlüsselung verschlüsselt. </li> <li>Die Data Box-Datenträger werden gesperrt, um nicht autorisierte Zugriffe zu verhindern.</li><li>Während dieses Vorgangs wird der Hauptschlüssel zum Entsperren der Datenträger generiert.</li>        |
|Versandt     | Der Auftrag wurde versandt. Die Lieferung sollte in ein bis zwei Tagen bei Ihnen eintreffen.        |
|Geliefert     | Der Auftrag wurde an die im Auftrag angegebene Adresse geliefert.        |
|Abgeholt     |Ihre Rücklieferung wurde abgeholt. <br> Nach Eingang Ihrer Lieferung im Azure-Datencenter werden die Daten automatisch in Azure hochgeladen.         |
|Empfangen     | Ihre Datenträger sind im Azure-Datencenter eingegangen. Der Datenkopiervorgang beginnt in Kürze.        |
|Daten kopiert     |Die Daten werden gerade kopiert.<br> Warten Sie, bis der Datenkopiervorgang abgeschlossen ist.         |
|Abgeschlossen       |Der Auftrag wurde erfolgreich abgeschlossen.<br> Vergewissern Sie sich, dass sich Ihre Daten in Azure befinden, bevor Sie die lokalen Daten von Servern löschen.         |
|Mit Fehlern abgeschlossen| Die Daten wurden kopiert, es sind jedoch Fehler aufgetreten. <br> Sehen Sie sich die Fehlerprotokolle für das Hochladen unter dem Pfad aus der **Übersicht** an. Weitere Informationen finden Sie unter [Herunterladen von Uploadprotokollen](data-box-disk-troubleshoot-upload.md#download-logs).   |
|Canceled            |Der Auftrag wurde abgebrochen. <br> Entweder haben Sie den Auftrag abgebrochen, oder es ist ein Fehler aufgetreten, und der Auftrag wurde durch den Dienst abgebrochen.     |



## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Behandeln von Data Box Disk-Problemen](data-box-disk-troubleshoot.md).
