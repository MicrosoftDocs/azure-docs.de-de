---
title: Installieren von Azure IoT Edge | Microsoft-Dokumentation
description: Anweisungen zur Azure IoT Edge-Installation auf Windows- oder Linux-Geräten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185095"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Installieren oder Deinstallieren der Azure IoT Edge-Runtime

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen. Weitere Informationen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Das Setup eines IoT Edge-Geräts erfolgt in zwei Schritten. Der erste Schritt besteht im Installieren der Runtime und ihrer Abhängigkeiten und wird in diesem Artikel behandelt. Der zweite Schritt besteht darin, das Gerät mit seiner Identität in der Cloud zu verbinden und die Authentifizierung bei IoT Hub einzurichten. Diese Schritte werden in den nächsten Artikeln behandelt.

In diesem Artikel sind die Schritte zum Installieren der Azure IoT Edge-Runtime auf Linux- oder Windows-Geräten aufgeführt. Bei Windows-Geräten haben Sie zusätzlich die Möglichkeit, Linux-Container oder Windows-Container zu verwenden. Derzeit werden Windows-Container unter Windows für Produktionsszenarien empfohlen. Linux-Container unter Windows sind für Entwicklungs-und Testszenarien nützlich, insbesondere dann, wenn Sie auf einem Windows-PC entwickeln, um auf Linux-Geräten bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

Neueste Informationen dazu, welche Betriebssysteme zurzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems).

# <a name="linux"></a>[Linux](#tab/linux)

Sie müssen über ein x64-, ARM32- oder ARM64 Linux-Gerät verfügen. Microsoft bietet Installationspakete für die Betriebssysteme Ubuntu Server 16.04, Ubuntu Server 18.04 und Raspbian Stretch.

>[!NOTE]
>Die Unterstützung für ARM64-Geräte befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vorbereiten Ihres Geräts für den Zugriff auf die Microsoft-Installationspakete.

1. Installieren Sie die zum Betriebssystem Ihres Geräts passende Repositorykonfiguration.

   * **Ubuntu Server 16.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Kopieren Sie die generierte Liste in das Verzeichnis „sources.list.d“.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows-Version

IoT Edge erfordert in Kombination mit Windows-Containern die Windows-Version 1809/Build 17762. Dies ist der neueste [Windows Long Term Support-Build](/windows/release-information/). Für Entwicklungs- und Testszenarien funktioniert jede SKU (Pro, Enterprise, Server usw.), die das Containerfeature unterstützt. Achten Sie jedoch unbedingt darauf, die Liste der unterstützten Systeme zu überprüfen, bevor Sie in den Produktionsbetrieb gehen.

IoT Edge mit Linux-Containern kann unter jeder Windows-Version ausgeführt werden, die die [Anforderungen für Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) erfüllt.

### <a name="container-engine-requirements"></a>Anforderungen an die Containerengine

Azure IoT Edge baut auf einer [OCI-kompatiblen](https://www.opencontainers.org/) Containerengine auf. Vergewissern Sie sich, dass Ihr Gerät Container unterstützen kann.

Wenn Sie IoT Edge auf einer VM installieren, sollten Sie die geschachtelte Virtualisierung aktivieren und mindestens 2 GB Arbeitsspeicher zuordnen. Für Hyper-V verfügen virtuelle Computer der zweiten Generation über eine geschachtelte Virtualisierung, die standardmäßig aktiviert ist. Für VMware gibt es einen Umschalter zum Aktivieren des Features auf dem virtuellen Computer.

Wenn Sie IoT Edge auf einem IoT Core-Gerät installieren, können Sie in einer [Remote-PowerShell-Sitzung](/windows/iot-core/connect-your-device/powershell) mit dem folgenden Befehl überprüfen, ob Windows-Container auf Ihrem Gerät unterstützt werden:

```powershell
Get-Service vmcompute
```

---

Für Azure IoT Edge-Softwarepakete gelten die in jedem Paket (im Verzeichnis `usr/share/doc/{package-name}` oder `LICENSE`) enthaltenen Lizenzbedingungen. Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

## <a name="install-a-container-engine"></a>Installieren einer Containerengine

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. Für Produktionsszenarien empfehlen wir die Verwendung der Moby-basierten Engine. Die Moby-Engine ist die einzige Container-Engine, das bei Azure IoT Edge offiziell unterstützt wird. Docker CE/EE-Containerimages sind mit der Moby-Runtime kompatibel.

# <a name="linux"></a>[Linux](#tab/linux)

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

Wenn beim Installieren der Moby-Containerengine Fehler angezeigt werden, überprüfen Sie Ihren Linux-Kernel auf Moby-Kompatibilität. Einige Hersteller von eingebetteten Geräten stellen Geräteimages mit benutzerdefinierten Linux-Kernels bereit, denen die Funktionen für die Containerengine-Kompatibilität fehlen. Führen Sie den folgenden Befehl aus, der das von Moby bereitgestellte [check-config-Skript](https://github.com/moby/moby/blob/master/contrib/check-config.sh) verwendet, um die Kernelkonfiguration zu überprüfen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Überprüfen Sie in der Ausgabe des Skripts, ob alle Elemente unter `Generally Necessary` und `Network Drivers` aktiviert sind. Wenn Funktionen fehlen, aktivieren Sie diese, indem Sie Ihren Kernel aus der Quelle neu erstellen und die zugehörigen Module zum Einbinden in die entsprechende Konfigurationsdatei für den Kernel auswählen. Gleiches gilt, wenn Sie einen Kernelkonfigurationsgenerator wie `defconfig` oder `menuconfig` verwenden: Suchen und aktivieren Sie die entsprechenden Funktionen, und erstellen Sie dann den Kernel dementsprechend neu. Sobald Sie Ihren bearbeiteten Kernel bereitgestellt haben, führen Sie das check-config-Skript erneut aus, um zu überprüfen, ob alle erforderlichen Funktionen erfolgreich aktiviert wurden.

# <a name="windows"></a>[Windows](#tab/windows)

Verwenden Sie für Produktionsszenarien die Moby-basierte Engine, die im Installationsskript enthalten ist. Es sind keine zusätzlichen Schritte erforderlich, um die-Engine zu installieren.

Für IoT Edge in Kombination mit Linux-Containern müssen Sie Ihre eigene Containerruntime bereitstellen. Installieren Sie [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) auf Ihrem Gerät, und konfigurieren Sie es für die [Verwendung von Linux-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers), bevor Sie fortfahren.

---

## <a name="install-the-iot-edge-security-daemon"></a>Installieren des Daemons für IoT Edge-Sicherheit

Der Daemon für IoT Edge-Sicherheit dient zum Bereitstellen und Einhalten von Sicherheitsstandards auf dem IoT Edge-Gerät. Der Daemon wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version installieren müssen, beispielsweise eine Vorabversion, oder eine Offlineinstallation durchführen müssen, folgen Sie den Schritten im nächsten Abschnitt [Offlineinstallation oder Installation einer bestimmten Version](#offline-or-specific-version-installation).

# <a name="linux"></a>[Linux](#tab/linux)

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

Überprüfen Sie, welche IoT Edge-Versionen verfügbar sind.

   ```bash
   apt list -a iotedge
   ```

Wenn Sie die neueste Version des Sicherheitsdaemons installieren möchten, verwenden Sie den folgenden Befehl, mit dem zugleich auch die neueste Version des **libiothsm-std**-Pakets installiert wird:

   ```bash
   sudo apt-get install iotedge
   ```

Wenn Sie eine bestimmte Version des Sicherheitsdaemons installieren möchten, geben Sie die Version in der Ausgabe von „apt list“ an. Geben Sie zudem die gleiche Version für das **libiothsm-std**-Paket an, das andernfalls seine neueste Version installieren würde. Mit dem folgenden Befehl wird beispielsweise die neueste Version des Release 1.0.8 installiert:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Wenn die Version, die Sie installieren möchten, nicht in der Liste aufgeführt ist, folgen Sie den Schritten im nächsten Abschnitt [Offlineinstallation oder Installation einer bestimmten Version](#offline-or-specific-version-installation). In diesem Abschnitt wird gezeigt, wie Sie eine frühere Version des IoT Edge-Sicherheitsdaemons oder Release Candidate-Versionen als Ziel verwenden.

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>Bei IoT Core-Geräten empfehlen wir die Ausführung der Installationsbefehle über eine Remote-PowerShell-Sitzung. Weitere Informationen finden Sie unter [Using PowerShell for Windows IoT](/windows/iot-core/connect-your-device/powershell) (Verwenden von PowerShell für Windows IoT).

1. Führen Sie PowerShell als Administrator aus.

   Verwenden Sie eine AMD64-Sitzung von PowerShell, nicht PowerShell(x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Führen Sie den Befehl [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) aus, der die folgenden Aufgaben ausführt:

   * Überprüft, ob Ihr Windows-Computer mit einer unterstützten Version läuft.
   * Aktiviert das Containerfeature.
   * Lädt die Moby-Engine und die IoT Edge-Runtime herunter.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   Der Befehl `Deploy-IoTEdge` verwendet standardmäßig Windows-Container. Wenn Sie Linux-Container verwenden möchten, fügen Sie den Parameter `ContainerOs` hinzu:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. An diesem Punkt könnten IoT Core-Geräte möglicherweise automatisch neu starten. Windows 10- oder Windows Server-Geräte könnten Sie zum Neustart auffordern. Wenn ja, starten Sie Ihr Gerät jetzt neu.

Wenn Sie IoT Edge auf einem Gerät installieren, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Legen Sie im Installationsprogramm ein lokales Verzeichnis für die Offlineinstallation fest.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge unter Windows](reference-windows-scripts.md).

---

Nachdem nun die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie für den nächsten Schritt bereit, das Registrieren Ihres Geräts bei IoT Hub und das Einrichten des Geräts mit seinen Cloudidentitäts- und Authentifizierungsinformationen.

Wählen Sie den nächsten Artikel auf der Grundlage des gewünschten Authentifizierungstyps aus:

* [Authentifizierung mit symmetrischen Schlüsseln](how-to-manual-provision-symmetric-key.md) ermöglicht den schnelleren Einstieg.
* [Authentifizierung mit X.509-Zertifikaten](how-to-manual-provision-x509.md) ist in Produktionsszenarien sicherer.

## <a name="offline-or-specific-version-installation"></a>Offlineinstallation oder Installation einer bestimmten Version

Die in diesem Abschnitt aufgeführten Schritte sind für Szenarien vorgesehen, die von den Schritten der Standardinstallation nicht abgedeckt werden. Das können beispielsweise sein:

* Die Offlineinstallation von IoT Edge
* Die Installation einer Release Candidate-Version
* Unter Windows die Installation einer anderen als der aktuellsten Version

# <a name="linux"></a>[Linux](#tab/linux)

Mithilfe der in diesem Abschnitt beschriebenen Schritte können Sie eine bestimmte Version der Azure IoT Edge-Runtime installieren, die über `apt-get install` nicht zur Verfügung steht. Die Microsoft-Paketliste enthält nur eine begrenzte Reihe aktueller Versionen und deren Unterversionen. Diese Schritte sind also für diejenigen gedacht, die eine ältere Version oder eine Release Candidate-Version installieren möchten.

Mithilfe von curl-Befehlen können Sie die Komponentendateien direkt aus dem IoT Edge-GitHub-Repository als Ziel verwenden.

<!-- TODO: Offline installation? -->

1. Navigieren Sie zu den [Veröffentlichungen von Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases), und suchen Sie die Version, die Sie verwenden möchten.

2. Erweitern Sie den Abschnitt **Assets** für diese Version.

3. Jedes Release sollte neue Dateien für den IoT Edge-Sicherheits-Daemon und hsmlib enthalten. Verwenden Sie die folgenden Befehle, um diese Komponenten zu aktualisieren.

   1. Suchen Sie die **libiothsm-std**-Datei, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   2. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version von hsmlib zu installieren:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Suchen Sie die **iotedge**-Datei, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   4. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version des IoT Edge-Sicherheits-Daemons zu installieren.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

Während der Installation werden drei Dateien heruntergeladen:

* Ein PowerShell-Skript, das die Installationsanweisungen enthält
* Eine Microsoft Azure IoT Edge-CAB-Datei, die IoT Edge-Sicherheitsdaemon (iotedged), Moby-Container-Engine und Moby-CLI enthält
* Ein Installationsprogramm für das Visual C++ Redistributable Package (VC-Runtime)

Wenn Ihr Gerät während der Installation offline ist oder wenn Sie eine bestimmte Version von IoT Edge installieren möchten, können Sie diese Dateien im Voraus auf das Gerät herunterladen. Wenn Sie die Installation durchführen möchten, verweisen Sie im Installationsskript auf das Verzeichnis, in dem die heruntergeladenen Dateien gespeichert sind. Das Installationsprogramm überprüft dieses Verzeichnis zuerst und lädt dann nur Komponenten herunter, die nicht gefunden werden konnten. Wenn alle Dateien offline verfügbar sind, können Sie die Installation ohne Internetverbindung durchführen.

1. Die aktuellen IoT Edge-Installationsdateien und die früheren Versionen finden Sie auf der Seite für [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases).

2. Suchen Sie die Version, die Sie installieren möchten, und laden Sie die folgenden Dateien aus dem Abschnitt **Assets** der Versionshinweise auf Ihr IoT-Gerät herunter:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab aus Release 1.0.9 oder neuer oder Microsoft-Azure-IoTEdge.cab aus Release 1.0.8 und älter.

   Microsoft-Azure-IotEdge-arm32.cab ist ab Version 1.0.9 nur für Testzwecke verfügbar. IoT Edge wird auf Windows-ARM32-Geräten derzeit nicht unterstützt.

   Es ist wichtig, das PowerShell-Skript für dasselbe Release zu verwenden, zu dem die CAB-Datei gehört, weil die Unterstützung für Features sich in jedem Release ändert.

3. Wenn die von Ihnen heruntergeladene CAB-Datei ein Suffix der Architektur enthält, benennen Sie die Datei in **Microsoft-Azure-IoTEdge.cab** um.

4. Optional können Sie auch ein Installationsprogramm für Visual C++ Redistributable herunterladen. Das PowerShell-Skript verwendet beispielsweise diese Version: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Speichern Sie das Installationsprogramm auf Ihrem IoT-Gerät im selben Ordner, in dem sich die IoT Edge-Dateien befinden.

5. Für eine Installation mit Offlinekomponenten führen Sie eine [DOT-Quellentnahme](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) in der lokalen Kopie des PowerShell-Skripts durch. 

6. Führen Sie den Befehl [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) mit dem Parameter `-OfflineInstallationPath` aus. Geben Sie den absoluten Pfad zum Dateiverzeichnis an. Beispiel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Der Bereitstellungsbefehl verwendet alle Komponenten, die im angegebenen lokalen Dateiverzeichnis gefunden werden. Wenn die CAB-Datei oder der Visual C++-Installer fehlt, versucht der Befehl, diese Komponenten herunterzuladen.

---

Nachdem nun die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie für den nächsten Schritt bereit, das [Authentifizieren eines IoT Edge-Geräts bei IoT Hub](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Deinstallieren von IoT Edge

Verwenden Sie die folgenden Befehle, wenn Sie die IoT Edge-Installationen von Ihrem Gerät entfernen möchten.

# <a name="linux"></a>[Linux](#tab/linux)

Entfernen Sie die IoT Edge-Runtime.

```bash
sudo apt-get remove --purge iotedge
```

Wenn die IoT Edge-Runtime entfernt wird, werden alle von ihr erstellten Container angehalten. Auf Ihrem Gerät sind sie jedoch weiterhin vorhanden. Zeigen Sie alle Container an, um die verbliebenen zu sehen.

```bash
sudo docker ps -a
```

Löschen Sie die Container von Ihrem Gerät, einschließlich der zwei Laufzeitcontainer.

```bash
sudo docker rm -f <container name>
```

Entfernen Sie schließlich die Containerruntime von Ihrem Gerät.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Wenn Sie IoT Edge von Ihrem Windows-Gerät entfernen möchten, sollten Sie den Befehl [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) in einem PowerShell-Fenster ausführen, das mit Administratorrechten geöffnet wurde. Dieser Befehl entfernt die IoT Edge-Runtime, die vorhandenen Konfigurationen und die Daten der Moby-Engine.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Der Befehl `Uninstall-IoTEdge` funktioniert nicht unter Windows IoT Core. Zum Entfernen von IoT Edge müssen Sie Ihr Windows IoT Core-Image erneut bereitstellen.

Um weitere Informationen zu den Deinstallationsoptionen zu erhalten, verwenden Sie den Befehl `Get-Help Uninstall-IoTEdge -full`.

---

## <a name="next-steps"></a>Nächste Schritte

Nach dem Installieren der IoT Edge-Runtime konfigurieren Sie Ihr Gerät für Verbindungen mit IoT Hub. Die folgenden Artikel leiten Sie durch die Registrierung eines neuen Geräts in der Cloud und die anschließende Bereitstellung des Geräts mit seinen Identitäts- und Authentifizierungsinformationen.

Wählen Sie den nächsten Artikel auf der Grundlage des gewünschten Authentifizierungstyps aus:

* **Symmetrischer Schlüssel**: Sowohl IoT Hub als auch das IoT Edge-Gerät verfügen über ein Exemplar eines sicheren Schlüssels. Wenn das Gerät eine Verbindung mit IoT Hub herstellt, wird überprüft, ob die Schlüssel übereinstimmen. Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

  [Einrichten eines Azure IoT Edge-Geräts mit Authentifizierung mit symmetrischem Schlüssel](how-to-manual-provision-symmetric-key.md)

* **X.509, selbstsigniert**: Das IoT Edge-Gerät verfügt über X.509-Identitätszertifikate, und IoT Hub wird der Fingerabdruck der Zertifikate übergeben. Wenn das Gerät eine Verbindung mit IoT Hub herstellt, wird das Zertifikat mit seinem Fingerabdruck verglichen. Diese Authentifizierungsmethode ist sicherer und wird in Produktionsszenarien empfohlen.

  [Einrichten eines Azure IoT Edge-Geräts mit der Authentifizierung mit X.509-Zertifikat](how-to-manual-provision-x509.md)