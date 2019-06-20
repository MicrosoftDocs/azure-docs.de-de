---
title: Herstellen einer Verbindung mit und Verwalten von Microsoft Azure Data Box Edge-Geräten über die Windows PowerShell-Schnittstelle | Microsoft-Dokumentation
description: Beschreibt, wie Sie Data Box Edge über die Windows PowerShell-Schnittstelle verbinden und dann verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 8cd89b21e80662ec50746e0c7721a5544cfbce30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717505"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Verwalten eines Azure Data Box Edge-Geräts mittels Windows PowerShell

Azure Data Box Edge ist eine Lösung, mit der Sie Daten verarbeiten und über ein Netzwerk an Azure senden können. In diesem Artikel werden einige der Konfigurations- und Verwaltungsaufgaben für Ihr Data Box Edge-Gerät beschrieben. Sie können das Azure-Portal, die lokale Webbenutzeroberfläche oder die Windows PowerShell-Schnittstelle verwenden, um Ihr Gerät zu verwalten.

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

Sie können auch IoT Edge-Zertifikate hochladen, um eine sichere Verbindung zwischen Ihrem IoT Edge-Gerät und den nachgeschalteten Geräten zu ermöglichen, die möglicherweise damit verbunden sind. Es gibt drei IoT Edge-Zertifikate ( *.PEM*-Format), die Sie installieren müssen:

- Zertifikat der Stammzertifizierungsstelle oder der Zertifizierungsstelle des Besitzers
- Zertifikat der Gerätezertifizierungsstelle
- Zertifikat des Geräteschlüssels

Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets zum Installieren von IoT Edge-Zertifikaten:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Weitere Informationen zu Zertifikaten finden Sie unter [Zertifikate für Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) oder [Installieren von Zertifikaten auf einem Gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

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
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Es folgt eine Beschreibung der verwendeten Parameter des Cmdlets:
    - `Path`: Geben Sie einen Netzwerkpfad für die Freigabe an, in der Sie das Computeprotokollpaket erstellen möchten.
    - `Credential`: Geben Sie den Benutzernamen und das Kennwort für die Netzwerkfreigabe an.
    - `RoleInstanceName`: Geben Sie die Zeichenfolge `IotRole` für diesen Parameter ein.
    - `FullLogCollection`: Dieser Parameter stellt sicher, dass das Protokollpaket alle Computeprotokolle enthält. Standardmäßig enthält das Protokollpaket nur eine Teilmenge der Protokolle.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Überwachung und Problembehandlung von Computemodulen

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Beenden der Remotesitzung

Schließen Sie das PowerShell-Fenster, um die PowerShell-Remotesitzung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie [Azure Data Box Edge](data-box-edge-deploy-prep.md) im Azure-Portal bereit.
