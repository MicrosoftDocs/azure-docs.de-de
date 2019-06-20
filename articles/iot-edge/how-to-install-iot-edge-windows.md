---
title: Installieren von Azure IoT Edge unter Windows | Microsoft-Dokumentation
description: Anweisungen zur Azure IoT Edge-Installation unter Windows 10, Windows Server und Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: f67f24cab907c3fe9998704e0a0a85d5b29f60a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808863"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installieren der Azure IoT Edge-Runtime unter Windows

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen. 

Weitere Informationen zur IoT Edge-Runtime finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

In diesem Artikel sind die Schritte zum Installieren der Azure IoT Edge-Runtime auf Ihrem Windows-x64-System (AMD/Intel) mit Windows-Containern aufgeführt.

> [!NOTE]
> Ein bekanntes Problem des Windows-Betriebssystems verhindert den Übergang in den Energiesparmodus und den Ruhezustand, wenn IoT Edge-Module (vom Prozess isolierte Windows Nano Server-Container) ausgeführt werden. Dieses Problem wirkt sich auf die Akkulaufzeit des Geräts aus.
>
> Geben Sie als Problemumgehung den Befehl `Stop-Service iotedge` ein, um die Ausführung aller IoT Edge-Module zu beenden, bevor Sie diese Energiesparzustände verwenden. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Die Verwendung von Linux-Containern auf Windows-Systemen ist keine empfohlene oder unterstützte Produktionskonfiguration für Azure IoT Edge. Die Container können jedoch zu Entwicklungs- und Testzwecken eingesetzt werden. Weitere Informationen finden Sie unter [Verwenden von IoT Edge unter Windows zum Ausführen von Linux-Containern](how-to-install-iot-edge-windows-with-linux.md).

Informationen zur aktuellen Version von IoT Edge finden Sie auf der Website für die [Releases von IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie anhand dieses Abschnitts, ob Ihr Windows-Gerät IoT Edge unterstützt, und bereiten Sie es vor der Installation für eine Container-Engine vor. 

### <a name="supported-windows-versions"></a>Unterstützte Windows-Versionen

Für Entwicklungs- und Testszenarien kann Azure IoT Edge mit Windows-Containern unter jeder Version von Windows 10 oder Windows Server 2019 (Build 17763) installiert werden, die das Containerfeature unterstützt. Weitere Informationen darüber, welche Betriebssysteme derzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Vorbereitungen für eine Container-Engine 

Azure IoT Edge basiert auf einer [OCI-kompatiblen](https://www.opencontainers.org/) Container-Engine. Verwenden Sie für Produktionsszenarios die Moby-Engine, die im Installationsskript enthalten ist, um Windows-Container auf Ihren Windows-Geräten auszuführen. 

## <a name="install-iot-edge-on-a-new-device"></a>Installieren von IoT Edge auf einem neuen Gerät

>[!NOTE]
>Für Azure IoT Edge-Softwarepakete gelten die in den Paketen enthaltenen Lizenzbedingungen (im Verzeichnis „LICENSE“). Lesen Sie vor Verwendung des Pakets die Lizenzbedingungen. Durch die Installation und Nutzung des Pakets erklären Sie sich mit diesen Bedingungen einverstanden. Wenn Sie mit den Lizenzbedingungen nicht einverstanden sind, verwenden Sie das Paket nicht.

Der Azure IoT Edge-Sicherheitsdaemon wird über ein PowerShell-Skript heruntergeladen und installiert. Der Sicherheitsdaemon startet dann das erste der beiden Runtimemodule, den IoT Edge-Agent. Dieser ermöglicht die Remotebereitstellung anderer Module. 

Wenn Sie die IoT Edge-Runtime zum ersten Mal auf einem Gerät installieren, müssen Sie dem Gerät eine Identität von einem IoT-Hub bereitstellen. Ein einzelnes IoT Edge-Gerät kann manuell bereitgestellt werden, indem eine von IoT Hub bereitgestellte Gerät-Verbindungszeichenfolge verwendet wird. Alternativ können Sie IoT Hub Device Provisioning Service verwenden, um automatisch Geräte bereitzustellen. Dieses Verfahren ist hilfreich, wenn viele Geräte eingerichtet werden müssen. Je nach gewählter Bereitstellung können Sie dazu das richtige Installationsskript auswählen. 

In den folgenden Abschnitten werden die häufigsten Anwendungsfälle und Parameter für IoT Edge-Installationsskripts auf einem neuen Gerät erläutert. 

### <a name="option-1-install-and-manually-provision"></a>Option 1: Installation und manuelle Bereitstellung

Hier stellen Sie eine **Geräteverbindungszeichenfolge** bereit, die von IoT Hub generiert wurde, um das Gerät bereitzustellen. 

In diesem Beispiel wird die manuelle Installation mit Windows-Containern veranschaulicht:

1. Registrieren Sie ein neues IoT Edge-Gerät, und rufen Sie die **Geräteverbindungszeichenfolge** ab, falls Sie dies noch nicht getan haben. Notieren Sie sich diese Verbindungszeichenfolge zur späteren Verwendung in diesem Abschnitt. Sie können diesen Schritt mithilfe der folgenden Tools ausführen:

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

3. Durch den Befehl **Deploy-IoTEdge** wird überprüft, ob Ihr Windows-Computer über eine unterstützte Version verfügt. Außerdem aktiviert der Befehl das Containerfeature und lädt dann die Moby-Runtime und die IoT Edge-Runtime herunter. Der Befehl verwendet standardmäßig Windows-Container. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. An diesem Punkt könnten IoT Core-Geräte möglicherweise automatisch neu starten. Andere Windows 10- oder Windows Server-Geräte könnten Sie zum Neustart auffordern. Wenn ja, starten Sie Ihr Gerät jetzt neu. Sobald Ihr Gerät bereit ist, führen Sie PowerShell erneut als Administrator aus.

5. Durch den Befehl **Initialize-IoTEdge** wird die IoT Edge-Runtime auf Ihrem Computer konfiguriert. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Geben Sie bei Aufforderung die Geräteverbindungszeichenfolge ein, die Sie in Schritt 1 abgerufen haben. Die Geräteverbindungszeichenfolge ordnet das physische Gerät in IoT Hub einer Geräte-ID zu. 

   Die Verbindungszeichenfolge hat das folgende Format und darf keine Anführungszeichen enthalten: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Überprüfen Sie mit den Schritten in [Bestätigen einer erfolgreichen Installation](#verify-successful-installation) den Status von IoT Edge auf Ihrem Gerät. 

Wenn Sie ein Gerät manuell installieren und bereitstellen, können Sie zusätzliche Parameter verwenden, um die Installation folgendermaßen anzupassen:
* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Sie können das Installationsprogramm auf ein Offlineverzeichnis verweisen.
* Sie können ein bestimmtes Containerimage für den Agent deklarieren und Anmeldeinformationen bereitstellen, wenn dieses sich in einer privaten Registrierung befindet.

Um weitere Informationen zu diesen Installationsoptionen zu erhalten, fahren Sie mit [Alle Installationsparameter](#all-installation-parameters) fort.

### <a name="option-2-install-and-automatically-provision"></a>Option 2: Installation und automatische Bereitstellung

Hier stellen Sie das Gerät mithilfe von IoT Hub Device Provisioning Service bereit. Geben Sie die **Bereichs-ID** aus einer Instanz von IoT Hub Device Provisioning Service und die **Registrierungs-ID** Ihres Geräts an.

Im folgenden Beispiel wird die automatische Installation mit Windows-Containern veranschaulicht:

1. Führen Sie die unter [Erstellen und Bereitstellen eines simulierten TPM-IoT Edge-Geräts unter Windows](how-to-auto-provision-simulated-device-windows.md) aufgeführten Schritte aus, um den Gerätebereitstellungsdient einzurichten und die **Bereichs-ID** abzurufen, ein TPM-Gerät zu simulieren und die zugehörige **Registrierungs-ID** abzurufen. Erstellen Sie anschließend eine individuelle Registrierung. Nachdem Ihr Gerät in Ihrem IoT Hub registriert wurde, fahren Sie mit diesen Installationsschritten fort.  

   >[!TIP]
   >Lassen Sie das Fenster, in dem der TPM-Simulator ausgeführt wird, während der Installation und beim Testen geöffnet. 

2. Führen Sie PowerShell als Administrator aus.

   >[!NOTE]
   >Verwenden Sie eine AMD64-PowerShell-Sitzung, um IoT Edge zu installieren, nicht PowerShell (x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Durch den Befehl **Deploy-IoTEdge** wird überprüft, ob Ihr Windows-Computer über eine unterstützte Version verfügt. Außerdem aktiviert der Befehl das Containerfeature und lädt dann die Moby-Runtime und die IoT Edge-Runtime herunter. Der Befehl verwendet standardmäßig Windows-Container. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. An diesem Punkt könnten IoT Core-Geräte möglicherweise automatisch neu starten. Andere Windows 10- oder Windows Server-Geräte könnten Sie zum Neustart auffordern. Wenn ja, starten Sie Ihr Gerät jetzt neu. Sobald Ihr Gerät bereit ist, führen Sie PowerShell erneut als Administrator aus.

6. Durch den Befehl **Initialize-IoTEdge** wird die IoT Edge-Runtime auf Ihrem Computer konfiguriert. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet. Verwenden Sie das `-Dps`-Flag, um den Device Provisioning Service anstelle der manuellen Bereitstellung zu verwenden.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Geben Sie bei Aufforderung die Bereichs-ID von Ihrem Device Provisioning Service und die Registrierungs-ID von Ihrem Gerät ein; beide sollten Sie in Schritt 1 abgerufen haben.

8. Überprüfen Sie mit den Schritten in [Bestätigen einer erfolgreichen Installation](#verify-successful-installation) den Status von IoT Edge auf Ihrem Gerät. 

Wenn Sie ein Gerät manuell installieren und bereitstellen, können Sie zusätzliche Parameter verwenden, um die Installation folgendermaßen anzupassen:
* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Sie können das Installationsprogramm auf ein Offlineverzeichnis verweisen.
* Sie können ein bestimmtes Containerimage für den Agent deklarieren und Anmeldeinformationen bereitstellen, wenn dieses sich in einer privaten Registrierung befindet.

Weitere Informationen zu diesen Installationsoptionen finden Sie in diesem Artikel. Sie können diesen auch überspringen und [hier](#all-installation-parameters) mehr über alle Installationsparameter erfahren.

## <a name="offline-installation"></a>Offlineinstallation

Während der Installation werden zwei Dateien mit folgendem Inhalt heruntergeladen: 
* Microsoft Azure IoT Edge-CAB-Datei, die IoT Edge-Sicherheitsdaemon (iotedged), Moby-Container-Engine und Moby-CLI enthält.
* Visual C++ Redistributable Package (VC-Runtime, MSI-Datei)

Sie können eine Datei oder beide vorher auf das Gerät herunterladen und das Verzeichnis dieser Dateien im Installationsskript angeben. Das Installationsprogramm überprüft dieses Verzeichnis dann zuerst und lädt nur Komponenten herunter, die nicht gefunden werden konnten. Wenn alle Dateien offline verfügbar sind, können Sie die Installation ohne Internetverbindung durchführen. Sie können mit diesem Feature auch eine bestimmte Version der Komponenten installieren.  

Die aktuellen IoT Edge-Installationsdateien und die früheren Versionen finden Sie auf der Seite für [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases).

Wenn Sie die Installation mit Offlinekomponenten durchführen möchten, müssen Sie den Parameter `-OfflineInstallationPath` als Teil des Deploy-IoTEdge-Befehls verwenden und den absoluten Pfad zum Dateiverzeichnis angeben. Beispiel:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Sie können auch den Pfadparameter der Offlineinstallation mit dem weiter unten in diesem Artikel vorgestellten Update-IoTEdge-Befehl verwenden. 

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Überprüfen Sie den Status des IoT Edge-Diensts. Er sollte als „Wird ausgeführt“ aufgelistet werden.  

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle der letzten fünf Minuten. Wenn Sie die Installation der IoT Edge-Runtime gerade beendet haben, wird möglicherweise eine Liste von Fehlern aus der Zeit zwischen den Ausführungen von **Deploy-IoTEdge** und **Initialize-IoTEdge** angezeigt. Diese Fehler werden erwartet, da der Dienst versucht, zu starten, bevor er konfiguriert ist. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf. Nach einer Neuinstallation sollte als einziges Modul **edgeAgent** ausgeführt werden. Nachdem Sie die [IoT Edge-Module bereitgestellt](how-to-deploy-modules-portal.md) haben, werden weitere angezeigt. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Verwalten von Modulcontainern

Der IoT Edge-Dienst erfordert eine Containerengine, die auf Ihrem Gerät ausgeführt wird. Wenn Sie ein Modul auf einem Gerät bereitstellen, verwendet die IoT Edge-Runtime die Containerengine, um das Containerimage per Pullvorgang aus einer Registrierung in der Cloud abzurufen. Der IoT Edge-Dienst ermöglicht es Ihnen, mit Ihren Modulen zu interagieren und Protokolle abzurufen, aber unter Umständen möchten Sie die Containerengine verwenden, um mit dem Container selbst zu interagieren. 

Weitere Informationen zu Modulkonzepten finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md). 

Wenn Sie Windows-Container auf Ihrem Windows IoT Edge-Gerät ausgeführt haben, dann umfasste die IoT Edge-Installation die Moby-Containerengine. Die Moby-Engine basierte auf denselben Standards wie Docker und wurde für die parallele Ausführung auf demselben Computer wie Docker Desktop entwickelt. Wenn Sie von der Moby-Engine verwaltete Container als Ziel festlegen möchten, müssen Sie diese Engine anstelle von Docker als Ziel auswählen. 

Verwenden Sie z. B. den folgenden Befehl, um alle Docker-Images aufzulisten:

```powershell
docker images
```

Ändern Sie denselben Befehl mit einem Zeiger auf die Moby-Engine, um alle Moby-Images aufzulisten: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Die Engine-URI ist in der Ausgabe des Installationsskripts aufgeführt, oder Sie finden sie im Abschnitt für die Einstellungen der Containerruntime der Datei „config.yaml“. 

![moby_runtime uri in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Weitere Informationen zu Befehlen, mit denen Sie mit Containern und Images interagieren können, die auf Ihrem Gerät ausgeführt werden, finden Sie unter [Docker-Befehlszeilenschnittstellen](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aktualisieren einer bestehenden Installation

Wenn Sie die IoT Edge-Runtime bereits auf einem Gerät installiert und mit einer Identität von IoT Hub bereitgestellt haben, können Sie die Runtime aktualisieren, ohne Ihre Geräteinformationen neu eingeben zu müssen. 

Weitere Informationen finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).

In diesem Beispiel wird eine Installation veranschaulicht, die auf eine vorhandene Konfigurationsdatei verweist und Windows-Container verwendet: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Bei der Aktualisierung von IoT Edge können Sie zusätzliche Parameter verwenden, um das Update zu ändern, einschließlich:
* Sie können direkten Datenverkehr über einen Proxyserver leiten, oder
* Sie können das Installationsprogramm auf ein Offlineverzeichnis verweisen. 
* Neustart ohne Aufforderung, falls erforderlich

Sie können das Containerimage eines IoT Edge-Agents nicht mit Skriptparametern deklarieren, da diese Informationen durch die vorherige Installation bereits in der Konfigurationsdatei festgelegt wurden. Wenn Sie das Containerimage eines Agents ändern möchten, müssen Sie dies in der config.yaml-Datei durchführen. 

Um weitere Informationen zu diesen Updateoptionen zu erhalten, verwenden Sie den Befehl `Get-Help Update-IoTEdge -full`, oder fahren Sie mit [Alle Installationsparameter](#all-installation-parameters) fort.

## <a name="uninstall-iot-edge"></a>Deinstallieren von IoT Edge

Wenn Sie IoT Edge von Ihrem Windows-Gerät entfernen möchten, sollten Sie folgenden Befehl in einem PowerShell-Fenster ausführen, das mit Administratorrechten geöffnet wurde. Dieser Befehl entfernt die IoT Edge-Runtime, die vorhandenen Konfigurationen und die Daten der Moby-Engine. 

```powershell
Uninstall-IoTEdge
```

Der Befehl Uninstall-IoTEdge funktioniert nicht unter Windows IoT Core. Um IoT Edge von Windows IoT Core-Geräten zu entfernen, müssen Sie Ihr Windows IoT Core-Image erneut bereitstellen. 

Um weitere Informationen zu den Deinstallationsoptionen zu erhalten, verwenden Sie den Befehl `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Alle Installationsparameter

In den vorherigen Abschnitten wurden gängige Installationsszenarios vorgestellt. Dabei wurden Beispiele angegeben, wie Sie Parameter verwenden können, um das Installationsskript anzupassen. Dieser Abschnitt enthält die Verweistabellen der allgemeinen Parameter, die zum Installieren, Aktualisieren oder Deinstallieren von IoT Edge verwendet werden. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Der Befehl Deploy-IoTEdge lädt den IoT Edge-Sicherheitsdaemon und die zugehörigen Abhängigkeiten herunter und stellt sie bereit. Der Bereitstellungsbefehl akzeptiert diese allgemeinen Parametern neben anderen. Mit dem Befehl `Get-Help Deploy-IoTEdge -full` können Sie die vollständige Liste anzeigen.  

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, ist „Windows“ der Standardwert.<br><br>Für Windows-Container verwendet IoT Edge die in der Installation enthaltene Moby-Container-Engine. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **Proxy** | Proxy-URL | Verwenden Sie diesen Parameter, wenn Ihr Gerät die Internetverbindung über einen Proxyserver herstellen muss. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Verzeichnispfad | Wenn dieser Parameter verwendet wird, überprüft das Installationsprogramm das aufgelistete Verzeichnis auf die IoT Edge-CAB- und die VC-Runtime-MSI-Dateien, die für die Installation erforderlich sind. Alle im Verzeichnis nicht gefundenen Dateien werden heruntergeladen. Wenn sich beide Dateien im Verzeichnis befinden, können Sie IoT Edge ohne Internetverbindung installieren. Sie können diesen Parameter auch verwenden, um eine bestimmte Version zu verwenden. |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Bereitstellungsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Der Initialize-IoTEdge-Befehl konfiguriert IoT Edge mit Ihrer Geräte-Verbindungszeichenfolge und Ihren Betriebsdetails. Ein Großteil der durch diesen Befehl generierten Informationen wird dann in der Datei „iotedge\config.yaml“ gespeichert. Der Initialisierungsbefehl akzeptiert diese allgemeinen Parametern neben anderen. Mit dem Befehl `Get-Help Initialize-IoTEdge -full` können Sie die vollständige Liste anzeigen. 

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **Manuell** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung der Standardwert.<br><br>Gibt an, dass Sie eine Geräteverbindungszeichenfolge angeben, um das Gerät manuell bereitzustellen. |
| **Dps** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung der Standardwert.<br><br>Gibt an, dass Sie eine Bereichs-ID von einem Gerätebereitstellungsdienst (Device Provisioning Service, DPS) und die Registrierungs-ID Ihres Geräts angeben, damit die Bereitstellung über einen Gerätebereitstellungsdienst durchgeführt wird.  |
| **DeviceConnectionString** | Eine Verbindungszeichenfolge, die von einem IoT Edge-Gerät stammt, das in einem IoT-Hub registriert ist (in einfachen Anführungszeichen). | Dieser Parameter ist für die manuelle Installation **erforderlich**. Wenn Sie keine Verbindungszeichenfolge in den Skriptparametern angeben, werden Sie während der Installation dazu aufgefordert. |
| **ScopeId** | Eine Bereichs-ID, die aus der Instanz des Gerätebereitstellungsdiensts stammt, die Ihrem IoT-Hub zugeordnet ist. | Dieser Parameter ist für die Installation über einen Gerätebereitstellungsdienst **erforderlich**. Wenn Sie keine Bereichs-ID in den Skriptparametern angeben, werden Sie während der Installation dazu aufgefordert. |
| **RegistrationId** | Eine Registrierungs-ID, die von Ihrem Gerät generiert wurde. | Dieser Parameter ist für die Installation über einen Gerätebereitstellungsdienst **erforderlich**. Wenn Sie keine Registrierungs-ID in den Skriptparametern angeben, werden Sie während der Installation dazu aufgefordert. |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, ist „Windows“ der Standardwert.<br><br>Für Windows-Container verwendet IoT Edge die in der Installation enthaltene Moby-Container-Engine. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | URI des IoT Edge-Agent-Images | Wenn Sie IoT Edge neu installieren, wird standardmäßig das neueste fortlaufende Tag für das IoT Edge-Agent-Image verwendet. Verwenden Sie diesen Parameter, um ein bestimmtes Tag für die Imageversion festzulegen, oder stellen Sie ein eigenes Agent-Image bereit. Weitere Informationen finden Sie unter [Grundlagen von IoT Edge-Tags](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Benutzername** | Benutzername der Containerregistrierung | Verwenden Sie diesen Parameter nur, wenn Sie den Parameter „-AgentImage“ auf einen Container in einer privaten Registrierung festlegen. Geben Sie einen Benutzernamen an, der auf die Registrierung zugreifen kann. |
| **Kennwort** | Sichere Kennwortzeichenfolge | Verwenden Sie diesen Parameter nur, wenn Sie den Parameter „-AgentImage“ auf einen Container in einer privaten Registrierung festlegen. Geben Sie das Kennwort an, mit dem auf die Registrierung zugegriffen werden kann. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, wird der Standardwert „Windows“ verwendet. Für Windows-Container wird eine Container-Engine im Rahmen der Installation installiert. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **Proxy** | Proxy-URL | Verwenden Sie diesen Parameter, wenn Ihr Gerät die Internetverbindung über einen Proxyserver herstellen muss. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Verzeichnispfad | Wenn dieser Parameter verwendet wird, überprüft das Installationsprogramm das aufgelistete Verzeichnis auf die IoT Edge-CAB- und die VC-Runtime-MSI-Dateien, die für die Installation erforderlich sind. Alle im Verzeichnis nicht gefundenen Dateien werden heruntergeladen. Wenn sich beide Dateien im Verzeichnis befinden, können Sie IoT Edge ohne Internetverbindung installieren. Sie können diesen Parameter auch verwenden, um eine bestimmte Version zu verwenden. |
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Bereitstellungsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **Force** | none | Dieses Flag erzwingt die Deinstallation für den Fall, dass der vorherige Versuch der Deinstallation nicht erfolgreich war. 
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Deinstallationsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein IoT Edge-Gerät für die installierte Runtime bereitgestellt haben, können Sie [IoT Edge-Module bereitstellen](how-to-deploy-modules-portal.md).

Wenn Probleme bei der Installation von IoT Edge auftreten, finden Sie auf der Seite für die [Problembehandlung](troubleshoot.md) weitere Informationen.

Weitere Informationen zum Aktualisieren einer vorhandenen Installation auf die aktuelle Version von IoT Edge finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).
