---
title: 'Tutorial: Einrichten von Azure Data Box Heavy | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Data Box Heavy verkabeln und die Verbindung herstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: f2f6b544b56977b3f1bfb6a4fb46a9f1e3bcc294
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427942"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Tutorial: Verkabeln und Herstellen der Verbindung mit Azure Data Box Heavy (Vorschauversion)


In diesem Tutorial erfahren Sie, wie Sie Azure Data Box Heavy verkabeln, verbinden und einschalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verkabeln von Data Box Heavy
> * Herstellen der Verbindung mit Data Box Heavy

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Bestellen von Azure Data Box Heavy (Vorschauversion)](data-box-heavy-deploy-ordered.md) ausgeführt.
2. Sie haben Ihr Data Box Heavy-Gerät erhalten, und der Auftrag wird im Portal mit dem Status **Geliefert** angezeigt.
3. Sie haben sich mit den [Sicherheitsrichtlinien für Data Box Heavy](data-box-safety.md) vertraut gemacht.
4. In Ihrem Datencenter muss ein ebener Aufstellungsort mit einer Netzwerkverbindung verfügbar sein, der genügend Platz für ein Gerät dieser Größe bietet. Dieses Gerät kann nicht in einem Rack montiert werden.
5. Für dieses Speichergerät haben Sie vier geerdete Netzkabel erhalten.
6. Sie verfügen über einen Hostcomputer, der mit dem Netzwerk des Datencenters verbunden ist. Data Box Heavy kopiert die Daten von diesem Computer. Ihr Hostcomputer muss über ein [unterstütztes Betriebssystem](data-box-heavy-system-requirements.md) verfügen.
7. Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. 
8. Sie benötigen einen Laptop und ein RJ45-Kabel, um eine Verbindung mit der lokalen Benutzeroberfläche herstellen und das Gerät konfigurieren zu können. Mithilfe des Laptops muss jeder Knoten des Geräts einmalig konfiguriert werden.
9. Für jeden Geräteknoten wird ein 40-GBit/s- oder 10-GBit/s-Kabel benötigt.
    - Die Kabel müssen mit der Netzwerkschnittstelle [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibel sein.
    - Bei 40-GBit/s-Kabeln muss ein Kabelende vom Typ QSFP+ verwendet werden.
    - Bei 10-GBit/s-Kabeln benötigen Sie ein SFP+-Kabel, das auf der einen Seite mit einem 10-GBit/s-Switch verbunden ist und an dem Ende, das mit dem Gerät verbunden wird, über einen QSFP+-auf-SFP+-Adapter (oder über den QSA-Adapter) verfügt.

## <a name="cable-your-device-for-power"></a>Anschließen des Geräts an die Stromversorgung

Gehen Sie zur Verkabelung Ihres Geräts wie folgt vor:

1. Überprüfen Sie das Gerät auf Anzeichen einer Manipulation oder andere erkennbare Schäden. Fahren Sie nicht mit den nächsten Schritten fort, wenn das Gerät manipuliert wurde oder stark beschädigt ist. [Wenden Sie sich umgehend an den Microsoft-Support](data-box-disk-contact-microsoft-support.md), um zu ermitteln, ob das Gerät in einwandfreiem Zustand ist und verwendet werden kann oder ob ein Ersatzgerät geliefert werden muss.
2. Bringen Sie das Gerät an den Aufstellungsort.

    ![Data Box Heavy: Aufstellungsort](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Blockieren Sie die hinteren Rollen des Geräts, wie hier zu sehen:

    ![Data Box Heavy: Rollen des Geräts blockiert](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Suchen Sie nach den Entriegelungsgriffen für die vordere und hintere Tür des Geräts. Entriegeln Sie die vordere Tür des Geräts, und öffnen Sie sie, sodass sie mit der Geräteseite bündig ist. Wiederholen Sie diesen Schritt für die hintere Tür.
    Während des Betriebs müssen beide Türen des Geräts geöffnet sein, um eine optimale Belüftung des Geräts zu gewährleisten.

    ![Data Box Heavy: Türen geöffnet](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Die Ablage auf der Rückseite des Geräts sollte vier Netzkabel enthalten. Entnehmen Sie alle Kabel aus der Ablage, und legen Sie sie zur Seite.

    ![Data Box Heavy: Netzkabel in der Ablage](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Machen Sie sich als Nächstes mit den verschiedenen Anschlüssen auf der Geräterückseite vertraut. Es gibt zwei Geräteknoten: **NODE1** und **NODE2**. Jeder Knoten verfügt über vier Netzwerkschnittstellen: **MGMT**, **DATA1**, **DATA2**, **DATA3**. **MGMT** dient zum Konfigurieren der Verwaltung im Rahmen der Erstkonfiguration des Geräts. Bei **DATA1**-**DATA3** handelt es sich um Datenanschlüsse. Die Anschlüsse **MGMT** und **DATA3** haben eine Kapazität von 1 GBit/s. **DATA1** und **DATA2** können dagegen als Anschluss mit 40 GBit/s oder 10 GBit/s verwendet werden. Unterhalb der beiden Geräteknoten befinden sich vier Netzteile (Power Supply Units, PSUs), die von den beiden Geräteknoten gemeinsam verwendet werden. Diese **PSUs** sind wie folgt angeordnet: **PSU1**, **PSU2**, **PSU3** und **PSU4** (von links nach rechts).

    ![Data Box Heavy: Anschlüsse](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Schließen Sie alle vier Netzkabel an die Netzteile des Geräts an. Die grünen LEDs beginnen zu blinken.
8. Schalten Sie die Geräteknoten mithilfe der Netzschalter auf der Vorderseite ein. Halten Sie den Netzschalter mehrere Sekunden gedrückt, bis die blauen Lichter zu leuchten beginnen. Auf der Geräterückseite sollten nun alle grünen LEDs für die Netzteile dauerhaft leuchten. Im vorderen Bedienfeld des Geräts befinden sich auch Fehler-LEDs. Eine leuchtende Fehler-LED deutet auf ein fehlerhaftes Netzteil, einen fehlerhaften Lüfter oder ein Laufwerkproblem hin.  

    ![Data Box Heavy: vorderes Bedienfeld](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Verkabeln des ersten Knotens, um eine Netzwerkverbindung herzustellen

Gehen Sie wie folgt vor, um die Netzwerkverkabelung für einen der Knoten des Geräts vorzunehmen:

1. Verbinden Sie den Hostcomputer über ein Cat 6-Netzwerkkabel vom Typ RJ45 (blaues Kabel in der Abbildung) mit dem 1-GBit/s-Verwaltungsanschluss.
2. Verbinden Sie mindestens eine 40 GBit/s-Netzwerkschnittstelle für die Datenübertragung (bevorzugt gegenüber 1 GBit/s) über ein Twinax-QSFP+-Kupferkabel (schwarze Kabel in der Abbildung). Verwenden Sie bei Verwendung eines 10-GBit/s-Switchs ein Twinax-SFP+-Kupferkabel mit einem QSFP+-auf-SFP+-Adapter (QSA-Adapter), um die 40-GBit/s-Netzwerkschnittstelle für die Datenübertragung anzuschließen.

    ![Data Box Heavy: verkabelte Anschlüsse](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA1 und DATA2 sind vertauscht und entsprechen nicht der Darstellung auf der lokalen Webbenutzeroberfläche.
    > Der 40-GBit/s-Kabeladapter muss wie unten dargestellt eingesteckt werden.

    ![Data Box Heavy: 40-GBit/s-Kabeladapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurieren des ersten Knotens

Gehen Sie wie folgt vor, um Ihr Gerät über die lokale Konfiguration und das Azure-Portal einzurichten:

1. Laden Sie die Anmeldeinformationen für das Gerät aus dem Portal herunter. Navigieren Sie zu **Allgemein > Gerätedetails**. Kopieren Sie das **Gerätekennwort**. Diese Kennwörter sind mit einem bestimmten Auftrag im Portal verknüpft. Da Data Box Heavy über zwei Knoten verfügt, werden zwei Geräteseriennummern angezeigt. Das Geräteadministratorkennwort ist für beide Knoten identisch.

    ![Data Box Heavy: Anmeldeinformationen für das Gerät](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Verbinden Sie Ihre Clientarbeitsstation über ein Cat 6-Netzwerkkabel vom Typ RJ45 mit dem Gerät.
3. Konfigurieren Sie den Ethernetadapter auf dem Computer, über den Sie eine Verbindung mit dem Gerät herstellen, mit der statischen IP-Adresse `192.168.100.5` und dem Subnetz `255.255.255.0`.

    ![Data Box Heavy: Verbindungsherstellung mit der lokalen Webbenutzeroberfläche](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Stellen Sie unter der folgenden URL eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her: `http://192.168.100.10`. Klicken Sie auf **Erweitert** und anschließend auf **Weiter zu 192.168.100.10 (unsicher)** .
5. Eine Seite **Anmelden** für die lokale Webbenutzeroberfläche wird angezeigt.
    
    - Eine der Knotenseriennummern auf dieser Seite ist sowohl auf der Benutzeroberfläche des Portals als auch auf der lokalen Webbenutzeroberfläche identisch. Notieren Sie sich, welche Knotennummer zu welcher Seriennummer gehört. Es gibt zwei Knoten und zwei Geräteseriennummern im Portal. Dank dieser Zuordnung lässt sich leichter nachvollziehen, welcher Knoten zu welcher Seriennummer gehört.
    - Das Gerät ist zu diesem Zeitpunkt gesperrt.
    - Geben Sie das Geräteadministratorkennwort aus dem vorherigen Schritt ein, um sich bei dem Gerät anzumelden. Klicken Sie auf **Anmelden**.

    ![Data Box Heavy: Anmelden bei der lokalen Webbenutzeroberfläche](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Vergewissern Sie sich auf dem Dashboard, dass die Netzwerkschnittstellen konfiguriert sind. Ihr Geräteknoten verfügt über vier Netzwerkschnittstellen: zwei mit 1 GBit/s und zwei mit 40 GBit/s. Eine der Schnittstellen mit 1 GBit/s ist eine Verwaltungsschnittstelle und kann daher nicht vom Benutzer konfiguriert werden. Die anderen drei Netzwerkschnittstellen dienen zur Datenübertragung und können vom Benutzer konfiguriert werden.

- Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert.
- Sollte DHCP nicht aktiviert sein, navigieren Sie zu „Netzwerkschnittstellen festlegen“, und weisen Sie ggf. statische IP-Adressen zu.

    ![Data Box Heavy: Dashboard (Knoten 1)](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurieren des zweiten Knotens

Führen Sie die unter [Konfigurieren des ersten Knotens](#configure-first-node) angegebenen Schritte für den zweiten Knoten des Geräts aus.

![Data Box Heavy: Dashboard (Knoten 2)](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Sobald die Einrichtung des Geräts abgeschlossen ist, können Sie eine Verbindung mit den Gerätefreigaben herstellen und die Daten von Ihrem Computer auf das Gerät kopieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Informationen zu Azure Data Box Heavy vermittelt:

> [!div class="checklist"]
> * Verkabeln von Data Box Heavy
> * Herstellen der Verbindung mit Data Box Heavy

Im nächsten Tutorial erfahren Sie, wie Sie Daten auf Ihr Data Box Heavy-Gerät kopieren.

> [!div class="nextstepaction"]
> [Kopieren der Daten auf die Azure Data Box](./data-box-heavy-deploy-copy-data.md)
