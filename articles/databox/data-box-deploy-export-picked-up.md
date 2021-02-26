---
title: Tutorial zur Rücksendung von Azure Data Box im Exportauftrag | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure Data Box nach Abschluss des Export Auftrags an Microsoft senden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537356"
---
# <a name="tutorial-return-azure-data-box"></a>Tutorial: Zurücksenden von Azure Data Box

In diesem Tutorial wird beschrieben, wie Azure Data Box zurückgesendet wird, und die Daten werden gelöscht, nachdem das Gerät in den Azure-Daten empfangen wurde.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Löschen von Daten von der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben die Schritte ausgeführt in [Tutorial: Kopieren von Daten aus Azure Data Box über SMB (Vorschau)](data-box-deploy-export-copy-data.md).
* Alle Kopieraufträge sind abgeschlossen. „Für Versand vorbereiten“ kann nicht ausgeführt werden, wenn noch Kopieraufträge ausgeführt werden.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Die nächsten Schritte hängen davon ab, wo Sie das Gerät zurückgeben.

## <a name="ship-data-box-back"></a>Zurücksenden der Data Box

Vergewissern Sie sich, dass die Daten vollständig vom Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war. Die Vorgehensweise ist abhängig von der Region, in der das Gerät versendet wird.

## <a name="us-canada-europe"></a>[USA, Kanada, Europa](#tab/in-us-canada-europe)

Wenn Sie das Gerät in den USA, in Kanada oder in Europa zurücksenden möchten, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass das Gerät ausgeschaltet ist und die Kabel entfernt wurden. 
2. Wickeln Sie das mit dem Gerät gelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Stellen Sie sicher, dass das Adressetikett in der E-Ink-Anzeige angezeigt wird, und vereinbaren Sie mit Ihrem Kurierdienst einen Abholtermin. Falls das Adressetikett beschädigt oder nicht mehr vorhanden ist oder aber in der E-Ink-Anzeige nicht angezeigt wird, wenden Sie sich an den Microsoft-Support. Sofern dies vom Support empfohlen wird, können Sie im Azure-Portal zu **Übersicht > Versandetikett herunterladen** navigieren. Laden Sie das Versandetikett herunter, und bringen Sie es am Gerät an. 
4. Planen Sie die Abholung durch UPS, falls Sie das Gerät zurücksenden. So planen Sie die Abholung:

   - Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
   - Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie in der E-Ink-Anzeige oder auf dem gedruckten Etikett finden. Wenn Sie die Nachverfolgungsnummer nicht angeben, wird bei der Abholung eine zusätzliche Gebühr erhoben.
   - Falls während der Planung einer Abholung Probleme auftreten oder wenn Sie aufgefordert werden, zusätzliche Gebühren zu bezahlen, wenden Sie sich an Azure Data Box Operations. Senden Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Wenn Sie keine Abholung planen können oder möchten, können Sie die Data Box auch an der nächstgelegenen Versandstelle abgeben.
4. Nachdem die Data Box vom Kurierdienst abgeholt und eingescannt wurde, wird der Status der Bestellung im Portal in **Abgeholt** geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

## <a name="australia"></a>[Australien](#tab/in-australia)

Bei Azure-Datencentern in Australien ist aus Sicherheitsgründen eine zusätzliche Benachrichtigung erforderlich. Alle eingehenden Lieferungen müssen vorab angekündigt werden. Gehen Sie für den Versand in Australien wie folgt vor:

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Vergewissern Sie sich, dass die Daten vollständig auf das Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war.
3. Schalten Sie das Gerät aus, und entfernen Sie die Kabel.
4. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
5. Reservieren Sie online unter dem [DHL-Link](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) einen Abholtermin.

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

Den Support von Quantium Solution erreichen Sie bei Bedarf wie folgt (in japanische Sprache): 

* E-Mail-Adresse: Customerservice.JP@quantiumsolutions.com
* Telefonnummer: 03-5755-0150

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Bewahren Sie den Originalversandkarton des Geräts für den Rückversand auf.
2. Notieren Sie sich die Nachverfolgungsnummer (als Referenznummer auf der Seite „Für den Versand vorbereiten“ der lokalen Webbenutzeroberfläche von Data Box angezeigt). Diese ist verfügbar, wenn der Schritt zur Versandvorbereitung erfolgreich abgeschlossen wurde. Laden Sie das Adressetikett von dieser Seite herunter, und bringen Sie es auf dem Versandkarton an.
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
   >
   > * Bei Anforderung vor 15 Uhr erfolgt die Abholung am nächsten Werktag zwischen 9 und 13 Uhr.
   > * Bei Anforderung nach 15 Uhr erfolgt die Abholung am nächsten Werktag zwischen 14 und 18 Uhr.  

## <a name="south-africa"></a>[Südafrika](#tab/in-sa)

1. Bewahren Sie den Originalkarton zum Verpacken des Geräts für den Rückversand auf.
2. Notieren Sie sich die Referenznummer (Frachtbriefnummer), die auf der lokalen Webbenutzeroberfläche des Geräts angezeigt wird. Diese Nummer wird nach erfolgreichem Abschluss der **Versandvorbereitung** angezeigt.
3. Laden Sie das Adressetikett über die lokale Webbenutzeroberfläche des Geräts herunter, drucken Sie es aus, und bringen Sie es am Versandpaket an.
4. Für die Reservierung der Abholung mit DHL stehen folgende Optionen zur Verfügung:

    * Wenden Sie sich vor 14 Uhr unter der Telefonnummer **+27(0) 11 9213600** an den Kundenservice, wählen Sie Option 1, und geben Sie dann die Frachtbriefnummer an.
    * Senden Sie eine E-Mail an [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com), und verwenden Sie dabei die folgende Vorlage:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Stattdessen können Sie das Paket in einem DHL-Paketshop in Ihrer Nähe abgeben.

5. Sollten Probleme auftreten, senden Sie eine E-Mail mit Einzelheiten zu dem/den aufgetretenen Problem(en) an [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com). Geben Sie im Betreff die Frachtbriefnummer an. Sie können auch die Nummer +27(0)119213902 anrufen.

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Verpacken Sie das Gerät für den Rückversand im Originalkarton.
2. Notieren Sie sich die Referenznummer (Sendungsverfolgungsnummer für die Rücksendung), die auf der lokalen Webbenutzeroberfläche des Geräts angezeigt wird. Diese Nummer wird nach erfolgreichem Abschluss der **Versandvorbereitung** angezeigt.
3. Laden Sie das Adressetikett über die lokale Webbenutzeroberfläche des Geräts herunter, drucken Sie es aus, und bringen Sie es am Versandpaket an.
4. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
5. Rufen Sie unter **(852) 2318 1213** die Hotline von **Quantium Solutions** an (Geschäftszeiten: Montag bis Freitag, 9 bis 18 Uhr).  
6. Geben Sie „Microsoft Azure Pickup“ (Microsoft Azure-Abholung) sowie die Referenz- und die Sendungsverfolgungsnummer (oberhalb des Strichcodes) auf dem Rücksendeetikett an, um eine Abholung zu veranlassen.
7. Sie erhalten eine mündliche Bestätigung des Abholplans. Sollte der Kurier den Abholtermin nicht einhalten, rufen Sie die Hotline von Quantium Solutions an, um eine alternative Lösung zu finden.
8. Wenn Sie eine Abholung mit Quantium vereinbart haben, leiten Sie die Bestätigung unter Verwendung der folgenden Vorlage an [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) weiter:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Sollten irgendwelche Probleme auftreten, senden Sie eine E-Mail an Data Box Operations Asia ([adbo@microsoft.com](mailto:adbo@microsoft.com)). Geben Sie dabei im Betreff den Auftragsnamen an, und beschreiben Sie das Problem.

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

## <a name="self-managed"></a>[Selbst verwalteter Versand](#tab/in-selfmanaged)

Wenn Sie Data Box in Japan, Singapur, Südkorea, Indien, Südafrika, im Vereinigten Königreich, in Westeuropa oder Australien verwenden und bei der Auftragserstellung die Option für den selbst verwalteten Versand ausgewählt haben, gehen Sie wie folgt vor:

1. Notieren Sie sich den Autorisierungscode, der auf der Seite „Für den Versand vorbereiten“ der lokalen Webbenutzeroberfläche von Data Box angezeigt wird, nachdem dieser Schritt erfolgreich abgeschlossen wurde.
2. Schalten Sie das Gerät aus, und entfernen Sie die Kabel. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Wenn Sie zum Rückversand des Geräts bereit sind, senden Sie eine E-Mail an das Azure Data Box Operations-Team. Verwenden Sie dafür die Vorlage unten.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Sobald das Gerät das Azure-Rechenzentrum erreicht, löscht die Data Box die Daten auf ihren Datenträgern gemäß den [NIST-Richtlinien SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu verschiedenen Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Löschen von Daten von der Data Box

Im nächsten Artikel erfahren Sie, wie Sie Ihre Data Box verwalten.

> [!div class="nextstepaction"]
> [Verwalten von Data Box über das Azure-Portal](./data-box-portal-admin.md)
