---
title: 'Tutorial: Bestellen von Azure Data Box Heavy | Microsoft-Dokumentation'
description: Hier erfahren Sie, welche Voraussetzungen für die Bereitstellung gelten und wie Sie ein Azure Data Box Heavy-Gerät bestellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 8453a3592c1822489a3724dacdf8f0ff5e8492f1
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427909"
---
# <a name="tutorial-order-azure-data-box-heavy-preview"></a>Tutorial: Bestellen von Azure Data Box Heavy (Vorschauversion)


Azure Data Box Heavy ist eine Hybridlösung, mit der Sie Ihre lokalen Daten auf schnelle, einfache und zuverlässige Weise in Azure importieren können. Sie übertragen Ihre Daten auf ein von Microsoft bereitgestelltes Speichergerät mit einer nutzbaren Kapazität von etwa 770 TB und senden es anschließend an Microsoft zurück. Diese Daten werden dann in Azure hochgeladen.

In diesem Tutorial erfahren Sie, wie Sie ein Azure Data Box Heavy-Gerät bestellen. In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> * Voraussetzungen für Data Box Heavy
> * Bestellen eines Data Box Heavy-Geräts
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Gerät bereitstellen, müssen die folgenden Konfigurationsvoraussetzungen für den Data Box-Dienst und das Data Box-Gerät erfüllt sein.

### <a name="for-installation-site"></a>Aufstellungsort

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Gerät passt durch normale Türen und Zugänge. Vergewissern Sie sich jedoch, dass das Gerät durch alle Ihre Zugänge passt. (Abmessungen des Geräts: Breite: 66,04 cm, Länge: 121,92 cm, Höhe: 71,12 cm)
- Falls sich der Aufstellungsort des Geräts nicht im Erdgeschoss befindet, muss das gewünschte Stockwerk über einen Aufzug oder über eine Rampe erreichbar sein. Das Gerät wiegt rund 227 kg.
- In Ihrem Datencenter muss ein ebener Aufstellungsort mit einer Netzwerkverbindung verfügbar sein, der genügend Platz für ein Gerät dieser Größe bietet.


### <a name="for-service"></a>Für den Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
- Sie verwenden ein Abonnement der folgenden Typen für den Data Box-Dienst:
    - Microsoft Enterprise Agreement (EA). Erfahren Sie mehr über [EA-Abonnements](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Erfahren Sie mehr über das [Azure CSP-Programm](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Erfahren Sie mehr über das [Azure Sponsorship-Programm](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Sie haben Zugriff auf das Abonnement (als Besitzer oder Mitwirkender), um eine Data Box Heavy-Bestellung erstellen zu können.

### <a name="for-device"></a>Für das Gerät

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Das Gerät ist ausgepackt.
- Sie verfügen über einen Hostcomputer, der mit dem Netzwerk des Datencenters verbunden ist. Data Box Heavy kopiert die Daten von diesem Computer. Auf dem Hostcomputer muss ein unterstütztes Betriebssystem ausgeführt werden (siehe [Systemanforderungen für Azure Data Box](data-box-system-requirements.md)).
- Sie benötigen einen Laptop und ein RJ45-Kabel, um eine Verbindung mit der lokalen Benutzeroberfläche herstellen und das Gerät konfigurieren zu können. Mithilfe des Laptops muss jeder Knoten des Geräts einmalig konfiguriert werden.
- Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen.
- Für jeden Geräteknoten wird ein 40-GBit/s- oder 10-GBit/s-Kabel benötigt. Die Kabel müssen mit der Netzwerkschnittstelle [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibel sein:

    - Bei 40-GBit/s-Kabeln muss ein Kabelende vom Typ QSFP+ verwendet werden.
    - Bei 10-GBit/s-Kabeln benötigen Sie ein SFP+-Kabel, das auf der einen Seite mit einem 10-G-Switch verbunden ist und an dem Ende, das mit dem Gerät verbunden wird, über einen QSFP+-auf-SFP+-Adapter (oder über den QSA-Adapter) verfügt.
    - Die Netzkabel sind im Lieferumfang des Geräts enthalten.

## <a name="order-data-box-heavy"></a>Bestellen von Data Box Heavy

Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Gerät zu bestellen.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen unter folgender URL an: [https://portal.azure.com](https://portal.azure.com).
2. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach *Azure Data Box*. Wählen Sie **Azure Data Box** aus.
    
   [![Suchen nach Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klicken Sie auf **Erstellen**.

4. Überprüfen Sie, ob der Data Box-Dienst in Ihrer Region verfügbar ist. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend **Übernehmen** aus.

    |Einstellung  |Wert  |
    |---------|---------|
    |Abonnement     | Wählen Sie ein EA-, CSP- oder Azure Sponsorship-Abonnement für den Data Box-Dienst aus. <br> Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft.       |
    |Übertragungstyp     | Wählen Sie **Import in Azure** aus.        |
    |Quellland/-region     | Wählen Sie das Land/die Region aus, in dem/der sich Ihre Daten zurzeit befinden.         |
    |Azure-Zielregion     | Wählen Sie die Azure-Region aus, in die Daten übertragen werden sollen.        |

    [![Auswählen der Verfügbarkeit der Data Box-Familie](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Wählen Sie **Data Box Heavy** aus. Die maximal nutzbare Kapazität für eine einzelne Bestellung beträgt 770 TB.

    [![Auswählen von „Data Box Heavy“](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Geben Sie in **Bestellung** die **Bestelldetails** an. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend **Weiter** aus.
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Name     | Geben Sie einen Anzeigenamen an, um die Bestellung nachzuverfolgen. <br> Der Name kann zwischen 3 und 24 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. <br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.      |
    |Ressourcengruppe     | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. <br> Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können.         |
    |Azure-Zielregion     | Wählen Sie eine Region für Ihr Speicherkonto aus. <br> Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Speicherziel     | Wählen Sie Speicherkonten, verwaltete Datenträger oder beides aus. <br> Wählen Sie basierend auf der angegebenen Azure-Region mindestens ein Speicherkonto in der gefilterten Liste eines vorhandenen Speicherkontos aus. <br>Data Box Heavy kann mit bis zu zehn Speicherkonten verknüpft werden. <br> Sie können auch ein neues Speicherkonto vom Typ **Universell V1** oder **Universell V2** oder ein **Blob Storage-Konto** erstellen. <br> Azure Data Lake Storage Gen 2-Konten werden nicht unterstützt. Weitere Informationen finden Sie unter [Unterstützte Speicherkonten](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Speicherkonten mit virtuellen Netzwerken werden unterstützt. Aktivieren Sie in den Netzwerkfirewalleinstellungen des Speicherkontos die vertrauenswürdigen Dienste, um dem Data Box-Dienst die Verwendung geschützter Speicherkonten zu ermöglichen. Weitere Informationen zum Hinzufügen von Azure Data Box als vertrauenswürdiger Dienst finden Sie [hier](../storage/common/storage-network-security.md#exceptions).|

    Wenn Sie als Speicherziel ein Speicherkonto verwenden, wird Folgendes angezeigt:

    ![Data Box Heavy-Bestellung für ein Speicherkonto](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Falls Sie nicht nur ein Speicherkonto als Speicherziel verwenden, sondern mit Data Box Heavy auch verwaltete Datenträger auf der Grundlage der lokalen virtuellen Festplatten erstellen möchten, geben Sie folgende Informationen an:

    |Einstellung  |Wert  |
    |---------|---------|
    |Ressourcengruppen     | Erstellen Sie neue Ressourcengruppen, wenn Sie verwaltete Datenträger auf der Grundlage lokaler VHDs erstellen möchten. Eine bereits vorhandene Ressourcengruppe kann nur verwendet werden, wenn diese zuvor beim Erstellen einer Data Box Heavy-Bestellung für einen verwalteten Datenträger durch den Data Box-Dienst erstellt wurde. <br> Bei Angabe mehrerer Ressourcengruppen müssen diese jeweils durch ein Semikolon getrennt werden. Es werden maximal zehn Ressourcengruppen unterstützt.|

    ![Data Box Heavy-Bestellung für einen verwalteten Datenträger](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Das für verwaltete Datenträger angegebene Speicherkonto wird als Stagingspeicherkonto verwendet. Der Data Box-Dienst lädt die VHDs als Seitenblobs in das Stagingspeicherkonto hoch, konvertiert es in verwaltete Datenträger und verschiebt es in die Ressourcengruppen. Weitere Informationen finden Sie unter [Überprüfen des Datenuploads in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. Geben Sie unter **Lieferadresse** Ihren Vor- und Nachnamen, den Namen und die Postanschrift des Unternehmens sowie eine gültige Telefonnummer an. Wählen Sie **Adresse überprüfen** aus. 

    Der Dienst überprüft anhand der Lieferadresse die Verfügbarkeit des Diensts. Wenn der Dienst für die angegebene Lieferadresse verfügbar ist, erhalten Sie eine entsprechende Benachrichtigung. Klicken Sie auf **Weiter**.

8. Geben Sie in den **Benachrichtigungsdetails** E-Mail-Adressen an. Der Dienst sendet E-Mail-Benachrichtigungen in Bezug auf Aktualisierungen des Auftragsstatus an die angegebenen E-Mail-Adressen.

    Es wird empfohlen, eine E-Mail-Gruppenadresse zu verwenden, damit Sie weiterhin Benachrichtigungen erhalten, wenn ein Administrator die Gruppe verlässt.

9. Überprüfen Sie die Informationen der **Zusammenfassung** im Zusammenhang mit der Bestellung, den Kontakt, die Benachrichtigungen und die Datenschutzbestimmungen. Aktivieren Sie das Kontrollkästchen für die Zustimmung zu den Datenschutzbestimmungen.

10. Wählen Sie **Bestellen** aus. Die Erstellung des Auftrags dauert einige Minuten.


## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Nachdem Sie die Bestellung aufgegeben haben, können Sie ihren Status im Azure-Portal nachverfolgen. Navigieren Sie zu Ihrer Data Box Heavy-Bestellung und anschließend zu **Übersicht**, um den Status anzuzeigen. Die Bestellung wird im Portal mit dem Status **Bestellt** angezeigt.

Sollte das Gerät nicht verfügbar sein, erhalten Sie eine entsprechende Benachrichtigung. Wenn das Gerät verfügbar ist, identifiziert Microsoft das Gerät für den Versand und bereitet den Versand vor. Während der Vorbereitung des Geräts werden folgende Aktionen ausgeführt:

- Für jedes mit dem Gerät verknüpfte Speicherkonto werden SMB-Freigaben erstellt.
- Für jede Freigabe werden Anmeldeinformationen wie Benutzername und Kennwort generiert.
- Ein Gerätekennwort zum Entsperren des Geräts wird ebenfalls generiert.
- Data Box Heavy wird gesperrt, um nicht autorisierte Zugriffe auf das Gerät zu verhindern.

Wenn die Vorbereitung des Geräts abgeschlossen ist, wird die Bestellung im Portal mit dem Status **Verarbeitet** angezeigt.

![Verarbeitete Data Box Heavy-Bestellung](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereitet dann Ihr Gerät vor und versendet es über einen regionalen Kurierdienst. Sobald das Gerät versandt wurde, erhalten Sie eine Nachverfolgungsnummer. Das Portal zeigt die Bestellung im Zustand **Versandt** an.

![Versendete Data Box Heavy-Bestellung](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Stornieren der Bestellung

Um die Bestellung zu stornieren, navigieren Sie im Azure-Portal zu **Übersicht**, und klicken Sie dann auf der Befehlsleiste auf **Stornieren**.

Solange eine Bestellung noch nicht mit dem Status „Verarbeitet“ angezeigt wird, können Sie sie jederzeit stornieren.
 
Um eine stornierte Bestellung zu löschen, navigieren Sie zu **Übersicht** und klicken dann auf der Befehlsleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Informationen zu Azure Data Box Heavy vermittelt:

> [!div class="checklist"]
> * Voraussetzungen
> * Bestellen von Data Box Heavy
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

Im nächsten Tutorial erfahren Sie, wie Sie Data Box Heavy einrichten.

> [!div class="nextstepaction"]
> [Verkabeln und Herstellen der Verbindung mit Azure Data Box Heavy (Vorschauversion)](./data-box-heavy-deploy-set-up.md)
