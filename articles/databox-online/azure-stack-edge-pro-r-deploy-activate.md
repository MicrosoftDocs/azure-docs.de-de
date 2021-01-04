---
title: Tutorial zum Aktivieren eines Azure Stack Edge Pro R-Geräts im Azure-Portal
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Pro R erfahren Sie, wie Sie Ihr physisches Gerät aktivieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: c1408c25ea7e5d805d17e084899b0ffeb6cfec28
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463903"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Tutorial: Aktivieren eines Azure Stack Edge Pro R-Geräts

In diesem Tutorial erfahren Sie, wie Sie Ihr Azure Stack Edge Pro R-Gerät über die lokale Webbenutzeroberfläche aktivieren.

Der Aktivierungsvorgang dauert ungefähr 5 Minuten.

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Aktivieren des physischen Geräts

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Pro R-Gerät konfigurieren und einrichten:

* Für Ihr physisches Gerät: 
    
    - Sie haben das physische Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) installiert.
    - Sie haben die Netzwerk- und Computenetzwerkeinstellungen gemäß [Konfigurieren von Netzwerk, Computenetzwerk und Webproxy](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md) konfiguriert.
    - Sie haben Ihre eigenen Gerätezertifikate hochgeladen oder auf Ihrem Gerät generiert, sofern Sie den Gerätenamen oder die DNS-Domäne auf der Seite **Gerät** geändert haben. Falls Sie diesen Schritt nicht durchgeführt haben, wird während der Aktivierung des Geräts ein Fehler angezeigt, und die Aktivierung wird blockiert. Weitere Informationen finden Sie unter [Konfigurieren von Zertifikaten](azure-stack-edge-placeholder.md).
    
* Sie verfügen über den Aktivierungsschlüssel aus dem Azure Stack Edge-Dienst, den Sie zum Verwalten des Azure Stack Edge Pro R-Geräts erstellt haben. Weitere Informationen finden Sie unter [Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivieren des Geräts

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche des Geräts zur Seite **Erste Schritte**.
2. Wählen Sie auf der Kachel **Aktivierung** die Option **Aktivieren** aus. 

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Im Bereich **Aktivieren**:
    1. Geben Sie den **Aktivierungsschlüssel** ein, den Sie unter [Abrufen des Aktivierungsschlüssels](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key) abgerufen haben.

    1. Sie können die proaktive Protokollsammlung aktivieren, damit Microsoft Protokolle basierend auf dem Integritätsstatus des Geräts sammeln kann. Die auf diese Weise gesammelten Protokolle werden in ein Azure Storage-Konto hochgeladen.
    
    1. Wählen Sie **Übernehmen**.

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Zunächst wird das Gerät aktiviert. Sie werden dann aufgefordert, die Schlüsseldatei herunterzuladen.
    
    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
    Wählen Sie **Herunterladen und fortfahren** aus, und speichern Sie die Datei *device-serial-no.json* an einem sicheren Speicherort außerhalb des Geräts. **Diese Schlüsseldatei enthält die Wiederherstellungsschlüssel für den Betriebssystem-Datenträger und die anderen Datenträger auf Ihrem Gerät**. Diese Schlüssel werden möglicherweise für eine zukünftige Systemwiederherstellung benötigt.

    Nachfolgend ist der Inhalt der *json*-Datei gezeigt:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    In der folgenden Tabelle sind die verschiedenen Schlüssel erläutert:
    
    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |`Id`    | Die ID des Geräts.        |
    |`DataVolumeBitLockerExternalKeys`|Die BitLocker-Schlüssel für die Datenträger, die zur Wiederherstellung der lokalen Daten auf Ihrem Gerät verwendet werden.|
    |`SystemVolumeBitLockerRecoveryKey`| Der BitLocker-Schlüssel für das Systemvolume. Dieser Schlüssel hilft bei der Wiederherstellung der Systemkonfiguration und der Systemdaten für Ihr Gerät. |
    |`ServiceEncryptionKey`| Dieser Schlüssel schützt die durch den Azure-Dienst übertragenen Daten. Dieser Schlüssel stellt sicher, dass eine Kompromittierung des Azure-Diensts nicht zu einer Kompromittierung gespeicherter Informationen führt. |

6. Wechseln Sie zur Seite **Übersicht**. Der Gerätestatus muss als **Aktiviert** angezeigt werden.

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Die Aktivierung des Geräts ist abgeschlossen. Sie können nun Freigaben auf Ihrem Gerät hinzufügen.

Wenn während der Aktivierung Probleme auftreten, gehen Sie zu [Beheben von Aktivierungsproblemen auf Ihrem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Aktivieren des physischen Geräts

Informationen zum Übertragen von Daten mit Ihrem Azure Stack Edge Pro R-Gerät finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Übertragen von Daten über Freigaben mit einem Azure Stack Edge Pro-Gerät mit GPU](./azure-stack-edge-j-series-deploy-add-shares.md)
