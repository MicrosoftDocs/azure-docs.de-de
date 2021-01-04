---
title: Tutorial zur Verwendung benutzerdefinierter Zuweisungsrichtlinien bei Azure IoT Hub Device Provisioning Service (DPS)
description: Tutorial zur Verwendung benutzerdefinierter Zuweisungsrichtlinien beim Azure IoT Hub Device Provisioning-Dienst (Azure IoT Hub Device Provisioning Service, DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 4cab1765a387bbae61c9c242a8e7a1ca881ea1f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966648"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Tutorial: Verwenden von benutzerdefinierten Zuweisungsrichtlinien bei Device Provisioning Service (DPS)

Mit einer Zuweisungsrichtlinie können Sie genauer steuern, wie Geräte einem IoT-Hub zugewiesen werden. Dazu werden Geräte mithilfe von benutzerdefiniertem Code in einer [Azure-Funktion](../azure-functions/functions-overview.md), die während der Bereitstellung ausgeführt wird, einem IoT-Hub zugewiesen. Device Provisioning Service ruft Ihren Azure-Funktionscode auf und stellt alle relevanten Informationen zum Gerät und zur Registrierung bereit. Der Funktionscode wird ausgeführt und gibt die IoT Hub-Informationen für die Bereitstellung des Geräts zurück.

Sie definieren eigene benutzerdefinierte Zuweisungsrichtlinien, wenn die vom Device Provisioning Service bereitgestellten Richtlinien nicht die Anforderungen Ihres Szenarios erfüllen.

Beispielsweise möchten Sie eventuell das Zertifikat überprüfen, das während der Bereitstellung von einem Gerät verwendet wird, und das Gerät basierend auf einer Zertifikateigenschaft einem IoT-Hub zuweisen. Oder möglicherweise sind in einer Datenbank Informationen für die Geräte gespeichert, und Sie müssen die Datenbank abfragen, um zu bestimmen, welchem IOT-Hub ein Gerät zugewiesen werden soll.

In diesem Artikel wird eine Registrierungsgruppe mit einer benutzerdefinierten Zuweisungsrichtlinie gezeigt, die eine in C# geschriebene Azure-Funktion zum Bereitstellen von Toastern mithilfe von symmetrischen Schlüsseln verwendet. Ein nicht als Toaster erkanntes Gerät wird in einem IoT-Hub nicht bereitgestellt.

Geräte fordern die Bereitstellung mithilfe des Bereitstellungsbeispielcodes an, der im [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) enthalten ist.


In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer neuen Azure-Funktions-App zur Unterstützung einer benutzerdefinierten Zuordnungsfunktion
> * Erstellen einer neuen Gruppenregistrierung mithilfe einer Azure-Funktion für die benutzerdefinierte Zuweisungsrichtlinie
> * Erstellen von Geräteschlüsseln für zwei Geräte
> * Einrichten der Entwicklungsumgebung für. Beispielgerätecode aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Ausführen der Geräte und Überprüfen, ob sie entsprechend der benutzerdefinierten Zuweisungsrichtlinie bereitgestellt wurden


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* In diesem Artikel wird davon ausgegangen, dass Sie die Schritte in [Einrichten von IoT Hub Device Provisioning Service über das Azure-Portal](./quick-setup-auto-provision.md) zum Erstellen Ihrer IoT Hub- und DPS-Instanz abgeschlossen haben.

* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.

* Bei einer Windows-Entwicklungsumgebung ist [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 mit aktivierter Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) erforderlich. Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt.

* Informationen zu Linux oder macOS finden Sie in der Dokumentation zu [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Erstellen der benutzerdefinierten Zuordnungsfunktion

In diesem Abschnitt erstellen Sie eine Azure-Funktion, die Ihre benutzerdefinierte Zuordnungsrichtlinie implementiert. Mit dieser Funktion wird bestimmt, ob ein Gerät für Ihren IoT Hub registriert werden sollte – abhängig davon, ob die Registrierungs-ID das Zeichenfolgenpräfix **contoso-toaster** enthält.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie auf der Startseite **+ Ressource erstellen** aus.

2. Geben Sie im Suchfeld *Marketplace durchsuchen* „Funktions-App“ ein. Wählen Sie in der Dropdownliste **Funktions-App** aus, und wählen Sie dann **Erstellen**.

3. Geben Sie in **Funktions-App** auf der Seite „Erstellen“ auf der Registerkarte **Grundeinstellungen** die folgenden Einstellungen für die neue Funktions-App ein, und wählen Sie **Überprüfen + erstellen** aus:

    **Abonnement**: Wenn Sie mehrere Abonnements haben und das gewünschte Abonnement nicht ausgewählt ist, wählen Sie es aus.

    **Ressourcengruppe**: In diesem Feld können Sie eine neue Ressourcengruppe erstellen oder eine bereits vorhandene für die Funktions-App auswählen. Wählen Sie dieselbe Ressourcengruppe aus, die Ihre zuvor zu Testzwecken erstellte IoT Hub-Instanz enthält (z. B. **TestResources**). Wenn Sie alle verwandten Ressourcen in einer Gruppe zusammenfassen, können Sie sie zusammen verwalten.

    **Name der Funktions-App**: Geben Sie einen eindeutigen Namen für die Funktions-App ein. In diesem Beispiel wird **contoso-function-app** verwendet.

    **Veröffentlichen**: Vergewissern Sie sich, dass **Code** ausgewählt ist.

    **Laufzeitstapel**: Wählen Sie in der Dropdownliste **.NET Core** aus.

    **Region**: Wählen Sie die gleiche Region wie für Ihre Ressourcengruppe aus. In diesem Beispiel wird **USA, Westen** verwendet.

    > [!NOTE]
    > Application Insights ist standardmäßig aktiviert. Application Insights ist für diesen Artikel nicht erforderlich, kann jedoch hilfreich sein, um Probleme zu verstehen und zu untersuchen, die bei der benutzerdefinierten Zuordnung auftreten. Wenn Sie möchten, können Sie Application Insights deaktivieren, indem Sie die Registerkarte **Überwachung** auswählen und dann für **Application Insights aktivieren** die Option **Nein** wählen.

    ![Erstellen einer Azure-Funktions-App zum Hosten der benutzerdefinierten Zuordnungsfunktion](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Wählen Sie auf der Seite **Zusammenfassung** die Option **Erstellen** aus, um die Funktions-App zu erstellen. Die Bereitstellung kann einige Minuten dauern. Wenn der Vorgang abgeschlossen ist, wählen Sie **Zu Ressource wechseln** aus.

5. Klicken Sie im linken Bereich unter **Funktionen** auf **Functions** und **+ Add**, um eine neue Funktion hinzuzufügen.

6. Wählen Sie auf der Seite „Vorlagen“ die Kachel **HTTP Trigger** und dann **Create Function** aus. Die Funktion **HttpTrigger1** wird erstellt, und im Portal wird die Übersichtsseite für Ihre Funktion angezeigt.

7. Klicken Sie für Ihre neue Funktion auf **Programmieren und testen**. Im Portal wird der Inhalt der Codedatei **run.csx** angezeigt. 

8. Ersetzen Sie den Code für die Funktion **HttpTrigger1** durch den folgenden Code, und wählen Sie **Speichern** aus. Ihr benutzerdefinierter Zuordnungscode kann jetzt verwendet werden.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. Klicken Sie direkt unter der Codedatei **run.csx** auf **Protokolle**, um die Protokollierung aus der benutzerdefinierten Zuordnungsfunktion zu überwachen. 


## <a name="create-the-enrollment"></a>Erstellen der Registrierung

In diesem Abschnitt erstellen Sie eine neue Registrierungsgruppe, von der die benutzerdefinierte Zuweisungsrichtlinie verwendet wird. Der Einfachheit halber wird in diesem Artikel ein [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md) für die Registrierung verwendet. Für eine Lösung mit höherer Sicherheit empfiehlt sich die Verwendung eines [X.509-Zertifikatnachweises](concepts-x509-attestation.md) mit einer Kette von Vertrauensstellungen.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Bereitstellungsdienst.

2. Wählen Sie im linken Bereich **Registrierungen verwalten** aus, und wählen Sie dann oben auf der Seite die Schaltfläche **Registrierungsgruppe hinzufügen**.

3. Geben Sie für **Registrierungsgruppe hinzufügen** die Informationen in der nachstehenden Tabelle ein, und klicken Sie auf die Schaltfläche **Speichern**.

    | Feld | Beschreibung und/oder vorgeschlagener Wert |
    | :---- | :----------------------------- |
    | **Gruppenname** | Geben Sie **contoso-custom-allocated-devices** ein. |
    | **Nachweistyp** | Wählen Sie **Symmetrischer Schlüssel** aus. |
    | **Schlüssel automatisch generieren** | Dieses Kontrollkästchen sollte bereits aktiviert sein. |
    | **Wählen Sie aus, wie Geräte den Hubs zugewiesen werden sollen** | Wählen Sie **Benutzerdefiniert (Azure-Funktion verwenden)** aus. |
    | **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann** | Wählen Sie den IoT-Hub aus, den Sie beim Abschluss des Schnellstarts erstellt haben. |
    | **Azure-Funktion auswählen** | Wählen Sie das Abonnement aus, das die von Ihnen erstellte Funktions-App enthält. Wählen Sie dann **contoso-function-app** und **HttpTrigger1** für die Funktion aus. |

    ![Hinzufügen einer benutzerdefinierten Zuweisungsregistrierungsgruppe für den Nachweis des symmetrischen Schlüssels](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Öffnen Sie die Registrierungsgruppe nach dem Speichern erneut, und notieren Sie sich den **Primärschlüssel**. Sie müssen die Registrierung speichern, damit die Schlüssel generiert werden. Dieser primäre symmetrische Schlüssel wird zum Generieren von eindeutigen Geräteschlüsseln für Geräte verwendet, die später eine Bereitstellung versuchen werden. 

## <a name="derive-unique-device-keys"></a>Ableiten eindeutiger Geräteschlüssel

Geräte verwenden den primären symmetrischen Schlüssel nicht direkt. Stattdessen verwenden Sie den primären Schlüssel zum Ableiten eines Geräteschlüssels für jedes Gerät. In diesem Abschnitt erstellen Sie zwei eindeutige Geräteschlüssel. Ein Schlüssel wird für einen simulierten Toaster verwendet. Der andere Schlüssel wird für eine simulierte Wärmepumpe verwendet. Mit den generierten Schlüsseln können beide Geräte versuchen, eine Registrierung durchführen. Nur eine einzige Geräteregistrierungs-ID wird ein gültiges Suffix haben, das vom Beispielcode für die benutzerdefinierte Zuweisungsrichtlinie akzeptiert wird. Daher wird eine ID akzeptiert und die andere abgelehnt.

Zum Ableiten des Geräteschlüssels verwenden Sie den symmetrischen Schlüssel, den Sie sich zuvor notiert haben, um die [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Verschlüsselung der Geräteregistrierungs-ID für jedes Gerät zu berechnen und das Ergebnis in das Base64-Format zu konvertieren. Weitere Informationen zum Erstellen von abgeleiteten Geräteschlüsseln mit Registrierungsgruppen finden Sie im Abschnitt Gruppenregistrierungen unter [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md).

Verwenden Sie für das Beispiel in diesem Artikel die folgenden beiden Geräteregistrierungs-IDs mit dem nachstehenden Code zum Berechnen eines Geräteschlüssels für beide Geräte:

* **contoso-toaster-007**
* **contoso-heatpump-088**

Ersetzen Sie den Wert der Variablen **KEY** durch den **Primärschlüssel**, den Sie sich nach der Erstellung Ihrer Registrierungsgruppe notiert haben. Der Schlüsselwert und die Ausgabe, die mit dem nachstehenden Code gezeigt werden, sind nur ein Beispiel.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie eine Windows-Arbeitsstation verwenden, können Sie die abgeleiteten Geräteschlüssel mit PowerShell generieren, wie im folgenden Beispiel gezeigt.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Wenn Sie eine Linux-Arbeitsstation verwenden, können Sie Ihre abgeleiteten Geräteschlüssel mit OpenSSL generieren, wie im folgenden Beispiel zu Bash gezeigt wird.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Vorbereiten einer Azure IoT C SDK-Entwicklungsumgebung

Geräte fordern die Bereitstellung mithilfe des Bereitstellungsbeispielcodes an, der im [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) enthalten ist.

In diesem Abschnitt bereiten Sie die Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. Das SDK enthält den Beispielcode für das simulierte Gerät. Dieses simulierte Gerät versucht, die Bereitstellung während seiner Startsequenz auszuführen.

In diesem Abschnitt wird eine Windows-Arbeitsstation vorausgesetzt. Ein Beispiel für Linux finden Sie in der Beschreibung der Einrichtung der virtuellen Computer in [Bereitstellen für Mehrinstanzenfähigkeit](how-to-provision-multitenant.md).

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die Voraussetzungen für Visual Studio (Visual Studio und die Workload „Desktopentwicklung mit C++“) müssen **vor** Beginn der Installation von `CMake` auf dem Computer installiert sein. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem.

2. Suchen Sie den Tagnamen für das [aktuelle Release](https://github.com/Azure/azure-iot-sdk-c/releases/latest) des SDK.

3. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des aktuellen Releases des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus. Verwenden Sie das im vorherigen Schritt gefundene Tag als Wert für den Parameter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

4. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. Führen Sie die folgenden Befehle im `azure-iot-sdk-c`-Verzeichnis aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsclientplattform: Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Wenn `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus.

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

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Device Provisioning Service, und notieren Sie sich den Wert unter **_ID-Bereich_**.

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öffnen Sie in Visual Studio die Projektmappendatei **azure_iot_sdks.sln**, die zuvor durch das Ausführen von CMake generiert wurde. Die Projektmappendatei befindet sich am folgenden Speicherort:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. Suchen Sie in der Funktion `main()` den Aufruf von `Prov_Device_Register_Device()`. Fügen Sie direkt vor diesem Aufruf die folgenden Codezeilen hinzu, die [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) verwenden, um während der Bereitstellung eine benutzerdefinierte JSON-Nutzlast zu übergeben. Damit können weitere Informationen für Ihre benutzerdefinierten Zuordnungsfunktionen bereitgestellt werden. Darüber hinaus kann damit der Gerätetyp übergeben werden, anstatt die Registrierungs-ID zu überprüfen.

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus.

### <a name="simulate-the-contoso-toaster-device"></a>Simulieren des Contoso-Toasters

1. Suchen Sie zum Simulieren des Toasters nach dem Aufruf von `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**. Der Aufruf ist auskommentiert.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Heben Sie die Auskommentierung für den Funktionsaufruf auf, und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die Registrierungs-ID für den Toaster und den von Ihnen zuvor generierten abgeleiteten Geräteschlüssel. Der unten angegebene Schlüsselwert **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** dient nur als Beispiel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Speichern Sie die Datei.

2. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Wählen Sie in der Aufforderung zum erneuten Erstellen des Projekts **Ja** aus, um das Projekt vor der Ausführung neu zu erstellen.

    Der nachstehende Text ist eine Beispielprotokollierungsausgabe aus dem Code der benutzerdefinierten Zuordnungsfunktion für den Toaster. Beachten Sie, dass ein Hub für einen Toaster erfolgreich ausgewählt wurde. Beachten Sie auch den Member `payload`, der den benutzerdefinierten JSON-Inhalt enthält, den Sie dem Code hinzugefügt haben. Dieser steht dem Code zur Verwendung in `deviceRuntimeContext` zur Verfügung.

    Diese Protokollierung steht zur Verfügung, wenn Sie unter dem Funktionscode im Portal auf **Protokolle** klicken:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    Die folgende Beispielgeräteausgabe zeigt einen erfolgreichen Start des simulierten Toasters und das Herstellen einer Verbindung mit der Instanz des Bereitstellungsdiensts, die durch die benutzerdefinierte Zuweisungsrichtlinie dem IoT-Hub für den Toaster zugewiesen werden soll:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulieren der Contoso-Wärmepumpe

1. Aktualisieren Sie zum Simulieren der Wärmepumpe den Aufruf von `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** erneut mit der Registrierungs-ID der Wärmepumpe und dem von Ihnen zuvor generierten abgeleiteten Geräteschlüssel. Der unten angegebene Schlüsselwert **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** dient ebenfalls nur als Beispiel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Speichern Sie die Datei.

2. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Wählen Sie in der Eingabeaufforderung zum Neuerstellen des Projekts **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Der nachstehende Text ist eine Beispielprotokollierungsausgabe aus dem Code der benutzerdefinierten Zuordnungsfunktion für die Wärmepumpe. Die benutzerdefinierte Zuweisungsrichtlinie lehnt diese Registrierung mit der Meldung „HTTP-Fehler 400 – Ungültige Anforderung“ ab. Beachten Sie den Member `payload`, der den benutzerdefinierten JSON-Inhalt enthält, den Sie dem Code hinzugefügt haben. Dieser steht dem Code zur Verwendung in `deviceRuntimeContext` zur Verfügung.

    Diese Protokollierung steht zur Verfügung, wenn Sie unter dem Funktionscode im Portal auf **Protokolle** klicken:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    Die folgende Beispielgeräteausgabe zeigt den Start der simulierten Wärmepumpe und das Herstellen einer Verbindung mit der Instanz des Bereitstellungsdiensts, um die Registrierung bei einem IoT-Hub mithilfe der benutzerdefinierten Zuweisungsrichtlinie zu versuchen. Dieser Versuch schlägt mit der Meldung (`Custom allocation failed with status code: 400`) fehl, da die benutzerdefinierte Zuweisungsrichtlinie nur für Toaster vorgesehen ist:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
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

3. Wählen Sie in der Ergebnisliste rechts neben Ihrer Ressourcengruppe **...** und dann **Ressourcengruppe löschen** aus.

4. Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen Ihrer Ressourcengruppe zur Bestätigung erneut ein, und wählen Sie **Löschen** aus. Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Ein detaillierteres Beispiel zur benutzerdefinierten Zuweisungsrichtlinie finden Sie unter [Verwenden benutzerdefinierter Zuweisungsrichtlinien](how-to-use-custom-allocation-policies.md).
* Weitere Informationen zur erneuten Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md).
* Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md).