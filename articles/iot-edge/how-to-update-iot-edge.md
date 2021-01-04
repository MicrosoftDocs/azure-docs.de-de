---
title: Aktualisieren der IoT Edge-Version auf Geräten – Azure IoT Edge | Microsoft-Dokumentation
description: Aktualisieren von IoT Edge-Geräten auf die neuesten Versionen des Sicherheitsdaemons und der IoT Edge-Runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 797b5f569f081065eb950f7c10bf6449002f733b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436979"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime

Wenn neue Versionen des IoT Edge-Diensts veröffentlicht werden, sollten Sie Ihre IoT Edge-Geräte aktualisieren, damit sie über die neuesten Funktionen und Verbesserungen der Sicherheit verfügen. Dieser Artikel enthält Informationen zum Aktualisieren Ihrer IoT Edge-Geräte, wenn eine neue Version verfügbar ist.

Zwei Komponenten eines IoT Edge-Geräts müssen bei der Umstellung auf eine neue Version aktualisiert werden. Die erste ist der Sicherheitsdaemon, der auf dem Gerät ausgeführt wird und beim Starten des Geräts die Runtimemodule startet. Der Sicherheitsdaemon kann zurzeit nur vom Gerät selbst aktualisiert werden. Die zweite Komponente ist die Runtime, die aus den IoT Edge-Hub- und den IoT Edge-Agent-Modulen besteht. Je nachdem, wie Ihre Bereitstellung strukturiert ist, kann die Runtime vom Gerät oder remote aktualisiert werden.

Informationen zur neuesten Version von Azure IoT Edge finden Sie unter [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) (Azure IoT Edge-Versionen).

## <a name="update-the-security-daemon"></a>Aktualisieren des Sicherheitsdaemons

Der IoT Edge-Sicherheitsdaemon ist eine native Komponente, die mithilfe des Paket-Managers auf dem IoT Edge-Gerät aktualisiert werden muss.

Überprüfen Sie die Version des auf Ihrem Geräts ausgeführten Sicherheitsdaemons mit dem Befehl `iotedge version`.

### <a name="linux-devices"></a>Linux-Geräte

Verwenden Sie auf Linux x64-Geräten „apt-get“ oder einen geeigneten Paket-Manager, um den Sicherheitsdaemon auf die neueste Version zu aktualisieren.

Verwenden Sie die neueste Repositorykonfiguration von Microsoft:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi-Betriebssystem Stretch:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopieren Sie die generierte Liste.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installieren Sie den öffentlichen GPG-Schlüssel von Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aktualisieren Sie „apt“.

   ```bash
   sudo apt-get update
   ```

Überprüfen Sie, welche IoT Edge-Versionen verfügbar sind.

   ```bash
   apt list -a iotedge
   ```

Wenn Sie ein Update auf die neueste Version des Sicherheitsdaemons durchführen möchten, verwenden Sie den folgenden Befehl, durch den auch **libiothsm-std** auf die neueste Version aktualisiert wird:

   ```bash
   sudo apt-get install iotedge
   ```

Wenn Sie ein Update auf eine bestimmte Version des Sicherheitsdaemons durchführen möchten, geben Sie die Version aus der Ausgabe von „apt list“ an. Bei jeder Aktualisierung von **iotedge** wird automatisch versucht, auch das Paket **libiothsm-std** auf die neueste Version zu aktualisieren, was ggf. einen Abhängigkeitskonflikt zur Folge hat. Wenn Sie kein Update auf die neueste Version durchführen, achten Sie darauf, beide Pakete auf die gleiche Version auszurichten. Mit dem folgenden Befehl wird beispielsweise eine bestimmte Version des Release 1.0.9 installiert:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Sollte die Version, die Sie installieren möchten, nicht über „apt-get“ verfügbar sein, können Sie mithilfe von cURL eine beliebige Version aus dem [Repository für IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases) als Ziel verwenden. Suchen Sie für die zu installierende Version nach den entsprechenden Dateien vom Typ **libiothsm-std** und **iotedge** für Ihr Gerät. Klicken Sie bei jeder Datei mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse. Verwenden Sie die Linkadresse, um die jeweiligen Versionen dieser Komponenten zu installieren:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-Geräte

Verwenden Sie auf Windows-Geräten das PowerShell-Skript, um den Sicherheitsdaemon zu aktualisieren. Das Skript lädt per Pull automatisch die neueste Version des Sicherheitsdaemons herunter.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Wenn Sie den Update-IoTEdge-Befehl ausführen, wird der Sicherheitsdaemon zusammen mit den beiden Runtimecontainerimages von Ihrem Gerät entfernt und aktualisiert. Die Datei „config.yaml“ verbleibt auf dem Gerät, ebenso wie Daten der Moby-Containerengine (sofern Sie Windows-Container verwenden). Durch die Beibehaltung der Konfigurationsinformationen müssen Sie während des Aktualisierungsvorgangs die Verbindungszeichenfolge oder die Device Provisioning Service-Informationen für Ihr Gerät nicht erneut angeben.

Wenn Sie ein Update auf eine bestimmte Version des Sicherheitsdaemons durchführen möchten, finden Sie die gewünschte Zielversion in [IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases). Laden Sie in dieser Version die Datei **Microsoft-Azure-IoTEdge.cab** herunter. Verwenden Sie anschließend den Parameter `-OfflineInstallationPath`, um auf den lokalen Speicherort der heruntergeladenen Datei zu verweisen. Beispiel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Der Parameter `-OfflineInstallationPath` sucht im angegebenen Verzeichnis nach der Datei **Microsoft-Azure-IoTEdge.cab**. Ab IoT Edge, Version 1.0.9-rc4, stehen zwei CAB-Dateien zur Verfügung – eine für AMD64-Geräte und eine für ARM32. Laden Sie die richtige Datei für Ihr Gerät herunter, und benennen Sie sie um, damit das Architektursuffix entfernt wird.

Weitere Informationen zu Update Optionen finden Sie unter dem Befehl `Get-Help Update-IoTEdge -full` oder in [PowerShell-Skript für IoT Edge unter Windows](reference-windows-scripts.md).

## <a name="update-the-runtime-containers"></a>Aktualisieren der Runtimecontainer

Die Art und Weise, auf die Sie die IoT Edge-Agent- und IoT Edge-Hubcontainer aktualisieren, hängt davon ab, ob Sie bei der Bereitstellung fortlaufende Versionstags (wie 1.0) oder spezifische Versionstags (wie 1.0.7) verwenden.

Überprüfen Sie die Version der aktuell auf Ihrem Gerät ausgeführten IoT Edge-Agent- und IoT Edge-Hubmodule mithilfe der Befehle `iotedge logs edgeAgent` und `iotedge logs edgeHub`.

  ![Suchen der Containerversion in Protokollen](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Grundlagen von IoT Edge-Tags

Die IoT Edge-Agent- und IoT Edge-Hubimages sind im Tag mit der IoT Edge-Version gekennzeichnet, der sie zugeordnet sind. Es gibt zwei Möglichkeiten zum Verwenden von Tags mit den Runtime-Images:

* **Fortlaufende Tags**: Verwenden Sie nur die ersten zwei Stellen der Versionsnummer, um zum neuesten Image zu gelangen, das mit diesen Stellen übereinstimmt. Beispielsweise wird, sobald eine neue Version veröffentlicht wird, 1.0 immer so aktualisiert, dass auf die neueste 1.0.x-Version verwiesen wird. Wenn die Containerruntime auf Ihrem IoT Edge-Gerät das Image erneut per Pull herunterlädt, werden die Laufzeitmodule auf die neueste Version aktualisiert. Dieses Vorgehen wird für Entwicklungszwecke vorgeschlagen. Bereitstellungen aus dem Azure-Portal weisen standardmäßig fortlaufende Tags auf.

* **Spezifische Tags**: Verwenden Sie alle drei Stellen der Versionsnummer, um die Imageversion explizit festzulegen. Beispielsweise ändert sich 1.0.7 nach dem ersten Release nicht. Sie können im Bereitstellungsmanifest eine neue Versionsnummer deklarieren, wenn Sie zur Aktualisierung bereit sind. Dieses Vorgehen wird für Produktionszwecke vorgeschlagen.

### <a name="update-a-rolling-tag-image"></a>Aktualisieren eines Images mit fortlaufendem Tag

Wenn Sie in Ihrer Bereitstellung fortlaufende Tags verwenden (Beispiel: mcr.microsoft.com/azureiotedge-hub:**1.0**), müssen Sie die Containerruntime auf Ihrem Gerät dazu zwingen, per Pull die neueste Version des Images zu laden.

Löschen Sie die lokale Version des Images von Ihrem IoT Edge-Gerät. Auf Windows-Computern werden bei der Deinstallation des Sicherheitsdaemons auch die Runtime-Images entfernt, sodass Sie diesen Schritt nicht erneut ausführen müssen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Möglicherweise müssen Sie das force-Flag `-f` verwenden, um die Images zu entfernen.

Der IoT Edge-Dienst lädt per Pull die neuesten Versionen der Runtimeimages herunter und startet sie automatisch wieder auf Ihrem Gerät.

### <a name="update-a-specific-tag-image"></a>Aktualisieren eines Images mit spezifischem Tag

Wenn Sie bei der Bereitstellung spezifische Tags verwenden (z. B. mcr.microsoft.com/azureiotedge-hub:**1.0.8**), müssen Sie lediglich das Tag in Ihrem Bereitstellungsmanifest aktualisieren und die Änderungen auf Ihr Gerät anwenden.

1. Wählen Sie Ihr IoT Edge-Gerät im IoT-Hub im Azure-Portal aus, und klicken Sie dann auf **Module festlegen**.

1. Klicken Sie im Abschnitt **IoT Edge-Module** auf **Laufzeiteinstellungen**.

   ![Laufzeiteinstellungen konfigurieren](./media/how-to-update-iot-edge/configure-runtime.png)

1. Ändern Sie in den **Laufzeiteinstellungen** den Wert **Image** für den **Edge-Hub** in die gewünschte Version. Wählen Sie noch nicht **Speichern** aus.

   ![Edge Hub-Imageversion aktualisieren](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Reduzieren Sie die **Edge Hub**-Einstellungen, oder scrollen Sie nach unten, und ändern Sie den Wert **Image** für den **Edge-Agent** in dieselbe gewünschte Version.

   ![Edge Hub-Agent-Version aktualisieren](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Wählen Sie **Speichern** aus.

1. Klicken Sie auf **Überprüfen + erstellen**, überprüfen Sie die Bereitstellung, und klicken Sie dann auf **Erstellen**.

## <a name="update-offline-or-to-a-specific-version"></a>Offineupdate oder Update auf eine bestimmte Version

Wenn Sie ein Gerät offline aktualisieren oder ein Update auf eine andere als die aktuelle IoT Edge-Version durchführen möchten, ist dies über den Parameter `-OfflineInstallationPath` möglich.

Zum Aktualisieren eines IoT Edge-Geräts werden zwei Komponenten verwendet:

* Ein PowerShell-Skript, das die Installationsanweisungen enthält
* Eine Microsoft Azure IoT Edge-CAB-Datei, die IoT Edge-Sicherheitsdaemon (iotedged), Moby-Container-Engine und Moby-CLI enthält

1. Die aktuellen IoT Edge-Installationsdateien und die früheren Versionen finden Sie auf der Seite für [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases).

2. Suchen Sie die Version, die Sie installieren möchten, und laden Sie die folgenden Dateien aus dem Abschnitt **Assets** der Versionshinweise auf Ihr IoT-Gerät herunter:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab aus Release 1.0.9 oder neuer oder Microsoft-Azure-IoTEdge.cab aus Release 1.0.8 und älter.

   Microsoft-Azure-IotEdge-arm32.cab ist ab Version 1.0.9 nur für Testzwecke verfügbar. IoT Edge wird auf Windows-ARM32-Geräten derzeit nicht unterstützt.

   Es ist wichtig, das PowerShell-Skript für dasselbe Release zu verwenden, zu dem die CAB-Datei gehört, weil die Unterstützung für Features sich in jedem Release ändert.

3. Wenn die von Ihnen heruntergeladene CAB-Datei ein Suffix der Architektur enthält, benennen Sie die Datei in **Microsoft-Azure-IoTEdge.cab** um.

4. Für ein Update mit Offlinekomponenten führen Sie eine [DOT-Quellentnahme](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) in der lokalen Kopie des PowerShell-Skripts durch. Verwenden Sie dann den `-OfflineInstallationPath`-Parameter als Teil des `Update-IoTEdge`-Befehls, und geben Sie den absoluten Pfad zum Dateiverzeichnis an. Beispiel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Update für eine Release Candidate-Version

Azure IoT Edge veröffentlicht regelmäßig neue Versionen des IoT Edge-Dienstes. Vor jedem stabilen Release gibt es einen oder mehrere Release Candidate-Versionen (RC). RC-Versionen enthalten alle geplanten Features für das Release, werden aber weiterhin getestet und geprüft. Wenn Sie eine neue Funktion vorab testen möchten, können Sie eine RC-Version installieren und uns Ihr Feedback über GitHub mitteilen.

Release Candidate-Versionen werden genauso nummeriert wie die Releases, am Ende wird jedoch **-rc** und eine inkrementelle Zahl angehängt. Sie finden die Release Candidates in derselben Liste der [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases) wie die stabilen Versionen. Suchen Sie beispielsweise nach **1.0.9-rc5** und **1.0.9-rc6**. Hierbei handelt es sich um zwei der Release Candidates, die vor **1.0.9** veröffentlicht wurden. Sie werden außerdem feststellen, dass die RC-Versionen die Bezeichnung **Vorabversion** aufweisen.

Der IoT Edge-Agent und die Hubmodule haben RC-Versionen, die mit derselben Konvention gekennzeichnet sind. Beispiel: **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6**.

Release Candidate-Versionen sind als Vorschauversionen nicht in der neusten Version enthalten, die reguläre Installationsprogramme aufrufen. Stattdessen müssen Sie die Objekte für die RC-Version manuell aufrufen, die Sie testen möchten. Zum größten Teil ist die Installation einer RC-Version oder die Aktualisierung darauf identisch mit der Ausrichtung auf eine andere spezifische Version von IoT Edge.

Informieren Sie sich anhand der Abschnitte in diesem Artikel, wie Sie ein IoT Edge-Gerät auf eine bestimmte Version des Sicherheitsdaemons oder der Laufzeitmodule aktualisieren.

Wenn Sie IoT Edge installieren, statt eine vorhandene Installation zu aktualisieren, führen Sie die Schritte in [Offlineinstallation oder Installation einer bestimmten Version](how-to-install-iot-edge.md#offline-or-specific-version-installation) aus.

## <a name="next-steps"></a>Nächste Schritte

Zeigen Sie die neuesten [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) (Azure IoT Edge-Versionen) an.

Bleiben Sie mit den neuesten Updates und Ankündigungen im [Internet of Things-Blog](https://azure.microsoft.com/blog/topics/internet-of-things/) auf dem Laufenden.