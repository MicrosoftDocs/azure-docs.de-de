---
title: Tutorial zur Rücksendung von Azure Data Box | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure Data Box an Microsoft zurücksenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 5b43241be4e161cd6051dce02a3574fbdb580f28
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606234"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Zurücksenden der Azure Data Box und Überprüfen des Datenuploads in Azure

In diesem Tutorial wird beschrieben, wie Sie die Azure Data Box zurücksenden und die in Azure hochgeladenen Daten überprüfen.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie haben die Schritte ausgeführt in [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-deploy-copy-data.md). 
- Alle Kopieraufträge sind abgeschlossen. „Für Versand vorbereiten“ kann nicht ausgeführt werden, wenn noch Kopieraufträge ausgeführt werden.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Zurücksenden der Data Box

1. Stellen Sie sicher, dass das Gerät ausgeschaltet ist und die Kabel entfernt wurden. Wickeln Sie das mit dem Gerät gelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
2. Stellen Sie sicher, dass das Adressetikett auf dem Freihanddisplay angezeigt wird, und vereinbaren Sie einen Abholtermin mit Ihrem Kurierdienst. Falls das Adressetikett beschädigt oder nicht mehr vorhanden ist oder nicht in der E-Ink-Anzeige angezeigt wird, wenden Sie sich an den Microsoft-Support. Sofern dies vom Support empfohlen wird, können Sie im Azure-Portal zu **Übersicht > Versandetikett herunterladen** navigieren. Laden Sie das Versandetikett herunter, und bringen Sie es am Gerät an. 
3. Planen Sie die Abholung durch UPS, falls Sie das Gerät zurücksenden. So planen Sie die Abholung:

    - Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
    - Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie in der E-Ink-Anzeige oder auf dem gedruckten Etikett finden.
    - Wenn Sie keine Nachverfolgungsnummer angeben, fordert UPS eine Zusatzgebühr, die Sie bei der Abholung entrichten müssen.

    Wenn Sie keine Abholung planen können oder möchten, können Sie die Data Box auch an der nächstgelegenen Versandstelle abgeben.
4. Nachdem die Data Box vom Kurierdienst abgeholt und eingescannt wurde, wird der Status der Bestellung im Portal in **Abgeholt** geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Nachdem das Gerät bei Microsoft eingegangen ist und gescannt wurde, wird der Status der Bestellung in **Empfangen** geändert. Das Gerät wird dann physisch auf Schäden oder Anzeichen einer Manipulation überprüft.

Nach der Überprüfung wird die Data Box mit dem Netzwerk im Azure-Datencenter verbunden. Das Kopieren der Daten wird automatisch gestartet. Je nach Datengröße kann der Kopiervorgang einige Stunden oder auch einige Tage dauern. Sie können den Status des Kopiervorgangs im Portal verfolgen.

Nachdem der Kopiervorgang abgeschlossen ist, wird der Auftragsstatus in **Completed** (Abgeschlossen) geändert.

Stellen Sie sicher, dass Ihre Daten in Azure hochgeladen wurden, bevor Sie sie aus der Quelle löschen. Ihre Daten können sich an folgenden Orten befinden:

- In Ihren Azure Storage-Konten. Wenn Sie die Daten in Data Box kopieren, werden die Daten abhängig vom Typ in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen:

  - Blockblobs und Seitenblobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativ hierzu können Sie auch im Azure-Portal auf Ihr Azure-Speicherkonto zugreifen und von dort aus entsprechend navigieren.

- In Ihren Ressourcengruppen für verwaltete Datenträger. Beim Erstellen von verwalteten Datenträgern werden die VHDs als Seitenblobs hochgeladen und dann in verwaltete Datenträger konvertiert. Die verwalteten Datenträger werden an die Ressourcengruppen angefügt, die zum Zeitpunkt der Auftragserstellung angegeben waren. 

    - Wenn der Kopiervorgang auf verwaltete Datenträger in Azure erfolgreich war, können Sie im Azure-Portal zu **Auftragsdetails** navigieren und sich die Ressourcengruppen notieren, die für verwaltete Datenträger angegeben sind.

        ![Identifizieren von Ressourcengruppen für verwaltete Datenträger](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Wechseln Sie zu der Ressourcengruppe, die Sie notiert haben, und suchen Sie Ihre verwalteten Datenträger.

        ![An Ressourcengruppen angefügter verwalteter Datenträger](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Wenn Sie eine VHDX oder eine dynamische/differenzierende VHD kopiert haben, wird die VHDX bzw. VHD als Seitenblob in das Stagingspeicherkonto hochgeladen, aber die VHD kann nicht in einen verwalteten Datenträger konvertiert werden. Wechseln Sie zu Ihrem **Stagingspeicherkonto > Blobs**, und wählen Sie den geeigneten Container aus: SSD Standard, HDD Standard oder SSD Premium. Die VHDs werden als Seitenblobs in Ihr Stagingspeicherkonto hochgeladen.

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von der Data Box

Im folgenden Artikel erfahren Sie, wie Sie Data Box über die lokale Webbenutzeroberfläche verwalten.

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten von Azure Data Box](./data-box-local-web-ui-admin.md)


