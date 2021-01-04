---
title: 'Tutorial: Einrichten eines Geräts für Azure IoT Hub Device Provisioning Service'
description: In diesem Tutorial erfahren Sie, wie Sie während des Geräteherstellungsprozesses ein bereitzustellendes Gerät über IoT Hub Device Provisioning Service (DPS) einrichten.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959797"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Tutorial: Einrichten eines bereitzustellenden Geräts mithilfe des Azure IoT Hub Device Provisioning-Diensts

Im vorherigen Tutorial haben Sie erfahren, wie Sie den Azure IoT Hub Device Provisioning-Dienst einrichten, um Ihre Geräte automatisch für IoT Hub bereitzustellen. In diesem Tutorial wird gezeigt, wie Sie Ihr Gerät während des Herstellungsprozesses einrichten und dadurch die automatische Bereitstellung mit IoT Hub ermöglichen. Ihr Gerät wird beim ersten Starten und Herstellen einer Verbindung mit dem Bereitstellungsdienst basierend auf seinem [Nachweismechanismus](concepts-service.md#attestation-mechanism) bereitgestellt. Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines plattformspezifischen Client-SDK für den Device Provisioning-Dienst
> * Extrahieren der Sicherheitsartefakte
> * Erstellen der Geräteregistrierungssoftware

In diesem Tutorial wird vorausgesetzt, dass Sie bereits Ihre Instanz des Device Provisioning-Diensts und einen IoT Hub erstellt haben, indem Sie die Anleitung im vorherigen Tutorial [Einrichten von Cloudressourcen](tutorial-set-up-cloud.md) verwendet haben.

In diesem Tutorial wird das [Repository „Azure IoT SDKs and libraries for C“](https://github.com/Azure/azure-iot-sdk-c) (Azure IoT SDKs und -Bibliotheken für C) verwendet. Dieses Repository enthält das Client-SDK für den Device Provisioning-Dienst für C. Das SDK bietet derzeit TPM- und X.509-Unterstützung für Geräte in Windows- oder Ubuntu-Implementierungen. Dieses Tutorial basiert auf der Nutzung eines Windows-Bereitstellungsclients und setzt allgemeine Kenntnisse zu Visual Studio voraus. 

Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen gelten für eine Windows-Entwicklungsumgebung. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 mit der aktivierten Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt.

* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Erstellen einer plattformspezifischen Version des SDK

Das Client-SDK für den Device Provisioning-Dienst unterstützt Sie beim Implementieren Ihrer Geräteregistrierungssoftware. Vor seiner Verwendung müssen Sie jedoch eine Version des SDK erstellen, das speziell für Ihre Entwicklungsclientplattform und den Nachweismechanismus gilt. In diesem Tutorial erstellen Sie ein SDK, das Visual Studio auf einer Windows-Entwicklungsplattform für einen unterstützten Nachweistyp nutzt:

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

4. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. Führen Sie die folgenden Befehle im Verzeichnis `azure-iot-sdk-c` aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Erstellen Sie das SDK für Ihre Entwicklungsplattform basierend auf den Nachweismechanismen, die Sie verwenden möchten. Verwenden Sie einen der folgenden Befehle (achten Sie jeweils auf die beiden nachgestellten Punkte). CMake erstellt anschließend das Unterverzeichnis `/cmake` mit für Ihr Gerät spezifischem Inhalt:
 
    - Bei Geräten, die den TPM-Simulator für den Nachweis verwenden:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Für alle anderen Geräte (physisches TPM/HSM/X.509 oder ein simuliertes X.509-Zertifikat):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Nun können Sie das SDK verwenden, um Ihren Geräteregistrierungscode zu erstellen. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrahieren der Sicherheitsartefakte 

Der nächste Schritt besteht darin, die Sicherheitsartefakte für den von Ihrem Gerät verwendeten Nachweismechanismus zu extrahieren. 

### <a name="physical-devices"></a>Physische Geräte 

Je nachdem, ob Sie das SDK für die Verwendung eines Nachweismechanismus für ein physisches TPM/HSM oder für die Verwendung von X.509-Zertifikaten erstellt haben, werden die Sicherheitsartefakte wie folgt gesammelt:

- Bei einem TPM-Gerät müssen Sie den zugehörigen **Endorsement Key** über den Hersteller des TPM-Chips in Erfahrung bringen. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie den Hash des Endorsement Key abrufen.  

- Für ein X.509-Gerät müssen Sie die Zertifikate abrufen, die für Ihre Geräte ausgestellt wurden. Der Bereitstellungsdienst macht zwei Arten von Registrierungseinträgen verfügbar, mit denen der Zugriff auf Geräte gesteuert wird, indem der X.509-Nachweismechanismus verwendet wird. Die benötigten Zertifikate richten sich nach den Registrierungstypen, die Sie verwenden.

    - Individuelle Registrierungen: Registrierung für ein bestimmtes einzelnes Gerät. Für diese Art von Registrierungseinträgen sind [Zertifikate für die endgültige Entität](concepts-x509-attestation.md#end-entity-leaf-certificate) erforderlich.
    
    - Registrierungsgruppen: Für diese Art von Registrierungseinträgen sind Zwischen- oder Stammzertifikate erforderlich. Weitere Informationen finden Sie unter [Steuern des Gerätezugriffs auf den Bereitstellungsdienst mit X.509-Zertifikaten](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Simulierte Geräte

Je nachdem, ob Sie das SDK für die Verwendung eines Nachweismechanismus für ein simuliertes Gerät mit TPM oder für X.509-Zertifikate erstellt haben, werden die Sicherheitsartefakte wie folgt gesammelt:

- Bei einem simulierten TPM-Gerät:

   1. Öffnen Sie eine Windows-Eingabeaufforderung, navigieren Sie zum Unterverzeichnis `azure-iot-sdk-c`, und führen Sie den TPM-Simulator aus. Dieser lauscht über einen Socket an den Ports 2321 und 2322. Lassen Sie das Befehlsfenster geöffnet. Es wird benötigt, um den Simulator bis zum Ende der folgenden Schnellstartanleitung auszuführen. 

      Führen Sie aus dem Unterverzeichnis `azure-iot-sdk-c` den folgenden Befehl aus, um den Simulator zu starten:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Wenn Sie für diesen Schritt die Git Bash-Eingabeaufforderung verwenden, müssen Sie die umgekehrten Schrägstriche in normale Schrägstriche ändern, z.B. `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Öffnen Sie mithilfe von Visual Studio die im Ordner *cmake* generierte Projektmappe namens `azure_iot_sdks.sln`, und erstellen Sie sie mithilfe des Befehls „Projektmappe erstellen“ im Menü „Build“.

   1. Navigieren Sie in Visual Studio im *Projektmappen-Explorer* zum Ordner **Provision\_Tools**. Klicken Sie mit der rechten Maustaste auf das Projekt **tpm_device_provision**, und wählen Sie **Als Startprojekt festlegen** aus. 

   1. Führen Sie die Projektmappe über einen der Startbefehle im Menü „Debuggen“ aus. Im Ausgabefenster werden die **_Registrierungs-ID_** und der **_Endorsement Key_** des TPM-Simulators für die Geräteregistrierung angezeigt. Kopieren Sie diese Werte für die spätere Verwendung. Sie können dieses Fenster (mit Registrierungs-ID und Endorsement Key) schließen. Lassen Sie aber das TPM-Simulatorfenster geöffnet, das Sie in Schritt 1 geöffnet haben.

- Bei einem simulierten X.509-Gerät:

  1. Öffnen Sie mithilfe von Visual Studio die im Ordner *cmake* generierte Projektmappe namens `azure_iot_sdks.sln`, und erstellen Sie sie mithilfe des Befehls „Projektmappe erstellen“ im Menü „Build“.

  1. Navigieren Sie in Visual Studio im *Projektmappen-Explorer* zum Ordner **Provision\_Tools**. Klicken Sie mit der rechten Maustaste auf das Projekt **dice\_device\_enrollment**, und wählen Sie **Als Startprojekt festlegen** aus. 
  
  1. Führen Sie die Projektmappe über einen der Startbefehle im Menü „Debuggen“ aus. Geben Sie im Ausgabefenster **i** für die individuelle Registrierung ein, wenn die entsprechende Aufforderung angezeigt wird. Im Ausgabefenster wird ein lokal generiertes X.509-Zertifikat für Ihr simuliertes Gerät angezeigt. Kopieren Sie die Ausgabe zwischen *-----BEGIN CERTIFICATE-----* und dem ersten Vorkommen von *-----END CERTIFICATE-----* in die Zwischenablage, und schließen Sie dabei auch diese beiden Zeile mit ein. Sie benötigen nur das erste Zertifikat aus dem Ausgabefenster.
 
  1. Erstellen Sie eine Datei mit dem Namen **_X509testcert.pem_** , öffnen Sie sie in einem Text-Editor Ihrer Wahl, und kopieren Sie den Inhalt aus der Zwischenablage in diese Datei. Speichern Sie die Datei, da Sie sie später für die Geräteregistrierung verwenden. Bei der Ausführung der Registrierungssoftware wird während der automatischen Bereitstellung dasselbe Zertifikat verwendet.    

Diese Sicherheitsartefakte sind bei der Registrierung Ihrer Geräte beim Device Provisioning-Dienst erforderlich. Der Bereitstellungsdienst wartet, bis das Gerät gestartet und zu einem beliebigen Zeitpunkt eine Verbindung mit diesem hergestellt wird. Wenn Ihr Gerät zum ersten Mal gestartet wird, interagiert die Client-SDK-Logik zur Extraktion der Sicherheitsartefakte vom Gerät mit Ihrem Chip (oder dem Simulator) und überprüft die Registrierung bei Ihrem Device Provisioning-Dienst. 

## <a name="create-the-device-registration-software"></a>Erstellen der Geräteregistrierungssoftware

Der letzte Schritt besteht darin, eine Registrierungsanwendung zu schreiben, die das Gerät mithilfe des Client-SDK des Device Provisioning-Dienst beim IoT Hub-Dienst registriert. 

> [!NOTE]
> Bei diesem Schritt wird von der Verwendung eines simulierten Geräts ausgegangen. Hierzu wird eine SDK-Beispielregistrierungsanwendung auf Ihrer Arbeitsstation ausgeführt. Die gleichen Konzepte gelten jedoch auch, wenn Sie eine Registrierungsanwendung für die Bereitstellung auf einem physischen Gerät erstellen. 

1. Navigieren Sie im Azure-Portal zum Blatt **Übersicht** für Ihren Device Provisioning-Dienst, und kopieren Sie den Wert unter **_ID-Bereich_** . Der *ID-Bereich* wird vom Dienst generiert und stellt Eindeutigkeit sicher. Er ist unveränderlich und wird zur eindeutigen Identifizierung der Registrierungs-IDs verwendet.

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. Navigieren Sie auf Ihrem Computer im *Projektmappen-Explorer* von Visual Studio zum Ordner **Provision\_Samples**. Wählen Sie das Beispielprojekt **prov\_dev\_client\_sample** aus, und öffnen Sie die Quelldatei **prov\_dev\_client\_sample.c**.

1. Weisen Sie den in Schritt 1 kopierten Wert für _ID-Bereich_ der Variablen `id_scope` zu (entfernen Sie jeweils die Klammern `[` und `]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Zur Referenz ist die Variable `global_prov_uri` angegeben, die der IoT Hub-Clientregistrierungs-API `IoTHubClient_LL_CreateFromDeviceAuth` das Herstellen einer Verbindung mit der designierten Instanz des Device Provisioning-Diensts ermöglicht.

1. Kommentieren Sie in der gleichen Datei in der **main()** -Funktion die Variable `hsm_type`, die dem Nachweismechanismus entspricht, der von der Registrierungssoftware Ihres Geräts verwendet wird (TPM oder X.509), bzw. heben Sie die Auskommentierung auf: 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Speichern Sie Ihre Änderungen, und erstellen Sie das Beispiel **prov\_dev\_client\_sample** neu, indem Sie im Menü „Build“ auf „Projektmappe erstellen“ klicken. 

1. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample** im Ordner **Provision\_Samples**, und wählen Sie die Option **Als Startprojekt festlegen**. Führen Sie die Beispielanwendung noch NICHT aus.

> [!IMPORTANT]
> Starten Sie das Gerät noch nicht! Bevor Sie das Gerät starten, müssen Sie den Prozess abschließen, indem Sie zuerst das Gerät beim Device Provisioning-Dienst registrieren. Der Abschnitt „Nächste Schritte“ weiter unten enthält einen Link zum nächsten Artikel.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>Während der Registrierung verwendete SDK-APIs (nur zur Referenz)

Zu Referenzzwecken stellt das SDK die folgenden APIs bereit, die von Ihrer Anwendung während der Registrierung verwendet werden können. Diese APIs ermöglichen dem Gerät beim Starten das Herstellen einer Verbindung mit dem sowie die Registrierung beim Device Provisioning-Dienst. Im Gegenzug empfängt Ihr Gerät die zum Herstellen einer Verbindung mit Ihrer IoT Hub-Instanz erforderlichen Informationen:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Sie müssen die Registrierungsanwendung Ihres Device Provisioning-Dienstclients unter Umständen zuerst über ein simuliertes Gerät und Testdiensteinstellungen anpassen. Sobald Ihre Anwendung ordnungsgemäß in der Testumgebung funktioniert, können Sie sie für das jeweilige Gerät erstellen und die ausführbare Datei in Ihr Geräteimage kopieren. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

An dieser Stelle werden der Device Provisioning- und der IoT Hub-Dienst wahrscheinlich im Portal ausgeführt. Wenn Sie die Einrichtung des Device Provisioning-Diensts abbrechen und/oder diese Tutorialreihe zu einem späteren Zeitpunkt abschließen möchten, sollten Sie sie deaktivieren, damit keine unnötigen Kosten entstehen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihren Device Provisioning-Dienst aus. Klicken Sie im oberen Bereich des Blatts **Alle Ressourcen** auf **Löschen**.  
1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihre IoT Hub-Instanz aus. Klicken Sie im oberen Bereich des Blatts **Alle Ressourcen** auf **Löschen**.  

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines plattformspezifischen Device Provisioning-Dienstclient-SDKs
> * Extrahieren der Sicherheitsartefakte
> * Erstellen der Geräteregistrierungssoftware

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie das Gerät auf IoT Hub bereitgestellt wird, indem Sie es beim Azure IoT Hub Device Provisioning-Dienst für die automatische Bereitstellung registrieren.

> [!div class="nextstepaction"]
> [Bereitstellen des Geräts auf IoT Hub](tutorial-provision-device-to-hub.md)