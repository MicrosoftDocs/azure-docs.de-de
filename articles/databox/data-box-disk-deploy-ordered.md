---
title: Tutorial zum Bestellen von Azure Data Box Disk | Microsoft-Dokumentation
description: Verwenden Sie dieses Tutorial, um zu erfahren, wie Sie sich für einen Azure Data Box-Datenträger registrieren und diesen bestellen können, um Daten in Azure zu importieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 1d25ea2ce6e365e0d04fab325f9c13bb37382758
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603165"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Tutorial: Bestellen von Azure Data Box Disk

Azure Data Box Disk ist eine Hybrid Cloud-Lösung, die es Ihnen ermöglicht, Ihre lokalen Daten auf schnelle, einfache und zuverlässige Weise in Azure zu importieren. Sie übertragen Ihre Daten auf SSDs, die von Microsoft bereitgestellt werden, und senden die Datenträger dann zurück. Diese Daten werden dann in Azure hochgeladen.

In diesem Tutorial wird beschrieben, wie Sie einen Azure Data Box-Datenträger bestellen können. In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> * Bestellen eines Data Box-Datenträgers
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

## <a name="prerequisites"></a>Voraussetzungen

Vor der Bereitstellung müssen die folgenden Konfigurationsvoraussetzungen für den Data Box-Dienst und Data Box Disk erfüllt sein.

### <a name="for-service"></a>Für den Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
- Sie verwenden ein Abonnement der folgenden Typen für den Data Box-Dienst:
    - Microsoft Enterprise Agreement (EA). Erfahren Sie mehr über [EA-Abonnements](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Erfahren Sie mehr über das [Azure CSP-Programm](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Sie haben Zugriff als Besitzer oder Mitwirkender auf das Abonnement (zum Erstellen einer Data Box-Bestellung erforderlich).

### <a name="for-device"></a>Für das Gerät

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Sie haben einen Clientcomputer, von dem Sie die Daten kopieren können. Ihr Client-Computer muss folgende Voraussetzungen erfüllen:
    - Es muss ein [unterstütztes Betriebssystem](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) ausgeführt werden.
    - Wenn es sich um einen Windows-Client handelt, muss weitere [erforderliche Software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) darauf installiert sein.  

## <a name="order-data-box-disk"></a>Bestellen des Data Box-Datenträgers

Führen Sie die folgenden Schritte im [Azure-Portal](https://aka.ms/azuredataboxfromdiskdocs) aus, um den Data Box-Datenträger zu bestellen.

1. Klicken Sie in der oberen linken Ecke des Portals auf **Ressource erstellen**, und suchen Sie dann nach *Azure Data Box*. Klicken Sie auf **Azure Data Box**.
    
   ![Suchen nach Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klicken Sie auf **Create**.

3. Überprüfen Sie, ob der Data Box-Dienst in Ihrer Region verfügbar ist. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus. Klicken Sie dann auf **Übernehmen**.

    ![Auswählen der Option „Data Box-Datenträger“](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Einstellung|Wert|
    |---|---|
    |Abonnement|Wählen Sie ein Abonnement aus, für das der Data Box-Dienst aktiviert ist.<br> Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Übertragungstyp| In Azure importieren|
    |Quellland/-region | Wählen Sie das Land/die Region aus, in dem/der sich Ihre Daten zurzeit befinden.|
    |Azure-Zielregion|Wählen Sie die Azure-Region aus, in die Daten übertragen werden sollen.|

  
5.  Wählen Sie **Data Box-Datenträger** aus. Die maximale Kapazität der Lösung für eine einzelne Bestellung von 5 Datenträgern beträgt 35 TB. Sie können ggf. mehrere Bestellungen für größere Datenmengen erstellen.

     ![Auswählen der Option „Data Box-Datenträger“](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  Geben Sie in **Bestellung** die **Bestelldetails** an. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus.

    |Einstellung|Wert|
    |---|---|
    |NAME|Geben Sie einen Anzeigenamen an, um die Bestellung nachzuverfolgen.<br> Der Name kann zwischen 3 und 24 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. <br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden. |
    |Ressourcengruppe| Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. <br> Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können. |
    |Azure-Zielregion| Wählen Sie eine Region für Ihr Speicherkonto aus.<br> Zurzeit werden Speicherkonten in allen Regionen in den USA sowie in West- und Nordeuropa, Kanada und Australien unterstützt. |
    |Geschätzte Datengröße in TB| Geben Sie einen geschätzten Wert in TB ein. <br>Basierend auf der Größe der Daten sendet Microsoft Ihnen eine geeignete Anzahl von SSDs mit jeweils 8 TB (7 TB nutzbare Kapazität) zu. <br>Die maximal nutzbare Kapazität von 5 Datenträgern beträgt bis zu 35 TB. |
    |Hauptschlüssel für Datenträger| Geben Sie den Hauptschlüssel für Datenträger an, wenn Sie **Benutzerdefinierten Schlüssel anstelle eines von Azure generierten Hauptschlüssels verwenden** aktivieren. <br> Geben Sie einen alphanumerischen Schlüssel mit 12 bis 32 Zeichen ein, der mindestens ein numerisches Zeichen und ein Sonderzeichen enthält. Zulässige Sonderzeichen: `@?_+`. <br> Sie können diese Option überspringen und den von Azure generierten Hauptschlüssel verwenden, um Ihre Datenträger zu entsperren.|
    |Speicherziel     | Wählen Sie Speicherkonten, verwaltete Datenträger oder beides aus. <br> Wählen Sie basierend auf der angegebenen Azure-Region ein Speicherkonto aus der gefilterten Liste aus. Data Box kann mit bis zu zehn Speicherkonten verknüpft werden. <br> Sie können auch ein neues Speicherkonto vom Typ **Universell V1** oder **Universell V2** oder ein **Blob Storage-Konto** erstellen. <br>Sie können keine Speicherkonten verwenden, für die Regeln konfiguriert sind. Die Speicherkonten müssen den **Zugriff von allen Netzwerken** im Abschnitt mit Firewalls und virtuellen Netzwerken zulassen.|

    Wenn Sie als Speicherziel ein Speicherkonto verwenden, wird Folgendes angezeigt:

    ![Data Box Disk-Auftrag für ein Speicherkonto](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Wenn Sie Data Box Disk verwenden, um auf der Grundlage lokaler VHDs verwaltete Datenträger zu erstellen, sind außerdem folgende Informationen erforderlich:

    |Einstellung  |Wert  |
    |---------|---------|
    |Ressourcengruppe     | Erstellen Sie eine neue Ressourcengruppe, wenn Sie verwaltete Datenträger auf der Grundlage lokaler VHDs erstellen möchten. Verwenden Sie nur dann eine bereits vorhandene Ressourcengruppe, wenn diese vom Data Box-Dienst für den Data Box Disk-Auftrag für einen verwalteten Datenträger erstellt wurde. <br> Es wird nur eine einzelne Ressourcengruppe unterstützt.|

    ![Data Box Disk-Auftrag für verwalteten Datenträger](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Das für verwaltete Datenträger angegebene Speicherkonto wird als Stagingspeicherkonto verwendet. Der Data Box-Dienst lädt die VHDs in das Stagingspeicherkonto hoch, konvertiert sie in verwaltete Datenträger und verschiebt sie in die Ressourcengruppen. Weitere Informationen finden Sie unter [Überprüfen des Datenuploads in Azure](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Klicken Sie auf **Weiter**.

    ![Angeben der Bestelldetails](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Geben Sie auf der Registerkarte **Lieferadresse** Ihren Vor- und Nachnamen, den Namen und die Postanschrift des Unternehmens sowie eine gültige Telefonnummer an. Klicken Sie auf **Adresse überprüfen**. Der Dienst überprüft anhand der Lieferadresse die Verfügbarkeit des Diensts. Wenn der Dienst für die angegebene Lieferadresse verfügbar ist, erhalten Sie eine entsprechende Benachrichtigung. 

    ![Angeben der Lieferadresse](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Geben Sie in den **Benachrichtigungsdetails** E-Mail-Adressen an. Der Dienst sendet E-Mail-Benachrichtigungen in Bezug auf Aktualisierungen des Auftragsstatus an die angegebenen E-Mail-Adressen. 

    Es wird empfohlen, eine E-Mail-Gruppenadresse zu verwenden, damit Sie weiterhin Benachrichtigungen erhalten, wenn ein Administrator die Gruppe verlässt.

16. Überprüfen Sie die Informationen der **Zusammenfassung** im Zusammenhang mit der Bestellung, den Kontakt, die Benachrichtigungen und die Datenschutzbestimmungen. Aktivieren Sie das Kontrollkästchen für die Zustimmung zu den Datenschutzbestimmungen.

17. Klicken Sie auf **Bestellen**. Die Erstellung des Auftrags dauert einige Minuten.

 
## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Nachdem Sie die Bestellung aufgegeben haben, können Sie ihren Status im Azure-Portal nachverfolgen. Navigieren Sie zu Ihrer Bestellung, und navigieren Sie dann zu **Übersicht**, um den Status anzuzeigen. Das Portal zeigt den Auftrag im Zustand **Bestellt** an.

![Status „Bestellt“ des Data Box-Datenträgers](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Wenn die Datenträger nicht verfügbar sind, erhalten Sie eine Benachrichtigung. Wenn die Datenträger verfügbar sind, identifiziert Microsoft die Datenträger für den Versand und bereitet das Datenträgerpaket vor. Während der Datenträgervorbereitung werden folgende Aktionen ausgeführt:

- Die Datenträger werden mithilfe von AES-128-BitLocker-Verschlüsselung verschlüsselt.  
- Die Datenträger werden gesperrt, um einen nicht autorisierten Zugriff auf die Datenträger zu verhindern.
- Der Hauptschlüssel, der die Datenträger entsperrt, wird während dieses Vorgangs generiert.

Wenn die Datenträgervorbereitung abgeschlossen ist, zeigt das Portal die Bestellung im Zustand **Verarbeitet** an.

Microsoft bereitet dann Ihre Datenträger vor und versendet sie über einen regionalen Spediteur. Wenn die Datenträger versandt wurden, erhalten Sie eine Nachverfolgungsnummer. Das Portal zeigt die Bestellung im Zustand **Versandt** an.

## <a name="cancel-the-order"></a>Stornieren der Bestellung

Um die Bestellung zu stornieren, navigieren Sie im Azure-Portal zu **Übersicht**, und klicken Sie dann auf der Befehlsleiste auf **Stornieren**.

Sie können die Bestellung nur stornieren, wenn die Datenträger bestellt sind und die Bestellung für den Versand verarbeitet wird. Sobald die Bestellung verarbeitet wurde, können Sie sie nicht mehr stornieren.

![Auftrag stornieren](media/data-box-disk-deploy-ordered/cancel-order1.png)

Um eine stornierte Bestellung zu löschen, navigieren Sie zu **Übersicht** und klicken dann auf der Befehlsleiste auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Bestellen des Data Box-Datenträgers
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihren Data Box-Datenträger einrichten.

> [!div class="nextstepaction"]
> [Einrichten des Azure Data Box-Datenträgers](./data-box-disk-deploy-set-up.md)
