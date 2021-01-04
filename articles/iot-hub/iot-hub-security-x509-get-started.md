---
title: Tutorial für X.509-Sicherheit in Azure IoT Hub | Microsoft-Dokumentation
description: Erste Schritte mit der X.509-basierten Sicherheit in Ihrem Azure IoT Hub in einer simulierten Umgebung.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: a3e328418a0f111cd0b985310ea6dc497999772d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909793"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub

In diesem Tutorial werden die Schritte gezeigt, mit denen Sie Ihren Azure IoT-Hub mithilfe der *X.509-Zertifikatauthentifizierung* sichern. Zur Veranschaulichung verwenden wir das Open Source-Tool OpenSSL zum lokalen Erstellen von Zertifikaten auf Ihrem Windows-Computer. Sie sollten dieses Tutorial nur zu Testzwecken verwenden. Für Produktionsumgebungen müssen Sie die Zertifikate bei einer *Stammzertifizierungsstelle* erwerben. Stellen Sie für die Produktionsumgebung auch sicher, dass Sie eine Strategie zur Handhabung des Rollovers von Zertifikaten haben, wenn ein Geräte- oder Zertifizierungsstellenzertifikat abläuft.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen:

* Sie haben in Ihrem Azure-Abonnement einen IoT Hub erstellt. Ausführliche Schrittbeschreibungen finden Sie unter [Erstellen eines IoT Hubs über das Portal](iot-hub-create-through-portal.md).

* [Visual Studio 2017 oder Visual Studio 2019](https://www.visualstudio.com/vs/) ist installiert.

## <a name="get-x509-ca-certificates"></a>Abrufen von X.509-CA-Zertifikaten

Die auf dem X.509-Zertifikat basierende Sicherheit in IoT Hub erfordert, dass Sie mit einer [X.509-Zertifikatkette](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) beginnen, die sowohl das Stammzertifizierungsstellen-Zertifikat als auch alle Zwischenzertifikate bis zum untergeordneten Zertifikat enthält.

Sie haben folgende Möglichkeiten zum Abrufen Ihrer Zertifikate:

* Erwerben Sie X.509-Zertifikate von einer *Stammzertifizierungsstelle (CA)* . Diese Methode wird für Produktionsumgebungen empfohlen.

* Erstellen Sie eigene X.509-Zertifikate mithilfe eines Drittanbietertools wie [OpenSSL](https://www.openssl.org/). Diese Methode eignet sich gut für Test- und Entwicklungszwecke. Unter [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) finden Sie Informationen zum Generieren von Test-ZS-Zertifikaten mithilfe von PowerShell oder Bash. Im restlichen Teil dieses Tutorials werden Test-ZS-Zertifikate verwendet, die mithilfe der Anweisungen in [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generiert wurden.

* Generieren Sie ein [X.509-Zertifikat der Zwischenzertifizierungsstelle](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust), das durch ein vorhandenes Zertifikat der Stammzertifizierungsstelle signiert wurde, und laden Sie es auf den Hub hoch. Sobald das Zwischenzertifikat entsprechend den nachstehenden Anweisungen hochgeladen und überprüft wurde, kann es statt eines (unten erwähnten) Zertifikats der Stammzertifizierungsstelle verwendet werden. Mithilfe von Tools wie OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) und [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) kann ein Zertifikat der Zwischenzertifizierungsstelle generiert und signiert werden.

> [!NOTE]
> Laden Sie das Drittanbieterstammzertifikat nicht hoch, wenn es nicht ausschließlich für Sie gilt, da dies anderen Kunden des Drittanbieters ermöglichen würde, ihre Geräte mit Ihrem IoT-Hub zu verbinden.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrieren der X.509-Zertifizierungsstellenzertifikate bei IoT Hub

Diese Schritte veranschaulichen das Hinzufügen einer neuen Zertifizierungsstelle zu Ihrem IoT Hub über das Portal. Wenn Sie die Authentifizierung mit X.509-Zertifizierungsstellenzertifikaten verwenden, stellen Sie sicher, dass Sie im Rahmen Ihrer Strategie für das Rollover von Zertifikaten Ihr neues Zertifikat registrieren, bevor das vorhandene abläuft.

> [!NOTE]
> Die maximale Anzahl von X.509-Zertifizierungsstellenzertifikaten, die bei IoT Hub registriert werden können, beträgt 25. Weitere Informationen finden Sie unter [Azure IoT Hub-Kontingente und -Drosselung](iot-hub-devguide-quotas-throttling.md).

1. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub, und wählen Sie **Einstellungen** > **Zertifikate** für den Hub aus.

1. Wählen Sie **Hinzufügen** aus, um ein neues Zertifikat hinzuzufügen.

1. Geben Sie in **Zertifikatname** einen Anzeigenamen ein, und wählen Sie die Zertifikatdatei aus, die Sie im vorherigen Abschnitt auf Ihrem Computer erstellt haben.

1. Wenn Sie eine Benachrichtigung erhalten, dass Ihr Zertifikat erfolgreich hochgeladen wurde, wählen Sie **Speichern** aus.

    ![Hochladen des Zertifikats](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Ihr Zertifikat wird in der Zertifikatliste mit dem Status **Nicht überprüft** angezeigt.

1. Wählen Sie das soeben hinzugefügte Zertifikat aus, um **Zertifikatdetails** anzuzeigen. Wählen Sie dann **Prüfcode generieren** aus.

   ![Überprüfen des Zertifikats](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Kopieren Sie den **Prüfcode** in die Zwischenablage. Sie verwenden ihn zum Überprüfen des Zertifikatbesitzes.

1. Führen Sie Schritt 3 unter [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Verwalten von Zertifizierungsstellen-Testzertifikaten für Beispiele und Tutorials) aus.  In diesem Prozess wird Ihr Prüfcode mit dem privaten Schlüssel signiert, der Ihrem X.509-Zertifizierungsstellenzertifikat zugeordnet ist, das eine Signatur generiert. Es sind Tools wie z.B. OpenSSL verfügbar, um diesen Signierungsprozess auszuführen. Dieser Prozess wird als [Eigentumsnachweis](https://tools.ietf.org/html/rfc5280#section-3.1) bezeichnet.

1. Suchen Sie in **Zertifikatdetails** unter **PEM- oder CER-Verifizierungszertifikatsdatei** die Signaturdatei, und öffnen Sie sie. Wählen Sie dann **Überprüfen** aus.

   Der Status Ihres Zertifikats ändert sich in **Überprüft**. Wenn das Zertifikat nicht automatisch aktualisiert wird, wählen Sie **Aktualisieren** aus.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Erstellen eines X.509-Geräts für Ihren IoT Hub

1. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub, und wählen Sie **Explorer** > **IoT-Geräte** aus.

1. Wählen Sie **Neu** aus, um ein neues Gerät hinzuzufügen.

1. Geben Sie in **Geräte-ID** einen Anzeigenamen ein. Wählen Sie unter **Authentifizierungstyp** den Typ **X.509, durch Zertifizierungsstelle signiert** und dann **Speichern** aus.

   ![Erstellen des X.509-Geräts im Portal](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Authentifizieren des X.509-Geräts mit den X.509-Zertifikaten

Um das X.509-Gerät zu authentifizieren, müssen Sie das Gerät zuerst mit dem CA-Zertifikat signieren. Das Signieren von Blattknotengeräten erfolgt normalerweise im Werk durch entsprechende Fertigungsgeräte. Beim Weitergeben des Geräts von einem Hersteller an einen anderen wird die Signierung jedes Herstellers als Zwischenzertifikat in der Kette erfasst. Das Ergebnis ist eine vom Zertifizierungsstellenzertifikat bis zum untergeordneten Zertifikat des Geräts verlaufende Vertrauenskette. Schritt 4 in [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generiert ein Gerätezertifikat.

Als Nächstes erfahren Sie, wie Sie eine C#-Anwendung zum Simulieren des für Ihren IoT Hub registrierten X.509-Geräts erstellen. Wir senden Temperatur- und Luftfeuchtigkeitswerte vom simulierten Gerät an Ihren Hub. In diesem Tutorial wird nur die Geräteanwendung erstellt. Es bleibt den Lesern überlassen, als Übung die IoT Hub-Dienstanwendung zu erstellen, die eine Antwort auf die vom simulierten Gerät gesendeten Ereignisse sendet. Die C#-Anwendung setzt voraus, dass Sie die Schritte unter [Managing test CA certificates for samples and tutorials (Verwalten von Test-ZS-Zertifikaten für Beispiele und Tutorials)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) ausgeführt haben.

1. Öffnen Sie Visual Studio, wählen Sie **Neues Projekt erstellen** und dann die Projektvorlage **Konsolen-App (.NET Framework)** aus. Wählen Sie **Weiter** aus.

1. Benennen Sie in **Neues Projekt konfigurieren** das Projekt mit *SimulateX509Device* , und wählen Sie **Erstellen** aus.

   ![Erstellen des X.509-Geräteprojekts in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateX509Device** , und wählen Sie **NuGet-Pakete verwalten** aus.

1. Wählen Sie in **NuGet-Paket-Manager** die Option **Durchsuchen** aus. Suchen Sie dann nach dem Paket **Microsoft.Azure.Devices.Client** , und wählen Sie es aus. Wählen Sie **Installieren** aus.

   ![Hinzufügen des Geräte-SDK NuGet-Pakets in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    In diesem Schritt wird das NuGet-Paket „Azure IoT-Geräte-SDK“ heruntergeladen und installiert sowie ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Verwenden Sie statt _<your_device_id>_ den im vorherigen Abschnitt verwendeten Anzeigenamen.

1. Fügen Sie die folgende Funktion zum Erstellen von Zufallszahlen für Temperatur- und Luftfeuchtigkeitswerte und Senden dieser Werte an den Hub hinzu:

    ```csharp
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

1. Fügen Sie schließlich der **Main** -Funktion die folgenden Codezeilen hinzu, und ersetzen Sie dabei die Platzhalter _device-id_ , _your-iot-hub-name_ und _absolute-path-to-your-device-pfx-file_ wie für das Setup erforderlich.

    ```csharp
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

1. Führen Sie die App aus. Weil diese Anwendung auf eine *PFX* -Datei zugreift, müssen Sie diese App möglicherweise als Administrator ausführen.

   1. Erstellen Sie die Visual Studio-Projektmappe.

   1. Öffnen Sie mithilfe von **Als Administrator ausführen** ein neues Eingabeaufforderungsfenster.  

   1. Navigieren Sie zu dem Ordner mit Ihrer Projektmappe und dann im Projektmappenordner zum Pfad *bin/Debug*.

   1. Führen Sie an der Eingabeaufforderung die Anwendung **SimulateX509Device.exe** aus.

   Die erfolgreiche Verbindung Ihres Geräts mit dem Hub und das Senden der Ereignisse sollten angezeigt werden.

   ![Ausführen der Geräte-App](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schützen Ihrer IoT-Lösung finden Sie hier:

* [Bewährte Methoden für die IoT-Sicherheit](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-Sicherheitsarchitektur](../iot-fundamentals/iot-security-architecture.md)

* [Schützen Ihrer IoT-Bereitstellung](../iot-fundamentals/iot-security-deployment.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)