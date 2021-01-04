---
title: Tutorial zum Verschlüsseln und Aktivieren eines Azure Stack Edge Mini R-Geräts im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Mini R erfahren Sie, wie Sie Ihr physisches Gerät verschlüsseln und aktivieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: e5076888e692ad16e89f30d455e7f8c4e22a41b9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464305"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Tutorial: Aktivieren von Azure Stack Edge Mini R

In diesem Tutorial erfahren Sie, wie Sie Ihr Azure Stack Edge Mini R-Gerät über die lokale Webbenutzeroberfläche aktivieren.

Der Aktivierungsprozess dauert ungefähr 15 Minuten.

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Aktivieren des physischen Geräts

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Mini R-Gerät konfigurieren und einrichten:

* Für Ihr physisches Gerät: 
    
    - Sie haben das physische Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md) installiert.
    - Sie haben die Netzwerk- und Computenetzwerkeinstellungen gemäß [Konfigurieren von Netzwerk, Computenetzwerk und Webproxy](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md) konfiguriert.
    - Sie haben Ihre eigenen Zertifikate auf das Gerät hochgeladen (sofern Sie den Gerätenamen oder die DNS-Domäne auf der Seite **Gerät** geändert haben). Eine entsprechende Anleitung finden Sie unter [Tutorial: Konfigurieren von Zertifikaten, VPN und Verschlüsselung für Ihr Azure Stack Edge Mini R-Gerät](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md). Ohne diesen Schritt wird die Aktivierung blockiert.
    - Sie haben die Verschlüsselung ruhender Daten für Ihr Gerät konfiguriert. Falls Sie diesen Schritt nicht durchgeführt haben, wird während der Aktivierung des Geräts ein Fehler angezeigt, und die Aktivierung wird blockiert. Weitere Informationen finden Sie unter [Tutorial: Konfigurieren von Zertifikaten, VPN und Verschlüsselung für Ihr Azure Stack Edge Mini R-Gerät](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md).
    
* Sie verfügen über den Aktivierungsschlüssel aus dem Azure Stack Edge-Dienst, den Sie zum Verwalten des Azure Stack Edge Mini R-Geräts erstellt haben. Weitere Informationen finden Sie unter [Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivieren des Geräts

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche des Geräts zur Seite **Erste Schritte**.
2. Wählen Sie auf der Kachel **Aktivierung** die Option **Aktivieren** aus. 

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. Im Bereich **Aktivieren**:
    1. Geben Sie den **Aktivierungsschlüssel** ein, den Sie unter [Abrufen des Aktivierungsschlüssels](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key) abgerufen haben.

    1. Sie können die proaktive Protokollsammlung aktivieren, damit Microsoft Protokolle basierend auf dem Integritätsstatus des Geräts sammeln kann. Die auf diese Weise gesammelten Protokolle werden in ein Azure Storage-Konto hochgeladen.
    
    1. Wählen Sie **Übernehmen**.

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Zunächst wird das Gerät aktiviert. Sie werden dann aufgefordert, die Schlüsseldatei herunterzuladen.
    
    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
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
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    In der folgenden Tabelle sind die verschiedenen Schlüssel erläutert:
    
    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |`Id`    | Die ID des Geräts.        |
    |`DataVolumeBitLockerExternalKeys`|Die BitLocker-Schlüssel für die Datenträger, die zur Wiederherstellung der lokalen Daten auf Ihrem Gerät verwendet werden.|
    |`SystemVolumeBitLockerRecoveryKey`| Der BitLocker-Schlüssel für das Systemvolume. Dieser Schlüssel hilft bei der Wiederherstellung der Systemkonfiguration und der Systemdaten für Ihr Gerät. |
    |`SEDEncryptionExternalKey`| Dieser vom Benutzer bereitgestellte oder vom System generierte Schlüssel wird verwendet, um die sich selbst verschlüsselnden Datenlaufwerke zu schützen, die über eine integrierte Verschlüsselung verfügen. |
    |`ServiceEncryptionKey`| Dieser Schlüssel schützt die durch den Azure-Dienst übertragenen Daten. Dieser Schlüssel stellt sicher, dass eine Kompromittierung des Azure-Diensts nicht zu einer Kompromittierung gespeicherter Informationen führt. |

6. Wechseln Sie zur Seite **Übersicht**. Der Gerätestatus muss als **Aktiviert** angezeigt werden.

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
Die Aktivierung des Geräts ist abgeschlossen. Sie können nun Freigaben auf Ihrem Gerät hinzufügen.

Wenn während der Aktivierung Probleme auftreten, gehen Sie zu [Beheben von Aktivierungsproblemen auf Ihrem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Aktivieren des physischen Geräts

Informationen zum Übertragen von Daten mit Ihrem Azure Stack Edge Mini R-Gerät finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Übertragen von Daten über Freigaben mit einem Azure Stack Edge Pro-Gerät mit GPU](./azure-stack-edge-j-series-deploy-add-shares.md)
