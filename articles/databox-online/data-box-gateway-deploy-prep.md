---
title: Tutorial zum Vorbereiten des Azure-Portals für die Bereitstellung von Data Box Gateway | Microsoft-Dokumentation
description: Im ersten Tutorial zum Bereitstellen von Azure Data Box Gateway geht es um die Vorbereitung des Azure-Portals.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: d99b10598b9f16da2cf202330f0b5bac9219699f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476834"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Data Box Gateway


Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen Ihres Azure Data Box Gateway erforderlich sind. In diesem Tutorial wird beschrieben, wie Sie das Azure-Portal für die Bereitstellung Ihrer Data Box Gateway-Ressource vorbereiten. 

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Herunterladen des Images des virtuellen Geräts
> * Abrufen des Aktivierungsschlüssels

## <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen Ihres Data Box Gateway die folgenden Tutorials in der angegebenen Reihenfolge aus.

| **#** | **Schritt** | **Dokumente** |
| --- | --- | --- | 
| 1. |**[Vorbereiten des Azure-Portals für Data Box Gateway](data-box-gateway-deploy-prep.md)** |Erstellen und konfigurieren Sie die Data Box Gateway-Ressource, bevor Sie ein virtuelles Data Box Gateway-Gerät bereitstellen. |
| 2. |**[Bereitstellen des Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Bereitstellen des Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md)**|Stellen Sie für Hyper-V ein virtuelles Data Box Gateway-Gerät auf einem Hostsystem mit Hyper-V unter Windows Server 2016 oder Windows Server 2012 R2 bereit, und stellen Sie eine Verbindung mit dem Gerät her. <br><br><br> Stellen Sie für VMware ein virtuelles Data Box Gateway-Gerät auf einem Hostsystem mit VMware ESXi 6.0, 6.5 oder 6.7 bereit, und stellen Sie eine Verbindung mit dem Gerät her.<br></br> |
| 3. |**[Verbinden, Einrichten und Aktivieren des Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche her, führen Sie die Einrichtung durch, und aktivieren Sie das Gerät. Anschließend können Sie die SMB-Freigaben bereitstellen.  |
| 4. |**[Übertragen von Daten mit Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Fügen Sie Freigaben hinzu, und stellen Sie über SMB oder NFS eine Verbindung mit Freigaben her. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden finden Sie die Konfigurationsvoraussetzungen für die Data Box Gateway-Ressource, das Data Box Gateway-Gerät und das Netzwerk des Datencenters.

### <a name="for-the-data-box-gateway-resource"></a>Für die Data Box Gateway-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Ihr Microsoft Azure-Abonnement wird für die Data Box Gateway-Ressource unterstützt. Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt.
- Sie verfügen für die Data Box Edge-/Data Box Gateway-, IoT Hub- und Azure Storage-Ressourcen über Zugriff als Besitzer oder Mitwirkender auf der Ressourcengruppenebene.

    - Für die Erstellung von Data Box Edge-/Data Box Gateway-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf der Ressourcengruppenebene verfügen. Vergewissern Sie sich außerdem, dass der Anbieter `Microsoft.DataBoxEdge` registriert ist. Eine Registrierungsanleitung finden Sie unter [Register resource provider](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
    - Für die Erstellung von IoT Hub-Ressourcen muss der Anbieter „Microsoft.Devices“ registriert sein. Eine Registrierungsanleitung finden Sie unter [Register resource provider](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
    - Für die Erstellung von Speicherkontoressourcen sind ebenfalls mindestens Berechtigungen als Mitwirkender auf der Ressourcengruppenebene erforderlich. Azure Storage ist standardmäßig als Ressourcenanbieter registriert.
- Sie haben als Administrator oder Benutzer Zugriff auf die Azure Active Directory Graph-API. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

### <a name="for-the-data-box-gateway-device"></a>Für das Data Box Gateway-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Gerät bereitstellen:

- Sie haben Zugriff auf ein Hostsystem mit Hyper-V unter Windows Server 2012 R2 oder höher oder VMware (ESXi 6.0, 6.5 oder 6.7), das zum Bereitstellen eines Geräts verwendet werden kann.
- Das Hostsystem verfügt für die Bereitstellung des virtuellen Data Box-Geräts über die folgenden Ressourcen:
  
  - Mindestens vier virtuelle Prozessoren
  - Mindestens 8 GB RAM
  - Eine Netzwerkschnittstelle
  - Einen Betriebssystemdatenträger mit 250 GB
  - Einen virtuellen Datenträger mit 2 TB für Systemdaten

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Netzwerk in Ihrem Datencenter ist gemäß den Netzwerkanforderungen für das Data Box Gateway-Gerät konfiguriert. Weitere Informationen finden Sie unter [Data Box Gateway – Systemanforderungen](data-box-gateway-system-requirements.md).

- Für den Normalbetrieb von Data Box Edge Gateway benötigen Sie Folgendes:

    - Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
    - Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie eine vorhandene Data Box Gateway-Ressource zum Verwalten Ihrer virtuellen Geräte haben, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

Führen Sie im Azure-Portal die folgenden Schritte aus, um eine Data Box Gateway-Ressource zu erstellen.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen bei einem der folgenden Portale an:

    - Azure-Portal (URL: [https://portal.azure.com](https://portal.azure.com))
    - Azure Government-Portal (URL: [https://portal.azure.us](https://portal.azure.us)). Ausführlichere Informationen finden Sie unter [Quickstart: Connect to Azure Government using portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal) (Schnellstart: Herstellen einer Verbindung mit Azure Government über das Portal).

2. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Data Box Edge/Data Box Gateway**. Wählen Sie „Data Box Edge/Data Box Gateway“ aus. Klicken Sie auf **Erstellen**.
3. Wählen Sie das Abonnement aus, das Sie für das Data Box Gateway-Gerät verwenden möchten. Wählen Sie die Region aus, in der Sie die Data Box Gateway-Ressource bereitstellen möchten. Für dieses Release sind „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“ verfügbar. Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist. Wählen Sie unter **Data Box Gateway** die Option **Erstellen** aus.

    ![Suchen nach dem Data Box Gateway-Dienst](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Abonnement    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Ressourcengruppe  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/resource-group-overview.md).     |

5. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |NAME   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |   
    |Region     |In diesem Release sind „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“ für die Ressourcenbereitstellung verfügbar. Die für Azure Government verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/). <br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|
    
    ![Erstellen der Data Box Gateway-Ressource](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Klicken Sie auf **Überprüfen + erstellen**.
 
7. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Klicken Sie auf **Erstellen**.

    ![Überprüfen der Details für die Data Box Gateway-Ressource](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Die Erstellung der Ressource dauert einige Minuten. Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

![Überprüfen der Details für die Data Box Gateway-Ressource](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Herunterladen des Images des virtuellen Geräts

Nachdem die Data Box Gateway-Ressource erstellt wurde, laden Sie das entsprechende Image des virtuellen Geräts herunter, um auf Ihrem Hostsystem ein virtuelles Gerät bereitzustellen. Die Images virtueller Geräte sind betriebssystemspezifisch.

> [!IMPORTANT]
> Die auf dem Data Box Gateway ausgeführte Software kann nur mit der Data Box Gateway-Ressource verwendet werden.

Gehen Sie im [Azure-Portal](https://portal.azure.com/) wie folgt vor, um ein Image eines virtuellen Geräts herunterzuladen.

1. Wählen Sie die von Ihnen erstellte Ressource und anschließend **Übersicht** aus. Falls Sie bereits über eine Azure Data Box Gateway-Ressource verfügen, wählen Sie die Ressource aus, und wechseln Sie zu **Übersicht**. Wählen Sie **Geräteeinrichtung** aus.

    ![Neue Data Box Gateway-Ressource](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Wählen Sie auf der Kachel **Image herunterladen** das Image des virtuellen Geräts aus. Das Image muss zu dem Betriebssystem des Hostservers passen, der zum Bereitstellen des virtuellen Computers verwendet wird. Die Imagedateien sind ca. 5,6 GB groß.
   
   * [VHDX für Hyper-V unter Windows Server 2012 R2 und höher](https://aka.ms/dbe-vhdx-2012).
   * [VMDK für VMWare ESXi 6.0, 6.5 oder 6.7](https://aka.ms/dbe-vmdk).

    ![Herunterladen des Images für das virtuelle Data Box Gateway-Gerät](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Laden Sie die Datei auf einen lokalen Datenträger herunter, und entzippen Sie sie. Notieren Sie den Speicherort der entzippten Datei.


## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Data Box Gateway-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Data Box Gateway-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die Ressource aus, die Sie erstellt haben, und wählen Sie dann **Übersicht** aus. Navigieren Sie in der **Geräteeinrichtung** zur Kachel **Configure and activate** (Konfigurieren und aktivieren).

    ![Kachel zum Konfigurieren und Aktivieren](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Wählen Sie **Schlüssel generieren** aus, um einen Aktivierungsschlüssel zu erstellen. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

    ![Abrufen des Aktivierungsschlüssels](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> - Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie sich mit den folgenden Themen zu Data Box Gateway befasst:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Herunterladen des Images des virtuellen Geräts
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie eine VM für Ihr Data Box Gateway bereitstellen. Sehen Sie sich je nach Hostbetriebssystem die folgenden ausführlichen Anweisungen an:

> [!div class="nextstepaction"]
> [Bereitstellen eines Data Box Gateway in Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [Bereitstellen eines Data Box Gateway in VMware](./data-box-gateway-deploy-provision-vmware.md)


