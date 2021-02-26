---
title: 'Bereitstellen von Geräten mit symmetrischen Schlüsseln: Azure IoT Hub Device Provisioning Service'
description: Erfahren Sie, wie Sie symmetrische Schlüssel zum Bereitstellen von Geräten mit Ihrer Device Provisioning Service-Instanz (DPS) verwenden.
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: a4c16347d1883e1522fda18c2382f2d67b8ace80
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051108"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>Bereitstellen von Geräten mit symmetrischen Schlüsseln für Registrierungsgruppen

In diesem Artikel wird gezeigt, wie Sie mehrere Geräte mit symmetrischen Schlüsseln für eine Gruppe von IoT-Hubs mithilfe einer Registrierungsgruppe auf sichere Weise bereitstellen.

Einige Geräte verfügen unter Umständen nicht über ein Zertifikat, TPM oder ein anderes Sicherheitsfeature, über das das Gerät sicher identifiziert werden kann. Der Device Provisioning Service bietet ein Verfahren zum [Nachweis mit symmetrischen Schlüsseln](concepts-symmetric-key-attestation.md). Der Nachweis mit symmetrischen Schlüsseln kann genutzt werden, um ein Gerät anhand von eindeutigen Informationen wie der MAC-Adresse oder der Seriennummer zu identifizieren.

Wenn Sie ohne größeren Aufwand ein [Hardwaresicherheitsmodul (HSM)](concepts-service.md#hardware-security-module) und ein Zertifikat installieren können, ist dies unter Umständen ein besserer Ansatz zum Identifizieren und Bereitstellen Ihrer Geräte. Bei Verwendung eines HSM können Sie nämlich ggf. die Aktualisierung des Codes umgehen, der auf Ihren Geräten bereitgestellt wird, und in Ihre Geräteimages ist kein geheimer Schlüssel eingebettet. In diesem Artikel wird vorausgesetzt, dass weder ein HSM noch ein Zertifikat eine geeignete Option darstellen. Es wird aber davon ausgegangen, dass Sie über eine Methode zum Aktualisieren des Gerätecodes verfügen, um den Device Provisioning Service für die Bereitstellung dieser Geräte zu nutzen. 

Weiterhin wird in diesem Artikel davon ausgegangen, dass das Geräteupdate in einer sicheren Umgebung stattfindet, um den unberechtigten Zugriff auf den Hauptgruppenschlüssel oder den abgeleiteten Geräteschlüssel zu verhindern.

In diesem Artikel wird von der Nutzung einer Windows-Arbeitsstation ausgegangen. Allerdings können Sie die Verfahren auch unter Linux ausführen. Ein Beispiel für Linux finden Sie unter [Bereitstellen für Mehrinstanzenfähigkeit](how-to-provision-multitenant.md).

> [!NOTE]
> Das in diesem Artikel verwendete Beispiel ist in C geschrieben. Es gibt auch ein [C#-Beispiel für einen symmetrischen Schlüssel zum Bereitstellen von Geräten](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample). Wenn Sie dieses Beispiel verwenden möchten, laden Sie das Repository [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) herunter, oder klonen Sie es, und befolgen Sie die Anweisungen im Beispielcode. Befolgen Sie die Anweisungen in diesem Artikel, um über das Portal eine Registrierungsgruppe von symmetrischen Schlüsseln zu erstellen sowie den ID-Bereich und die primären und sekundären Schlüssel der Registrierungsgruppe zu ermitteln, die für die Ausführung des Beispiels erforderlich sind. Sie können mithilfe des Beispiels auch individuelle Registrierungen erstellen.

## <a name="overview"></a>Übersicht

Für jedes Gerät wird basierend auf Informationen, mit denen das Gerät identifiziert wird, eine eindeutige Registrierungs-ID definiert. Beispiele hierfür sind die MAC-Adresse oder eine Seriennummer.

Mit dem Device Provisioning Service wird eine Registrierungsgruppe erstellt, für die der [Nachweis symmetrischer Schlüssel](concepts-symmetric-key-attestation.md) verwendet wird. Die Registrierungsgruppe enthält einen Gruppenhauptschlüssel. Dieser Hauptschlüssel wird für die Hasherstellung für jede eindeutige Registrierungs-ID verwendet, um einen eindeutigen Geräteschlüssel für jedes Gerät zu erhalten. Das Gerät verwendet diesen abgeleiteten Geräteschlüssel mit seiner eindeutigen Registrierungs-ID, um gegenüber dem Device Provisioning Service den Nachweis zu erbringen und einem IoT Hub zugewiesen zu werden.

Der in diesem Artikel veranschaulichte Gerätecode basiert auf dem gleichen Muster wie unter [Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischen Schlüsseln](quick-create-simulated-device-symm-key.md). Mit diesem Code wird ein Gerät simuliert, indem ein Beispiel aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) genutzt wird. Wie in der Schnellstartanleitung gezeigt, verwendet das simulierte Gerät für den Nachweis keine einzelne Registrierung, sondern eine Registrierungsgruppe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

* Vollständige Bearbeitung der Schnellstartanleitung [Einrichten des IoT Hub Device Provisioning Service über das Azure-Portal](./quick-setup-auto-provision.md).

Die folgenden Voraussetzungen gelten für eine Windows-Entwicklungsumgebung. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 mit der aktivierten Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt.

* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Vorbereiten einer Azure IoT C SDK-Entwicklungsumgebung

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. 

Das SDK enthält den Beispielcode für das simulierte Gerät. Dieses simulierte Gerät versucht, die Bereitstellung während seiner Startsequenz auszuführen.

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

4. Erstellen Sie das Unterverzeichnis `cmake` im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. Führen Sie die folgenden Befehle im `azure-iot-sdk-c`-Verzeichnis aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsclientplattform: Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus. 

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Erstellen einer Registrierungsgruppe mit symmetrischem Schlüssel

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Device Provisioning Service-Instanz.

2. Wählen Sie die Registerkarte **Registrierungen verwalten** aus, und klicken Sie dann oben auf der Seite auf die Schaltfläche **Registrierungsgruppe hinzufügen**. 

3. Geben Sie unter **Registrierungsgruppe hinzufügen** die folgenden Informationen ein, und klicken Sie auf die Schaltfläche **Speichern**.

   - **Gruppenname**: Geben Sie **mylegacydevices** ein.

   - **Nachweistyp**: Wählen Sie **Symmetrischer Schlüssel**.

   - **Schlüssel automatisch generieren**: Aktivieren Sie dieses Kontrollkästchen.

   - **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen**: Wählen Sie **Statische Konfiguration**, um das Zuweisen zu einem bestimmten Hub zu ermöglichen.

   - **Select the IoT hubs this group can be assigned to** (Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann): Wählen Sie einen Ihrer Hubs aus.

     ![Hinzufügen einer Registrierungsgruppe für den Nachweis des symmetrischen Schlüssels](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Nachdem Sie Ihre Registrierung gespeichert haben, werden der **Primärschlüssel** und **Sekundärschlüssel** generiert und dem Registrierungseintrag hinzugefügt. Ihre Registrierungsgruppe mit symmetrischem Schlüssel wird als **mylegacydevices** unter der Spalte *Gruppenname* auf der Registerkarte *Registrierungsgruppen* angezeigt. 

    Öffnen Sie die Registrierung, und kopieren Sie den Wert des von Ihnen generierten **Primärschlüssels**. Dieser Schlüssel ist Ihr Gruppenhauptschlüssel.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Auswählen einer eindeutigen Registrierungs-ID für das Gerät

Eine eindeutige Registrierungs-ID muss definiert werden, um jedes Gerät zu identifizieren. Sie können die MAC-Adresse, Seriennummer oder eindeutige Informationen vom Gerät verwenden. 

In diesem Beispiel wird eine Kombination aus MAC-Adresse und Seriennummer genutzt, um die folgende Zeichenfolge für eine Registrierungs-ID zu bilden.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Erstellen Sie eindeutige Registrierungs-IDs für jedes Gerät. Gültige Zeichen sind alphanumerische Kleinbuchstaben und Bindestriche („-“).


## <a name="derive-a-device-key"></a>Ableiten eines Geräteschlüssels 

Verwenden Sie zum Generieren von Geräteschlüsseln den Hauptschlüssel der Registrierungsgruppe, um einen [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Wert der Registrierungs-ID für jedes Gerät zu berechnen. Das Ergebnis wird dann für jedes Gerät in das Base64-Format konvertiert.

> [!WARNING]
> Der Gerätecode für jedes Gerät sollte nur den entsprechenden abgeleiteten Geräteschlüssel für dieses Gerät enthalten. Fügen Sie Ihren Gruppenhauptschlüssel nicht in Ihren Gerätecode ein. Ein kompromittierter Hauptschlüssel kann die Sicherheit aller Geräte gefährden, die mit ihm authentifiziert werden.


# <a name="windows"></a>[Windows](#tab/windows)

Wenn Sie eine Windows-Arbeitsstation verwenden, können Sie die abgeleiteten Geräteschlüssel mit PowerShell generieren, wie im folgenden Beispiel gezeigt.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch Ihre Registrierungs-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Wenn Sie eine Linux-Arbeitsstation verwenden, können Sie Ihren abgeleiteten Geräteschlüssel mit OpenSSL generieren, wie im folgenden Beispiel gezeigt.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch Ihre Registrierungs-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

Jedes Gerät verwendet den abgeleiteten Geräteschlüssel mit der eindeutigen Registrierungs-ID, um während der Bereitstellung den Nachweis symmetrischer Schlüssel mit der Registrierungsgruppe durchzuführen.



## <a name="create-a-device-image-to-provision"></a>Erstellen eines Geräteimages für die Bereitstellung

In diesem Abschnitt aktualisieren Sie ein Bereitstellungsbeispiel mit dem Namen **prov\_dev\_client\_sample** im Azure IoT C SDK, das Sie zuvor eingerichtet haben. 

Dieser Beispielcode simuliert eine Gerätestartsequenz, von der die Bereitstellungsanforderung an die Instanz des Device Provisioning Service gesendet wird. Die Startsequenz bewirkt, dass das Gerät erkannt und dem IoT-Hub zugewiesen wird, den Sie in der Registrierungsgruppe konfiguriert haben. Dies wird für jedes Gerät durchgeführt, das mithilfe der Registrierungsgruppe bereitgestellt wird.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Device Provisioning Service, und notieren Sie sich den Wert unter **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öffnen Sie in Visual Studio die Projektmappendatei **azure_iot_sdks.sln**, die zuvor durch das Ausführen von CMake generiert wurde. Die Projektmappendatei befindet sich am folgenden Speicherort:

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

6. Suchen Sie den Aufruf von `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, der auskommentiert ist.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Heben Sie die Auskommentierung für den Funktionsaufruf auf, und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die eindeutige Registrierungs-ID für Ihr Gerät und den von Ihnen generierten abgeleiteten Geräteschlüssel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Speichern Sie die Datei.

7. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus. 

8. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Klicken Sie in der Eingabeaufforderung zum Neuerstellen des Projekts auf **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel für das erfolgreiche Starten des simulierten Geräts und das Herstellen der Verbindung mit der Provisioning Service-Instanz, die einem IoT Hub zugewiesen werden soll:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Navigieren Sie im Portal zu der IoT Hub-Instanz, der Ihr simuliertes Gerät zugewiesen wurde, und klicken Sie auf die Registerkarte **IoT-Geräte**. Nach erfolgreicher Bereitstellung des simulierten Geräts auf dem Hub wird die dazugehörige Geräte-ID auf dem Blatt **IoT-Geräte** angezeigt, und der *STATUS* lautet **Aktiviert**. Unter Umständen müssen Sie oben auf die Schaltfläche **Aktualisieren** klicken. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Sicherheitsaspekte

Beachten Sie, dass der abgeleitete Geräteschlüssel hierbei als Teil des Images für jedes Gerät vorliegt und dass dies nicht einer empfohlenen bewährten Sicherheitsmethode entspricht. Dies ist ein Grund, warum zwischen Sicherheit und Benutzerfreundlichkeit häufig ein Kompromiss gefunden werden muss. Sie müssen die Sicherheit Ihrer Geräte gemäß Ihren eigenen Anforderungen umfassend überprüfen.


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur erneuten Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md). 
* [Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischen Schlüsseln](quick-create-simulated-device-symm-key.md)
* Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md).
