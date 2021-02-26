---
title: Tutorial zur Rücksendung von Azure Data Box | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure Data Box zurücksenden. Die Informationen umfassen die Versandvorbereitung, den Versand von Data Box, die Überprüfung des Datenuploads sowie die Datenlöschung von Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 267094ea6a7295a65b93fb7700c97e4280da341d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539110"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Zurücksenden der Azure Data Box und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Zurücksenden der Data Box und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target="docs"

In diesem Tutorial wird beschrieben, wie Sie die Azure Data Box zurücksenden und die in Azure hochgeladenen Daten überprüfen.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben die Schritte ausgeführt in [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-deploy-copy-data.md).
* Die Durchführung der Kopieraufträge ist abgeschlossen, und auf der Seite **Verbindung herstellen und Daten kopieren** werden keine Fehler angezeigt. Die **Versandvorbereitung** kann nicht durchgeführt werden, wenn noch Kopieraufträge in Bearbeitung oder auf der Seite **Verbindung herstellen und Daten kopieren** Fehler aufgeführt sind.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Nach dem Datenkopiervorgang bereiten Sie das Gerät vor und versenden es. Wenn das Gerät das Azure-Datencenter erreicht, werden die Daten automatisch in Azure hochgeladen.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

Vergewissern Sie sich vor der Vorbereitung für den Versand, dass Kopieraufträge abgeschlossen sind.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zur Seite **Für den Versand vorbereiten**, und beginnen Sie mit der Versandvorbereitung.
2. Schalten Sie das Gerät auf der lokalen Webbenutzeroberfläche aus. Ziehen Sie die Kabel vom Gerät ab.

Die nächsten Schritte hängen davon ab, wo Sie das Gerät zurückgeben.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Zurücksenden der Data Box

Vergewissern Sie sich, dass die Daten vollständig auf das Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war. Die Vorgehensweise ist abhängig von der Region, in der das Gerät versendet wird.

::: zone-end

## <a name="us-canada-europe"></a>[USA, Kanada, Europa](#tab/in-us-canada-europe)

Wenn Sie das Gerät in den USA, in Kanada oder in Europa zurücksenden möchten, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass das Gerät ausgeschaltet ist und die Kabel entfernt wurden.
2. Wickeln Sie das mit dem Gerät gelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Stellen Sie sicher, dass das Adressetikett auf dem Freihanddisplay angezeigt wird, und vereinbaren Sie einen Abholtermin mit Ihrem Kurierdienst. Falls das Adressetikett beschädigt oder nicht mehr vorhanden ist oder nicht in der E-Ink-Anzeige angezeigt wird, wenden Sie sich an den Microsoft-Support. Sofern dies vom Support empfohlen wird, können Sie im Azure-Portal zu **Übersicht > Versandetikett herunterladen** navigieren. Laden Sie das Versandetikett herunter, und bringen Sie es am Gerät an. 
4. Planen Sie die Abholung durch UPS, falls Sie das Gerät zurücksenden. So planen Sie die Abholung:

    * Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
    * Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie in der E-Ink-Anzeige oder auf dem gedruckten Etikett finden. Wenn Sie die Nachverfolgungsnummer nicht angeben, wird bei der Abholung eine zusätzliche Gebühr erhoben.
    * Falls während der Planung einer Abholung Probleme auftreten oder wenn Sie aufgefordert werden, zusätzliche Gebühren zu bezahlen, wenden Sie sich an Azure Data Box Operations. Senden Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Wenn Sie keine Abholung planen können oder möchten, können Sie die Data Box auch an der nächstgelegenen Versandstelle abgeben.
4. Nachdem die Data Box vom Kurierdienst abgeholt und eingescannt wurde, wird der Status der Bestellung im Portal in **Abgeholt** geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="australia"></a>[Australien](#tab/in-australia)

Bei Azure-Datencentern in Australien ist aus Sicherheitsgründen eine zusätzliche Benachrichtigung erforderlich. Alle eingehenden Lieferungen müssen vorab angekündigt werden. Gehen Sie für den Versand in Australien wie folgt vor:

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Vergewissern Sie sich, dass die Daten vollständig auf das Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war.
3. Schalten Sie das Gerät aus, und entfernen Sie die Kabel.
4. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
5. Reservieren Sie online unter dem [DHL-Link](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) einen Abholtermin.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="japan"></a>[Japan](#tab/in-japan)

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Schalten Sie das Gerät aus, und entfernen Sie die Kabel.
3. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
4. Tragen Sie den Namen Ihres Unternehmens und Ihre Adressdaten als Absenderinformationen in den Rücksendeschein ein.
5. Senden Sie Quantium Solutions über die folgende E-Mail-Vorlage eine E-Mail.

    * Falls der Rücksendeschein der japanischen Post für eine Nachnahmesendung („Chakubarai“) nicht enthalten war oder fehlt, weisen Sie in dieser E-Mail darauf hin. Quantium Solutions Japan fordert die japanische Post dann auf, den Rücksendeschein bei der Abholung mitzubringen.
    * Senden Sie bei mehreren Bestellungen eine E-Mail, um sicherzustellen, dass die einzelnen Bestellungen abgeholt werden.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body:
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Nachdem die Abholung gebucht wurde, erhalten Sie eine E-Mail-Bestätigung von Quantium Solutions. Die E-Mail-Bestätigung enthält auch Informationen zum Nachnahme-Rücksendeschein („Chakubarai“).

Den Support von Quantium Solutions erreichen Sie bei Bedarf wie folgt (in japanische Sprache):

* E-Mail-Adresse: Customerservice.JP@quantiumsolutions.com 
* Telefonnummer: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Notieren Sie sich die Nachverfolgungsnummer (als Referenznummer auf der Seite **Versandvorbereitung** der lokalen Webbenutzeroberfläche von Data Box angezeigt). Die Nachverfolgungsnummer ist nach erfolgreichem Abschluss der **Versandvorbereitung** verfügbar. Laden Sie das Adressetikett von dieser Seite herunter, und bringen Sie es auf dem Versandkarton an.
3. Schalten Sie das Gerät aus, und entfernen Sie die Kabel.
4. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts. 
5. Senden Sie eine E-Mail an den SingPost-Kundendienst, indem Sie die folgende E-Mail-Vorlage mit der Nachverfolgungsnummer verwenden:

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Für Anforderungen, die an einem Werktag eingehen, gilt Folgendes:
   > * Bei Anforderung vor 15 Uhr erfolgt die Abholung am nächsten Werktag zwischen 9 und 13 Uhr.
   > * Bei Anforderung nach 15 Uhr erfolgt die Abholung am nächsten Werktag zwischen 14 und 18 Uhr.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="south-africa"></a>[Südafrika](#tab/in-sa)

1. Verpacken Sie das Gerät für den Rückversand im Originalkarton.
2. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Notieren Sie sich die Nachverfolgungsnummer, die auf der lokalen Webbenutzeroberfläche von Data Box auf der Seite **Für den Versand vorbereiten** als Referenznummer angezeigt wird. Die Nachverfolgungsnummer ist nach erfolgreichem Abschluss der Versandvorbereitung verfügbar. Laden Sie das Adressetikett von dieser Seite herunter, und bringen Sie es am Versandkarton an.
4. Fordern Sie beim Azure Data Box Operations-Team einen Rückgabecode an. Ein Rückgabecode ist für den Rückversand des Pakets an das Rechenzentrum erforderlich. Senden Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com). Notieren Sie diesen Code auf dem Adressetikett neben der Rücksendeadresse. Dort ist er gut sichtbar.
5. Vereinbaren Sie eine Abholung mit DHL mit einer der folgenden Methoden:
 
   * Reservieren Sie online bei DHL Express für Südafrika unter [**Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) (Buchen Sie eine Abholung) einen Abholtermin.
   * Senden Sie eine E-Mail an [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com), und verwenden Sie dabei die folgende Vorlage:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Alternativ können Sie das Paket auch in einem DHL-Paketshop in Ihrer Nähe abgeben.

6. Sollten Probleme auftreten, senden Sie eine E-Mail mit Einzelheiten zu den Problemen an [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com). Geben Sie im Betreff die Frachtbriefnummer an. Sie können auch die Nummer +27(0)119213902 anrufen.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Verpacken Sie das Gerät für den Rückversand im Originalkarton.
2. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Rufen Sie unter **(852) 2318 1213** die Hotline von **Quantium Solutions** an (Geschäftszeiten: Montag bis Freitag von 9 bis 18 Uhr).  
4. Geben Sie „Microsoft Azure Pickup“ (Microsoft Azure-Abholung) sowie die Referenz- und die Sendungsverfolgungsnummer (oberhalb des Strichcodes) auf dem Rücksendeetikett an, um eine Abholung zu veranlassen.
5. Sie erhalten eine mündliche Bestätigung des Abholplans. Sollte der Kurier den Abholtermin nicht einhalten, rufen Sie die Hotline von Quantium Solutions an, um eine alternative Lösung zu finden.
6. Wenn Sie eine Abholung mit Quantium Solutions vereinbart haben, leiten Sie die Bestätigung unter Verwendung der folgenden Vorlage an [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) weiter:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Sollten Probleme auftreten, senden Sie eine E-Mail mit Einzelheiten zu den Problemen an Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com). Geben Sie im Betreff den Auftragsnamen an.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 

::: zone-end

## <a name="united-arab-emirates"></a>[Vereinigte Arabische Emirate](#tab/in-uae)

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Vergewissern Sie sich, dass die Daten vollständig auf das Gerät kopiert wurden und dass der Schritt **Versandvorbereitung** erfolgreich war.
3. Notieren Sie sich die Referenznummer, die auf der lokalen Webbenutzeroberfläche des Geräts auf der Seite **Versandvorbereitung** angezeigt wird.
4. Schalten Sie das Gerät aus, und entfernen Sie die Kabel. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
6. Verpacken Sie das Gerät für den Rückversand im Originalkarton.
7. Senden Sie eine E-Mail an [Azure Data Box Operations](mailto:adbops@microsoft.com), um eine ID zum Identifizieren des Pakets beim Eintreffen im Rechenzentrum zu erhalten.
8. Notieren Sie diese ID gut sichtbar auf dem gedruckten Adressetikett neben der Adresse für den Rückversand.  
9. Reservieren Sie online unter [DHL Express für VAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab) > **Schedule a Pickup (Buchen Sie eine Abholung)** einen Abholtermin.
   - Geben Sie die Referenznummer an, die auf der lokalen Webbenutzeroberfläche des Geräts auf der Seite **Versandvorbereitung** im Feld für die Frachtbriefnummer angezeigt wird.
   - Buchungen werden an sechs Tagen die Woche (außer freitags und an öffentlichen Feiertagen) von 9:00 Uhr bis 14:00 Uhr angenommen.
   - Abholungsanforderungen müssen mindestens 90 Minuten vor Kundengeschäftsschluss erteilt werden.
10. Wenn beim DHL-Buchungstool Probleme auftreten, können Sie sich wie folgt an DHL wenden:
    - Rufen Sie DHL unter 04-2924545 an.
    - Senden Sie eine E-Mail mit Einzelheiten zu den Problemen an [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com). Geben Sie im Betreff die Frachtbriefnummer an.
    - Wenden Sie sich unter 600 567567 an den DHL-Kundensupport.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Selbst verwalteter Versand](#tab/in-selfmanaged)

Gehen Sie wie folgt vor, wenn Sie Data Box in den Regionen „US Government“, „Japan“, „Singapur“, „Südkorea“, „Indien“, „Südafrika“, „Vereinigtes Königreich“, „Europa, Westen“ oder „Australien“ verwenden und bei der Auftragserstellung die Option für selbst verwalteten Versand ausgewählt haben.

1. Notieren Sie sich den Autorisierungscode, der auf der Seite **Versandvorbereitung** der lokalen Webbenutzeroberfläche von Data Box angezeigt wird, nachdem dieser Schritt erfolgreich abgeschlossen wurde.
2. Schalten Sie das Gerät aus, und entfernen Sie die Kabel. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Wenn Sie zum Rückversand des Geräts bereit sind, senden Sie eine E-Mail an das Azure Data Box Operations-Team. Verwenden Sie dafür die Vorlage unten.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

