---
title: 'Tutorial: Installieren eines physischen Azure Stack Edge Pro R-Geräts | Microsoft-Dokumentation'
description: Das zweite Tutorial zur Installation von Azure Stack Edge Pro R enthält Informationen zur Verkabelung (Stromversorgung und Netzwerk) des physischen Geräts.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463999"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Tutorial: Installieren von Azure Stack Edge Pro R

In diesem Tutorial erfahren Sie, wie Sie ein physisches Azure Stack Edge Pro R-Gerät installieren. Im Rahmen der Installation muss das Gerät verkabelt werden.

Die Installation kann bis zu 30 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Untersuchen des Geräts
> * Verkabeln des Geräts

## <a name="prerequisites"></a>Voraussetzungen

Für die Installation eines physischen Geräts wird Folgendes vorausgesetzt:

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben alle Schritte des Tutorials [Vorbereiten der Bereitstellung von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md) ausgeführt.
    * Sie haben eine Azure Stack Edge-Ressource für die Bereitstellung Ihres Geräts erstellt.
    * Sie haben den Aktivierungsschlüssel zum Aktivieren Ihres Geräts mit der Azure Stack Edge-Ressource erstellt.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Für das physische Azure Stack Edge Pro R-Gerät

Führen Sie folgende Schritte aus, bevor Sie ein Gerät bereitstellen:

- Achten Sie darauf, dass das Gerät sicher auf einer flachen, stabilen und ebenen Arbeitsfläche liegt.
- Vergewissern Sie sich, dass der geplante Aufstellungsort über Folgendes verfügt:
    - Standardmäßige unabhängige Wechselstromversorgung

        ODER
    - Rack-PDU (Power Distribution Unit). Das Gerät wird mit einer unterbrechungsfreien Stromversorgung (USV) ausgeliefert.
    

### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter

Vorbereitungen

- Machen Sie sich mit den Netzwerkanforderungen für die Bereitstellung von Azure Stack Edge Pro R vertraut, und konfigurieren Sie das Netzwerk des Rechenzentrums entsprechend. Weitere Informationen finden Sie in den [Netzwerkanforderungen für Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Vergewissern Sie sich, dass die Internetbandbreite mindestens 20 MBit/s beträgt, damit das Gerät optimal funktioniert.


## <a name="inspect-the-device"></a>Untersuchen des Geräts

Dieses Gerät wird als einzelne Einheit ausgeliefert. Führen Sie die folgenden Schritte aus, um das Gerät auszupacken.

1. Legen Sie den Karton auf einen flachen, ebenen Untergrund.
2. Überprüfen Sie das Behältnis des Geräts auf Beschädigungen. Öffnen Sie das Behältnis, und überprüfen Sie das Gerät. Sollte das Behältnis oder das Gerät beschädigt sein, wenden Sie sich an den Microsoft-Support, um zu überprüfen, ob das Gerät funktionsfähig ist.
3. Vergewissern Sie sich nach dem Öffnen des Behältnisses, dass Folgendes vorhanden ist:
    - Ein Azure Stack Edge Pro R-Gerät (einzelnes Gehäuse)
    - Eine unterbrechungsfreie Stromversorgung (USV)
    - Zwei kurze Netzkabel für die Verbindung zwischen Gerät und USV
    - Ein Netzkabel für die Verbindung zwischen USV und Stromquelle

Sollten Sie nicht alle oben aufgeführten Komponenten erhalten haben, wenden Sie sich an den Support für Azure Stack Edge Pro R. Im nächsten Schritt wird Ihr Gerät verkabelt.


## <a name="cable-the-device"></a>Verkabeln des Geräts

In diesem Abschnitt erfahren Sie, wie Sie Ihr Azure Stack Edge Pro R-Gerät an die Stromversorgung und an das Netzwerk anschließen.

Bevor Sie mit der Verkabelung Ihres Geräts beginnen, benötigen Sie Folgendes:

- Ihr physisches Azure Stack Edge Pro R-Gerät am Installationsort
- Ein Netzkabel
- Mindestens ein Netzwerkkabel (1 GbE, RJ45) zum Anschließen an die Verwaltungsschnittstelle. Das Gerät verfügt über zwei 1-GbE-Netzwerkschnittstellen: eine für die Verwaltung und eine für Daten.
- Ein SFP+-Kupferkabel (10/25 GbE) für jede zu konfigurierende Datennetzwerkschnittstelle. Mindestens eine Datennetzwerkschnittstelle (PORT 3 oder PORT 4) muss mit dem Internet verbunden sein und über Azure-Konnektivität verfügen.  
- Zugang zu einer PDU (Power Distribution Unit) (empfohlen)

> [!NOTE]
> - Wenn Sie nur eine einzelne Datennetzwerkschnittstelle nutzen, sollten Sie eine 25/10-GbE-Netzwerkschnittstelle wie „PORT 3“ oder „PORT 4“ verwenden, um Daten an Azure zu senden. 
> - Aus Leistungsgründen und für die Verarbeitung großer Datenmengen empfiehlt es sich, alle Datenports zu verwenden.
> - Das Azure Stack Edge Pro R-Gerät sollte mit dem Rechenzentrumsnetzwerk verbunden sein, um Daten von Datenquellservern erfassen zu können.

Ihr Azure Stack Edge Pro R-Gerät bietet Folgendes:

- Auf der Vorderseite befinden sich Laufwerke und ein Netzschalter.

    - Es stehen acht Datenträgerslots zur Verfügung.
    - Das Gerät verfügt auch über zwei integrierte M.2-SATA-Datenträger, die als Betriebssystemdatenträger fungieren. 

- Auf der Rückseite befinden sich redundante Netzteile (Power Supply Units, PSUs).
- Auf der Rückseite stehen vier Netzwerkschnittstellen zur Verfügung:

    - Zwei Schnittstellen mit jeweils 1 GBit/s
    - Zwei Schnittstellen mit jeweils 25 GBit/s, die auch als Schnittstellen mit 10 GBit/s fungieren können
    - Ein Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC)

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Gehen Sie wie folgt vor, um Ihr Gerät an die Stromversorgung und an das Netzwerk anzuschließen:

1. Machen Sie sich mit den verschiedenen Anschlüssen auf der Geräterückseite vertraut.

    ![Rückseite eines verkabelten Geräts](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Machen Sie die Datenträgerslots und den Netzschalter auf der Vorderseite des Geräts ausfindig.

    ![Vorderseite eines Geräts](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Verbinden Sie ein Ende des Netzkabels mit der USV. Schließen Sie das andere Ende des Netzkabels an die Rack-PDU (Power Distribution Unit) an. 
5. Drücken Sie den Netzschalter, um das Gerät einzuschalten.
6. Verbinden Sie die 1-GbE-Netzwerkschnittstelle „PORT 1“ mit dem Computer, der zum Konfigurieren des physischen Geräts verwendet wird. „PORT 1“ ist die dedizierte Verwaltungsschnittstelle.
7. Verbinden Sie mindestens einen der Ports 2, 3 oder 4 mit dem Datencenternetzwerk/Internet.

    - Wenn Sie sich für „PORT 2“ entscheiden, verwenden Sie das RJ45-Netzwerkkabel.
    - Verwenden Sie für die 10/25-GbE-Netzwerkschnittstellen die SFP+-Kupferkabel.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro R-Themen behandelt:

> [!div class="checklist"]
> * Auspacken des Geräts
> * Verkabeln des Geräts

Im nächsten Tutorial erfahren Sie, wie Sie eine Verbindung mit Ihrem Gerät herstellen.

> [!div class="nextstepaction"]
> [Tutorial: Herstellen einer Verbindung mit Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
