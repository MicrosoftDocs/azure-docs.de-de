---
title: Tutorial zur Rücksendung der Microsoft Azure Data Box Disk | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Ihren Azure Data Box-Datenträger an Microsoft zurücksenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 8df9c2a5e45cf222debc733b3e051c3f707715c5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603092"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Zurücksenden von Azure Data Box Disk und Überprüfen des Datenuploads in Azure

Dies ist das letzte Tutorial der Reihe „Bereitstellen von Azure Data Box Disk“. In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Senden des Data Box-Datenträgers an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten vom Data Box-Datenträger

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie die Schritte unter [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Zurücksenden des Data Box-Datenträgers

1. Trennen Sie die Datenträger vom Computer, nachdem die Überprüfung der Daten abgeschlossen ist. Entfernen Sie die Verbindungskabel.
2. Verpacken Sie die Datenträger und die Verbindungskabel in Luftpolsterfolie, und legen Sie sie in das Versandpaket. Fehlendes Zubehör wird ggf. in Rechnung gestellt.
    - Verwenden Sie das Verpackungsmaterial aus der ursprünglichen Lieferung.  
    - Es empfiehlt sich, die Datenträger sorgfältig in Luftpolsterfolie zu verpacken.
    - Achten Sie auf einen festen Sitz der Teile im Karton, um ein Verrutschen innerhalb der Verpackung zu vermeiden.

Die nächsten Schritte hängen davon ab, wo Sie das Gerät zurückgeben.

### <a name="pick-up-in-us-canada"></a>Abholung in den USA/in Kanada

Wenn Sie das Gerät in den USA oder in Kanada zurückgeben möchten, gehen Sie wie folgt vor:

1. Verwenden Sie das Etikett für den Rückversand in der durchsichtigen Kunststoffhülle, die am Paket angebracht ist. Sollte das Etikett beschädigt oder verloren gegangen sein:
    - Navigieren Sie zu **Übersicht > Versandetikett herunterladen**.

        ![Versandetikett herunterladen](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Mit dieser Aktion können Sie wie unten gezeigt ein Etikett für den Rückversand herunterladen.

        ![Beispiel für Versandetikett](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Bringen Sie das Etikett am Gerät an.

2. Verschließen Sie das Versandpaket, und stellen Sie sicher, dass das Etikett für den Rückversand sichtbar ist.
3. Planen Sie eine Abholung durch UPS. So planen Sie die Abholung:

    - Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
    - Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie auf dem gedruckten Etikett finden.
    - Wenn Sie keine Nachverfolgungsnummer angeben, fordert UPS eine Zusatzgebühr, die Sie bei der Abholung entrichten müssen.
    - Wenn Sie keine Abholung planen können oder möchten, können Sie den Data Box-Datenträger auch an der nächstgelegenen Versandstelle abgeben.


### <a name="pick-up-in-europe"></a>Abholung in Europa

Wenn Sie das Gerät in Europa zurückgeben möchten, gehen Sie wie folgt vor:

1. Verwenden Sie das Etikett für den Rückversand in der durchsichtigen Kunststoffhülle, die am Paket angebracht ist. Sollte das Etikett beschädigt oder verloren gegangen sein:
    - Navigieren Sie zu **Übersicht > Versandetikett herunterladen**.

        ![Versandetikett herunterladen](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Mit dieser Aktion können Sie wie unten gezeigt ein Etikett für den Rückversand herunterladen.

        ![Beispiel für Versandetikett](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Bringen Sie das Etikett am Gerät an.

2. Verschließen Sie das Versandpaket, und stellen Sie sicher, dass das Etikett für den Rückversand sichtbar ist.
3. Wenn Sie das Gerät in Europa per DHL zurücksenden, können Sie die Abholung durch DHL anfordern, indem Sie auf der DHL-Website die Luftfrachtbrief-Nummer angeben.
4. Navigieren Sie zur DHL Express-Website für Ihr Land/Ihre Region, und wählen Sie **Abholung buchen > eReturn-Abholung buchen**.

    ![Rücklieferung mit DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Geben Sie die Luftfrachtbrief-Nummer an, und klicken Sie auf **Abholung beauftragen**, um die Abholung anzufordern.

      ![Abholung beauftragen](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Abholung in der Asien-Pazifik-Region

Dieser Abschnitt enthält Anweisungen für die Abholung in Japan, Korea und Australien.

#### <a name="pick-up-in-australia"></a>Abholung in Australien

Bei Azure-Datencentern in Australien ist aus Sicherheitsgründen eine zusätzliche Benachrichtigung erforderlich. Alle eingehenden Lieferungen müssen vorab angekündigt werden. Gehen Sie für eine Abholung in Australien wie folgt vor:

1. Senden Sie eine E-Mail an `adbops@microsoft.com`, um ein Versandetikett mit eindeutiger Eingangs-ID oder TAU-Code anzufordern. Die Anforderung muss mindestens drei Tage vor dem geplanten Lieferdatum gesendet werden, um das Etikett rechtzeitig zu erhalten.
2. Der Betreff der E-Mail muss wie folgt lauten: *Request for reverse shipping label with TAU code*. Geben Sie in der E-Mail folgende Informationen an: 

    - Auftragsname
    - Adresse
    - Kontaktname

#### <a name="pick-up-in-japan"></a>Abholung in Japan

1. Legen Sie den Rücksendeschein der japanischen Post für eine Nachnahmesendung („Chakubarai“) bei.
2. Geben Sie auf dem Rücksendeschein den Namen und die Adresse Ihres Unternehmens als Absender an.
3. Fordern Sie unter der gebührenfreien Nummer 0800-0800-111 eine Abholung durch die japanische Post an. Geben Sie die siebenstellige Postleitzahl für die Abholadresse ein, um an die nächstgelegene Poststelle weitergeleitet zu werden.
    - Die verfügbaren Abholzeiten hängen von der jeweiligen Poststelle ab.
    - Geben Sie an, dass es sich um eine Sendung vom Typ "Japan Post Chakubarai Yu-Pack" handelt.
    - Verwenden Sie den Nachnahme-Rücksendeschein („Chakubarai“), der in der Lieferung enthalten war.
4. Sollte in der Lieferung kein solcher Rücksendeschein enthalten gewesen sein, senden Sie eine E-Mail an unseren Partner *Quantium Solutions*: `Customerservice.JP@quantiumsolutions.com`. *Quantium Solutions* fordert eine Abholung bei der japanischen Post an und sorgt dafür, dass Sie den Rücksendeschein bei der Abholung erhalten.
    - Geben Sie die Referenznummer in der Spalte für Bemerkungen auf dem Nachnahme-Rücksendeschein („Chakubarai“) an, den Sie von der japanischen Post erhalten.
    - Geben Sie die Lieferanschrift wie unten gezeigt an:   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Sollte der Nachnahme-Rücksendeschein („Chakubarai“) nicht vorhanden sein, können Sie die Abholung per E-Mail veranlassen. Verwenden Sie dazu die folgende E-Mail-Vorlage:

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Abholung in Korea

1. Legen Sie den Rücksendeschein bei.
2. Gehen Sie zum Anfordern der Abholung wie folgt vor:
    1. Rufen Sie unter 070-8231-1418 die Hotline von *Quantium Solutions International* an (Geschäftszeiten: Montag bis Freitag, 10 bis 17 Uhr). Geben Sie *Microsoft pickup* (Microsoft-Abholung) und die Nummer des Rücksendescheins an, um eine Abholung zu veranlassen.  
    2. Ist die Hotline überlastet, senden Sie eine E-Mail mit dem Betreff *Microsoft Pickup* und der Nummer des Rücksendescheins an `microsoft@rocketparcel.com`.
    3. Sollte der Kurier den Abholtermin nicht einhalten, rufen Sie die Hotline von *Quantium Solutions International* an, um eine alternative Lösung zu finden. 

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Nachdem die Datenträger vom Kurierdienst abgeholt wurden, wird der Auftragsstatus im Portal in **Picked up** (Abgeholt) geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

![Datenträger abgeholt](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Nachdem der Datenträger bei Microsoft eingegangen ist und gescannt wurde, wird der Auftragsstatus in **Received** (Empfangen) geändert. 

![Datenträger empfangen](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Die Daten werden automatisch kopiert, nachdem die Datenträger im Azure-Rechenzentrum mit einem Server verbunden wurden. Je nach Datengröße kann der Kopiervorgang einige Stunden oder auch einige Tage dauern. Sie können den Status des Kopiervorgangs im Portal verfolgen.

Nachdem der Kopiervorgang abgeschlossen ist, wird der Auftragsstatus in **Completed** (Abgeschlossen) geändert.

![Kopieren der Daten abgeschlossen](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Stellen Sie sicher, dass sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen. Ihre Daten können sich an folgenden Orten befinden:

- In Ihren Azure Storage-Konten. Wenn Sie die Daten in Data Box kopieren, werden die Daten abhängig vom Typ in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen:

  - Blockblobs und Seitenblobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativ hierzu können Sie auch im Azure-Portal auf Ihr Azure-Speicherkonto zugreifen und von dort aus entsprechend navigieren.

- In Ihren Ressourcengruppen für verwaltete Datenträger. Beim Erstellen von verwalteten Datenträgern werden die VHDs als Seitenblobs hochgeladen und dann in verwaltete Datenträger konvertiert. Die verwalteten Datenträger werden an die Ressourcengruppen angefügt, die zum Zeitpunkt der Auftragserstellung angegeben waren.

  - Wenn der Kopiervorgang auf verwaltete Datenträger in Azure erfolgreich war, können Sie im Azure-Portal zu **Auftragsdetails** navigieren und sich die Ressourcengruppen notieren, die für verwaltete Datenträger angegeben sind.

      ![Anzeigen der Auftragsdetails](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Wechseln Sie zu der Ressourcengruppe, die Sie notiert haben, und suchen Sie Ihre verwalteten Datenträger.

      ![Ressourcengruppe für verwaltete Datenträger](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Wenn Sie eine VHDX oder eine dynamische oder differenzierende VHD kopiert haben, wird die VHDX bzw. VHD als Blockblob in das Stagingspeicherkonto hochgeladen. Wechseln Sie zu Ihrem **Stagingspeicherkonto > Blobs**, und wählen Sie den geeigneten Container aus: StandardSSD, StandardHDD oder PremiumSSD. Die VHDX/VHDs sollten als Blockblobs in Ihrem Stagingspeicherkonto angezeigt werden.

Führen Sie die folgenden Schritte aus, um sich zu vergewissern, dass die Daten in Azure hochgeladen wurden:

1. Navigieren Sie zu dem Speicherkonto, das Ihrem Datenträgerauftrag zugeordnet ist.
2. Navigieren Sie zu **Blob-Dienst > Blobs durchsuchen**. Die Liste mit den Containern wird angezeigt. Gemäß dem Unterordner, den Sie unter den Ordnern *BlockBlob* und *PageBlob* erstellt haben, werden unter Ihrem Speicherkonto Container mit demselben Namen erstellt.
    Falls die Ordnernamen nicht den Azure-Namenskonventionen entsprechen, tritt beim Hochladen der Daten nach Azure ein Fehler auf.

4. Verwenden Sie Microsoft Azure Storage-Explorer, um zu überprüfen, ob das gesamte Dataset geladen wurde. Fügen Sie das Speicherkonto an, das dem Mietauftrag für den Datenträger entspricht, und sehen Sie sich die Liste mit den Blobcontainern an. Wählen Sie einen Container aus, und klicken Sie auf **…Mehr** und dann auf **Folder statistics** (Ordnerstatistik). Im Bereich **Aktivitäten** wird die Statistik für diesen Ordner angezeigt, z.B. die Anzahl von Blobs und die Gesamtgröße der Blobs. Die Gesamtgröße der Blobs in Byte sollte mit der Größe des Datasets übereinstimmen.

    ![Ordnerstatistik in Storage-Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Löschen von Daten vom Data Box-Datenträger

Nachdem der Kopiervorgang abgeschlossen ist und Sie überprüft haben, dass sich die Daten unter dem Azure-Speicherkonto befinden, werden die Datenträger gemäß dem NIST-Standard auf sichere Weise gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Datenträgern erhalten, z.B.:

> [!div class="checklist"]
> * Senden des Data Box-Datenträgers an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten vom Data Box-Datenträger


Fahren Sie mit dem nächsten Artikel zur Vorgehensweise fort, um zu erfahren, wie Sie den Data Box-Datenträger über das Azure-Portal verwalten.

> [!div class="nextstepaction"]
> [Use Azure portal to administer Azure Data Box Disk](./data-box-portal-ui-admin.md) (Verwenden des Azure-Portals zum Verwalten des Azure Data Box-Datenträgers)


