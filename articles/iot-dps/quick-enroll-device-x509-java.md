---
title: 'Schnellstart: Registrieren von X.509-Geräten bei Azure Device Provisioning Service mithilfe von Java'
description: In dieser Schnellstartanleitung werden sowohl Gruppenregistrierungen als auch individuelle Registrierungen verwendet. In dieser Schnellstartanleitung registrieren Sie X.509-Geräte mithilfe von Java in Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 8591220eb80d9c60ed7873be546007449ab46f89
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463032"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Schnellstart: Registrieren von X.509-Geräten für den Device Provisioning-Dienst mit Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In dieser Schnellstartanleitung verwenden Sie Java, um eine Gruppe mit simulierten X.509-Geräten programmgesteuert für den Azure IoT Hub Device Provisioning-Dienst zu registrieren. Geräte werden für eine Instanz des Device Provisioning-Diensts registriert, indem eine Registrierungsgruppe oder eine individuelle Registrierung erstellt wird. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie beide Arten von Registrierungen erstellen, indem Sie das Java-Dienst-SDK und eine Java-Beispielanwendung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ausführung der Schritte unter [Schnellstart: Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md).
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) In dieser Schnellstartanleitung wird weiter unten das [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) installiert. Es funktioniert sowohl unter Windows als auch unter Linux. In dieser Schnellstartanleitung wird Windows verwendet.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Herunterladen und Ändern des Java-Beispielcodes

In diesem Abschnitt wird ein selbstsigniertes X.509-Zertifikat verwendet, wobei folgende Punkte zu beachten sind:

* Selbstsignierte Zertifikate dienen nur zu Testzwecken und sollten nicht in der Produktion verwendet werden.
* Die Standardgültigkeitsdauer für ein selbstsigniertes Zertifikat beträgt ein Jahr.

In den folgenden Schritten wird gezeigt, wie Sie die Bereitstellungsdetails Ihres X.509-Geräts dem Beispielcode hinzufügen. 

1. Öffnen Sie eine Eingabeaufforderung. Klonen Sie das GitHub-Repository für das Codebeispiel zur Geräteregistrierung, indem Sie das [Java-Dienst-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) verwenden:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigieren Sie im heruntergeladenen Quellcode zum Beispielordner **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** . Öffnen Sie die Datei **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** in einem Editor Ihrer Wahl, und fügen Sie die folgenden Details hinzu:

    1. Fügen Sie das Element `[Provisioning Connection String]` für Ihren Provisioning-Dienst wie folgt aus dem Portal hinzu:
        1. Navigieren Sie zu Ihrem Provisioning-Dienst im [Azure-Portal](https://portal.azure.com). 
        2. Öffnen Sie **Freigegebene Zugriffsrichtlinien**, und wählen Sie eine Richtlinie aus, die über die Berechtigung *EnrollmentWrite* verfügt.
        3. Kopieren Sie die **Primärschlüssel-Verbindungszeichenfolge**. 

            ![Abrufen der Verbindungszeichenfolge für die Bereitstellung aus dem Portal](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. Ersetzen Sie in der Beispielcodedatei **_ServiceEnrollmentGroupSample.java_** das Element `[Provisioning Connection String]` durch die **Primärschlüssel-Verbindungszeichenfolge**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Fügen Sie das Stammzertifikat für die Gruppe mit den Geräten hinzu. Verwenden Sie das Tool _X.509 Certificate Generator_: Gehen Sie wie folgt vor, wenn Sie ein Beispielstammzertifikat benötigen:
        1. Navigieren Sie in einem Befehlsfenster zum Ordner **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_** .
        2. Erstellen Sie das Tool, indem Sie den folgenden Befehl ausführen:

            ```cmd\sh
            mvn clean install
            ```

        4. Führen Sie das Tool mit den folgenden Befehlen aus:

            ```cmd\sh
            cd target
            java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
            ```

        5. Wenn die Aufforderung angezeigt wird, können Sie optional einen _allgemeinen Namen_ für Ihre Zertifikate eingeben.
        6. Das Tool erstellt lokal ein Clientzertifikat (**Client Cert**), den privaten Schlüssel für das Clientzertifikat (**Client Cert Private Key**) und das Stammzertifikat (**Root Cert**).
        7. Kopieren Sie das **Stammzertifikat**, einschließlich der Zeilen **_-----BEGIN CERTIFICATE-----_** und **_-----END CERTIFICATE-----_** . 
        8. Weisen Sie den Wert des **Stammzertifikats** wie hier dargestellt dem Parameter **PUBLIC_KEY_CERTIFICATE_STRING** zu:

            ```Java
            private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
            ```

        9. Schließen Sie das Befehlsfenster, oder geben Sie **n** ein, wenn Sie zur Eingabe des *Verifizierungscodes* aufgefordert werden. 
 
    3. Optional können Sie Ihren Provisioning-Dienst über den Beispielcode konfigurieren:
        - Führen Sie diese Schritte aus, um diese Konfiguration dem Beispiel hinzuzufügen:
            1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum IoT Hub, der mit Ihrem Provisioning-Dienst verknüpft ist. Öffnen Sie die Registerkarte **Übersicht** für den Hub, und kopieren Sie den **Hostnamen**. Weisen Sie diesen **Hostnamen** dem Parameter *IOTHUB_HOST_NAME* zu.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Weisen Sie dem Parameter *DEVICE_ID* einen Anzeigenamen zu, und behalten Sie für *PROVISIONING_STATUS* den Standardwert *ENABLED* bei. 

        - ODER: Falls Sie Ihren Provisioning-Dienst nicht konfigurieren möchten, sollten Sie die folgenden Anweisungen in der Datei _ServiceEnrollmentGroupSample.java_ auskommentieren oder löschen:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Sehen Sie sich den Beispielcode näher an. Er bewirkt, dass eine Gruppenregistrierung für X.509-Geräte erstellt, aktualisiert, abgefragt und gelöscht wird. Um zu überprüfen, ob die Registrierung im Portal erfolgreich war, können Sie die folgenden Codezeilen am Ende der Datei _ServiceEnrollmentGroupSample.java_ vorübergehend auskommentieren:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Speichern Sie die Datei _ServiceEnrollmentGroupSample.java_. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Erstellen und Ausführen der Beispielgruppenregistrierung

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

Bei diesem Verfahren wird eine Registrierungsgruppe verwendet. Im nächsten Abschnitt wird dann eine individuelle Registrierung genutzt.

1. Öffnen Sie ein Befehlsfenster, und navigieren Sie zum Ordner **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** .

2. Erstellen Sie den Beispielcode mit diesem Befehl:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Mit diesem Befehl wird das Maven-Paket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) auf Ihren Computer heruntergeladen. Das Paket enthält die Binärdateien für das Java-Dienst-SDK, das mit dem Code erstellt werden soll. Wenn Sie im vorherigen Abschnitt das Tool _X.509-Zertifikatgenerator_ ausgeführt haben, wurde dieses Paket bereits auf Ihren Computer heruntergeladen. 

3. Führen Sie das Beispiel aus, indem Sie im Befehlsfenster diese Befehle verwenden:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Verfolgen Sie im Ausgabefenster, ob die Registrierung erfolgreich ist.

5. Navigieren Sie im Azure-Portal zu Ihrem Provisioning-Dienst. Klicken Sie auf **Registrierungen verwalten**. Beachten Sie, dass Ihre Gruppe mit X.509-Geräten unter der Registerkarte **Registrierungsgruppen** mit einem automatisch generierten *GRUPPENNAMEN* angezeigt wird. 

    ![Überprüfen der erfolgreichen X.509-Registrierung im Portal](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Änderungen zur Registrierung eines einzelnen X.509-Geräts

Ändern Sie zum Registrieren eines einzelnen X.509-Geräts den Beispielcode für die *individuelle Registrierung*, der unter [Registrieren eines TPM-Geräts für den IoT Hub Device Provisioning-Dienst per Java-Dienst-SDK](quick-enroll-device-tpm-java.md#javasample) verwendet wurde, wie folgt:

1. Kopieren Sie den *allgemeinen Namen* Ihres X.509-Clientzertifikats in die Zwischenablage. Wenn Sie das Tool _X.509 Certificate Generator_ wie im [vorherigen Codeabschnitt](#javasample) gezeigt verwenden möchten, können Sie entweder einen _allgemeinen Namen_ für Ihr Zertifikat eingeben oder den Standardwert **microsoftriotcore** verwenden. Verwenden Sie diesen **allgemeinen Namen** als Wert für die Variable *REGISTRATION_ID*. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Benennen Sie die Variable *TPM_ENDORSEMENT_KEY* in *PUBLIC_KEY_CERTIFICATE_STRING* um. Kopieren Sie Ihr Clientzertifikat bzw. das **Client Cert** aus der Ausgabe des Tools _X.509 Certificate Generator_ als Wert für die Variable *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. Ersetzen Sie in der Funktion **main** die Zeile `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` durch Folgendes, um das X.509-Clientzertifikat zu verwenden:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Führen Sie das Speichern, Erstellen und Ausführen der Beispieldatei für die *individuelle Registrierung* durch, indem Sie die Schritte im Abschnitt [Build and run the sample code for individual enrollment](quick-enroll-device-tpm-java.md#runjavasample) (Erstellen und Ausführen des Beispielcodes für die individuelle Registrierung) befolgen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie planen, sich das Beispiel des Java-Diensts näher anzusehen, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Schnellstarts erstellt wurden.

1. Schließen Sie das Ausgabefenster des Java-Beispiels auf Ihrem Computer.
1. Schließen Sie das Fenster _X509 Cert Generator_ auf Ihrem Computer.
1. Navigieren Sie im Azure-Portal zu Ihrem Gerätebereitstellungsdienst, wählen Sie **Registrierungen verwalten** aus, und wählen Sie dann die Registerkarte **Registrierungsgruppen** aus. Aktivieren Sie das Kontrollkästchen des *Gruppennamens* für die X.509-Geräte, die Sie in diesem Schnellstart registriert haben, und klicken Sie oben im Bereich auf die Schaltfläche **Löschen**.  

## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie eine simulierte Gruppe von X.509-Geräten für Ihren Gerätebereitstellungsdienst registriert. Ausführlichere Informationen zur Gerätebereitstellung finden Sie im Tutorial zur Einrichtung des Device Provisioning-Diensts über das Azure-Portal. 

> [!div class="nextstepaction"]
> [Tutorials für den Azure IoT Hub Device Provisioning-Dienst](./tutorial-set-up-cloud.md)