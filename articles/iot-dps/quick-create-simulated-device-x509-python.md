---
title: Bereitstellen eines simulierten X.509-Geräts für Azure IoT Hub mithilfe von Python | Microsoft-Dokumentation
description: 'Azure-Schnellstartanleitung: Erstellen und Bereitstellen eines simulierten X.509-Geräts mithilfe des Python-Geräte-SDK für IoT Hub Device Provisioning Service In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet.'
author: wesmc7777
ms.author: wesmc
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 12ccff3676cb35ac27126360dbcf5a1b9b3e131f
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914156"
---
# <a name="create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Erstellen und Bereitstellen eines simulierten X.509-Geräts mithilfe des Python-Geräte-SDKs für den IoT Hub Device Provisioning-Dienst
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Hier erfahren Sie Schritt für Schritt, wie Sie auf Ihrem Entwicklungscomputer unter dem Windows-Betriebssystem ein X.509-Gerät simulieren und ein Python-Codebeispiel verwenden, um für dieses simulierte Gerät eine Verbindung mit dem Device Provisioning-Dienst und mit Ihrer IoT Hub-Instanz herzustellen. 

Sollten Sie mit der automatischen Bereitstellung nicht vertraut sein, lesen Sie auch die Informationen unter [Konzepte für die automatische Bereitstellung](concepts-auto-provisioning.md). Vergewissern Sie sich außerdem, dass Sie die Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) ausgeführt haben, bevor Sie fortfahren. 

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:
- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

In diesem Artikel werden individuelle Registrierungen veranschaulicht.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung 

1. Stellen Sie sicher, dass [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 oder höher installiert und die Workload „Desktopentwicklung mit C++“ für Ihre Visual Studio-Installation aktiviert ist.

2. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter, und installieren Sie es.

3. Vergewissern Sie sich, dass `git` auf Ihrem Computer installiert ist und den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Unter [Git-Clienttools von Software Freedom Conservancy](https://git-scm.com/download/) finden Sie die neueste Version der zu installierenden `git`-Tools. Hierzu zählt auch die Befehlszeilen-App **Git Bash**, über die Sie mit Ihrem lokalen Git-Repository interagieren können. 

4. Öffnen Sie eine Eingabeaufforderung oder Git Bash. Klonen Sie das GitHub-Repository mit dem Codebeispiel für die Gerätesimulation.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. Erstellen Sie in Ihrer lokalen Kopie des GitHub-Repositorys einen Ordner für den CMake-Buildprozess. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Führen Sie den folgenden Befehl aus, um die Visual Studio-Lösung für den Bereitstellungsclient zu erstellen.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Erstellen eines selbstsignierten X.509-Gerätezertifikats und eines individuellen Registrierungseintrags

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat. Dabei sollten Sie unbedingt die folgenden Punkte berücksichtigen:

* Selbstsignierte Zertifikate dienen nur zu Testzwecken und sollten nicht in der Produktion verwendet werden.
* Die Standardgültigkeitsdauer für ein selbstsigniertes Zertifikat beträgt ein Jahr.

Sie verwenden Beispielcode aus dem Azure IoT C SDK, um das Zertifikat zu erstellen, das mit dem individuellen Registrierungseintrag für das simulierte Gerät verwendet werden soll.

1. Öffnen Sie die im Ordner *cmake* generierte Projektmappe `azure_iot_sdks.sln`, und erstellen Sie sie in Visual Studio.

2. Klicken Sie im Ordner **Provision\_Tools** mit der rechten Maustaste auf das Projekt **dice\_device\_enrollment**, und wählen Sie die Option **Als Startprojekt festlegen** aus. Führen Sie die Lösung aus. 

3. Geben Sie im Ausgabefenster `i` für die individuelle Registrierung ein, wenn die Aufforderung angezeigt wird. Im Ausgabefenster wird ein lokal generiertes X.509-Zertifikat für Ihr simuliertes Gerät angezeigt. 
    
    Kopieren Sie das erste Zertifikat in die Zwischenablage. Beginnen Sie mit dem ersten Vorkommen von:
    
        -----BEGIN CERTIFICATE----- 
        
    Beenden Sie den Kopiervorgang nach dem ersten Vorkommen von:
    
        -----END CERTIFICATE-----
        
    Achten Sie darauf, auch diese beiden Zeilen einzuschließen. 

    ![Anwendung „dice_device_enrollment“](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Erstellen Sie eine Datei mit dem Namen **_X509testcertificate.pem_** auf Ihrem Windows-Computer, öffnen Sie sie in einem Editor Ihrer Wahl, und kopieren Sie den Inhalt der Zwischenablage in diese Datei. Speichern Sie die Datei . 

5. Melden Sie sich am Azure-Portal an, klicken Sie im Menü auf der linken Seite auf die Schaltfläche **Alle Ressourcen**, und öffnen Sie Ihren Provisioning-Dienst.

6. Wählen Sie auf dem Zusammenfassungsblatt des Device Provisioning-Diensts die Option **Registrierungen verwalten** aus. Wählen Sie die Registerkarte **Individuelle Registrierungen** aus, und klicken Sie oben auf die Schaltfläche **Individuelle Registrierung hinzufügen**. 

7. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:
   - Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
   - Klicken Sie unter *Primary certificate .pem or .cer file* (PEM- oder CER-Datei des primären Zertifikats) auf *Datei auswählen*, und wählen Sie die in den vorherigen Schritten erstellte Zertifikatdatei **X509testcertificate.pem** aus.
   - Optional können Sie die folgenden Informationen angeben:
     - Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
     - Geben Sie eine eindeutige Geräte-ID ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben. 
     - Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
   - Klicken Sie abschließend auf die Schaltfläche **Speichern**. 

     [![Hinzufügen einer individuellen Registrierung für den X.509-Nachweis im Portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Nach der Registrierung wird Ihr X.509-Gerät auf der Registerkarte *Individuelle Registrierungen* in der Spalte *Registrierungs-ID* als **riot-device-cert** angezeigt. 

## <a name="simulate-the-device"></a>Simulieren des Geräts

1. Klicken Sie auf dem Zusammenfassungsblatt des Device Provisioning-Diensts auf **Übersicht**. Notieren Sie sich den _ID-Bereich_ und den _globalen Dienstendpunkt_.

    ![Dienstinformationen](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Laden Sie [Python 2.x oder 3.x](https://www.python.org/downloads/) herunter, und installieren Sie es. Stellen Sie je nach Einrichtung sicher, dass die 32-Bit- bzw. die 64-Bit-Installation verwendet wird. Fügen Sie Python Ihren plattformspezifischen Umgebungsvariablen hinzu, wenn Sie während der Installation dazu aufgefordert werden. Bei Verwendung von Python 2.x müssen Sie ggf. [*pip*, das Python-Paketverwaltungssystem, installieren oder upgraden](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Installieren Sie bei Verwendung von Windows auch [Visual C++ Redistributable für Visual Studio 2015](https://www.microsoft.com/download/confirmation.aspx?id=48145). Die weitervertreibbare Komponente wird von den PIP-Paketen zum Laden/Ausführen der C-DLLs benötigt.

3. Eine Anleitung für die Erstellung der Python-Pakete finden Sie [hier](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md).

   > [!NOTE]
   > Bei Verwendung von `pip` muss auch das Paket `azure-iot-provisioning-device-client` installiert werden.

4. Navigieren Sie zum Beispielordner.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. Bearbeiten Sie mithilfe Ihrer Python-IDE das Python-Skript **provisioning\_device\_client\_sample.py**. Legen Sie die Variablen _GLOBAL\_PROV\_URI_ und _ID\_SCOPE_ auf die zuvor notierten Werte fest.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Führen Sie das Beispiel aus. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. Die Anwendung stellt eine Verbindung her, registriert das Gerät und zeigt eine Erfolgsmeldung an.

    ![Erfolgreiche Registrierung](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. Navigieren Sie im Portal zu dem IoT Hub, der mit Ihrem Provisioning-Dienst verknüpft ist, und öffnen Sie das Blatt **Device Explorer**. Nach der erfolgreichen Bereitstellung des simulierten X.509-Geräts auf dem Hub wird die dazugehörige Geräte-ID auf dem Blatt **Device Explorer** mit dem *STATUS* **Aktiviert** angezeigt. Falls das Blatt bereits vor dem Ausführen der Beispielgeräteanwendung geöffnet war, müssen Sie unter Umständen im oberen Bereich auf die Schaltfläche **Aktualisieren** klicken. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiter verwenden und erkunden möchten, überspringen Sie die Bereinigung der in dieser Schnellstartanleitung erstellten Ressourcen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle erstellten Ressourcen zu löschen, die im Rahmen dieser Schnellstartanleitung erstellt wurden:

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
2. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihren Device Provisioning-Dienst aus. Öffnen Sie das Blatt **Registrierungen verwalten** für Ihren Dienst, und klicken Sie dann auf die Registerkarte **Individuelle Registrierungen**. Wählen Sie die *REGISTRIERUNGS-ID* des Geräts aus, das Sie mit dieser Schnellstartanleitung registriert haben, und klicken Sie im oberen Bereich auf die Schaltfläche **Löschen**. 
3. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihre IoT Hub-Instanz aus. Öffnen Sie das Blatt **IoT-Geräte** für Ihren Hub, wählen Sie die *GERÄTE-ID* des Geräts aus, die Sie in dieser Schnellstartanleitung registriert haben, und klicken Sie dann im oberen Bereich auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie auf Ihrem Windows-Computer ein simuliertes X.509-Gerät erstellt und es im Portal mithilfe des Azure IoT Hub Device Provisioning-Diensts für Ihre IoT Hub-Instanz bereitgestellt. Informationen zum programmgesteuerten Registrieren Ihres X.509-Geräts finden Sie in der Schnellstartanleitung für die programmgesteuerte Registrierung von X.509-Geräten. 

> [!div class="nextstepaction"]
> [Registrieren von X.509-Geräten für den Azure IoT Hub Device Provisioning-Dienst per Java-Dienst-SDK](quick-enroll-device-x509-python.md)
