---
title: Schnellstart für Microsoft Azure Data Box Heavy | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure Data Box Heavy über das Azure-Portal schnell bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 930da7367b3f5d7e20617afedef007efea97c51a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515939"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Schnellstart: Bereitstellen von Azure Data Box Heavy über das Azure-Portal

In dieser Schnellstartanleitung wird beschrieben, wie Sie die Azure Data Box Heavy über das Azure-Portal bereitstellen. Die Schritte umfassen das Verkabeln, Konfigurieren und Kopieren von Daten in der Data Box Heavy, damit sie in Azure hochgeladen werden können. Der Schnellstart erfolgt im Azure-Portal und auf der lokalen Webbenutzeroberfläche des Geräts.

Eine ausführliche Anleitung zur Schritt-für-Schritt-Bereitstellung und zur Nachverfolgung finden Sie unter [Tutorial: Bestellen der Azure Data Box Heavy ](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Gerät bereitstellen, müssen die folgenden Konfigurationsvoraussetzungen für den Aufstellungsort, den Data Box-Dienst und das Data Box-Gerät erfüllt sein.

### <a name="for-installation-site"></a>Aufstellungsort

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Gerät passt durch alle Zugänge. Abmessungen des Geräts: Breite: 66,04 cm, Länge: 121,92 cm, Höhe: 71,12 cm.
- Falls sich der Aufstellungsort des Geräts nicht im Erdgeschoss befindet, muss das gewünschte Stockwerk über einen Aufzug oder über eine Rampe erreichbar sein.
- Es sind zwei Personen für den Transport des Geräts verfügbar. Das Gerät wiegt rund 227 kg, und auf seiner Unterseite befinden sich Räder.
- In Ihrem Datencenter ist ein ebener Aufstellungsort mit einer Netzwerkverbindung verfügbar, der genügend Platz für ein Gerät dieser Größe bietet.

### <a name="for-service"></a>Für den Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
- Das Abonnement, das Sie für den Data Box-Dienst verwenden, lautet [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Sie haben Zugriff auf das Abonnement (als Besitzer oder Mitwirkender), um eine Data Box Heavy-Bestellung erstellen zu können.

### <a name="for-device"></a>Für das Gerät

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie haben sich mit den [Sicherheitsrichtlinien für Data Box Heavy](data-box-safety.md) vertraut gemacht.
- Sie verfügen über einen Hostcomputer, der mit dem Netzwerk des Datencenters verbunden ist. Die Data Box Heavy kopiert die Daten von diesem Computer. Ihr Hostcomputer muss über ein [unterstütztes Betriebssystem](data-box-heavy-system-requirements.md) verfügen.
- Sie verfügen über einen Laptop und ein RJ45-Kabel, um eine Verbindung mit der lokalen Benutzeroberfläche herstellen und das Gerät konfigurieren zu können. Mithilfe des Laptops muss jeder Knoten des Geräts einmalig konfiguriert werden.
- Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk, und Sie verfügen über mindestens eine 10-GbE-Verbindung.
- Für jeden Geräteknoten wird ein 40-GBit/s- oder 10-GBit/s-Kabel benötigt. Die Kabel müssen mit der Netzwerkschnittstelle Mellanox MCX314A-BCCT kompatibel sein:
    - Bei 40-GBit/s-Kabeln muss ein Kabelende vom Typ QSFP+ verwendet werden.
    - Bei 10-GBit/s-Kabeln benötigen Sie ein SFP+-Kabel, das auf der einen Seite mit einem 10-G-Switch verbunden ist und an dem Ende, das mit dem Gerät verbunden wird, über einen QSFP+-auf-SFP+-Adapter (oder über den QSA-Adapter) verfügt.
- Die Netzkabel sind in einem Fach auf der Rückseite des Geräts enthalten.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [http://portal.azure.com](http://portal.azure.com) beim Azure-Portal an.

## <a name="order"></a>Reihenfolge

Dieser Schritt dauert ungefähr fünf Minuten.

1. Erstellen Sie im Azure-Portal eine neue Azure Data Box-Ressource.
2. Wählen Sie ein vorhandenes Abonnement aus, das für diesen Dienst aktiviert ist, und als Übertragungstyp **Import** aus. Geben Sie unter **Quellland/-region** an, wo sich die Daten befinden, und legen Sie die **Azure-Zielregion** für die Datenübertragung fest.
3. Wählen Sie **Data Box Heavy** aus. Die maximal nutzbare Kapazität ist 770 TB. Für größere Datenmengen können Sie mehrere Aufträge erteilen.
4. Geben Sie die Auftragsdetails und Versandinformationen ein. Wenn der Dienst in Ihrer Region verfügbar ist, können Sie E-Mail-Adressen für Benachrichtigungen angeben, die Zusammenfassung prüfen und anschließend den Auftrag erstellen.

Nachdem der Auftrag erstellt wurde, wird das Gerät für den Versand vorbereitet.

## <a name="cable-for-power"></a>Verkabeln des Geräts für die Stromversorgung

Dieser Schritt dauert ungefähr 5 Minuten.

Nach Erhalt der Data Box Heavy führen Sie die folgenden Schritte zum Verkabeln des Geräts aus, um es an die Stromversorgung anzuschließen und einzuschalten.

1. Wenn es Anzeichen gibt, dass das Gerät manipuliert oder beschädigt wurde, fahren Sie nicht fort. Wenden Sie sich an den Microsoft-Support mit der Bitte, Ihnen ein Austauschgerät zu senden.
2. Bringen Sie das Gerät an den Aufstellungsort, und arretieren Sie die hinteren Räder.
3. Schließen Sie alle vier Netzkabel an die Netzteile auf der Rückseite des Geräts an.
4. Schalten Sie die Geräteknoten mithilfe der Netzschalter auf der Vorderseite ein.

## <a name="cable-first-node-for-network"></a>Verkabeln des ersten Knotens, um eine Netzwerkverbindung herzustellen

Dieser Schritt dauert ungefähr 10 bis 15 Minuten.

1. Verwenden Sie das RJ-45 CAT 6 -Netzwerkkabel, um Ihren Hostcomputer mit dem Verwaltungsport (MGMT) des Geräts zu verbinden.
2. Verwenden Sie das Twinax QSFP+-Kupferkabel, um mindestens eine Netzwerkschnittstelle, DATA 1 oder DATA 2, mit 40 GBit/s (bevorzugt gegenüber 1 GBit/s) für die Datenübertragung anzuschließen. Verwenden Sie bei Verwendung eines 10-GBit/s-Switchs ein Twinax-SFP+-Kupferkabel mit einem QSFP+-auf-SFP+-Adapter (QSA-Adapter), um die 40-GBit/s-Netzwerkschnittstelle für die Datenübertragung anzuschließen.
3. Verkabeln Sie das Gerät wie unten gezeigt.  

    ![Verkabelte Data Box Heavy](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurieren des ersten Knotens

Dieser Schritt dauert ungefähr 5 bis 7 Minuten.

1. Zum Abrufen des Gerätekennworts wechseln Sie im [Azure-Portal](http://portal.azure.com) zu **Allgemein > Gerätedetails**. Für beide Knoten des Geräts wird dasselbe Kennwort verwendet.
2. Weisen Sie dem Ethernet-Adapter auf dem Computer, mit dem Sie sich mit der Data Box Heavy verbinden, die statische IP-Adresse 192.168.100.5 und das Subnetz 255.255.255.255.0 zu. Greifen Sie unter `https://192.168.100.10` auf die lokale Webbenutzeroberfläche des Geräts zu. Der Verbindungsaufbau kann nach dem Einschalten des Geräts bis zu 5 Minuten dauern.
3. Melden Sie sich mit dem aus dem Azure-Portal abgerufenen Kennwort an. Sie sehen eine Fehlermeldung, die auf ein Problem mit dem Sicherheitszertifikat der Website hinweist. Befolgen Sie die browserspezifischen Anweisungen, um zur Webseite zu gelangen.
4. Standardmäßig werden die Netzwerkeinstellungen für die Schnittstellen (mit Ausnahme der MGMT-Schnittstelle) als DHCP konfiguriert. Bei Bedarf können Sie diese Schnittstellen als statisch konfigurieren und eine IP-Adresse angeben.

## <a name="cable-and-configure-the-second-node"></a>Verkabeln und Konfigurieren des zweiten Knotens

Dieser Schritt dauert ungefähr 15 bis 20 Minuten.

Führen Sie die gleichen Schritte wie zum Verkabeln des ersten Knotens aus, und konfigurieren Sie den zweiten Knoten auf dem Gerät.  

## <a name="copy-data"></a>Kopieren von Daten

Die Dauer dieses Vorgangs hängt von der Datenmenge und der Geschwindigkeit des Netzwerks ab, über das die Daten kopiert werden.
 
1. Kopieren Sie die Daten auf beide Geräteknoten, indem Sie beide 40-Gbit/s-Datenschnittstellen parallel verwenden.

    - Wenn Sie mit einem Windows-Host arbeiten, verwenden Sie ein SMB-kompatibles Dateikopierprogramm wie [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Verwenden Sie bei einem NFS-Host den Befehl `cp` oder `rsync` zum Kopieren der Daten.
2. Stellen Sie eine Verbindung mit den Freigaben auf dem Gerät über diesen Pfad her: `\\<IP address of your device>\ShareName`. Um die Anmeldeinformationen für den Freigabezugriff zu erhalten, wechseln Sie auf der lokalen Webbenutzeroberfläche der Data Box Heavy zur Seite **Verbindung herstellen und Daten kopieren**.
3. Stellen Sie sicher, dass die Namen der Freigaben und Ordner sowie die Daten den Vorgaben entsprechen, die unter [Azure Storage and Data Box Heavy service limits](data-box-heavy-limits.md) (Für Azure Storage und den Data Box Heavy-Dienst geltende Einschränkungen, in englischer Sprache) beschrieben sind.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

Der Zeitraum, der für die Durchführung dieses Vorgangs erforderlich ist, hängt von Ihrer Datengröße ab.

1. Nachdem das Kopieren der Daten ohne Fehler abgeschlossen wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zur Seite **Für den Versand vorbereiten**, und beginnen Sie mit der Versandvorbereitung.
2. Nachdem **Für den Versand vorbereiten** auf beiden Knoten erfolgreich abgeschlossen wurde, deaktivieren Sie das Gerät auf der lokalen Webbenutzeroberfläche.

## <a name="ship-to-azure"></a>Senden an Azure

Dieser Vorgang dauert ca. 15-20 Minuten.

1. Entfernen Sie die Kabel, und verstauen Sie sie wieder in dem Fach auf der Rückseite des Geräts.
2. Planen Sie eine Abholung mit Ihrem regionalen Paketdienst.
3. Wenden Sie sich an [Data Box Operations](mailto:DataBoxOps@microsoft.com), um sie über die Abholung zu informieren und das Adressetikett für den Rückversand zu erhalten.
4. Das Rücksendeetikett sollte im vorderen transparenten Fach des Geräts sichtbar sein.

## <a name="verify-data"></a>Überprüfen der Daten

Der Zeitraum, der für die Durchführung dieses Vorgangs erforderlich ist, hängt von Ihrer Datengröße ab.

1. Wenn das Data Box Heavy-Gerät mit dem Netzwerk des Azure-Rechenzentrums verbunden wird, werden die Daten automatisch in Azure hochgeladen.
2. Sie werden vom Data Box-Dienst über das Azure-Portal benachrichtigt, dass der Kopiervorgang für die Daten abgeschlossen ist.

    1. Prüfen Sie die Fehlerprotokolle auf Fehler, und ergreifen Sie ggf. entsprechende Maßnahmen.
    2. Stellen Sie sicher, dass sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieser Schritt dauert zwei bis drei Minuten.

- Im Azure-Portal können Sie den Data Box Heavy-Auftrag stornieren, bevor er verarbeitet wurde. Nachdem der Auftrag verarbeitet wurde, kann er nicht mehr storniert werden. Der Auftrag wird abgearbeitet, bis er den Status „Abgeschlossen“ erreicht hat. Navigieren Sie zum Stornieren des Auftrags zu **Übersicht**, und klicken Sie in der Befehlsleiste auf **Stornieren**.

- Sie können einen Auftrag löschen, wenn im Azure-Portal dafür der Status **Abgeschlossen** oder **Abgebrochen** angezeigt wird. Navigieren Sie zum Löschen des Auftrags zu **Übersicht**, und klicken dann in der Befehlsleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Data Box Heavy bereitgestellt, über die Ihre Daten in Azure importiert werden. Fahren Sie mit dem folgenden Tutorial fort, um sich weiter über die Azure Data Box Heavy-Verwaltung zu informieren:

> [!div class="nextstepaction"]
> [Verwenden des Azure-Portals zum Verwalten der Data Box Heavy](data-box-portal-admin.md)
