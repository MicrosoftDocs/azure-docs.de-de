---
title: Bereitstellen von Geräten mit einem virtuellen TPM auf Linux-VMs – Azure IoT Edge
description: Verwenden eines simulierten TPM auf einem virtuellen Linux-Computer, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fccd1bd6f808fad11946c6f0b0dff1f453b61d66
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430627"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts mit einem TPM unter Linux

In diesem Artikel erfahren Sie, wie Sie die automatische Bereitstellung für ein Linux-basiertes IoT Edge-Gerät mit einem Trusted Platform Module (TPM) testen. Azure IoT Edge-Geräte können per [Gerätebereitstellungsdienst](../iot-dps/index.yml) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie fortfahren.

Aufgaben:

1. Erstellen Sie einen virtuellen Linux-Computer (VM) in Hyper-V mit einem simulierten Trusted Platform Module (TPM) für die Hardwaresicherheit.
1. Erstellen Sie eine neue Instanz für den IoT Hub Device Provisioning-Dienst (DPS).
1. Erstellen Sie eine individuelle Registrierung für das Gerät.
1. Installieren Sie IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub.

> [!TIP]
> In diesem Artikel wird beschrieben, wie Sie die DPS-Bereitstellung mithilfe eines TPM-Simulators testen. Viele Informationen darin gelten aber auch für physische TPM-Hardware, wie z.B. das [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), ein Azure Certified for IoT-Gerät.
>
> Wenn Sie ein physisches Gerät verwenden, können Sie mit dem Abschnitt [Abrufen von Bereitstellungsinformationen von einem physischen Gerät](#retrieve-provisioning-information-from-a-physical-device) in diesem Artikel fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Windows-Entwicklungscomputer mit [aktiviertem Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In diesem Artikel wird das Betriebssystem Windows 10 verwendet, auf dem ein Ubuntu Server-VM ausgeführt wird.
* Aktiver IoT Hub.

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird. Es kann nur zum Erstellen von einzelnen Registrierungen (keinen Gruppenregistrierungen) verwendet werden.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Erstellen eines virtuellen Linux-Computers mit virtuellem TPM

In diesem Abschnitt erstellen Sie einen neuen virtuellen Linux-Computer unter Hyper-V. Dieser virtuelle Computer wird mit einem simulierten TPM konfiguriert, um die Funktionsweise der automatischen Bereitstellung mit IoT Edge zu testen.

### <a name="create-a-virtual-switch"></a>Erstellen eines virtuellen Switches

Mit einem virtuellen Switch können Sie Ihren virtuellen Computer mit einem physischen Netzwerk verbinden.

1. Öffnen Sie Hyper-V-Manager auf Ihrem Windows-Computer.

2. Klicken Sie im Menü **Aktionen** auf **Manager für virtuelle Switches**.

3. Wählen Sie einen **externen** virtuellen Switch, und klicken Sie anschließend auf **Virtuellen Switch erstellen**.

4. Geben Sie Ihrem neuen virtuellen Switch einen Namen, z.B. **EdgeSwitch**. Stellen Sie sicher, dass der Verbindungstyp auf **Externes Netzwerk**, festgelegt ist, und klicken Sie anschließend auf **OK**.

5. Ein Popup warnt Sie, dass die Netzwerkkonnektivität möglicherweise unterbrochen wird. Wählen Sie **Yes** (Ja), um fortzufahren.

Wenn beim Erstellen des neuen Switches Fehler angezeigt werden, stellen Sie sicher, dass keine anderen Switches den Ethernet-Adapter verwenden und dass keine anderen Switches den gleichen Namen verwenden.

### <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Laden Sie eine für Ihren virtuellen Computer zu verwendende Festplatten-Imagedatei herunter, und speichern Sie sie lokal. Beispiel: [Ubuntu-Server 18.04](http://releases.ubuntu.com/18.04/). Informationen zu unterstützten Betriebssystemen für IoT Edge-Geräte finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md).

2. Navigieren Sie im Hyper-V-Manager im Menü **Aktionen** zu **Aktion** > **Neu** > **Virtueller Computer**.

3. Geben Sie im **Assistenten für neue virtuelle Computer** die folgenden speziellen Konfigurationen ein:

   1. **Generation angeben**: Wählen Sie **Generation 2** aus. Für virtuelle Computer der zweiten Generation ist die geschachtelte Virtualisierung aktiviert, die zum Ausführen von IoT Edge auf einem virtuellen Computer erforderlich ist.
   2. **Konfigurieren der Netzwerkeinstellungen**: Legen Sie den Wert der **Verbindung** auf den virtuellen Switch fest, den Sie im vorherigen Abschnitt erstellt haben.
   3. **Installationsoptionen**: Wählen Sie **Betriebssystem von einer startbaren Imagedatei installieren** aus, und navigieren Sie zu der Datenträger-Imagedatei, die Sie lokal gespeichert haben.

4. Wählen Sie im Assistenten die Option **Fertig stellen**, um den virtuellen Computer zu erstellen.

Die Erstellung der neuen VM kann einige Minuten dauern.

### <a name="enable-virtual-tpm"></a>Virtuelles TPM aktivieren

Öffnen Sie nach der Erstellung Ihrer VM die zugehörigen Einstellungen, um das virtuelle Trusted Platform Module (TPM) zu aktivieren, mit dem Sie das Gerät automatisch bereitstellen lassen können.

1. Klicken Sie im Hyper-V-Manager mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Einstellungen** aus.

2. Navigieren zu **Sicherheit**.

3. Deaktivieren Sie **Sicheren Start aktivieren**.

4. Aktivieren Sie **Trusted Platform Module aktivieren**.

5. Klicken Sie auf **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starten des virtuellen Computers und Erfassen von TPM-Daten

Erstellen Sie auf dem virtuellen Computer ein Tool, mit dem Sie die **Registrierungs-ID** und den **Endorsement Key** für das Gerät abrufen können.

1. Starten Sie im Hyper-V-Manager Ihren virtuellen Computer, und stellen Sie eine Verbindung damit her.

1. Befolgen Sie die Anweisungen auf dem virtuellen Computer, um die Installation abzuschließen und den Computer zu starten.

1. Melden Sie sich am virtuellen Computer an, und führen Sie dann die Schritte unter [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um das Azure IoT-Geräte-SDK für C zu installieren und zu erstellen.

   >[!TIP]
   >Im Rahmen dieses Artikels werden auf dem virtuellen Computer Kopier- und Einfügevorgänge ausgeführt, was über die Hyper-V-Manager-Verbindungsanwendung nicht ganz einfach ist. Es empfiehlt sich gegebenenfalls, einmalig über den Hyper-V-Manager eine Verbindung mit dem virtuellen Computer herzustellen, um dessen IP-Adresse abzurufen. Führen Sie `sudo apt install net-tools` und dann `hostname -I` aus. Anschließend können Sie die IP-Adresse verwenden, um eine Verbindung per SSH herzustellen: `ssh <username>@<ipaddress>`.

1. Führen Sie die folgenden Befehle aus, um das SDK-Tool zum Abrufen Ihrer Gerätebereitstellungsinformationen vom TPM zu erstellen.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** des Geräts angezeigt. Kopieren Sie diese Werte. Sie werden später zur Erstellung einer individuellen Registrierung für Ihr Gerät benötigt.

Wenn Sie über Ihre Registrierungs-ID und Ihren Endorsement Key verfügen, können Sie mit dem Abschnitt [Einrichten des IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service) fortfahren.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Abrufen von Bereitstellungsinformationen von einem physischen Gerät

Sollten Sie anstelle eines virtuellen Computers ein physisches IoT Edge-Gerät verwenden, erstellen Sie ein Tool, mit dem Sie die Bereitstellungsinformationen des Geräts abrufen können.

1. Führen Sie die Schritte in [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um das Azure IoT-Geräte-SDK für C zu installieren und zu erstellen.

1. Führen Sie die folgenden Befehle zum Erstellen des SDK-Tools aus, das Ihre Gerätebereitstellungsinformationen vom TPM-Gerät abruft.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieren Sie die Werte für **Registrierungs-ID** und **Endorsement Key**. Sie können diese Werte verwenden, um eine individuelle Registrierung für Ihr Gerät im DPS zu erstellen.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Einrichten des IoT Hub Device Provisioning Service

Erstellen Sie eine neue Instanz des IoT Hub Device Provisioning Service in Azure, und verknüpfen Sie sie mit Ihrem IoT-Hub. Befolgen Sie die Anweisungen unter [Einrichten des IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md).

Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Rufen Sie die Bereitstellungsinformationen von Ihrem virtuellen Computer ab, und verwenden Sie diese, um eine individuelle Registrierung im Device Provisioning-Dienst zu erstellen.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

> [!TIP]
> Über die Azure-Befehlszeilenschnittstelle können Sie eine [Registrierung](/cli/azure/ext/azure-iot/iot/dps/enrollment) erstellen und mithilfe des Flags **edge-enabled** angeben, dass es sich bei einem Gerät um ein IoT Edge-Gerät handelt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

2. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

3. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Klicken Sie unter **Mechanismus** auf die Option **TPM**.

   2. Geben Sie den **Endorsement Key** und die **Registrierungs-ID** an, den bzw. die Sie von Ihrem virtuellen Computer kopiert haben.

      > [!TIP]
      > Wenn Sie ein physisches TPM-Gerät verwenden, müssen Sie den **Endorsement Key** ermitteln, der für jeden TPM-Chip eindeutig ist und von dem ihm zugeordneten TPM-Chiphersteller abgerufen wird. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

   3. Stellen Sie nach Wunsch eine ID für Ihr Gerät bereit. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   4. Wählen Sie **True**, um anzugeben, dass dieser virtuelle Computer ein IoT Edge-Gerät ist.

   5. Wählen Sie die verknüpfte IoT Hub-Instanz aus, mit der Sie Ihr Gerät verbinden möchten, oder wählen Sie **Link to new IoT Hub** (Mit neuer IoT Hub-Instanz verknüpfen) aus. Sie können mehrere Hubs auswählen. Das Gerät wird dann je nach gewählter Zuweisungsrichtlinie einem dieser Hubs zugewiesen.

   6. Fügen Sie nach Wunsche einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md).

   7. Wählen Sie **Speichern** aus.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Die Komponenten werden in Containern ausgeführt, und Sie können weitere Container auf dem Gerät bereitstellen, um Code im Edge-Bereich auszuführen. Installieren Sie die IoT Edge-Runtime auf Ihrem virtuellen Computer.

Führen Sie die Schritte unter [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) aus, und kehren Sie dann zu diesem Artikel zurück, um das Gerät bereitzustellen.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

1. Ermitteln Sie Ihren DPS-**ID-Bereich** und die **Registrierungs-ID** des Geräts, die in den vorhergehenden Abschnitten erfasst wurden.

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen der Datei. Heben Sie die Auskommentierung der Zeilen für die TPM-Bereitstellung auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   Der Zeile `provisioning:` sollte kein Leerzeichen vorangestellt und geschachtelte Elemente sollten um zwei Leerzeichen eingerückt sein.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

   Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst versuchen, mit DPS bereitzustellen, und – falls das fehlschlägt – auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch erneut bereitstellt, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein erneutes Bereitstellungsereignis erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Aktualisieren Sie die Werte `scope_id` und `registration_id` mit Ihren DPS- und Geräteinformationen.

## <a name="give-iot-edge-access-to-the-tpm"></a>Gewähren von IoT Edge-Zugriff für das TPM

Für die automatische Gerätebereitstellung benötigt die IoT Edge-Runtime Zugriff auf das TPM.

Sie können TPM-Zugriff auf die IoT Edge-Runtime gewähren, indem Sie die Systemeinstellungen überschreiben, sodass der Dienst `iotedge` über Stammrechte verfügt. Wenn Sie die Dienstberechtigungen nicht erhöhen möchten, können Sie auch die folgenden Schritte ausführen, um den TPM-Zugriff manuell bereitzustellen.

1. Suchen Sie den Pfad zum TPM-Hardwaremodul auf Ihrem Gerät, und speichern Sie ihn als lokale Variable.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf tpm0 erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieren Sie die folgenden Informationen, in die Regeldatei.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Speichern und beenden Sie die Datei.

6. Lösen Sie das Udev-System aus, um die neue Regel ausgewertet.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Vergewissern Sie sich, dass die Regel erfolgreich angewendet wurde.

   ```bash
   ls -l /dev/tpm0
   ```

   Die erfolgreiche Ausgabe sieht wie folgt aus:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um Udev zu aktualisieren.

## <a name="restart-the-iot-edge-runtime"></a>Neustart der IoT Edge-Runtime

Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

Überprüfen Sie, ob die IoT Edge-Runtime ausgeführt wird.

   ```bash
   sudo systemctl status iotedge
   ```

Wenn Bereitstellungsfehler angezeigt werden, sind die Konfigurationsänderungen möglicherweise noch nicht wirksam. Starten Sie den IoT Edge-Daemon erneut.

   ```bash
   sudo systemctl daemon-reload
   ```

Oder starten Sie Ihren virtuellen Computer erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zum IoT Hub wechseln und sehen, dass Ihr neues Gerät automatisch bereitgestellt wurde. Es ist jetzt bereit für die Ausführung von IoT Edge-Modulen.

Überprüfen Sie den Status des IoT Daemons.

```cmd/sh
systemctl status iotedge
```

Untersuchen Sie die Daemonprotokolle.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

## <a name="next-steps"></a>Nächste Schritte

Der DPS-Registrierungsprozess ermöglicht das Festlegen der Geräte-ID und der Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).