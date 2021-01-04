---
title: Herstellen einer Verbindung mit und Verwalten von Microsoft Azure Stack Edge Pro-Geräten über die Windows PowerShell-Schnittstelle | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie über die Windows PowerShell-Schnittstelle eine Verbindung mit einem Azure Stack Edge Pro-Gerät herstellen und dieses anschließend verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: c9c6247f021b7af4cfdd899ffd4b6bd178f2256c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345592"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>Verwalten eines Azure Stack Edge Pro FPGA-Geräts über Windows PowerShell

Azure Stack Edge Pro ist eine Lösung, mit der Sie Daten verarbeiten und über ein Netzwerk an Azure senden können. In diesem Artikel werden einige der Konfigurations- und Verwaltungsaufgaben für Ihr Azure Stack Edge Pro-Gerät beschrieben. Sie können das Azure-Portal, die lokale Webbenutzeroberfläche oder die Windows PowerShell-Schnittstelle verwenden, um Ihr Gerät zu verwalten.

Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe der PowerShell-Schnittstelle ausgeführt werden. 

Dieser Artikel enthält die folgenden Verfahren:

- Herstellen einer Verbindung mit der PowerShell-Schnittstelle
- Unterstützungspaket erstellen
- Hochladen des Zertifikats
- Zurücksetzen des Geräts
- Anzeigen von Geräteinformationen
- Abrufen von Computeprotokollen
- Überwachung und Problembehandlung von Computemodulen

## <a name="connect-to-the-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Unterstützungspaket erstellen

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Hochladen des Zertifikats

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Sie können auch IoT Edge-Zertifikate hochladen, um eine sichere Verbindung zwischen Ihrem IoT Edge-Gerät und den nachgeschalteten Geräten zu ermöglichen, die möglicherweise damit verbunden sind. Es müssen drei Dateien (*PEM*-Format) installiert werden:

- Zertifikat der Stammzertifizierungsstelle oder der Zertifizierungsstelle des Besitzers
- Zertifikat der Gerätezertifizierungsstelle
- Privater Schlüssel des Geräts 

Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets zum Installieren von IoT Edge-Zertifikaten:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
Wenn Sie dieses Cmdlet ausführen, werden Sie aufgefordert, das Kennwort für die Netzwerkfreigabe anzugeben.

Weitere Informationen zu Zertifikaten finden Sie unter [Zertifikate für Azure IoT Edge](../iot-edge/iot-edge-certs.md) oder [Installieren von Zertifikaten auf einem Gateway](../iot-edge/how-to-create-transparent-gateway.md).

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Zurücksetzen Ihres Geräts

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Abrufen von Computeprotokollen

Wenn die Computerolle auf Ihrem Gerät konfiguriert ist, können Sie die Computeprotokolle auch über die PowerShell-Schnittstelle abrufen.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Verwenden Sie `Get-AzureDataBoxEdgeComputeRoleLogs` zum Abrufen der Computeprotokolle für Ihr Gerät.

    Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Es folgt eine Beschreibung der verwendeten Parameter des Cmdlets:
    - `Path`: Geben Sie einen Netzwerkpfad für die Freigabe an, in der Sie das Computeprotokollpaket erstellen möchten.
    - `Credential`: Geben Sie den Benutzernamen für die Netzwerkfreigabe an. Wenn Sie dieses Cmdlet ausführen, müssen Sie das Freigabekennwort angeben.
    - `FullLogCollection`: Dieser Parameter stellt sicher, dass das Protokollpaket alle Computeprotokolle enthält. Standardmäßig enthält das Protokollpaket nur eine Teilmenge der Protokolle.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Überwachung und Problembehandlung von Computemodulen

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Beenden der Remotesitzung

Schließen Sie das PowerShell-Fenster, um die PowerShell-Remotesitzung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) im Azure-Portal bereit.