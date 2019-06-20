---
title: Tutorial für X.509-Sicherheit in Azure IoT Hub | Microsoft-Dokumentation
description: Erste Schritte mit der X.509-basierten Sicherheit in Ihrem Azure IoT Hub in einer simulierten Umgebung.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 0bfb66f54ec09e86b46a41499211e93a0083e8d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779924"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub

In diesem Tutorial werden die Schritte simuliert, mit denen Sie Ihren Azure IoT Hub mit der *X.509-Zertifikatauthentifizierung* sichern. Zur Veranschaulichung zeigen wir Ihnen die Verwendung des Open Source-Tools OpenSSL zum lokalen Erstellen von Zertifikaten auf Ihrem Windows-Computer. Sie sollten dieses Tutorial nur zu Testzwecken verwenden. Für Produktionsumgebungen sollten Sie die Zertifikate von einer *Stammzertifizierungsstelle (Root Certificate Authority, CA)* erwerben.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen:

* Sie haben in Ihrem Azure-Abonnement einen IoT Hub erstellt. Ausführliche Schrittbeschreibungen finden Sie unter [Erstellen eines IoT Hubs über das Portal](iot-hub-create-through-portal.md).

* Sie haben auf Ihrem Computer entweder [Visual Studio 2017 oder Visual Studio 2019](https://www.visualstudio.com/vs/) installiert.

## <a name="get-x509-ca-certificates"></a>Abrufen von X.509-CA-Zertifikaten

Die auf dem X.509-Zertifikat basierende Sicherheit in IoT Hub erfordert, dass Sie mit einer [X.509-Zertifikatkette](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) beginnen, die sowohl das Stammzertifizierungsstellen-Zertifikat als auch alle Zwischenzertifikate bis zum untergeordneten Zertifikat enthält.

Sie haben folgende Möglichkeiten, Ihre Zertifikate abzurufen:

* Erwerben Sie X.509-Zertifikate von einer *Stammzertifizierungsstelle (CA)* . Dies wird für die Produktionsumgebung empfohlen.

* Erstellen Sie eigene X.509-Zertifikate mithilfe eines Drittanbietertools wie [OpenSSL](https://www.openssl.org/). Dies ist gut für Test- und Entwicklungszwecke. Unter [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) finden Sie Informationen zum Generieren von Test-ZS-Zertifikaten mithilfe von PowerShell oder Bash. Im restlichen Teil dieses Tutorials werden Test-ZS-Zertifikate verwendet, die mithilfe der Anweisungen in [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generiert wurden.

* Generieren Sie ein [X.509-Zertifikat der Zwischenzertifizierungsstelle](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust), das durch ein vorhandenes Zertifikat der Stammzertifizierungsstelle signiert wurde, und laden Sie es auf den IoT Hub hoch. Sobald das Zwischenzertifikat entsprechend den nachstehenden Anweisungen hochgeladen und überprüft wurde, kann es statt eines (unten erwähnten) Zertifikats der Stammzertifizierungsstelle verwendet werden. Mithilfe von Tools wie OpenSSL ([openssl req](https://www.openssl.org/docs/manmaster/man1/openssl-req.html) und [openssl ca](https://www.openssl.org/docs/manmaster/man1/openssl-ca.html)) kann ein Zertifikat der Zwischenzertifizierungsstelle generiert und signiert werden.


## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrieren der X.509-Zertifizierungsstellenzertifikate bei IoT Hub

Diese Schritte veranschaulichen das Hinzufügen einer neuen Zertifizierungsstelle zu Ihrem IoT Hub über das Portal.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub und öffnen Sie das Menü **EINSTELLUNGEN** > **Zertifikate**.

2. Klicken Sie auf **Hinzufügen**, um ein neues Zertifikat hinzuzufügen.

3. Geben Sie einen Anzeigenamen zu Ihrem Zertifikat ein. Wählen Sie die im vorherigen Abschnitt auf Ihrem Computer erstellte Stammzertifikatdatei mit dem Namen *RootCA.cer*. Klicken Sie auf **Hochladen**.

4. Wenn Sie eine Benachrichtigung erhalten, dass das Zertifikat erfolgreich hochgeladen wurde, klicken Sie auf **Speichern**.

    ![Hochladen des Zertifikats](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Damit wird angezeigt, dass Ihr Zertifikat in der Liste **Zertifikat-Explorer** enthalten ist. Beachten Sie: Der **STATUS** dieses Zertifikats lautet *Nicht überprüft*.

5. Klicken Sie auf das Zertifikat, das Sie im vorherigen Schritt hinzugefügt haben.

6. Klicken Sie auf dem Blatt **Zertifikatdetails** auf **Prüfcode generieren**.

7. Es wird ein **Prüfcode** erstellt, um den Zertifikatsbesitz zu überprüfen. Kopieren Sie den Code in die Zwischenablage.

   ![Überprüfen des Zertifikats](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Jetzt müssen Sie diesen *Prüfcode* mit dem privaten Schlüssel signieren, der Ihrem X.509-Zertifikat zugeordnet ist und eine Signatur generiert. Es sind Tools wie z.B. OpenSSL verfügbar, um diesen Signierungsprozess auszuführen. Dies wird als [Eigentumsnachweis](https://tools.ietf.org/html/rfc5280#section-3.1) bezeichnet. Schritt 3 in [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generiert einen Überprüfungscode.

9. Laden Sie die resultierende Signatur aus Schritt 8 in Ihren IoT Hub im Portal hoch. Navigieren Sie auf dem Blatt **Zertifikatdetails** im Azure-Portal zur **Verifizierungszertifikat-PEM- oder -CER-Datei**, und wählen Sie die vom Beispiel-PowerShell-Befehl erstellte Signatur, z.B. *VerifyCert4.cer*, mithilfe des nebenstehenden _Datei-Explorer_-Symbols aus.

10. Nachdem das Zertifikat hochgeladen wurde, klicken Sie auf **Überprüfen**. Der **STATUS** Ihres Zertifikats ändert sich zu **_Überprüft_** im Blatt **Zertifikate**. Klicken Sie auf **Aktualisieren**, wenn es nicht automatisch aktualisiert wird.

    ![Hochladen der Zertifikatprüfung](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>Erstellen eines X.509-Geräts für Ihren IoT Hub

1. Navigieren Sie im Azure-Portal zum **Explorer > IoT-Geräte** Ihres IoT Hubs.

2. Klicken Sie auf **+ Hinzufügen**, um ein neues Gerät hinzuzufügen.

3. Geben Sie einen Anzeigenamen für die **Geräte-ID** ein, und wählen Sie **_X.509 CA signiert_** als **Authentifizierungstyp**. Klicken Sie auf **Speichern**.

   ![Erstellen des X.509-Geräts im Portal](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Authentifizieren des X.509-Geräts mit den X.509-Zertifikaten

Um das X.509-Gerät zu authentifizieren, müssen Sie das Gerät zuerst mit dem CA-Zertifikat signieren. Das Signieren von Blattknotengeräten erfolgt normalerweise im Werk durch entsprechende Fertigungsgeräte. Bei Weitergabe des Geräts von einem Hersteller an einen anderen wird die Signierung jedes Herstellers als Zwischenzertifikat in der Kette erfasst. Das Endergebnis ist eine vom CA-Zertifikat bis zum Blattknotengeräte-Zertfikat verlaufende Zertifikatkette. Schritt 4 in [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generiert ein Gerätezertifikat.

Als Nächstes erfahren Sie, wie Sie eine C#-Anwendung zum Simulieren des für Ihren IoT Hub registrierten X.509-Geräts erstellen. Wir senden Temperatur- und Luftfeuchtigkeitswerte vom simulierten Gerät an Ihren Hub. Beachten Sie, dass in diesem Tutorial nur die Geräteanwendung erstellt wird. Es bleibt den Lesern überlassen, als Übung die IoT Hub-Dienstanwendung zu erstellen, die eine Antwort auf die vom simulierten Gerät gesendeten Ereignisse sendet. Die C#-Anwendung setzt voraus, dass Sie die Schritte unter [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) ausgeführt haben.

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage „Console Application“ ein neues Visual C#-Projekt für den klassischen Windows-Desktop. Nennen Sie das Projekt **SimulateX509Device**.

   ![Erstellen des X.509-Geräteprojekts in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateX509Device**, und klicken Sie dann auf **NuGet-Pakete verwalten**. Wählen Sie im Fenster NuGet-Paket-Manager die Option **Durchsuchen** aus, und suchen Sie dann nach **microsoft.azure.devices.client**. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices.Client** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket mit dem Azure IoT-Geräte-SDK heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

   ![Hinzufügen des Geräte-SDK NuGet-Pakets in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Fügen Sie am Anfang der Datei *Program.cs* die folgenden Codezeilen hinzu:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Fügen Sie innerhalb der **Program**-Klasse die folgenden Codezeilen hinzu:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     Verwenden Sie den Anzeigenamen, den Sie im vorherigen Abschnitt verwendet haben, anstelle des Platzhalters _<your_device_id>_ .

5. Fügen Sie die folgende Funktion zum Erstellen von Zufallszahlen für Temperatur- und Luftfeuchtigkeitswerte und Senden dieser Werte an den Hub hinzu:

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Fügen Sie schließlich die folgenden Codezeilen der **Main**-Funktion hinzu, und ersetzen Sie dabei die Platzhalter _device-id_, _your-iot-hub-name_ und _absolute-path-to-your-device-pfx-file_ wie für das Setup erforderlich.

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Dieser Code stellt mittels Erstellen der Verbindungszeichenfolge für das X.509-Gerät die Verbindung mit Ihrem IoT Hub her. Nach erfolgreicher Verbindungsherstellung werden Temperatur- und Luftfeuchtigkeitsereignisse an den Hub gesendet, und die Antwort wird abgewartet. 
7. Da diese Anwendung auf eine *PFX*-Datei zugreift, müssen Sie sie möglicherweise im *Admin*-Modus ausführen. Erstellen Sie die Visual Studio-Projektmappe. Öffnen Sie ein neues Befehlsfenster als **Administrator**, und navigieren Sie zu dem Ordner mit dieser Projektmappe. Navigieren Sie im Projektmappenordner zum Pfad *bin/Debug*. Führen Sie die Anwendung **SimulateX509Device.exe** im _Admin_-Befehlsfenster aus. Die erfolgreiche Verbindung Ihres Geräts mit dem Hub und das Senden der Ereignisse sollten angezeigt werden. 

   ![Ausführen der Geräte-App](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schützen Ihrer IoT-Lösung finden Sie hier:

* [Bewährte Methoden für die IoT-Sicherheit](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-Sicherheitsarchitektur](../iot-fundamentals/iot-security-architecture.md)

* [Schützen Ihrer IoT-Bereitstellung](../iot-fundamentals/iot-security-deployment.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
