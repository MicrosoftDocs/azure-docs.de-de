---
title: Schnellstart – Bereitstellen eines simulierten X.509-Geräts für Azure IoT Hub mithilfe von Java
description: 'Azure-Schnellstartanleitung: Erstellen und Bereitstellen eines simulierten X.509-Geräts mithilfe des Java-Geräte-SDK für IoT Hub Device Provisioning Service (DPS). In dieser Schnellstartanleitung werden individuelle Registrierungen verwendet.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 2ed458ee0267a65bb7551940fc522509dc310311
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966614"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Schnellstart: Erstellen und Bereitstellen eines simulierten X.509-Geräts mithilfe des Java-Geräte-SDKs für den IoT Hub Device Provisioning-Dienst

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In dieser Schnellstartanleitung erstellen Sie ein simuliertes X.509-Gerät auf einem Windows-Computer. Sie verwenden Java-Beispielcode für das Gerät, um dieses simulierte Gerät mit Ihrem IoT-Hub zu verbinden, indem eine einzelne Registrierung beim Device Provisioning Service (DPS) verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

- Mit Konzepten für die [Bereitstellung](about-iot-dps.md#provisioning-process) vertraut sein.
- Ausführung der Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md).
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung 

1. Vergewissern Sie sich, dass das [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) auf Ihrem Computer installiert ist.

2. Laden Sie [Maven](https://maven.apache.org/install.html) herunter, und installieren Sie es.

3. Vergewissern Sie sich, dass Git auf Ihrem Computer installiert ist und den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Unter [Git-Clienttools von Software Freedom Conservancy](https://git-scm.com/download/) finden Sie die neueste Version der zu installierenden `git`-Tools. Hierzu zählt auch die Befehlszeilen-App **Git Bash**, über die Sie mit Ihrem lokalen Git-Repository interagieren können. 

4. Öffnen Sie eine Eingabeaufforderung. Klonen Sie das GitHub-Repository mit dem Codebeispiel für die Gerätesimulation:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Navigieren Sie zum Stammverzeichnis `azure-iot-sdk-`java`, und erstellen Sie das Projekt, um alle erforderlichen Pakete herunterzuladen.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navigieren Sie zum Zertifikatgeneratorprojekt, und erstellen Sie es. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Erstellen eines selbstsignierten X.509-Gerätezertifikats und eines individuellen Registrierungseintrags

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat, wobei Folgendes zu beachten ist:

* Selbstsignierte Zertifikate dienen nur zu Testzwecken und sollten nicht in der Produktion verwendet werden.
* Die Standardgültigkeitsdauer für ein selbstsigniertes Zertifikat beträgt ein Jahr.

Sie verwenden Beispielcode aus dem [Azure IoT SDK für Java](https://github.com/Azure/azure-iot-sdk-java.git), um das Zertifikat zu erstellen, das mit dem individuellen Registrierungseintrag für das simulierte Gerät verwendet werden soll.

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

- [Registrierungsgruppen:](concepts-service.md#enrollment-group) Werden zum Registrieren mehrerer verwandter Geräte verwendet.
- [Individuelle Registrierungen](concepts-service.md#individual-enrollment): Werden zum Registrieren eines einzelnen Geräts verwendet.

In diesem Artikel werden individuelle Registrierungen veranschaulicht.

1. Navigieren Sie mithilfe der Befehlszeile aus den vorherigen Schritten zum Ordner `target`, und führen Sie die im vorherigen Schritt erstellte JAR-Datei aus.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Beantworten Sie die Frage _Do you want to input common name_ (Möchten Sie einen allgemeinen Namen eingeben?) mit **N**. Kopieren Sie die Ausgabe von `Client Cert` (von *-----BEGIN CERTIFICATE-----* bis einschließlich *-----END CERTIFICATE-----* ) in die Zwischenablage.

   ![Generator für individuelles Zertifikat](./media/java-quick-create-simulated-device-x509/individual.png)

3. Erstellen Sie auf Ihrem Windows-Computer eine Datei mit dem Namen **_X509individual.pem_** , öffnen Sie sie in einem Editor Ihrer Wahl, und kopieren Sie den Inhalt der Zwischenablage in diese Datei. Speichern Sie die Datei, und schließen Sie den Editor.

4. Beantworten Sie an der Eingabeaufforderung die Frage _Do you want to input Verification Code_ (Möchten Sie einen Prüfcode eingeben?) mit **N**, und lassen Sie die Programmausgabe für später geöffnet. Die Werte `Client Cert` und `Client Cert Private Key` werden später zur Verwendung im nächsten Abschnitt kopiert.

5. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, wählen Sie im Menü links die Schaltfläche **Alle Ressourcen** aus, und öffnen Sie Ihre Device Provisioning Service-Instanz.

6. Wählen Sie im Menü von Device Provisioning Service die Option **Registrierungen verwalten** aus. Wählen Sie die Registerkarte **Individuelle Registrierungen** und dann oben die Schaltfläche **Individuelle Registrierung hinzufügen** aus. 

7. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:
   - Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
   - Wählen Sie unter *Primary certificate .pem or .cer file* (PEM- oder CER-Datei des primären Zertifikats) die Option *Datei auswählen* und anschließend die in den vorherigen Schritten erstellte Zertifikatdatei **X509individual.pem** aus.  
   - Optional können Sie die folgenden Informationen angeben:
     - Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
     - Geben Sie eine eindeutige Geräte-ID ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben. 
     - Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
     - Klicken Sie abschließend auf die Schaltfläche **Speichern**. 

     [![Hinzufügen einer individuellen Registrierung für den X.509-Nachweis im Portal](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Nach der Registrierung wird Ihr X.509-Gerät auf der Registerkarte *Individuelle Registrierungen* in der Spalte *Registrierungs-ID* als **microsoftriotcore** angezeigt. 



## <a name="simulate-the-device"></a>Simulieren des Geräts

1. Wählen Sie im Device Provisioning Service-Menü die Option **Übersicht** aus, und notieren Sie sich den _ID-Bereich_ und den _globalen Endpunkt für den Bereitstellungsdienst_.

    ![Dienstinformationen](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Öffnen Sie eine Eingabeaufforderung. Navigieren Sie zum Beispielprojektordner des Java SDK-Repositorys.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Geben Sie in Ihrem Code den Bereitstellungsdienst und die X.509-Identitätsinformationen ein. Diese werden vor der Geräteregistrierung im Rahmen der automatischen Bereitstellung als Nachweis für das simulierte Gerät verwendet:

   - Bearbeiten Sie die Datei `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, um die zuvor notierten Werte für den _ID-Bereich_ und den _globalen Endpunkt für den Bereitstellungsdienst_ einzufügen. Fügen Sie außerdem die im vorherigen Abschnitt notierten Werte für _Client Cert_ und _Client Cert Private Key_ ein.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Verwenden Sie beim Kopieren/Einfügen Ihres Zertifikats und Ihres privaten Schlüssels das folgende Format:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Erstellen Sie das Beispiel. Navigieren Sie zum Ordner `target`, und führen Sie die erstellte JAR-Datei aus.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Navigieren Sie im Azure-Portal zu der IoT Hub-Instanz, die mit Ihrem Bereitstellungsdienst verknüpft ist, und öffnen Sie das Blatt **Device Explorer**. Nach erfolgreicher Bereitstellung des simulierten X.509-Geräts auf dem Hub wird die dazugehörige Geräte-ID auf dem Blatt **Device Explorer** angezeigt, und der *STATUS* lautet **Aktiviert**.  Falls das Blatt bereits vor dem Ausführen der Beispielgeräteanwendung geöffnet war, müssen Sie unter Umständen im oberen Bereich auf die Schaltfläche **Aktualisieren** klicken. 

    ![Geräteregistrierung bei der IoT Hub-Instanz](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiterhin verwenden und erkunden möchten, überspringen Sie die Bereinigung der in diesem Schnellstart erstellten Ressourcen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Schnellstarts erstellt wurden.

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
2. Wählen Sie im Azure-Portal im Menü links **Alle Ressourcen** und dann Ihren Gerätebereitstellungsdienst aus. Öffnen Sie das Blatt **Registrierungen verwalten** für Ihren Dienst, und wählen Sie die Registerkarte **Individuelle Registrierungen** aus. Aktivieren Sie das Kontrollkästchen *REGISTRIERUNGS-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie oben im Bereich die Schaltfläche **Löschen** aus. 
3. Wählen Sie im linken Menü im Azure-Portal **Alle Ressourcen** und dann Ihren IoT-Hub aus. Öffnen Sie das Blatt **IoT-Geräte** für Ihren Hub, aktivieren Sie das Kontrollkästchen *GERÄTE-ID* für das Gerät, das Sie in diesem Schnellstart registriert haben, und wählen Sie dann oben im Bereich die Schaltfläche **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein simuliertes X.509-Gerät auf Ihrem Windows-Computer erstellt. Sie haben die Registrierung in Azure IoT Hub Device Provisioning Service konfiguriert und das Gerät automatisch in Ihrer IoT Hub-Instanz bereitgestellt. Informationen zum programmgesteuerten Registrieren Ihres X.509-Geräts finden Sie im Schnellstart für die programmgesteuerte Registrierung von X.509-Geräten. 

> [!div class="nextstepaction"]
> [Azure-Schnellstart: Registrieren von X.509-Geräten bei Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)