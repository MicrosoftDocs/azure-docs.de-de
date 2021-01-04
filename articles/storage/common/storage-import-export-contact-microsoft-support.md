---
title: Erstellen eines Supporttickets oder -falls für Azure Import/Export-Aufträge | Microsoft-Dokumentation
description: Erfahren Sie, wie Supportanfragen bei Problemen im Zusammenhang mit Ihrem Import/Export-Auftrag protokolliert werden.
services: storsimple
author: alkohli
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: fdee5420fa6a50974250c45260a3727755bfcba7
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542591"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Öffnen eines Supporttickets für einen Import/Export-Auftrag

Wenn Probleme mit Ihrem Import/Export-Dienst auftreten, können Sie eine Serviceanfrage für den technischen Support erstellen. In diesem Artikel wird Folgendes beschrieben:

* Erstellen einer Supportanfrage
* Verwalten des Lebenszyklus einer Supportanfrage innerhalb des Portals

## <a name="create-a-support-request"></a>Erstellen einer Supportanfrage

Führen Sie die folgenden Schritte aus, um eine Supportanfrage zu erstellen.

1. Navigieren Sie zu Ihrem Import/Export-Auftrag. Navigieren Sie zum Abschnitt **SUPPORT + PROBLEMBEHANDLUNG**, und wählen Sie anschließend **Neue Supportanfrage** aus.
     
    ![Grundlagen](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. Wählen Sie unter **Neue Supportanfrage** die Option **Grundlagen** aus. Führen Sie unter **Grundlagen** folgende Schritte aus:
    
    1. Wählen Sie aus der Dropdownliste **Problemtyp** den Eintrag **Technisch** aus.
    2. Wählen Sie Ihr **Abonnement** aus.
    3. Überprüfen Sie unter **Dienst** die Option **Meine Dienste**. In der Dropdownliste können Sie eine der folgenden Optionen auswählen: **Speicherkontoverwaltung**, **Blob** oder **Datei**. 
        - Wenn Sie **Speicherkontoverwaltung** wählen, wählen Sie **Ressource** und **Supportplan** aus.
            ![Auswählen der Option „Speicherkontoverwaltung“](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - Wenn Sie **Blob** wählen, wählen Sie **Ressource**, **Containernamen** (optional) und **Supportplan** aus.
            ![Auswählen der Option „Blob“](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - Wenn Sie **Datei** auswählen, wählen Sie **Ressource**, **Dateifreigabenamen** (optional) und **Supportplan** aus. ![Auswählen der Option „Datei“](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. Wählen Sie **Weiter** aus.

3. Wählen Sie unter **Neue Supportanfrage** Schritt 2 **Problem** aus. Führen Sie unter **Problem** folgende Schritte aus:
    
    1. Wählen Sie für **Schweregrad** die Option **C – Minimale Auswirkungen**. Diese Auswahl wird bei Bedarf vom Support aktualisiert.
    2. Wählen Sie für **Problemtyp** die Option **Datenmigration** aus.
    3. Wählen Sie für **Kategorie** die Option **Import – Export** aus.
    4. Geben Sie einen **Titel** für das Problem und weitere **Details** an.
    5. Geben Sie Startdatum und -uhrzeit des Problems an.
    6. Wählen Sie unter **Dateiupload** das Ordnersymbol aus, um zu weiteren Dateien zu navigieren, die hochgeladen werden sollen.
    7. Aktivieren Sie **Diagnoseinformationen freigeben**.
    8. Wählen Sie **Weiter** aus.

       ![Problem](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. Wählen Sie in **Neue Supportanfrage** die Option **Schritt 3: Kontaktinformationen** aus. Führen Sie unter **Kontaktinformationen** folgende Schritte aus:

   1. Geben Sie in **Kontaktoptionen** Ihre bevorzugte Kontaktmethode (telefonisch oder per E-Mail) und die Sprache ein. Die Antwortzeit wird die anhand Ihres Abonnementplans automatisch ausgewählt.
   2. Geben Sie in den „Kontaktinformationen“ Ihren Namen, Ihre E-Mail-Adresse, einen optionalen Kontakt und das Land/die Region ein. Aktivieren Sie das Kontrollkästchen **Kontaktänderungen für zukünftige Supportanfragen speichern**.
   3. Klicken Sie auf **Erstellen**.
   
       ![Kontaktinformationen](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Microsoft Support verwendet diese Informationen, um Ihnen weitere Informationen, Diagnosen und Problemlösungen zur Verfügung zu stellen.
      Nach dem Übermitteln Ihrer Anfrage setzt sich ein Supporttechniker mit Ihnen in Verbindung, um Ihre Anfrage zu bearbeiten.

## <a name="manage-a-support-request"></a>Erstellen einer Supportanfrage

Nach dem Erstellen einer Supportanfrage können Sie den Lebenszyklus des Tickets vom Portal aus verwalten.

#### <a name="to-manage-your-support-requests"></a>So verwalten Sie Ihre Supportanfragen

1. Um zur Hilfe- und Supportseite zu gelangen, navigieren Sie zu **Durchsuchen > Hilfe und Support**.

    ![Der Screenshot zeigt das Dialogfeld „Hilfe“.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. Die Tabelle **Kürzlich gesendete Supportanfragen** wird unter **Hilfe + Support** angezeigt.

    ![Der Screenshot zeigt die Seite „Hilfe und Support“ mit Ihrer Supportanfrage in einem geöffneten Status.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. Klicken Sie auf eine Supportanfrage. Sie können den Status und die Details dieser Anfrage anzeigen. Wählen Sie **+ Neue Nachricht** aus, wenn Sie diese Anfrage nachverfolgen möchten.

    ![Der Screenshot zeigt die Option „Neue Nachricht“, die für diese Anfrage ausgewählt wurde.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [den Azure Import/Export-Dienst zum Übertragen von Daten in und aus Azure Storage verwenden](storage-import-export-service.md).
