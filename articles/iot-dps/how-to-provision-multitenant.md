---
title: Bereitstellen von Geräten für Mehrinstanzenfähigkeit in Azure IoT Hub Device Provisioning Service
description: Bereitstellen von Geräten für Mehrinstanzenfähigkeit mit Ihrer Device Provisioning Service-Instanz (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d0c132d1aa7a37dc8e7620352bb7b9a078d79a09
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571605"
---
# <a name="how-to-provision-for-multitenancy"></a>Bereitstellen für Mehrinstanzenfähigkeit 

In diesem Artikel wird gezeigt, wie Sie mehrere symmetrische Schlüsselgeräte für eine Gruppe von IoT Hubs mithilfe einer [Zuordnungsrichtlinie](concepts-service.md#allocation-policy) auf sichere Weise bereitstellen. Bei den vom Bereitstellungsdienst definierten Zuordnungsrichtlinien werden verschiedene Zuordnungsszenarien unterstützt. Zwei häufige Szenarien sind:

* **Geolocation oder Geolatenz**: Wenn ein Gerät von einem Standort an einen anderen verlagert wird, lässt sich die Netzwerklatenz verbessern, indem das Gerät jeweils auf dem IoT Hub bereitgestellt wird, der dem jeweiligen Standort am nächsten liegt. In diesem Szenario wird eine Gruppe mit regionsübergreifenden IoT Hubs für Registrierungen ausgewählt. Für diese Registrierungen wird die Zuordnungsrichtlinie **Niedrigste Latenz** ausgewählt. Diese Richtlinie bewirkt, dass der Device Provisioning Service die Gerätelatenz auswertet und aus der Gruppe mit den IoT Hubs den nächstgelegenen IoT Hub ermittelt. 

* **Mehrinstanzenfähigkeit**: Geräte, die in einer IoT-Lösung verwendet werden, müssen unter Umständen einem bestimmten IoT Hub oder einer Gruppe mit IoT Hubs zugewiesen werden. Für die Lösung kann es erforderlich sein, dass alle Geräte für einen bestimmten Mandanten mit einer bestimmten Gruppe von IoT Hubs kommunizieren. In einigen Fällen kann auch ein Mandant der Besitzer von IoT Hubs sein und erzwingen, dass Geräte seinen IoT Hubs zugewiesen werden.

Diese beiden Szenarien werden häufig kombiniert. Eine mehrinstanzenfähige IoT-Lösung weist beispielsweise Mandantengeräte zu, indem eine Gruppe mit IoT Hubs verwendet wird, die auf mehrere Regionen verteilt sind. Diese Mandantengeräte können dem IoT Hub in dieser Gruppe zugewiesen werden, der basierend auf dem geografischen Standort über die niedrigste Latenz verfügt.

In diesem Artikel wird ein Beispiel für ein simuliertes Gerät aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet, um zu veranschaulichen, wie Geräte in einem Szenario mit Mehrinstanzenfähigkeit regionsübergreifend bereitgestellt werden. In diesem Artikel führen Sie die folgenden Schritte aus:

* Verwenden der Azure CLI, um zwei regionale IoT Hubs (**West US** (USA, Westen) und **East US** (USA, Osten)) zu erstellen
* Erstellen einer mehrinstanzenfähigen Registrierung
* Verwenden der Azure CLI, um zwei regionale Linux-VMs zu erstellen, die als Geräte in denselben Regionen (**West US** (USA, Westen) und **East US** (USA, Osten)) fungieren
* Einrichten der Entwicklungsumgebung für das Azure IoT C SDK auf beiden Linux-VMs
* Simulieren der Geräte, um darzustellen, dass sie für denselben Mandanten in der am nächsten gelegenen Region bereitgestellt wurden


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

- Vollständige Bearbeitung der Schnellstartanleitung [Einrichten des IoT Hub Device Provisioning Service über das Azure-Portal](./quick-setup-auto-provision.md).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Erstellen von zwei regionalen IoT Hubs

In diesem Abschnitt verwenden Sie Azure Cloud Shell, um zwei neue regionale IoT Hubs in den Regionen **West US** (USA, Westen) und **East US** (USA, Osten) für einen Mandanten zu erstellen.


1. Erstellen Sie in Azure Cloud Shell mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

    Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *contoso-us-resource-group* in der Region *eastus* erstellt. Es wird empfohlen, diese Gruppe für alle in diesem Artikel erstellten Ressourcen zu verwenden. Dies erleichtert das Bereinigen nach Abschluss der Schritte in diesem Artikel.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Verwenden Sie Azure Cloud Shell, um mit dem Befehl [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) in der Region **eastus** einen IoT Hub zu erstellen. Der IoT Hub wird *contoso-us-resource-group* hinzugefügt.

    Im folgenden Beispiel wird in der Region *eastus* ein IoT Hub mit dem Namen *contoso-east-hub* erstellt. Sie müssen anstelle von **contoso-east-hub** Ihren eigenen eindeutigen Hubnamen verwenden.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

3. Verwenden Sie Azure Cloud Shell, um mit dem Befehl [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) in der Region **westus** einen IoT Hub zu erstellen. Dieser IoT Hub wird ebenfalls *contoso-us-resource-group* hinzugefügt.

    Im folgenden Beispiel wird am Standort *westus* eine IoT Hub-Instanz mit dem Namen *contoso-west-hub* erstellt. Sie müssen anstelle von **contoso-west-hub** Ihren eigenen eindeutigen Hubnamen verwenden.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.



## <a name="create-the-multitenant-enrollment"></a>Erstellen der mehrinstanzenfähigen Registrierung

In diesem Abschnitt erstellen Sie eine neue Registrierungsgruppe für die Mandantengeräte.  

Der Einfachheit halber wird in diesem Artikel ein [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md) für die Registrierung verwendet. Für eine Lösung mit höherer Sicherheit empfiehlt sich die Verwendung eines [X.509-Zertifikatnachweises](concepts-x509-attestation.md) mit einer Kette von Vertrauensstellungen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Device Provisioning Service-Instanz.

2. Wählen Sie die Registerkarte **Registrierungen verwalten** aus, und klicken Sie dann oben auf der Seite auf die Schaltfläche **Registrierungsgruppe hinzufügen**. 

3. Geben Sie unter **Registrierungsgruppe hinzufügen** die folgenden Informationen ein, und klicken Sie auf die Schaltfläche **Speichern**.

    **Gruppenname**: Geben Sie **contoso-us-devices** ein.

    **Nachweistyp**: Wählen Sie **Symmetrischer Schlüssel**.

    **Schlüssel automatisch generieren**: Dieses Kontrollkästchen sollte bereits aktiviert sein.

    **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen**: Wählen Sie die Option **Niedrigste Latenz**.

    ![Hinzufügen einer mehrinstanzenfähigen Registrierungsgruppe für den Nachweis des symmetrischen Schlüssels](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Klicken Sie unter **Registrierungsgruppe hinzufügen** auf **Neuen IoT Hub verknüpfen**, um die beiden regionalen Hubs zu verknüpfen.

    **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, sollten Sie das Abonnement auswählen, in dem Sie die IoT Hubs für die Regionen erstellt haben.

    **IoT Hub**: Wählen Sie einen der regionalen Hubs aus, die Sie erstellt haben.

    **Zugriffsrichtlinie**: Wählen Sie **iothubowner** aus.

    ![Verknüpfen der regionalen IoT Hubs mit dem Provisioning Service](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Nachdem die beiden regionalen IoT Hubs verknüpft wurden, müssen Sie sie für die Registrierungsgruppe auswählen und auf **Speichern** klicken, um die regionale IoT Hub-Gruppe für die Registrierung zu erstellen.

    ![Erstellen der regionalen Hubgruppe für die Registrierung](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Öffnen Sie die Registrierungsgruppe nach dem Speichern erneut, und notieren Sie sich den **Primärschlüssel**. Sie müssen die Registrierung speichern, damit die Schlüssel generiert werden. Mit diesem Schlüssel werden später eindeutige Geräteschlüssel für beide simulierten Geräte generiert.


## <a name="create-regional-linux-vms"></a>Erstellen von regionalen Linux-VMs

In diesem Abschnitt erstellen Sie zwei regionale virtuelle Linux-Computer (VMs). Auf diesen VMs wird ein Beispiel für eine Gerätesimulation aus jeder Region ausgeführt, um die Gerätebereitstellung für Mandantengeräte aus beiden Regionen zu veranschaulichen.

Zur Vereinfachung der Bereinigung werden diese VMs derselben Ressourcengruppe hinzugefügt, in der die erstellten IoT Hubs enthalten sind: *contoso-us-resource-group*. Die VMs werden allerdings in separaten Regionen (**USA, Westen** und **USA, Osten**) ausgeführt.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um eine VM in der Region **USA, Osten** zu erstellen, nachdem im Befehl die folgenden Parameteränderungen vorgenommen wurden:

    **--name**: Geben Sie einen eindeutigen Namen für den virtuellen Computer in der Region **USA, Osten** ein. 

    **--admin-username**: Verwenden Sie Ihren eigenen Administratorbenutzernamen.

    **--admin-password**: Verwenden Sie Ihr eigenes Administratorkennwort.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Die Ausführung dieses Befehls dauert einige Minuten. Notieren Sie sich nach Abschluss des Befehls den Wert von **publicIpAddress** für Ihre VM in der Region „USA, Osten“.

1. Führen Sie in Azure Cloud Shell den Befehl aus, um eine VM in der Region **USA, Westen** zu erstellen, nachdem im Befehl die folgenden Parameteränderungen vorgenommen wurden:

    **--name**: Geben Sie einen eindeutigen Namen für den virtuellen Computer in der Region **USA, Westen** ein. 

    **--admin-username**: Verwenden Sie Ihren eigenen Administratorbenutzernamen.

    **--admin-password**: Verwenden Sie Ihr eigenes Administratorkennwort.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Die Ausführung dieses Befehls dauert einige Minuten. Notieren Sie sich nach Abschluss des Befehls den Wert von **publicIpAddress** für Ihre VM in der Region „USA, Westen“.

1. Öffnen Sie zwei Befehlszeilenshells. Stellen Sie in jeder Shell eine Verbindung mit einem der regionalen virtuellen Computer her, indem Sie SSH verwenden. 

    Übergeben Sie Ihren Administratorbenutzernamen und die öffentliche IP-Adresse, die Sie sich für den virtuellen Computer notiert haben, als Parameter an SSH. Geben Sie das Administratorkennwort ein, wenn Sie dazu aufgefordert werden.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Vorbereiten der Azure IoT C SDK-Entwicklungsumgebung

In diesem Abschnitt klonen Sie das Azure IoT C SDK auf jedem virtuellen Computer. Das SDK enthält ein Beispiel, mit dem die Gerätebereitstellung eines Mandanten in jeder Region simuliert wird.

1. Installieren Sie für jede VM **CMake**, **g++**, **gcc** und [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) mit den folgenden Befehlen:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Suchen Sie den Tagnamen für das [aktuelle Release](https://github.com/Azure/azure-iot-sdk-c/releases/latest) des SDK.

1. Klonen Sie das [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) auf beiden VMs.  Verwenden Sie das im vorherigen Schritt gefundene Tag als Wert für den Parameter `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

1. Erstellen Sie für beide VMs im Repository einen neuen Ordner **cmake**, und wechseln Sie in diesen Ordner.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Führen Sie für beide VMs den folgenden Befehl aus, um eine spezifische SDK-Version für Ihre Entwicklungsclientplattform zu erstellen. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Ableiten eindeutiger Geräteschlüssel

Bei Verwendung des Nachweises des symmetrischen Schlüssels mit Gruppenregistrierungen verwenden Sie die Registrierungsgruppenschlüssel nicht direkt. Stattdessen erstellen Sie einen eindeutigen abgeleiteten Schlüssel für jedes Gerät. Hierbei helfen Ihnen die Informationen zu [Gruppenregistrierungen](concepts-symmetric-key-attestation.md#group-enrollments) mit symmetrischen Schlüsseln weiter.

Verwenden Sie für die Generierung des Geräteschlüssels den Gruppenhauptschlüssel, um einen [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Wert für die eindeutige Registrierungs-ID für das Gerät zu berechnen und das Ergebnis in das Base64-Format zu konvertieren.

Fügen Sie Ihren Gruppenhauptschlüssel nicht in Ihren Gerätecode ein.

Verwenden Sie das Bash Shell-Beispiel, um mit **openssl** einen abgeleiteten Geräteschlüssel für jedes Gerät zu erstellen.

- Ersetzen Sie den Wert für **KEY** durch den **Primärschlüssel**, den Sie sich zuvor für Ihre Registrierung notiert haben.

- Ersetzen Sie den Wert für **REG_ID** durch Ihre eigene eindeutige Registrierungs-ID für jedes Gerät. Verwenden Sie alphanumerische Kleinbuchstaben und Bindestriche („-“), um beide IDs zu definieren.

Beispiel für die Generierung eines Geräteschlüssels für *contoso-simdevice-east*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Beispiel für die Generierung eines Geräteschlüssels für *contoso-simdevice-west*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Für die Mandantengeräte werden jeweils der abgeleitete Geräteschlüssel und die eindeutige Registrierungs-ID verwendet, um den Nachweis symmetrischer Schlüssel mit den Registrierungsgruppen während der Bereitstellung auf den Mandanten-IoT Hubs durchzuführen.




## <a name="simulate-the-devices-from-each-region"></a>Simulieren der Geräte aus den einzelnen Regionen


In diesem Abschnitt aktualisieren Sie ein Bereitstellungsbeispiel im Azure IoT C SDK für beide regionalen VMs. 

Der Beispielcode simuliert eine Gerätestartsequenz, über die die Bereitstellungsanforderung an die Instanz des Device Provisioning-Diensts gesendet wird. Die Startsequenz bewirkt, dass das Gerät erkannt und dem IoT Hub zugewiesen wird, der bezogen auf die Latenz am nächsten liegt.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Device Provisioning Service, und notieren Sie sich den Wert unter **_ID-Bereich_**.

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Öffnen Sie **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** auf beiden VMs für die Bearbeitung.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten gezeigt für die Nachweismethode für Registrierungsgruppen auf `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` festgelegt ist. 

    Speichern Sie Ihre Änderungen an den Dateien auf beiden VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Suchen Sie auf beiden VMs den Aufruf zum `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, der auskommentiert ist.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Heben Sie die Auskommentierung für die Funktionsaufrufe auf, und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die eindeutige Registrierungs-ID und abgeleiteten Geräteschlüssel für jedes Gerät. Die nachfolgenden Schlüssel dienen nur als Beispiel. Verwenden Sie die zuvor erstellten Schlüssel.

    USA, Osten:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA, Westen:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Speichern Sie die Dateien.

1. Navigieren Sie auf beiden VMs zum unten angegebenen Beispielordner, und erstellen Sie das Beispiel.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Führen Sie nach der erfolgreichen Erstellung **prov\_dev\_client\_sample.exe** auf beiden VMs aus, um ein Mandantengerät aus jeder Region zu simulieren. Beachten Sie, dass jedes Gerät dem Mandanten-IoT Hub zugeordnet ist, der den Regionen des simulierten Geräts am nächsten liegt.

    Führen Sie die Simulation aus:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Beispielausgabe der VM „USA, Osten“:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Beispielausgabe der VM „USA, Westen“:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen weiterverwenden möchten, können Sie sie beibehalten. Wenn Sie nicht beabsichtigen, die Ressourcen weiterzuverwenden, führen Sie die folgenden Schritte aus, um alle in diesem Artikel erstellten Ressourcen zu löschen und unnötige Kosten zu vermeiden.

Bei dieser Vorgehensweise wird davon ausgegangen, dass Sie alle in diesem Artikel verwendeten Ressourcen gemäß den Anweisungen in der Ressourcengruppe **contoso-us-resource-group** erstellt haben.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Wenn Sie die IoT Hub-Ressource in einer bereits vorhandenen Ressourcengruppe erstellt haben, die Ressourcen enthält, die Sie behalten möchten, löschen Sie nicht die Ressourcengruppe, sondern nur die IoT Hub-Ressource.
>

Löschen Sie die Ressourcengruppen wie folgt nach Namen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

2. Geben Sie im Textfeld **Nach Name filtern...** den Namen der Ressourcengruppe ein, die Ihre Ressourcen enthält: **contoso-us-resource-group**. 

3. Klicken Sie in der Ergebnisliste rechts neben Ihrer Ressourcengruppe auf **...** und dann auf **Ressourcengruppe löschen**.

4. Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie zur Bestätigung erneut den Namen Ihrer Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**. Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur erneuten Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md). 
- Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md). 











