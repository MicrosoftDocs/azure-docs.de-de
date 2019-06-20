---
title: Verwalten von Azure Data Box und Azure Data Box Heavy über das Azure-Portal | Microsoft-Dokumentation
description: Beschreibt die Verwaltung von Azure Data Box und Azure Data Box Heavy über das Azure-Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/30/2019
ms.author: alkohli
ms.openlocfilehash: 5ece12e117db7b334bce8edfd49a5a622e46e13c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427724"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Verwalten von Azure Data Box und Azure Data Box Heavy über das Azure-Portal

Dieser Artikel gilt sowohl für Azure Data Box als auch für Azure Data Box Heavy. Dieser Artikel beschreibt einige der komplexen Workflows und Verwaltungsaufgaben, die auf dem Azure Data Box-Gerät ausgeführt werden können. Sie können das Data Box-Gerät im Azure-Portal oder auf der lokalen Webbenutzeroberfläche verwalten.

Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe des Azure-Portals ausgeführt werden können. Verwenden Sie das Azure-Portal, um Aufträge und Data Box-Gerät zu verwalten sowie den Auftragsstatus bis zum Abschluss nachzuverfolgen.


## <a name="cancel-an-order"></a>Abbrechen eines Auftrags

Das Abbrechen eines erteilten Auftrags kann aus verschiedenen Gründen erforderlich sein. Sie können den Auftrag nur stornieren, bevor er bearbeitet wird. Sobald der Auftrag bearbeitet und das Data Box-Gerät vorbereitet ist, ist es nicht mehr möglich, den Auftrag zu stornieren.

Führen Sie die folgenden Schritte aus, um einen Auftrag zu stornieren.

1.  Navigieren Sie zu **Übersicht > Abbrechen**.

    ![Abbrechen eines Auftrags (1)](media/data-box-portal-admin/cancel-order1.png)

2.  Geben Sie einen Grund für den Abbruch an.  

    ![Abbrechen eines Auftrags (2)](media/data-box-portal-admin/cancel-order2.png)

3.  Nach Abbruch des Auftrags aktualisiert das Portal den Status des Auftrags und zeigt **Abgebrochen** an.

## <a name="clone-an-order"></a>Klonen eines Auftrags

Klonen kann in bestimmten Situationen hilfreich sein. Angenommen, ein Benutzer hat mithilfe von Data Box einige Daten übertragen. Bei zunehmender Datenmenge ist ein weiteres Data Box-Gerät erforderlich, um die Daten in Azure zu übertragen. In diesem Fall kann der gleiche Auftrag einfach geklont werden.

Führen Sie die folgenden Schritte aus, um einen Auftrag zu klonen.

1.  Navigieren Sie zu **Übersicht > Klonen**. 

    ![Klonen eines Auftrags (1)](media/data-box-portal-admin/clone-order1.png)

2.  Die Auftragsdetails bleiben unverändert. Als Auftragsname wird der Name des ursprünglichen Auftrags mit dem Zusatz *-Clone* verwendet. Aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie die Datenschutzinformationen gelesen haben. Klicken Sie auf **Create**.

Der Klon wird in wenigen Minuten erstellt und im Portal angezeigt.


## <a name="delete-order"></a>Löschen eines Auftrags

Abgeschlossene Aufträge können bei Bedarf gelöscht werden. Der Auftrag enthält Ihre persönlichen Informationen wie Name, Adresse und Kontaktinformationen. Diese persönlichen Informationen werden zusammen mit dem Auftrag gelöscht.

Sie können nur Aufträge löschen, die abgeschlossen sind oder abgebrochen wurden. Führen Sie die folgenden Schritte aus, um einen Auftrag zu löschen.

1. Wechseln Sie zu **All resources** (Alle Ressourcen). Suchen Sie nach Ihrem Auftrag.

2. Klicken Sie auf den Auftrag, den Sie löschen möchten, und navigieren zu **Übersicht**. Klicken Sie auf der Befehlsleiste auf **Löschen**.

    ![Löschen eines Data Box Disk-Auftrags (1)](media/data-box-portal-admin/delete-order1.png)

3. Geben Sie bei entsprechender Aufforderung den Namen des Auftrags ein, um die Auftragslöschung zu bestätigen. Klicken Sie auf **Löschen**.

## <a name="download-shipping-label"></a>Versandetikett herunterladen

Möglicherweise müssen Sie das Adressetikett herunterladen, wenn das E-Ink-Display Ihrer Data Box nicht funktioniert und das Rücksendeetikett nicht angezeigt wird. Da auf der Data Box Heavy kein E-Ink-Display vorhanden ist, gilt dieser Workflow nicht für Data Box Heavy.

Führen Sie die folgenden Schritte aus, um ein Adressetikett herunterzuladen.

1.  Navigieren Sie zu **Übersicht > Versandetikett herunterladen**. Diese Option steht erst zur Verfügung, nachdem das Gerät versendet wurde. 

    ![Versandetikett herunterladen](media/data-box-portal-admin/download-shipping-label.png)

2.  Dadurch wird das folgende Rücksendeetikett heruntergeladen. Speichern Sie das Etikett, und drucken Sie es aus. Falten Sie das Etikett, und stecken Sie es in die Klarsichthülle am Gerät. Vergewissern Sie sich, dass das Etikett sichtbar ist. Entfernen Sie alle Etiketten, die vom vorherigen Versand noch auf dem Gerät vorhanden sind.

    ![Beispiel für Versandetikett](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Bearbeiten der Lieferadresse

Unter Umständen müssen Sie nach Erteilung des Auftrags die Lieferadresse bearbeiten. Dies ist nur bis zum Versand des Geräts möglich. Nach dem Versand des Geräts ist diese Option nicht mehr verfügbar.

Führen Sie die folgenden Schritte aus, um den Auftrag zu bearbeiten.

1. Navigieren Sie zu **Auftragsdetails > Lieferadresse bearbeiten**.

    ![Bearbeiten der Lieferadresse (1)](media/data-box-portal-admin/edit-shipping-address1.png)

2. Bearbeiten und bestätigen Sie die Lieferadresse, und speichern Sie die Änderungen.

    ![Bearbeiten der Lieferadresse (2)](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Bearbeiten der Benachrichtigungsdetails

Unter Umständen müssen Sie die Benutzer ändern, die die E-Mails zum Auftragsstatus erhalten sollen. So muss beispielsweise ein Benutzer informiert werden, wenn das Gerät geliefert oder abgeholt wird. Ein anderer Benutzer muss ggf. informiert werden, wenn der Datenkopiervorgang abgeschlossen ist, damit er sich vor dem Löschen aus der Quelle vergewissern kann, dass sich die Daten im Azure-Speicherkonto befinden. In diesen Fällen können Sie die Benachrichtigungsdetails bearbeiten.

Führen Sie die folgenden Schritte aus, um die Benachrichtigungsdetails zu bearbeiten.

1. Navigieren Sie zu **Auftragsdetails > Benachrichtigungsdetails bearbeiten**.

    ![Bearbeiten der Benachrichtigungsdetails (1)](media/data-box-portal-admin/edit-notification-details1.png)

2. Nun können Sie die Benachrichtigungsdetails bearbeiten und die Änderungen speichern.
 
    ![Bearbeiten der Benachrichtigungsdetails (2)](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Herunterladen des Auftragsverlaufs

Nach Abschluss des Data Box-Auftrags werden die Daten auf den Gerätedatenträgern gelöscht. Ist die Gerätebereinigung abgeschlossen, können Sie den Auftragsverlauf im Azure-Portal herunterladen.

Führen Sie die folgenden Schritte aus, um den Auftragsverlauf herunterzuladen:

1. Navigieren Sie in Ihrem Data Box-Auftrag zu **Übersicht**. Vergewissern Sie sich, dass der Auftrag abgeschlossen ist. Sind Auftrag und Gerätebereinigung abgeschlossen, navigieren Sie zu **Auftragsdetails**. Die Option **Bestellverlauf herunterladen** ist verfügbar.

    ![Herunterladen des Auftragsverlaufs](media/data-box-portal-admin/download-order-history-1.png)

2. Klicken Sie auf **Bestellverlauf herunterladen**. Im heruntergeladenen Verlauf sehen Sie einen Eintrag für die Sendungsverfolgungsprotokolle des Zustelldiensts. Den zwei Knoten auf einer Data Box Heavy-Instanz entsprechend gibt es zwei Protokollgruppen. Unten in diesem Protokoll finden Sie Links zu den folgenden Elementen:
    
   - **Kopierprotokolle:** Diese Protokolle enthalten die Liste der Dateien, bei denen während des Kopierens von Daten von Data Box in Ihr Azure-Speicherkonto Fehler aufgetreten sind.
   - **Überwachungsprotokolle:** Diese Protokolle enthalten Informationen zum Einschalten und zum Zugriff auf Data Box, wenn sich der Dienst außerhalb des Azure-Rechenzentrums befindet.
   - **BOM-Dateien:** Diese Dateien enthalten die Dateiliste (auch als Dateimanifest bezeichnet), die Sie bei der **Versandvorbereitung** herunterladen können und die die Dateinamen, Dateigrößen und Dateiprüfsummen enthält.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     Anschließend können Sie zu Ihrem Speicherkonto navigieren und die Kopierprotokolle anzeigen.

![Protokolle in Speicherkonten](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Sie können auch die Protokolle zur Rückverfolgbarkeit anzeigen, zu denen die Überwachungsprotokolle und die BOM-Dateien zählen.

![Protokolle in Speicherkonten](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Anzeigen des Auftragsstatus

Sobald sich der Gerätestatus im Portal ändert, werden Sie per E-Mail benachrichtigt.

|Auftragsstatus |BESCHREIBUNG |
|---------|---------|
|Bestellt     | Der Auftrag wurde erfolgreich erteilt. <br>Wenn das Gerät verfügbar ist, bestimmt Microsoft das Gerät für den Versand und bereitet es darauf vor. <br> Wenn das Gerät nicht sofort verfügbar ist, wird der Auftrag bearbeitet, sobald das Gerät verfügbar ist. Die Auftragsbearbeitung kann von mehreren Tagen bis zu einigen Monaten dauern. Wenn der Auftrag nicht binnen 90 Tagen erfüllt werden kann, wird er storniert, worüber Sie benachrichtigt werden.         |
|Verarbeitet     | Die Auftragsverarbeitung ist abgeschlossen. Gemäß Ihrem Auftrag wird das Gerät für den Versand im Rechenzentrum vorbereitet.         |
|Versandt     | Der Auftrag wurde versandt. Verwenden Sie die in Ihrem Auftrag im Portal angezeigte Sendungsverfolgungs-ID, um die Sendung zu verfolgen.        |
|Geliefert     | Der Auftrag wurde an die im Auftrag angegebene Adresse geliefert.        |
|Abgeholt     |Ihre Rücksendung wurde vom Zusteller abgeholt und gescannt.         |
|Empfangen     | Ihr Gerät wurde im Azure-Rechenzentrum empfangen und gescannt. <br> Nach Überprüfung der Sendung beginnt das Hochladen der Daten auf dem Gerät.      |
|Daten kopieren     | Die Daten werden gerade kopiert. Verfolgen Sie den Kopierfortschritt Ihres Auftrags im Azure-Portal. <br> Warten Sie, bis der Datenkopiervorgang abgeschlossen ist. |
|Abgeschlossen       |Der Auftrag wurde erfolgreich abgeschlossen.<br> Vergewissern Sie sich, dass sich Ihre Daten in Azure befinden, bevor Sie die lokalen Daten von Servern löschen.         |
|Mit Fehlern abgeschlossen| Die Datenkopie wurde abgeschlossen, aber es sind Fehler beim Kopiervorgang aufgetreten. <br> Überprüfen Sie im Azure-Portal im angegebenen Pfad die Kopierprotokolle.   |
|Canceled            |Der Auftrag wurde abgebrochen. <br> Entweder haben Sie den Auftrag abgebrochen, oder es ist ein Fehler aufgetreten, und der Auftrag wurde durch den Dienst abgebrochen. Wenn der Auftrag nicht binnen 90 Tagen erfüllt werden kann, wird er storniert, und Sie werden benachrichtigt.     |
|Bereinigen | Die Daten auf den Datenträgern des Geräts werden gelöscht. Die Gerätebereinigung gilt als abgeschlossen, wenn der Auftragsverlauf im Azure-Portal zum Download zur Verfügung steht.|



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy](data-box-troubleshoot.md).
