---
title: 'Tutorial: Konfigurieren eines IoT Edge-Geräts: Machine Learning in Azure IoT Edge'
description: In diesem Tutorial konfigurieren Sie einen virtuellen Azure-Computer mit Linux als Azure IoT Edge-Gerät, das als transparentes Gateway fungiert.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: bc62590b9517b2c6d16fdf2637990b845248d2ec
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932352"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: Konfigurieren eines IoT Edge-Geräts

In diesem Artikel wird ein virtueller Azure-Computer unter Linux als IoT Edge-Gerät konfiguriert, das als transparentes Gateway fungiert. Die Konfiguration eines transparenten Gateways ermöglicht es Geräten, eine Verbindung mit Azure IoT Hub über das Gateway herzustellen, ohne zu wissen, dass das Gateway vorhanden ist. Auch Benutzer, die mit den Geräten in Azure IoT Hub interagieren, bemerken das zwischengeschaltete Gatewaygerät nicht. Am Ende fügen wir unserem System eine Edgeanalyse hinzu, indem wir dem transparenten Gateway IoT Edge-Module hinzufügen.

Die Schritte in diesem Artikel werden normalerweise von einem Cloudentwickler ausgeführt.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen von Zertifikaten, um Ihrem Gatewaygerät das Herstellen einer sicheren Verbindung mit den nachgeschalteten Geräten zu ermöglichen
> * Erstellen eines IoT Edge-Geräts
> * Erstellen eines virtuellen Azure-Computers zum Simulieren des IoT Edge-Geräts

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Azure Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="create-certificates"></a>Erstellen von Zertifikaten

Damit ein Gerät als Gateway fungieren kann, muss es in der Lage sein, sich sicher mit nachgeschalteten Geräten zu verbinden. Mit Azure IoT Edge können Sie mithilfe der Public Key-Infrastruktur sichere Verbindungen zwischen Geräten einrichten. In diesem Fall lassen Sie zu, dass ein nachgeschaltetes IoT-Gerät eine Verbindung mit einem IoT Edge-Gerät herstellt, das als transparentes Gateway fungiert. Um eine angemessene Sicherheit zu gewährleisten, sollte das nachgeschaltete Gerät die Identität des IoT Edge-Geräts bestätigen. Weitere Informationen dazu, wie IoT Edge-Geräte Zertifikate verwenden, finden Sie unter [Details zur Verwendung von Azure IoT Edge-Zertifikaten](iot-edge-certs.md).

In diesem Abschnitt erstellen wir die selbstsignierten Zertifikate mit einem Docker-Image, das wir anschließend erstellen und ausführen. Wir haben uns bei diesem Schritt für ein Docker-Image entschieden, weil sich hierdurch der Aufwand zum Erstellen der Zertifikate auf dem Windows-Entwicklungscomputer deutlich verringert. Informationen zu den mit dem Docker-Image automatisierten Komponenten finden Sie unter [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md).

1. Melden Sie sich bei Ihrem virtuellen Entwicklungscomputer an.

2. Erstellen Sie einen neuen Ordner mit dem folgenden Pfad und Namen: `c:\edgeCertificates`.

3. Starten Sie bei Bedarf **Docker für Windows** über das Windows-Startmenü.

4. Öffnen Sie Visual Studio Code.

5. Wählen Sie **Datei** > **Ordner öffnen...** und dann **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Klicken Sie im Explorer-Bereich mit der rechten Maustaste auf **dockerfile**, und wählen Sie **Image erstellen** aus.

7. Übernehmen Sie im Dialogfeld den Standardwert für Imagename und Tag: **createcertificates: latest**.

    ![Erstellen von Zertifikaten in Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Warten Sie, bis der Buildvorgang abgeschlossen wird.

    > [!NOTE]
    > Unter Umständen wird eine Warnung zu einem fehlenden öffentlichen Schlüssel angezeigt. Sie können diese Warnung ignorieren. Sie sehen auch eine Sicherheitswarnung mit dem Hinweis, dass Sie die Berechtigungen auf Ihrem Image überprüfen bzw. zurücksetzen sollten. Sie können diesen Hinweis für dieses Image ignorieren.

9. Führen Sie im Visual Studio Code-Terminalfenster den Container „createcertificates“ aus.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker fordert Sie auf, Zugriff auf das Laufwerk **c:\\** zu gewähren. Wählen Sie **Teilen**.

11. Geben Sie Ihre Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden.

12. Überprüfen Sie nach Abschluss der Containerausführung, ob die folgenden Dateien unter **c:\\edgeCertificates** vorhanden sind:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Hochladen von Zertifikaten in Azure Key Vault

Wir laden die Zertifikate in Azure Key Vault hoch, um unsere Zertifikate sicher zu speichern und den Zugriff darauf von mehreren Geräten aus zu ermöglichen. In der obigen Liste ist zu sehen, dass zwei Arten von Zertifikatdateien vorhanden sind: PFX und PEM. Wir behandeln die PFX-Dateien als Key Vault-Zertifikate, die in Key Vault hochgeladen werden sollen. Die PEM-Dateien sind Nur-Text-Dateien und werden als Key Vault-Geheimnisse behandelt. Wir verwenden die Key Vault-Instanz, die dem Azure Machine Learning-Arbeitsbereich zugeordnet ist, den wir durch Ausführen der [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks) erstellt haben.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Machine Learning-Arbeitsbereich.

2. Suchen Sie auf der Übersichtsseite des Azure Machine Learning-Arbeitsbereichs nach dem Namen der **Key Vault-Instanz**.

    ![Kopieren des Key Vault-Namens](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Laden Sie auf Ihrem Entwicklungscomputer die Zertifikate in Key Vault hoch. Ersetzen Sie **\<subscriptionId\>** und **\<keyvaultname\>** durch Ihre Ressourceninformationen.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Melden Sie sich an Azure an, wenn Sie dazu aufgefordert werden.

5. Das Skript wird einige Minuten lang ausgeführt, und in der Ausgabe sind die neuen Key Vault-Einträge aufgelistet.

    ![Key Vault-Skriptausgabe](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge-Gerät erstellen

Für die Herstellung einer Verbindung für ein Azure IoT Edge-Gerät mit einem IoT-Hub erstellen wir zuerst im Hub eine Identität für das Gerät. Wir verwenden die Verbindungszeichenfolge aus der Geräteidentität in der Cloud und nutzen sie zum Konfigurieren der Runtime auf unserem IoT Edge-Gerät. Nachdem ein konfiguriertes Gerät eine Verbindung mit dem Hub hergestellt hat, können wir Module bereitstellen und Nachrichten senden. Wir können auch die Konfiguration des physischen IoT Edge-Geräts ändern, indem wir die entsprechende Geräteidentität im IoT-Hub ändern.

In diesem Tutorial erstellen wir die neue Geräteidentität mit Visual Studio Code. Sie können diese Schritte auch über das Azure-Portal oder die Azure CLI ausführen.

1. Öffnen Sie Visual Studio Code auf Ihrem Entwicklungscomputer.

2. Erweitern Sie in der Explorer-Ansicht von Visual Studio Code den Bereich **Azure IoT Hub**.

3. Klicken Sie auf die Auslassungspunkte, und wählen Sie **IoT Edge-Gerät erstellen**.

4. Geben Sie dem Gerät einen Namen. Der Einfachheit halber verwenden wir den Namen **aaTurbofanEdgeDevice**, da dieser am Anfang der Geräteliste angezeigt wird.

5. Das neue Gerät wird in der Liste mit den Geräten angezeigt.

    ![Anzeigen des neuen Geräts „aaTurbofanEdgeDevice“ im Explorer von VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Bereitstellen eines virtuellen Azure-Computers

Wir verwenden das Image [Azure IoT Edge unter Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) vom Azure Marketplace, um unser IoT Edge-Gerät für dieses Tutorial zu erstellen. Mit dem Image „Azure IoT Edge unter Ubuntu“ werden beim Starten die aktuelle Azure IoT Edge-Runtime und die zugehörigen Abhängigkeiten installiert. Wir stellen die VM mit dem PowerShell-Skript `Create-EdgeVM.ps1`, der Resource Manager-Vorlage `IoTEdgeVMTemplate.json` und dem Shellskript `install packages.sh` bereit.

### <a name="enable-programmatic-deployment"></a>Aktivieren der programmgesteuerten Bereitstellung

Um das Image aus dem Marketplace für eine skriptgesteuerte Bereitstellung verwenden zu können, müssen wir die programmgesteuerte Bereitstellung für das Image aktivieren.

1. Melden Sie sich beim Azure-Portal an.

1. Wählen Sie **Alle Dienste** aus.

1. Geben Sie in der Suchleiste **Marketplace** ein, und wählen Sie den entsprechenden Eintrag aus.

1. Geben Sie auf der Marketplace-Suchleiste **Azure IoT Edge unter Ubuntu** ein, und wählen Sie den entsprechenden Eintrag aus.

1. Wählen Sie den Link **Erste Schritte** aus, um die programmgesteuerte Bereitstellung durchzuführen.

1. Wählen Sie die Schaltfläche **Aktivieren** und dann **Speichern**.

    ![Aktivieren der programmgesteuerten Bereitstellung für die VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Eine Erfolgsmeldung wird angezeigt.

### <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Führen Sie als Nächstes das Skript zum Erstellen des virtuellen Computers für Ihr IoT Edge-Gerät aus.

1. Öffnen Sie ein PowerShell-Fenster, und navigieren Sie zum Verzeichnis **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Führen Sie das Skript aus, um den virtuellen Computer zu erstellen.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Geben Sie einen Wert für jeden Parameter an, wenn Sie dazu aufgefordert werden. Wir empfehlen Ihnen, für das Abonnement, die Ressourcengruppe und den Standort die gleichen Angaben wie für alle anderen Ressourcen im Rahmen dieses Tutorials anzugeben.

    * **Azure-Abonnement-ID**: Über das Azure-Portal ermittelbar.
    * **Ressourcengruppenname**: Einprägsamer Name für die Gruppierung der Ressourcen für dieses Tutorial.
    * **Standort**: Azure-Standort, an dem der virtuelle Computer erstellt wird. Beispiel: „westus2“ oder „northeurope“. Weitere Standorte finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: Der Name für das Administratorkonto, das Sie zum Anmelden am virtuellen Computer verwenden.
    * **AdminPassword**: Das Kennwort, das für den AdminUsername auf dem virtuellen Computer festgelegt wird.

4. Damit die VM mit dem Skript eingerichtet werden kann, müssen Sie sich bei Azure mit den Anmeldeinformationen anmelden, die dem von Ihnen genutzten Azure-Abonnement zugeordnet sind.

5. Das Skript bestätigt die Informationen für die Erstellung Ihres virtuellen Computers. Drücken Sie **y** oder die **EINGABETASTE**, um fortzufahren.

6. Die Ausführung des Skripts dauert mehrere Minuten, und es werden die folgenden Schritte ausgeführt:

    * Erstellen der Ressourcengruppe, falls sie noch nicht vorhanden ist
    * Erstellen des virtuellen Computers
    * Hinzufügen von NSG-Ausnahmen für die VM für Ports 22 (SSH), 5671 (AMQP), 5672 (AMPQ) und 443 (TLS)
    * Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli-apt).

7. Das Skript gibt die SSH-Verbindungszeichenfolge für die Herstellung der Verbindung mit der VM aus. Kopieren Sie die Verbindungszeichenfolge für den nächsten Schritt.

    ![Kopieren der SSH-Verbindungszeichenfolge für die VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Herstellen einer Verbindung mit Ihrem IoT Edge-Gerät

In den nächsten Abschnitten wird der von uns erstellte virtuelle Azure-Computer konfiguriert. Der erste Schritt ist die Herstellung der Verbindung mit dem virtuellen Computer.

1. Öffnen Sie eine Eingabeaufforderung, und fügen Sie die SSH-Verbindungszeichenfolge ein, die Sie aus der Ausgabe des Skripts kopiert haben. Geben Sie Ihre eigenen Informationen für Benutzername, Suffix und Region gemäß den Werten ein, die Sie im vorherigen Abschnitt für das PowerShell-Skript angegeben haben.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Wählen Sie **Ja**, und drücken Sie die **EINGABETASTE**, wenn Sie zum Überprüfen der Echtheit des Hosts aufgefordert werden.

3. Geben Sie bei entsprechender Aufforderung Ihr Kennwort an.

4. In Ubuntu wird eine Begrüßungsnachricht eingeblendet, und dann sollte in etwa `<username>@<machinename>:~$` angezeigt werden.

## <a name="download-key-vault-certificates"></a>Herunterladen von Key Vault-Zertifikaten

Weiter oben in diesem Artikel haben wir Zertifikate in Key Vault hochgeladen, um sie für unser IoT Edge-Gerät und für unser Blattgerät verfügbar zu machen. Das Blattgerät ist ein nachgeschaltetes Gerät, das das IoT Edge-Gerät als Gateway für die Kommunikation mit IoT Hub verwendet.

Wir gehen zu einem späteren Zeitpunkt des Tutorials auf das Blattgerät ein. In diesem Abschnitt laden Sie die Zertifikate auf das IoT Edge-Gerät herunter.

1. Melden Sie sich über die SSH-Sitzung auf dem virtuellen Linux-Computer mit der Azure CLI an Azure an.

    ```azurecli
    az login
    ```

1. Sie werden aufgefordert, <https://microsoft.com/devicelogin> in einem Browser zu öffnen und einen eindeutigen Code anzugeben. Sie können diese Schritte auf Ihrem lokalen Computer ausführen. Schließen Sie das Browserfenster, wenn Sie die Authentifizierung abgeschlossen haben.

1. Wenn die Authentifizierung erfolgreich war, wird der virtuelle Linux-Computer angemeldet, und Ihre Azure-Abonnements werden aufgelistet.

1. Legen Sie das Azure-Abonnement fest, das Sie für Azure CLI-Befehle verwenden möchten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Erstellen Sie auf der VM ein Verzeichnis für die Zertifikate.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Laden Sie die Zertifikate herunter, die Sie im Schlüsseltresor gespeichert haben: „new-edge-device-full-chain.cert.pem“, „new-edge-device.key.pem“ und „azure-iot-test-only.root.ca.cert.pem“.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualisieren der Konfiguration für das IoT Edge-Gerät

Die IoT Edge-Runtime verwendet die Datei `/etc/iotedge/config.yaml`, um die Konfiguration zu speichern. Wir müssen in dieser Datei drei Arten von Informationen aktualisieren:

* **Geräte-Verbindungszeichenfolge**: Die Verbindungszeichenfolge der Identität dieses Geräts in IoT Hub.
* **Zertifikate:** Die Zertifikate, die für die Verbindungsherstellung mit nachgeschalteten Geräten verwendet werden.
* **Hostname:** Der vollqualifizierte Domänenname (FQDN) des IoT Edge-Geräts der VM.

Das Image *Azure IoT Edge unter Ubuntu*, das wir zum Erstellen des virtuellen IoT Edge-Computers verwendet haben, verfügt über ein Shellskript, mit dem die Datei „config.yaml“ mit der Verbindungszeichenfolge aktualisiert wird.

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf das IoT Edge-Gerät, und wählen Sie anschließend die Option **Copy Device Connection String** (Verbindungszeichenfolge des Geräts kopieren).

    ![Kopieren der Verbindungszeichenfolge aus Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Führen Sie in Ihrer SSH-Sitzung den Befehl zum Aktualisieren der Datei „config.yaml“ mit Ihrer Geräteverbindungszeichenfolge aus.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Als Nächstes aktualisieren wir die Zertifikate und den Hostnamen, indem wir die Datei „config.yaml“ direkt bearbeiten.

1. Öffnen Sie die Datei „config.yaml“.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Aktualisieren Sie den Abschnitt „certificates“ der Datei „config.yaml“, indem Sie das vorangestellte Zeichen `#` entfernen und den Pfad festlegen, damit die Datei wie im folgenden Beispiel aussieht:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Stellen Sie sicher, dass der Zeile **certificates:** kein Leerzeichen vorangestellt ist und dass die geschachtelten Zertifikate jeweils um zwei Leerstellen eingerückt sind.

    Wenn Sie in Nano mit der rechten Maustaste klicken, wird der Inhalt Ihrer Zwischenablage an der aktuellen Cursorposition eingefügt. Verwenden Sie zum Ersetzen der Zeichenfolge die Pfeiltasten auf der Tastatur, um zur Zeichenfolge zu navigieren, die ersetzt werden soll. Löschen Sie die Zeichenfolge, und klicken Sie dann mit der rechten Maustaste, um das Einfügen aus dem Puffer durchzuführen.

3. Navigieren Sie im Azure-Portal zu Ihrem virtuellen Computer. Kopieren Sie den DNS-Namen (FQDN des Computers) aus dem Abschnitt **Übersicht**.

4. Fügen Sie den FQDN in den Bereich „hostname“ der Datei „config.yml“ ein. Stellen Sie sicher, dass der Name nur Kleinbuchstaben enthält.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Speichern und schließen Sie die Datei (`Ctrl + X`, `Y`, `Enter`).

6. Starten Sie den Daemon „iotedge“ neu.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Überprüfen Sie den Status des IoT Edge-Daemon. (Geben Sie nach dem Befehl „:q“ ein, um den Vorgang zu beenden.)

    ```bash
    systemctl status iotedge
    ```

8. Falls im Statusbereich Fehler angezeigt werden (farbiger Text mit dem Präfix „\[ERROR\]“), sollten Sie die Daemonprotokolle auf ausführliche Informationen zum Fehler untersuchen.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieses Tutorial ist Teil einer Reihe, in der jeder Artikel auf den Schritten aufbaut, die jeweils im vorherigen Artikel ausgeführt wurden. Warten Sie mit dem Bereinigen von Ressourcen, bis Sie das letzte Tutorial abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

Wir haben die Konfiguration einer Azure-VM als transparentes Azure IoT Edge-Gateway durchgeführt. Zunächst haben wir Testzertifikate generiert, die wir in Azure Key Vault hochgeladen haben. Anschließend haben Sie ein Skript und eine Resource Manager-Vorlage verwendet, um den virtuellen Computer mit dem Image „Ubuntu Server 16.04 LTS + Azure IoT Edge-Runtime“ aus dem Azure Marketplace bereitzustellen. Als der virtuelle Computer bereit war, haben wir eine SSH-Verbindung hergestellt, uns bei Azure angemeldet und Zertifikate aus Key Vault heruntergeladen. Wir haben die Datei „config.yaml“ aktualisiert, um verschiedene Aspekte der IoT Edge-Runtimekonfiguration zu aktualisieren.

Fahren Sie mit dem nächsten Artikel fort, um IoT Edge-Module zu erstellen.

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen von benutzerdefinierten IoT Edge-Modulen](tutorial-machine-learning-edge-06-custom-modules.md)
