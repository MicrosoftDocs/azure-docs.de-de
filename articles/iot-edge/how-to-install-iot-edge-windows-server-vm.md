---
title: Ausführen von Azure IoT Edge auf virtuellen Windows Server-Computern | Microsoft-Dokumentation
description: Anleitung zur Einrichtung von Azure IoT Edge auf virtuellen Windows Server-Computern im Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 9e3f7e3b23cba3fab87ee35aa2a15b6305d9ece4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054179"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Ausführen von Azure IoT Edge auf virtuellen Windows Server-Computern
Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen.

Weitere Informationen zur Funktionsweise und zu den Komponenten der IoT Edge-Runtime finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

In diesem Artikel werden die Schritte zum Ausführen der Azure IoT Edge-Runtime auf einem virtuellen Windows Server 2019-Computer mithilfe des [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)-Angebots auf dem Azure Marketplace beschrieben. Befolgen Sie die Anweisungen im Artikel [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge-windows.md) unter Windows, um diese mit anderen Versionen zu verwenden.

## <a name="deploy-from-the-azure-marketplace"></a>Bereitstellen über den Azure Marketplace
1.  Navigieren Sie zum [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)-Angebot auf dem Azure Marketplace, oder suchen Sie auf dem [Azure Marketplace](https://azuremarketplace.microsoft.com/) nach „Windows Server“.
2.  Wählen Sie **Jetzt holen** aus. 
3.  Suchen Sie unter **Softwareplan** die Option „Windows Server 2019 Datacenter Server Core with Containers“ (Windows Server 2019 Datacenter Server Core mit Containern), und wählen Sie dann im nächsten Dialogfeld die Option **Weiter** aus.
    * Sie können diese Anweisungen auch für andere Versionen von Windows Server mit Containern verwenden.
4.  Wählen Sie im Azure-Portal **Erstellen**, und befolgen Sie die Anweisungen des Assistenten zum Bereitstellen des virtuellen Computers. 
    *   Wenn Sie zum ersten Mal einen virtuellen Computer verwenden, ist es am einfachsten, ein Kennwort zu verwenden und im Menü für den öffentlichen eingehenden Port RDP und SSH zu aktivieren. 
    *   Bei einer ressourcenintensiven Workload sollten Sie mehr CPUs und/oder Arbeitsspeicher hinzufügen, um die VM-Größe zu erhöhen.
5.  Konfigurieren Sie den virtuellen Computer nach seiner Bereitstellung, sodass er eine Verbindung mit Ihrer IoT Hub-Instanz herstellen kann:
    1.  Kopieren Sie die Geräte-Verbindungszeichenfolge von Ihrem IoT Edge-Gerät, das Sie in Ihrer IoT Hub-Instanz erstellt haben. (Befolgen Sie die Schrittanleitung unter [Registrieren eines neuen Azure IoT Edge-Geräts über das Azure-Portal](how-to-register-device-portal.md), wenn Sie mit diesem Prozess nicht vertraut sind.)
    1.  Wählen Sie die neu erstellte VM-Ressource im Azure-Portal aus, und öffnen Sie die Option **Befehl ausführen**.
    1.  Wählen Sie die Option **RunPowerShellScript** aus.
    1.  Kopieren Sie dieses Skript in das Befehlsfenster mit Ihrer Geräteverbindungszeichenfolge: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Führen Sie das Skript aus, um die Edge-Runtime zu installieren, und legen Sie Ihre Verbindungszeichenfolge durch Auswahl von **Ausführen** fest.
    1.  Nach eins bis zwei Minuten sollte eine Meldung angezeigt werden, dass die Edge-Runtime erfolgreich installiert und bereitgestellt wurde.


## <a name="deploy-from-the-azure-portal"></a>Bereitstellen über das Azure-Portal
1. Suchen Sie im Azure-Portal nach „Windows Server“, und wählen Sie **Windows Server 2019 Datacenter** aus, um den Workflow zur Erstellung des virtuellen Computers zu starten. 
2. Wählen Sie unter **Softwareplan auswählen** den Eintrag „Windows Server 2019 Datacenter Server Core with Containers“ aus, und wählen Sie dann **Erstellen** aus.
3. Führen Sie Schritt 5 der obigen Anweisungen („Bereitstellen über den Azure Marketplace“) aus.

## <a name="deploy-from-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle
1. Wenn Sie die Azure-Befehlszeilenschnittstelle auf Ihrem Desktop verwenden, melden Sie sich zuerst an:

   ```azurecli-interactive
   az login
   ```
    
1. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das gewünschte Abonnement aus:
   1. Listen Sie Ihre Abonnements auf:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopieren Sie das SubscriptionID-Feld für das zu verwendende Abonnement.
   1. Führen Sie den folgenden Befehl mit der kopierten ID aus:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Erstellen Sie eine neue Ressourcengruppe (oder geben Sie in den nächsten Schritten eine vorhandene Ressourcengruppe an):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Erstellen Sie einen neuen virtuellen Computer:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Mit diesem Befehl werden Sie zur Eingabe eines Kennworts aufgefordert. Sie können jedoch die Option `--admin-password` hinzufügen, um das Kennwort in einem Skript festzulegen, was einfacher ist.
   * Das Windows Server Core-Image unterstützt Befehlszeilen nur auf dem Remotedesktop, wenn Sie also die vollständige Desktopoberfläche verwenden möchten, müssen Sie `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` als Image angeben.

1. Legen Sie die Verbindungszeichenfolge fest. (Befolgen Sie die Schrittanleitung unter [Registrieren eines neuen Azure IoT Edge-Geräts mithilfe der Azure-Befehlszeilenschnittstelle](how-to-register-device-cli.md), wenn Sie mit diesem Prozess nicht vertraut sind.)

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein IoT Edge-Gerät für die installierte Runtime bereitgestellt haben, können Sie [IoT Edge-Module bereitstellen](how-to-deploy-modules-portal.md).

Wenn Sie Probleme mit der ordnungsgemäßen Installation der Edge-Runtime haben, lesen Sie die Informationen auf der Seite [Problembehandlung](troubleshoot.md).

Weitere Informationen zum Aktualisieren einer vorhandenen Installation auf die aktuelle Version von IoT Edge finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).

Weitere Informationen zur Verwendung virtueller Windows-Computer enthält die [Dokumentation zu virtuellen Windows-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/).

Wenn Sie nach der Einrichtung SSH auf diesem virtuellen Computer verwenden möchten, befolgen Sie die Anweisungen in der Anleitung [Installation von OpenSSH für Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell). Verwenden Sie dabei den Remotedesktop oder die Remote-PowerShell.
