---
title: 'Tutorial: Installieren eines physischen Azure Stack Edge Mini R-Geräts | Microsoft-Dokumentation'
description: Das zweite Tutorial zur Installation eines Azure Stack Edge Mini R-Geräts enthält Informationen zur Verkabelung (Stromversorgung und Netzwerk) des physischen Geräts.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464227"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Tutorial: Installieren von Azure Stack Edge Mini R

In diesem Tutorial erfahren Sie, wie Sie ein physisches Azure Stack Edge Mini R-Gerät installieren. Im Rahmen der Installation muss das Gerät verkabelt werden.

Die Installation kann bis zu 30 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Untersuchen des Geräts
> * Verkabeln des Geräts

## <a name="prerequisites"></a>Voraussetzungen

Für die Installation eines physischen Geräts wird Folgendes vorausgesetzt:

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben alle Schritte des Tutorials [Vorbereiten der Bereitstellung von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md) ausgeführt.
    * Sie haben eine Azure Stack Edge-Ressource für die Bereitstellung Ihres Geräts erstellt.
    * Sie haben den Aktivierungsschlüssel zum Aktivieren Ihres Geräts mit der Azure Stack Edge-Ressource erstellt.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Für das physische Azure Stack Edge Mini R-Gerät

Führen Sie folgende Schritte aus, bevor Sie ein Gerät bereitstellen:

- Achten Sie darauf, dass das Gerät sicher auf einer flachen, stabilen und ebenen Arbeitsfläche liegt.
- Vergewissern Sie sich, dass der geplante Aufstellungsort über Folgendes verfügt:
    - Standardmäßige unabhängige Wechselstromversorgung. ODER:
    - Rack-PDU (Power Distribution Unit) 
    

### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter

Vorbereitungen

- Machen Sie sich mit den Netzwerkanforderungen für die Bereitstellung von Azure Stack Edge Mini R vertraut, und konfigurieren Sie das Netzwerk des Rechenzentrums entsprechend. Weitere Informationen finden Sie im Abschnitt mit den [Azure Stack Edge-Netzwerkanforderungen](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Vergewissern Sie sich, dass die Internetbandbreite mindestens 20 MBit/s beträgt, damit das Gerät optimal funktioniert. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Untersuchen des Geräts

Dieses Gerät wird als einzelne Einheit ausgeliefert. Führen Sie die folgenden Schritte aus, um das Gerät auszupacken.

1. Legen Sie den Karton auf einen flachen, ebenen Untergrund.
2. Überprüfen Sie das Behältnis des Geräts auf Beschädigungen. Öffnen Sie das Behältnis, und überprüfen Sie das Gerät. Sollte das Behältnis oder das Gerät beschädigt sein, wenden Sie sich an den Microsoft-Support, um zu überprüfen, ob das Gerät funktionsfähig ist.
3. Vergewissern Sie sich nach dem Öffnen des Behältnisses, dass Folgendes vorhanden ist:
    - Ein tragbares Azure Stack Edge Mini R-Gerät mit seitlich angebrachten Dämpfern
    - Ein Akku und die auf der Geräterückseite angebrachte Abdeckung 
    - Ein Netzkabel, um den Akku mit einer Stromquelle zu verbinden 

Sollten Sie nicht alle oben aufgeführten Teile erhalten haben, wenden Sie sich an den Azure Stack Edge-Support. Im nächsten Schritt wird Ihr Gerät verkabelt.


## <a name="cable-the-device"></a>Verkabeln des Geräts

In diesem Abschnitt erfahren Sie, wie Sie Ihr Azure Stack Edge-Gerät an die Stromversorgung und an das Netzwerk anschließen.

Bevor Sie mit der Verkabelung Ihres Geräts beginnen, benötigen Sie Folgendes:

- Ihr physisches Azure Stack Edge Mini R-Gerät am Installationsort
- Ein Netzkabel
- Mindestens ein Netzwerkkabel (1 GbE, RJ45) zum Anschließen an die Verwaltungsschnittstelle. Das Gerät verfügt über zwei 1-GbE-Netzwerkschnittstellen: eine für die Verwaltung und eine für Daten.
- Ein SFP+-Kupferkabel (10 GbE) für jede zu konfigurierende Datennetzwerkschnittstelle. Mindestens eine Datennetzwerkschnittstelle (PORT 3 oder PORT 4) muss mit dem Internet verbunden sein und über Azure-Konnektivität verfügen.  
- Zugang zu einer PDU (Power Distribution Unit) (empfohlen)

> [!NOTE]
> - Wenn Sie nur eine einzelne Datennetzwerkschnittstelle nutzen, sollten Sie eine 10-GbE-Netzwerkschnittstelle wie „PORT 3“ oder „PORT 4“ verwenden, um Daten an Azure zu senden. 
> - Aus Leistungsgründen und für die Verarbeitung großer Datenmengen empfiehlt es sich, alle Datenports zu verwenden.
> - Das Azure Stack Edge-Gerät sollte mit dem Rechenzentrumsnetzwerk verbunden sein, um Daten von Datenquellservern erfassen zu können. <!-- engg TBC -->

Azure Stack Edge-Gerät:

- Auf der Vorderseite des Geräts befindet sich ein SSD-Träger. 

    - In dem Einschub des Geräts befindet sich ein einzelner SSD-Datenträger. 
    - Außerdem verfügt das Gerät über eine CFX-Karte, die als Speicher für den Betriebssystemdatenträger fungiert.
    
- Auf der Vorderseite befinden sich Netzwerkschnittstellen und das WLAN-Modul.

    - Zwei RJ-45-Netzwerkschnittstellen mit 1 GbE („PORT 1“ und „PORT 2“ auf der lokalen Benutzeroberfläche des Geräts)
    - Zwei SFP+-Netzwerkschnittstellen mit 10 GbE („PORT 3“ und „PORT 4“ auf der lokalen Benutzeroberfläche des Geräts) 
    - Ein WLAN-Port mit angeschlossenem WLAN-Transceiver

- Auf der Vorderseite befindet sich auch ein Ein-/Aus-Schalter. 

- Auf der Rückseite befinden sich ein Akku und eine am Gerät angebrachte Abdeckung. 


Gehen Sie wie folgt vor, um Ihr Gerät an die Stromversorgung und an das Netzwerk anzuschließen:

1. Machen Sie sich mit den verschiedenen Netzwerk- und Speicherkomponenten auf der Vorderseite Ihres Geräts vertraut.

    ![Netzwerk- und Speicherschnittstellen des Geräts](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Der Ein-/Aus-Schalter befindet sich links unten auf der Vorderseite des Geräts. 

    ![Gerätevorderseite mit Ein-/Aus-Schalter des Geräts](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Der Akku ist mit der Rückseite des Geräts verbunden. Am Akku befindet sich ein zweiter Ein-/Aus-Schalter. 

    ![Gerätevorderseite mit Ein-/Aus-Schalter des Akkus](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Verbinden Sie ein Ende des Netzkabels mit dem Akku und das andere mit der Steckdose. 

    ![Anschluss für Netzkabel](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Im reinen Akkubetrieb (also ohne Verbindung mit der Stromversorgung) müssen sich sowohl der Ein-/Aus-Schalter auf der Vorderseite als auch der Schalter am Akku in der Position „ON“ (EIN) befinden. Ist der Akku mit einer Stromquelle verbunden, sollte sich nur der Ein-/Aus-Schalter auf der Vorderseite des Geräts in der Position „ON“ (EIN) befinden. 

4. Schalten Sie das Gerät durch Drücken des Ein-/Aus-Schalters auf der Vorderseite ein. 
    
    > [!NOTE]
    > Wenn Sie die Stromversorgung für das Gerät ein- oder ausschalten möchten, müssen Sie den schwarzen Knopf über dem Ein-/Aus-Schalter drücken und den Schalter in die Position „ON“ (EIN) bzw. „OFF“ (AUS) bringen. 

5. Wenn Sie WLAN für dieses Gerät konfigurieren möchten, verwenden Sie das folgende Verkabelungsdiagramm:

    ![Verkabelung für WLAN](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Verbinden Sie die 1-GbE-Netzwerkschnittstelle „PORT 1“ mit dem Computer, der zum Konfigurieren des physischen Geräts verwendet wird. „PORT 1“ ist die dedizierte Verwaltungsschnittstelle.


    Orientieren Sie sich bei Verwendung einer kabelgebundenen Konfiguration des Geräts an das folgende Diagramm:
     
    ![Verkabelung für kabelgebundene Konfiguration](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Verbinden Sie die 1-GbE-Netzwerkschnittstelle „PORT 1“ mit dem Computer, der zum Konfigurieren des physischen Geräts verwendet wird. „PORT 1“ ist die dedizierte Verwaltungsschnittstelle.
    - Verbinden Sie mindestens einen der Ports 2, 3 oder 4 mit dem Datencenternetzwerk/Internet.
    
        - Wenn Sie sich für „PORT 2“ entscheiden, verwenden Sie das RJ45-Netzwerkkabel.
        - Verwenden Sie für die 10-GbE-Netzwerkschnittstellen die SFP+-Kupferkabel.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge-Themen behandelt:

> [!div class="checklist"]
> * Auspacken des Geräts
> * Verkabeln des Geräts

Im nächsten Tutorial erfahren Sie, wie Sie Ihr Gerät verbinden, einrichten und aktivieren.

> [!div class="nextstepaction"]
> [Tutorial: Verbinden, Einrichten und Aktivieren von Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
