---
title: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein simuliertes X.509-Gerät für Azure IoT Hub mithilfe von C bereitstellen | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet. In dieser Schnellstartanleitung erstellen Sie ein simuliertes X.509-Gerät mithilfe des C-Geräte-SDK für Azure IoT Hub Device Provisioning Service und stellen es bereit.
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ae5a1708b56db45bec38a4482b5dfd936bdedd4a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901237"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Schnellstart: Bereitstellen eines simulierten X.509-Geräts mithilfe des Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In dieser Schnellstartanleitung erfahren Sie, wie Sie einen X.509-Gerätesimulator auf einem Windows-Entwicklungscomputer erstellen und ausführen. Sie konfigurieren dieses simulierte Gerät so, dass es einem IoT Hub über eine Registrierung bei einer Instanz des Device Provisioning-Diensts zugewiesen wird. Beispielcode aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) wird verwendet, um eine Startsequenz für das Gerät zu simulieren. Das Gerät wird anhand der Anmeldung beim Bereitstellungsdienst erkannt und dem IoT Hub zugeordnet.

Sollten Sie mit der automatischen Bereitstellung nicht vertraut sein, lesen Sie die Informationen unter [Konzepte für die automatische Bereitstellung](concepts-auto-provisioning.md). Vergewissern Sie sich außerdem, dass Sie die Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) ausgeführt haben, bevor Sie mit dieser Schnellstartanleitung fortfahren. 

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:
- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

In diesem Artikel werden individuelle Registrierungen veranschaulicht.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 oder höher mit aktivierter Workload [Desktopentwicklung mit C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Vorbereiten einer Entwicklungsumgebung für das Azure IoT C SDK

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird, das den Beispielcode für die X.509-Startsequenz enthält.

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die Voraussetzungen für Visual Studio (Visual Studio und die Workload „Desktopentwicklung mit C++“) müssen **vor** Beginn der Installation von `CMake` auf dem Computer installiert sein. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem.

2. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie den folgenden Befehl zum Klonen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus:
    
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

4. Das Codebeispiel verwendet ein X.509-Zertifikat für den Nachweis mittels X.509-Authentifizierung. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsclientplattform: Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs) aus. 

    Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```



<a id="portalenroll"></a>

## <a name="create-a-self-signed-x509-device-certificate"></a>Erstellen eines selbstsignierten X.509-Gerätezertifikats

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat, wobei folgende Punkte zu beachten sind:

* Selbstsignierte Zertifikate dienen nur zu Testzwecken und sollten nicht in der Produktion verwendet werden.
* Die Standardgültigkeitsdauer für ein selbstsigniertes Zertifikat beträgt ein Jahr.

Sie verwenden Beispielcode aus dem Azure IoT C SDK, um das Zertifikat zu erstellen, das mit dem individuellen Registrierungseintrag für das simulierte Gerät verwendet werden soll.

1. Starten Sie Visual Studio, und öffnen Sie die neue Projektmappendatei namens `azure_iot_sdks.sln`. Diese Projektmappendatei befindet sich im `cmake`-Ordner, den Sie zuvor im Stammverzeichnis des azure-iot-sdk-c-Git-Repositorys erstellt haben.

2. Wählen Sie im Visual Studio-Menü die Option **Erstellen** > **Projektmappe erstellen** aus, um alle Projekte in der Projektmappe zu erstellen.

3. Navigieren Sie im Visual Studio-Fenster *Projektmappen-Explorer* zum Ordner **Provision\_Tools**. Klicken Sie mit der rechten Maustaste auf das Projekt **dice\_device\_enrollment**, und wählen Sie **Als Startprojekt festlegen** aus. 

4. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Geben Sie im Ausgabefenster **i** für die individuelle Registrierung ein, wenn die entsprechende Aufforderung angezeigt wird. 

    Im Ausgabefenster wird ein lokal generiertes selbstsigniertes X.509-Zertifikat für Ihr simuliertes Gerät angezeigt. Kopieren Sie die Ausgabe zwischen **-----BEGIN CERTIFICATE-----** und dem ersten Vorkommen von **-----END CERTIFICATE-----** in die Zwischenablage, und schließen Sie dabei auch diese beiden Zeile mit ein. Sie benötigen nur das erste Zertifikat aus dem Ausgabefenster.
 
5. Speichern Sie das Zertifikat mithilfe eines Text-Editors in einer neuen Datei namens **_X509testcert.pem_** . 


## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Erstellen eines Geräteregistrierungseintrags im Portal

1. Melden Sie sich beim Azure-Portal an, klicken Sie im Menü auf der linken Seite auf die Schaltfläche **Alle Ressourcen**, und öffnen Sie Ihren Device Provisioning-Dienst.

2. Wählen Sie die Registerkarte **Registrierungen verwalten** aus, und klicken Sie dann oben auf die Schaltfläche **Individuelle Registrierung hinzufügen**. 

3. Geben Sie unter **Registrierung hinzufügen** die folgenden Informationen ein, und klicken Sie auf die Schaltfläche **Speichern**.

    - **Mechanismus**: Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
    - **Primäres Zertifikat (PEM- oder CER-Datei)** : Klicken Sie auf **Datei auswählen**, und wählen Sie die zuvor erstellte Zertifikatdatei „X509testcert.pem“ aus.
    - **IoT Hub-Geräte-ID**: Geben Sie **test-docs-cert-device** ein, um dem Gerät eine ID zuzuweisen.

      [![Hinzufügen einer individuellen Registrierung für den X.509-Nachweis im Portal](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Nach der erfolgreichen Registrierung wird Ihr X.509-Gerät als **riot-device-cert**auf der Registerkarte *Individuelle Registrierungen* in der Spalte *Registrierungs-ID* angezeigt. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulieren der ersten Startsequenz für das Gerät

Aktualisieren Sie in diesem Abschnitt den Beispielcode, um die Startsequenz des Geräts an Ihre Instanz des Device Provisioning-Diensts zu senden. Diese Startsequenz bewirkt, dass das Gerät, die erkannt und einem mit der Instanz des Device Provisioning-Diensts verknüpften IoT Hub zugewiesen wird.



1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Device Provisioning Service, und notieren Sie sich den Wert unter **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Navigieren Sie im Visual Studio-Fenster *Projektmappen-Explorer* zum Ordner **Provision\_Samples**. Erweitern Sie das Beispielprojekt mit dem Namen **prov\_dev\_client\_sample**. Erweitern Sie **Quelldateien**, und öffnen Sie **prov\_dev\_client\_sample.c**.

3. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten dargestellt auf `SECURE_DEVICE_TYPE_X509` anstelle von `SECURE_DEVICE_TYPE_TPM` festgelegt ist.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus. 

6. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Klicken Sie in der Eingabeaufforderung zum Neuerstellen des Projekts auf **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel dafür, wie der Gerätebereitstellungsclient erfolgreich hochfährt und sich mit der Instanz des Bereitstellungsdiensts verbindet, um IoT Hub-Informationen zu erhalten und sich zu registrieren:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device    
    ```

7. Navigieren Sie im Portal zu der mit Ihrem Bereitstellungsdienst verknüpften IoT Hub-Instanz, und klicken Sie auf die Registerkarte **IoT-Geräte**. Nach erfolgreicher Bereitstellung des simulierten X.509-Geräts auf dem Hub wird die dazugehörige Geräte-ID auf dem Blatt **IoT-Geräte** angezeigt, und der *STATUS* lautet **Aktiviert**. Unter Umständen müssen Sie oben auf die Schaltfläche **Aktualisieren** klicken. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiter verwenden und erkunden möchten, überspringen Sie die Bereinigung der in dieser Schnellstartanleitung erstellten Ressourcen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle erstellten Ressourcen zu löschen, die im Rahmen dieser Schnellstartanleitung erstellt wurden:

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihren Device Provisioning-Dienst aus. Öffnen Sie **Registrierungen verwalten** für Ihren Dienst, und klicken Sie dann auf die Registerkarte **Individuelle Registrierungen**. Wählen Sie die *REGISTRIERUNGS-ID* des Geräts aus, das Sie mit dieser Schnellstartanleitung registriert haben, und klicken Sie im oberen Bereich auf die Schaltfläche **Löschen**. 
1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihre IoT Hub-Instanz aus. Öffnen Sie **IoT-Geräte** für Ihren Hub, wählen Sie die *GERÄTE-ID* des Geräts aus, die Sie in diesem Schnellstart registriert haben, und klicken Sie dann im oberen Bereich auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie auf Ihrem Windows-Computer ein simuliertes X.509-Gerät erstellt und es im Portal mithilfe des Azure IoT Hub Device Provisioning-Diensts für Ihre IoT Hub-Instanz bereitgestellt. Informationen zum programmgesteuerten Registrieren Ihres X.509-Geräts finden Sie in der Schnellstartanleitung für die programmgesteuerte Registrierung von X.509-Geräten. 

> [!div class="nextstepaction"]
> [Registrieren von X.509-Geräten für den Azure IoT Hub Device Provisioning-Dienst per Java-Dienst-SDK](quick-enroll-device-x509-java.md)
