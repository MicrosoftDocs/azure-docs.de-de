---
title: Verwenden benutzerdefinierter Zuweisungsrichtlinien mit dem Azure IoT Hub Device Provisioning Service | Microsoft-Dokumentation
description: Verwenden benutzerdefinierter Zuweisungsrichtlinien mit dem Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 03d39ed01907a2ad61e089946673b96b8a2cc83e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65916990"
---
# <a name="how-to-use-custom-allocation-policies"></a>Verwenden benutzerdefinierter Zuweisungsrichtlinien


Mit einer Zuweisungsrichtlinie können Sie genauer steuern, wie Geräte einem IoT-Hub zugewiesen werden. Dazu werden Geräte mithilfe von benutzerdefiniertem Code in einer [Azure-Funktion](../azure-functions/functions-overview.md) einem IoT-Hub zugewiesen. Device Provisioning Service ruft Ihren Azure-Funktionscode auf und stellt alle relevanten Informationen zum Gerät und zur Registrierung bereit. Der Funktionscode wird ausgeführt und gibt die IoT Hub-Informationen für die Bereitstellung des Geräts zurück.

Sie definieren eigene benutzerdefinierte Zuweisungsrichtlinien, wenn die vom Device Provisioning Service bereitgestellten Richtlinien nicht die Anforderungen Ihres Szenarios erfüllen.

Beispielsweise möchten Sie eventuell das Zertifikat überprüfen, das während der Bereitstellung von einem Gerät verwendet wird, und das Gerät basierend auf einer Zertifikateigenschaft einem IoT-Hub zuweisen. Möglicherweise sind in einer Datenbank Informationen für die Geräte gespeichert, und Sie müssen die Datenbank abfragen, um zu bestimmen, welchem IOT-Hub ein Gerät zugewiesen werden soll.


In diesem Artikel wird eine benutzerdefinierte Zuweisungsrichtlinie mithilfe einer in C# geschriebenen Azure-Funktion veranschaulicht. Es werden zwei neue IoT-Hubs werden erstellt, die die *Contoso-Abteilung „Toaster“* und die *Contoso-Abteilung „Wärmepumpen“* darstellen. Für Geräte, deren Bereitstellung angefordert wird, ist eine Registrierungs-ID mit einem der folgenden Suffixe erforderlich, damit die Bereitstellung möglich ist:

- **-contoso-tstrsd-007**: Contoso-Abteilung „Toaster“
- **-contoso-hpsd-088**: Contoso-Abteilung „Wärmepumpen“

Die Geräte werden basierend auf einem dieser erforderlichen Suffixe der Registrierungs-ID bereitgestellt. Diese Geräte werden mit einem Bereitstellungsbeispiel simuliert, das im [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) enthalten ist. 

In diesem Artikel führen Sie die folgenden Schritte aus:

* Verwenden der Azure CLI zum Erstellen der beiden IoT-Hubs für die Contoso-Abteilungen (**Contoso-Abteilung „Toaster“** und **Contoso-Abteilung „Wärmepumpen“** )
* Erstellen einer neuen Gruppenregistrierung mithilfe einer Azure-Funktion für die benutzerdefinierte Zuweisungsrichtlinie
* Erstellen von Geräteschlüsseln für die beiden Gerätesimulationen
* Einrichten der Entwicklungsumgebung für das Azure IoT C SDK
* Simulieren der Geräte, um zu ermitteln, ob sie gemäß dem Beispielcode der benutzerdefinierten Zuweisungsrichtlinie bereitgestellt sind


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Vollständige Bearbeitung der Schnellstartanleitung [Einrichten des IoT Hub Device Provisioning Service über das Azure-Portal](./quick-setup-auto-provision.md).
* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 oder höher mit aktivierter Workload [Desktopentwicklung mit C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Erstellen von zwei IoT-Hubs für die Abteilungen

In diesem Abschnitt verwenden Sie Azure Cloud Shell zum Erstellen von zwei neuen IoT-Hubs, die die **Contoso-Abteilung „Toaster“** und die **Contoso-Abteilung „Wärmepumpen“** darstellen.

1. Erstellen Sie in Azure Cloud Shell mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

    Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *contoso-us-resource-group* in der Region *eastus* erstellt. Es wird empfohlen, diese Gruppe für alle in diesem Artikel erstellten Ressourcen zu verwenden. Dies erleichtert das Bereinigen, nachdem Sie die Schritte in diesem Artikel ausgeführt haben.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Erstellen Sie in Azure Cloud Shell mit dem Befehl [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) den IoT-Hub für die **Contoso-Abteilung „Toaster“** . Der IoT-Hub wird zu *contoso-us-resource-group* hinzugefügt.

    Im folgenden Beispiel wird in der Region *eastus* ein IoT-Hub mit dem Namen *contoso-toasters-hub-1098* erstellt. Sie müssen einen eigenen eindeutigen Hubnamen verwenden. Verwenden Sie im Hubnamen ein eigenes Suffix anstelle von **1098**. Im Beispielcode für die benutzerdefinierte Zuweisungsrichtlinie muss der Hubname `-toasters-` enthalten.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

3. Erstellen Sie in Azure Cloud Shell mit dem Befehl [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) den IoT-Hub für die **Contoso-Abteilung „Wärmepumpen“** . Dieser IoT-Hub wird ebenfalls zu *contoso-us-resource-group* hinzugefügt.

    Im folgenden Beispiel wird am Standort *eastus* ein IoT-Hub mit dem Namen *contoso-heatpumps-hub-1098* erstellt. Sie müssen einen eigenen eindeutigen Hubnamen verwenden. Verwenden Sie im Hubnamen ein eigenes Suffix anstelle von **1098**. Im Beispielcode für die benutzerdefinierte Zuweisungsrichtlinie muss der Hubname `-heatpumps-` enthalten.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Die Ausführung dieses Befehls kann ebenfalls einige Minuten in Anspruch nehmen.




## <a name="create-the-enrollment"></a>Erstellen der Registrierung

In diesem Abschnitt erstellen Sie eine neue Registrierungsgruppe, von der die benutzerdefinierte Zuweisungsrichtlinie verwendet wird. Der Einfachheit halber wird in diesem Artikel ein [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md) für die Registrierung verwendet. Für eine Lösung mit höherer Sicherheit empfiehlt sich die Verwendung eines [X.509-Zertifikatnachweises](concepts-security.md#x509-certificates) mit einer Kette von Vertrauensstellungen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Device Provisioning-Dienstinstanz.

2. Wählen Sie die Registerkarte **Registrierungen verwalten** aus, und klicken Sie dann oben auf der Seite auf die Schaltfläche **Registrierungsgruppe hinzufügen**. 

3. Geben Sie unter **Registrierungsgruppe hinzufügen** die folgenden Informationen ein, und klicken Sie auf die Schaltfläche **Speichern**.

    **Gruppenname**: Geben Sie **contoso-custom-allocated-devices** ein.

    **Nachweistyp**: Wählen Sie **Symmetrischer Schlüssel**.

    **Schlüssel automatisch generieren**: Dieses Kontrollkästchen sollte bereits aktiviert sein.

    **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen**: Wählen Sie **Benutzerdefiniert (Azure-Funktion verwenden)** .

    ![Hinzufügen einer benutzerdefinierten Zuweisungsregistrierungsgruppe für den Nachweis des symmetrischen Schlüssels](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. Klicken Sie unter **Registrierungsgruppe hinzufügen** auf **Neuen IoT-Hub verknüpfen**, um die beiden neuen IoT-Hubs für die Abteilungen zu verknüpfen. 

    Sie müssen diesen Schritt für beide IoT Hubs ausführen.

    **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, sollten Sie das Abonnement auswählen, in dem Sie die IoT Hubs für die Abteilungen erstellt haben.

    **IoT-Hub**: Wählen Sie einen der von Ihnen erstellten Abteilungshubs aus.

    **Zugriffsrichtlinie**: Wählen Sie **iothubowner**.

    ![Verknüpfen der Abteilungs-IoT-Hubs mit dem Bereitstellungsdienst](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Nachdem die beiden IoT-Hubs für die Abteilungen verknüpft wurden, müssen Sie sie unter **Registrierungsgruppe hinzufügen** wie unten gezeigt als IoT-Hub-Gruppe für die Registrierungsgruppe auswählen:

    ![Erstellen der Abteilungshubgruppe für die Registrierung](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Scrollen Sie in **Registrierungsgruppe hinzufügen** nach unten zum Abschnitt **Azure-Funktions-App auswählen**, und klicken Sie auf **Neue Funktions-App erstellen**.

7. Geben Sie in **Funktions-App** auf der Seite „Erstellen“ die folgenden Einstellungen für die neue Funktion ein, und klicken Sie auf **Erstellen**:

    **App-Name**: Geben Sie einen eindeutigen Namen für die Funktions-App ein. Als Beispiel wird **contoso-function-app-1098** angezeigt.

    **Ressourcengruppe**: Wählen Sie **Vorhandene verwenden** und **contoso-us-resource-group** aus, damit sich alle in diesem Artikel erstellten Ressourcen in derselben Gruppe befinden.

    **Application Insights**: Für diese Übung können Sie diese Option deaktivieren.

    ![Erstellen der Funktionen-App](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Stellen Sie sicher, dass auf der Seite **Registrierungsgruppe hinzufügen** die neue Funktions-App ausgewählt ist. Möglicherweise müssen Sie das Abonnement erneut auswählen, um die Liste der Funktions-Apps zu aktualisieren.

    Wenn die neue Funktions-App ausgewählt ist, klicken Sie auf **Neue Funktion erstellen**.

    ![Erstellen der Funktionen-App](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Die neue Funktions-App wird geöffnet.

9. Klicken Sie in der Funktions-App, um eine neue Funktion zu erstellen.

    ![Erstellen der Funktionen-App](./media/how-to-use-custom-allocation-policies/new-function.png)

    Verwenden Sie für die neue Funktion die Standardeinstellungen, um in der Sprache **CSharp** eine neue **Webhook + API**-Instanz zu erstellen. Klicken Sie dann auf **Diese Funktion erstellen**.

    Hierdurch wird eine neue C#-Funktion mit dem Namen **HttpTriggerCSharp1** erstellt.

10. Ersetzen Sie den Code für die neue C#-Funktion durch den folgenden Code, und klicken Sie auf **Speichern**:    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Kehren Sie zur Seite **Registrierungsgruppe hinzufügen** zurück, und stellen Sie sicher, dass die neue Funktion ausgewählt ist. Möglicherweise müssen Sie die Funktion erneut auswählen, um die Liste der Funktionen zu aktualisieren.

    Wenn die neue Funktion ausgewählt ist, klicken Sie auf **Speichern**, um die Registrierungsgruppe zu speichern.

    ![Speichern Sie schließlich die Registrierungsgruppe.](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Nachdem Sie die Registrierungsgruppe gespeichert haben, öffnen Sie sie erneut, und notieren Sie sich den **Primärschlüssel**. Sie müssen die Registrierung speichern, damit die Schlüssel generiert werden. Mit diesem Schlüssel werden später eindeutige Geräteschlüssel für simulierte Geräte generiert.


## <a name="derive-unique-device-keys"></a>Ableiten eindeutiger Geräteschlüssel

In diesem Abschnitt erstellen Sie zwei eindeutige Geräteschlüssel. Ein Schlüssel wird für einen simulierten Toaster verwendet. Der andere Schlüssel wird für eine simulierte Wärmepumpe verwendet.

Verwenden Sie zum Generieren des Geräteschlüssels den **Primärschlüssel**, den Sie zuvor notiert haben, um die [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Verschlüsselung der Geräteregistrierungs-ID für jedes Gerät zu berechnen und das Ergebnis in das Base64-Format zu konvertieren. Weitere Informationen zum Erstellen von abgeleiteten Geräteschlüsseln mit Registrierungsgruppen finden Sie im Abschnitt Gruppenregistrierungen unter [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md).

Verwenden Sie für das Beispiel in diesem Artikel die folgenden beiden Geräteregistrierungs-IDs, und berechnen Sie einen Geräteschlüssel für beide Geräte. Beide Registrierungs-IDs haben ein gültiges Suffix für den Beispielcode der benutzerdefinierten Zuweisungsrichtlinie:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux-Arbeitsstationen

Wenn Sie eine Linux-Arbeitsstation verwenden, können Sie die abgeleiteten Geräteschlüssel mit OpenSSL generieren, wie im folgenden Beispiel gezeigt.

1. Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Windows-Arbeitsstationen

Wenn Sie eine Windows-Arbeitsstation verwenden, können Sie die abgeleiteten Geräteschlüssel mit PowerShell generieren, wie im folgenden Beispiel gezeigt.

1. Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


Die simulierten Geräte verwenden die abgeleiteten Geräteschlüssel mit der jeweiligen Registrierungs-ID für den Nachweis des symmetrischen Schlüssels.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Vorbereiten einer Azure IoT C SDK-Entwicklungsumgebung

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. Das SDK enthält den Beispielcode für das simulierte Gerät. Dieses simulierte Gerät versucht, die Bereitstellung während seiner Startsequenz auszuführen.

In diesem Abschnitt wird eine Windows-Arbeitsstation vorausgesetzt. Ein Beispiel für Linux finden Sie in der Beschreibung der Einrichtung der virtuellen Computer in [Bereitstellen für Mehrinstanzenfähigkeit](how-to-provision-multitenant.md).

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die Voraussetzungen für Visual Studio (Visual Studio und die Workload „Desktopentwicklung mit C++“) müssen **vor** Beginn der Installation von `CMake` auf dem Computer installiert sein. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem.

2. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie den folgenden Befehl zum Klonen des Azure IoT C SDK-GitHub-Repositorys aus:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.


3. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsclientplattform: Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs) aus. 

    Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>Simulieren der Geräte

In diesem Abschnitt aktualisieren Sie ein Bereitstellungsbeispiel mit dem Namen **prov\_dev\_client\_sample** im Azure IoT C SDK, das Sie zuvor eingerichtet haben. 

Dieser Beispielcode simuliert eine Gerätestartsequenz, von der die Bereitstellungsanforderung an die Instanz des Device Provisioning Service gesendet wird. Die Startsequenz bewirkt, dass der Toaster erkannt und mithilfe der benutzerdefinierten Zuweisungsrichtlinie dem IoT-Hub zugewiesen wird.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Device Provisioning Service, und notieren Sie sich den Wert unter **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öffnen Sie in Visual Studio die Projektmappendatei **azure_iot_sdks.sln**, die zuvor durch das Ausführen von CMake generiert wurde. Die Projektmappendatei befindet sich an folgendem Speicherort:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Navigieren Sie im Visual Studio-Fenster *Projektmappen-Explorer* zum Ordner **Provision\_Samples**. Erweitern Sie das Beispielprojekt mit dem Namen **prov\_dev\_client\_sample**. Erweitern Sie **Quelldateien**, und öffnen Sie **prov\_dev\_client\_sample.c**.

4. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten dargestellt auf `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` festgelegt ist:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus. 


#### <a name="simulate-the-contoso-toaster-device"></a>Simulieren des Contoso-Toasters

1. Suchen Sie zum Simulieren des Toasters nach dem Aufruf von `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**. Der Aufruf ist auskommentiert.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Heben Sie die Auskommentierung für den Funktionsaufruf auf, und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die Registrierungs-ID für den Toaster und den von Ihnen zuvor generierten abgeleiteten Geräteschlüssel. Der unten angegebene Schlüsselwert **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** dient nur als Beispiel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Speichern Sie die Datei .

2. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Klicken Sie in der Eingabeaufforderung zum Neuerstellen des Projekts auf **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel für einen erfolgreichen Start des Toasters und das Herstellen der Verbindung mit der Provisioning Service-Instanz, die durch die benutzerdefinierte Zuweisungsrichtlinie dem IoT-Hub für den Toaster zugewiesen werden soll:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simulieren der Contoso-Wärmepumpe

1. Aktualisieren Sie zum Simulieren der Wärmepumpe den Aufruf von `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** erneut mit der Registrierungs-ID der Wärmepumpe und dem von Ihnen zuvor generierten abgeleiteten Geräteschlüssel. Der unten angegebene Schlüsselwert **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** dient ebenfalls nur als Beispiel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Speichern Sie die Datei .

2. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Klicken Sie in der Eingabeaufforderung zum Neuerstellen des Projekts auf **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel für einen erfolgreichen Start der Wärmepumpe und das Herstellen der Verbindung mit der Provisioning Service-Instanz, die durch die benutzerdefinierte Zuweisungsrichtlinie dem IoT-Hub für die Contoso-Wärmepumpe zugewiesen werden soll:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Behandeln von Problemen bei benutzerdefinierten Zuweisungsrichtlinien

Die folgende Tabelle enthält die erwarteten Szenarien und die resultierenden Fehlercodes, die auftreten können. Greifen Sie auf diese Tabelle zurück, um Fehler bei benutzerdefinierten Zuweisungsrichtlinien mit Azure Functions zu beheben.


| Szenario | Registrierungsergebnis des Provisioning Service | Ergebnisse des Bereitstellungs-SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Der Webhook gibt „200 OK“ zurück, und „iotHubHostName“ ist auf einen gültigen IoT-Hub-Hostnamen festgelegt. | Ergebnisstatus: Zugewiesen  | Das SDK gibt „PROV_DEVICE_RESULT_OK“ zusammen mit Hubinformationen zurück. |
| Der Webhook gibt „200 OK“ zurück, und „iotHubHostName“ ist in der Antwort vorhanden, jedoch auf eine leere Zeichenfolge oder NULL festgelegt. | Ergebnisstatus: Fehler<br><br> Fehlercode: CustomAllocationIotHubNotSpecified (400208) | Das SDK gibt „PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED“ zurück. |
| Der Webhook gibt „401 – Nicht autorisiert“ zurück. | Ergebnisstatus: Fehler<br><br>Fehlercode: CustomAllocationUnauthorizedAccess (400209) | Das SDK gibt „PROV_DEVICE_RESULT_UNAUTHORIZED“ zurück. |
| Es wurde eine individuelle Registrierung erstellt, um das Gerät zu deaktivieren. | Ergebnisstatus: Deaktiviert | Das SDK gibt „PROV_DEVICE_RESULT_DISABLED“ zurück. |
| Der Webhook gibt einen Fehlercode > = 429 zurück. | Die Orchestrierung des DPS wird mehrmals wiederholt. Die Wiederholungsrichtlinie lautet derzeit:<br><br>&nbsp;&nbsp;– Wiederholungsanzahl: 10<br>&nbsp;&nbsp;– Anfängliches Intervall: 1 s<br>&nbsp;&nbsp;– Inkrement: 9 s | Das SDK ignoriert den Fehler und übermittelt im angegebenen Zeitraum eine weitere Statusabrufmeldung. |
| Der Webhook gibt einen anderen Statuscode zurück. | Ergebnisstatus: Fehler<br><br>Fehlercode: CustomAllocationFailed (400207) | Das SDK gibt „PROV_DEVICE_RESULT_DEV_AUTH_ERROR“ zurück. |


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen weiterverwenden möchten, können Sie sie beibehalten. Wenn Sie nicht beabsichtigen, die Ressourcen weiterzuverwenden, führen Sie die folgenden Schritte aus, um alle in diesem Artikel erstellten Ressourcen zu löschen und unnötige Kosten zu vermeiden.

Bei dieser Vorgehensweise wird davon ausgegangen, dass Sie alle in diesem Artikel verwendeten Ressourcen gemäß den Anweisungen in der Ressourcengruppe **contoso-us-resource-group** erstellt haben.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Wenn Sie die IoT Hub-Ressource in einer bereits vorhandenen Ressourcengruppe erstellt haben, die Ressourcen enthält, die Sie behalten möchten, löschen Sie nicht die Ressourcengruppe, sondern nur die IoT Hub-Ressource.
>

So löschen Sie die Ressourcengruppen nach Namen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

2. Geben Sie im Textfeld **Nach Name filtern...** den Namen der Ressourcengruppe ein, die Ihre Ressourcen enthält: **contoso-us-resource-group**. 

3. Klicken Sie in der Ergebnisliste rechts neben Ihrer Ressourcengruppe auf **...** und dann auf **Ressourcengruppe löschen**.

4. Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie zur Bestätigung erneut den Namen Ihrer Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**. Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur erneuten Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md). 
- Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md). 











