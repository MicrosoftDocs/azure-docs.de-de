---
title: Installieren von Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Azure IoT Edge-Installationsanweisungen für Linux-Container unter Windows 10, Windows Server und Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151704"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Verwenden von IoT Edge unter Windows zum Ausführen von Linux-Containern

Testen Sie IoT Edge-Module für Linux-Geräte mithilfe eines Windows-Computers. 

In einem Produktionsszenario sollten Windows-Geräte nur Windows-Container ausführen. Ein häufiges Entwicklungsszenario besteht jedoch darin, Windows-Computer zum Erstellen von IoT Edge-Modulen für Linux-Geräte zu verwenden. Die IoT Edge-Runtime für Windows ermöglicht es Ihnen, Linux-Container für **Tests und Entwicklung** auszuführen. 

In diesem Artikel sind die Schritte zum Installieren der Azure IoT Edge-Runtime mit Linux-Containern auf Ihrem Windows-x64-System (AMD/Intel) aufgeführt. Weitere Informationen zum Installationsprogramm für die IoT Edge-Runtime sowie Details zu allen Installationsparametern finden Sie unter [Installieren der Azure IoT Edge-Runtime unter Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie anhand dieses Abschnitts, ob Ihr Windows-Gerät IoT Edge unterstützt, und bereiten Sie es vor der Installation für eine Container-Engine vor. 

### <a name="supported-windows-versions"></a>Unterstützte Windows-Versionen

Azure IoT Edge-mit-Linux-Container können unter den folgenden Windows-Versionen ausgeführt werden: 
* Windows 10 Anniversary Update (Build 14393) oder höher
* Windows Server 2016 oder höher

Weitere Informationen zur aktuellen Version von IoT Edge finden Sie auf der Website für die [Releases von IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Wenn Sie IoT Edge auf einer VM installieren möchten, sollten Sie die geschachtelte Virtualisierung aktivieren und mindestens 2 GB Arbeitsspeicher zuordnen. Wie Sie die geschachtelte Virtualisierung aktivieren, hängt vom verwendeten Hypervisor ab. Für Hyper-V verfügen virtuelle Computer der zweiten Generation über eine geschachtelte Virtualisierung, die standardmäßig aktiviert ist. Für VMware gibt es einen Umschalter zum Aktivieren des Features auf dem virtuellen Computer. 

### <a name="prepare-the-container-engine"></a>Vorbereiten der Container-Engine 

Azure IoT Edge basiert auf einer [OCI-kompatiblen](https://www.opencontainers.org/) Container-Engine. Hinsichtlich der Konfiguration besteht der größte Unterschied zwischen der Ausführung von Windows- und Linux-Containern auf einem Windows-Computer darin, dass die IoT Edge-Installation eine Windows-Containerruntime enthält, Sie aber für Linux-Container selbst eine Runtime bereitstellen müssen, bevor Sie IoT Edge installieren. 

Zum Einrichten eines Windows-Computers, um Container für Linux-Geräte zu entwickeln und zu testen, können Sie [Docker Desktop](https://www.docker.com/docker-windows) als Container-Engine verwenden. Sie müssen Docker installieren und für die [Verwendung von Linux-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) konfigurieren, bevor Sie IoT Edge installieren.  

Wenn es sich bei Ihrem IoT Edge-Gerät um einen Windows-Computer handelt, sollten Sie überprüfen, ob dieser die [Systemanforderungen](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) für Hyper-V erfüllt.

## <a name="install-iot-edge-on-a-new-device"></a>Installieren von IoT Edge auf einem neuen Gerät

>[!NOTE]
>Für Azure IoT Edge-Softwarepakete gelten die in den Paketen enthaltenen Lizenzbedingungen (im Verzeichnis „LICENSE“). Lesen Sie vor Verwendung des Pakets die Lizenzbedingungen. Durch die Installation und Nutzung des Pakets erklären Sie sich mit diesen Bedingungen einverstanden. Wenn Sie mit den Lizenzbedingungen nicht einverstanden sind, verwenden Sie das Paket nicht.

Der Azure IoT Edge-Sicherheitsdaemon wird über ein PowerShell-Skript heruntergeladen und installiert. Der Sicherheitsdaemon startet dann das erste der beiden Runtimemodule, den IoT Edge-Agent. Dieser ermöglicht die Remotebereitstellung anderer Module. 

Wenn Sie die IoT Edge-Runtime zum ersten Mal auf einem Gerät installieren, müssen Sie dem Gerät eine Identität von einem IoT-Hub bereitstellen. Ein einzelnes IoT Edge-Gerät kann mithilfe einer von IoT Hub bereitgestellten Geräteverbindungszeichenfolge manuell bereitgestellt werden. Alternativ können Sie IoT Hub Device Provisioning Service verwenden, um automatisch Geräte bereitzustellen. Dieses Verfahren ist hilfreich, wenn viele Geräte eingerichtet werden müssen. 

Mehr über die verschiedenen Installationsoptionen und -parameter finden Sie im Artikel [Installieren der Azure IoT Edge-Runtime unter Windows](how-to-install-iot-edge-windows.md). Sobald Sie Docker Desktop für Linux-Container installiert und konfiguriert haben, besteht der Hauptunterschied bei der Installation im Deklarieren von Linux mit dem Parameter **-ContainerOs**. Beispiel: 

1. Registrieren Sie ein neues IoT Edge-Gerät, und rufen Sie die Geräteverbindungszeichenfolge ab, falls Sie dies noch nicht getan haben. Notieren Sie sich diese Verbindungszeichenfolge zur späteren Verwendung in diesem Abschnitt. Sie können diesen Schritt mithilfe der folgenden Tools ausführen:

   * [Azure-Portal](how-to-register-device-portal.md)
   * [Azure-Befehlszeilenschnittstelle](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Führen Sie PowerShell als Administrator aus.

   >[!NOTE]
   >Verwenden Sie eine AMD64-PowerShell-Sitzung, um IoT Edge zu installieren, nicht PowerShell (x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Der Befehl **Deploy-IoTEdge** überprüft, ob Ihr Windows-Computer über eine unterstützte Version verfügt. Außerdem aktiviert der Befehl das Containerfeature und lädt dann die Moby-Runtime (die für Linux-Container nicht verwendet wird) und die IoT Edge-Runtime herunter. Der Befehl ist standardmäßig auf Windows-Container ausgerichtet, daher müssen Sie Linux als gewünschtes Containerbetriebssystem deklarieren. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. An diesem Punkt könnten IoT Core-Geräte möglicherweise automatisch neu starten. Andere Windows 10- oder Windows Server-Geräte könnten Sie zum Neustart auffordern. Wenn ja, starten Sie Ihr Gerät jetzt neu. Sobald Ihr Gerät bereit ist, führen Sie PowerShell erneut als Administrator aus.

5. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Dieser Befehl ist standardmäßig auf die manuelle Bereitstellung mit einer Geräteverbindungszeichenfolge ausgerichtet. Deklarieren Sie Linux erneut als gewünschtes Containerbetriebssystem. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Geben Sie bei Aufforderung die Geräteverbindungszeichenfolge ein, die Sie in Schritt 1 abgerufen haben. Die Geräteverbindungszeichenfolge ordnet das physische Gerät in IoT Hub einer Geräte-ID zu. 

   Die Verbindungszeichenfolge hat das folgende Format und darf keine Anführungszeichen enthalten: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Überprüfen Sie den Status des IoT Edge-Diensts. Er sollte als „Wird ausgeführt“ aufgelistet werden.  

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle der letzten fünf Minuten. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf. Nach einer Neuinstallation sollte als einziges Modul **edgeAgent** ausgeführt werden. Nachdem Sie die [IoT Edge-Module bereitgestellt](how-to-deploy-modules-portal.md) haben, werden weitere angezeigt. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein IoT Edge-Gerät für die installierte Runtime bereitgestellt haben, können Sie [IoT Edge-Module bereitstellen](how-to-deploy-modules-portal.md).

Wenn Probleme bei der Installation von IoT Edge auftreten, finden Sie auf der Seite für die [Problembehandlung](troubleshoot.md) weitere Informationen.

Weitere Informationen zum Aktualisieren einer vorhandenen Installation auf die aktuelle Version von IoT Edge finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).
