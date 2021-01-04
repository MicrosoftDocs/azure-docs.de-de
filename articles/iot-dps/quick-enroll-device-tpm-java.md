---
title: Schnellstart – Registrieren eines TPM-Geräts bei Azure Device Provisioning Service mit Java
description: 'Schnellstart: Registrieren eines TPM-Geräts bei Azure IoT Hub Device Provisioning Service (DPS) mithilfe des Java-Dienst-SDK In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463047"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Schnellstart: Registrieren eines TPM-Geräts bei IoT Hub Device Provisioning Service per Java Service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In dieser Schnellstartanleitung erfahren Sie, wie Sie programmgesteuert eine individuelle Registrierung für ein simuliertes TPM-Gerät in Azure IoT Hub Device Provisioning Service erstellen, indem Sie das Java Service SDK und eine Java-Beispielanwendung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ausführung der Schritte unter [Schnellstart: Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md).
- Ausführung der Schritte unter [Lesen kryptografischer Schlüssel vom TPM-Gerät](quick-create-simulated-device.md#simulatetpm).
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) In dieser Schnellstartanleitung wird weiter unten das [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) installiert. Es funktioniert sowohl unter Windows als auch unter Linux. In dieser Schnellstartanleitung wird Windows verwendet.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung 

1. Vergewissern Sie sich, dass das [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) auf Ihrem Computer installiert ist. 

2. Richten Sie Umgebungsvariablen für Ihre Java-Installation ein. Die Variable `PATH` sollte den vollständigen Pfad zum Verzeichnis *jdk1.8.x\bin* enthalten. Falls dies die erste Java-Installation Ihres Computers ist, sollten Sie eine neue Umgebungsvariable mit dem Namen `JAVA_HOME` erstellen und dafür auf den vollständigen Pfad zum Verzeichnis *jdk1.8.x* verweisen. Auf einem Windows-Computer befindet sich dieses Verzeichnis im Ordner *C:\\Programme\\Java\\* . Sie können Umgebungsvariablen erstellen oder bearbeiten, indem Sie in der **Systemsteuerung** Ihres Windows-Computers nach **Systemumgebungsvariablen bearbeiten** suchen. 

   Sie können überprüfen, ob die Java-Einrichtung auf Ihrem Computer erfolgreich war, indem Sie im Befehlsfenster den folgenden Befehl ausführen:

    ```cmd\sh
    java -version
    ```

3. Laden Sie [Maven 3](https://maven.apache.org/download.cgi) auf Ihren Computer herunter, und extrahieren Sie die Datei. 

4. Bearbeiten Sie die Umgebungsvariable `PATH` so, dass sie auf den Ordner *apache-maven-3.x.x\\bin* in dem Ordner verweist, in den Sie Maven extrahiert haben. Sie können sicherstellen, dass die Installation von Maven erfolgreich war, indem Sie diesen Befehl in Ihrem Befehlsfenster ausführen:

    ```cmd\sh
    mvn --version
    ```

5. Stellen Sie sicher, dass [git](https://git-scm.com/download/) auf Ihrem Computer installiert ist und der Umgebungsvariablen `PATH` hinzugefügt wurde. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Herunterladen und Ändern des Java-Beispielcodes

In diesem Abschnitt wird veranschaulicht, wie Sie die Bereitstellungsdetails Ihres TPM-Geräts dem Beispielcode hinzufügen. 

1. Öffnen Sie eine Eingabeaufforderung. Klonen Sie das GitHub-Repository für das Codebeispiel zur Geräteregistrierung, indem Sie das [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) verwenden:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigieren Sie im heruntergeladenen Quellcode zum Beispielordner **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** . Öffnen Sie die Datei **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** in einem Editor Ihrer Wahl, und fügen Sie die folgenden Details hinzu:

   1. Fügen Sie das Element `[Provisioning Connection String]` für Ihren Provisioning-Dienst wie folgt aus dem Portal hinzu:
       1. Navigieren Sie zu Ihrem Provisioning-Dienst im [Azure-Portal](https://portal.azure.com). 
       2. Öffnen Sie **Freigegebene Zugriffsrichtlinien**, und wählen Sie eine Richtlinie aus, die über die Berechtigung *EnrollmentWrite* verfügt.
       3. Kopieren Sie die **Primärschlüssel-Verbindungszeichenfolge**. 

           ![Abrufen der Verbindungszeichenfolge für die Bereitstellung aus dem Portal](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. Ersetzen Sie in der Beispielcodedatei **_ServiceEnrollmentSample.java_** das Element `[Provisioning Connection String]` durch die **Primärschlüssel-Verbindungszeichenfolge**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Fügen Sie die Details des TPM-Geräts hinzu:
       1. Rufen Sie die *Registrierungs-ID* und den *TPM Endorsement Key* für eine TPM-Gerätesimulation ab, indem Sie die Schritte ausführen, die zum Abschnitt [Simulate TPM device](quick-create-simulated-device.md#simulatetpm) (Simulieren des TPM-Geräts) führen.
       2. Verwenden Sie die **_Registrierungs-ID_** und den **_Endorsement Key_** aus der Ausgabe des vorherigen Schritts, um die Elemente `[RegistrationId]` und `[TPM Endorsement Key]` in der Beispielcodedatei **_ServiceEnrollmentSample.java_** zu ersetzen:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Optional können Sie Ihren Provisioning-Dienst über den Beispielcode konfigurieren:
      - Führen Sie diese Schritte aus, um diese Konfiguration dem Beispiel hinzuzufügen:
        1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum IoT Hub, der mit Ihrem Provisioning-Dienst verknüpft ist. Öffnen Sie die Registerkarte **Übersicht** für den Hub, und kopieren Sie den **Hostnamen**. Weisen Sie diesen **Hostnamen** dem Parameter *IOTHUB_HOST_NAME* zu.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Weisen Sie dem Parameter *DEVICE_ID* einen Anzeigenamen zu, und behalten Sie für *PROVISIONING_STATUS* den Standardwert *ENABLED* bei. 
    
      - ODER: Falls Sie Ihren Provisioning-Dienst nicht konfigurieren möchten, sollten Sie die folgenden Anweisungen in der Datei _ServiceEnrollmentSample.java_ auskommentieren oder löschen:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Sehen Sie sich den Beispielcode näher an. Er bewirkt, dass eine individuelle Registrierung eines TPM-Geräts erstellt, aktualisiert, abgefragt und gelöscht wird. Um zu überprüfen, ob die Registrierung im Portal erfolgreich war, können Sie die folgenden Codezeilen am Ende der Datei _ServiceEnrollmentSample.java_ vorübergehend auskommentieren:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Speichern Sie die Datei _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Erstellen und Ausführen des Java-Beispielcodes

1. Öffnen Sie ein Befehlsfenster, und navigieren Sie zum Ordner **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** .

2. Erstellen Sie den Beispielcode mit diesem Befehl:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Mit diesem Befehl wird das Maven-Paket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) auf Ihren Computer heruntergeladen. Das Paket enthält die Binärdateien für das [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/), das mit dem Beispielcode erstellt werden soll. 

3. Führen Sie das Beispiel aus, indem Sie im Befehlsfenster diese Befehle verwenden:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Verfolgen Sie im Ausgabefenster, ob die Registrierung erfolgreich ist. 

5. Navigieren Sie im Azure-Portal zu Ihrem Provisioning-Dienst. Wählen Sie **Registrierungen verwalten** und dann die Registerkarte **Individuelle Registrierungen** aus. Beachten Sie, dass die *Registrierungs-ID* Ihres simulierten TPM-Geräts jetzt aufgeführt ist. 

    ![Überprüfen der erfolgreichen TPM-Registrierung im Portal](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie planen, sich das Beispiel des Java-Diensts näher anzusehen, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Schnellstarts erstellt wurden.

1. Schließen Sie das Ausgabefenster des Java-Beispiels auf Ihrem Computer.
1. Schließen Sie ggf. das TPM-Simulatorfenster, das Sie erstellt haben, um Ihr TPM-Gerät zu simulieren.
1. Navigieren Sie im Azure-Portal zu Ihrem Device Provisioning Service, klicken Sie auf **Registrierungen verwalten**, und klicken Sie anschließend auf die Registerkarte **Individuelle Registrierungen**. Aktivieren Sie das Kontrollkästchen der *Registrierungs-ID* für den Registrierungseintrag, den Sie in diesem Schnellstart erstellt haben, und klicken Sie oben im Bereich auf die Schaltfläche **Löschen**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie ein simuliertes TPM-Gerät für Ihren Gerätebereitstellungsdienst registriert. Ausführlichere Informationen zur Gerätebereitstellung finden Sie im Tutorial zur Einrichtung des Device Provisioning-Diensts über das Azure-Portal. 

> [!div class="nextstepaction"]
> [Tutorials für den Azure IoT Hub Device Provisioning-Dienst](./tutorial-set-up-cloud.md)