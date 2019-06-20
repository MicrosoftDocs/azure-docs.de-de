---
title: Hochladen von Dateien von Geräten nach Azure IoT Hub mit Java | Microsoft-Dokumentation
description: Informationen zum Hochladen von Dateien von einem Gerät in die Cloud mithilfe des Azure IoT-Geräte-SDK für Java. Hochgeladene Dateien werden in einem Azure Storage-Blobcontainer gespeichert.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 3658b57d003ddc5429c6857f88044376fe1aaa93
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399111"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Dieses Tutorial baut auf dem Code im Tutorial [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub](iot-hub-java-java-c2d.md) auf, um zu zeigen, wie Sie die [IoT Hub-Funktionen zum Hochladen von Dateien](iot-hub-devguide-file-upload.md) zum Hochladen einer Datei in [Azure Blob Storage](../storage/index.yml) nutzen. Das Tutorial veranschaulicht folgende Vorgehensweisen:

* Sicheres Bereitstellen eines Geräts mit einem Azure-Blob-URI für das Hochladen einer Datei.

* Verwenden der IoT Hub-Dateihochlade-Benachrichtigungen zum Auslösen der Dateiverarbeitung in Ihrem App-Back-End.

Die Artikel [Senden von Telemetriedaten an IoT Hub (Java)](quickstart-send-telemetry-java.md) und [Senden von C2D-Nachrichten (Cloud-to-Device) mit IoT Hub (Java)](iot-hub-java-java-c2d.md) veranschaulichen die grundlegenden Gerät-zu-Cloud- und Cloud-zu-Gerät-Messagingfunktionen von IoT Hub. Unter [Tutorial: Konfigurieren der Nachrichtenweiterleitung mit IoT Hub](tutorial-routing.md) wird eine Möglichkeit für das zuverlässige Speichern von Gerät-zu-Cloud-Nachrichten in Azure Blob Storage beschrieben. In einigen Szenarien können Sie allerdings nicht einfach die Daten, die Ihre Geräte senden, den relativ kleinen D2C-Nachrichten zuordnen, die IoT Hub akzeptiert. Beispiel:

* Große Dateien, die Bilder enthalten
* Videos
* Vibrationsdaten, die mit hoher Häufigkeit als Stichproben erfasst werden
* Eine Form vorverarbeiteter Daten.

Diese Dateien werden normalerweise als Batch in der Cloud mit Tools wie [Azure Data Factory](../data-factory/introduction.md) oder dem [Hadoop](../hdinsight/index.yml)-Stapel verarbeitet. Wenn Sie Dateien von einem Gerät hochladen müssen, können Sie weiterhin die Sicherheit und Zuverlässigkeit des IoT Hub nutzen.

Am Ende dieses Tutorials führen Sie zwei Java-Konsolen-Apps aus:

* **simulated-device**, eine geänderte Version der App, die im Tutorial [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub] erstellt wurde. Diese App lädt mithilfe eines SAS-URI, den Ihr IoT Hub bereitstellt, eine Datei in den Speicher hoch.

* **read-file-upload-notification**, die Benachrichtigungen zum Hochladen von Dateien von Ihrem IoT Hub empfängt.

> [!NOTE]
> IoT Hub bietet über Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, .NET und JavaScript). Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit Azure IoT Hub.

Für dieses Tutorial benötigen Sie Folgendes:

* Das neueste [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Hochladen einer Datei von einer Geräte-App

In diesem Abschnitt ändern Sie die Geräte-App, die Sie in [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub](iot-hub-java-java-c2d.md) erstellt haben, um eine Datei nach IoT Hub hochzuladen.

1. Kopieren Sie eine Bilddatei in den `simulated-device`-Ordner und benennen Sie sie in `myimage.png` um.

2. Öffnen Sie in einem Text-Editor die Datei `simulated-device\src\main\java\com\mycompany\app\App.java`.

3. Fügen Sie die Variablendeklaration der **App**-Klasse hinzu:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Fügen Sie zum Verarbeiten der Rückrufmeldungen zum Dateihochladestatus die folgende geschachtelte Klasse der **App**-Klasse hinzu:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Um Bilder in IoT Hub hochzuladen, fügen Sie die folgende Methode zum Hochladen von Bildern in IoT Hub der **App**-Klasse hinzu:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Ändern Sie die Methode **main** zum Aufrufen der Methode **uploadFile**, wie im folgenden Codeausschnitt dargestellt:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Verwenden Sie den folgenden Befehl zum Erstellen der **simulated-device**-App, und prüfen Sie, ob Fehler vorliegen:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Erhalten einer Benachrichtigung zum Dateiupload

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die Uploadbenachrichtigungen von IoT Hub empfängt.

Sie müssen diesen Abschnitt mit der Verbindungszeichenfolge **iothubowner** für Ihren IoT Hub abschließen. Die Verbindungszeichenfolge finden Sie im [Azure-Portal](https://portal.azure.com/) auf dem Blatt **SAS-Richtlinie**.

1. Erstellen Sie mithilfe des folgenden Befehls über die Eingabeaufforderung ein Maven-Projekt namens **read-file-upload-notification**. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie an der Eingabeaufforderung zum neuen `read-file-upload-notification`-Ordner.

3. Öffnen Sie mit einem Text-Editor die Datei `pom.xml` im Ordner `read-file-upload-notification`, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Durch Hinzufügen der Abhängigkeit können Sie in Ihrer Anwendung das Paket **iothub-java-service-client** verwenden, um mit Ihrem IoT Hub-Dienst zu kommunizieren:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-service-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Speichern und schließen Sie die `pom.xml`-Datei.

5. Öffnen Sie in einem Text-Editor die Datei `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

6. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Fügen Sie der **App** -Klasse die folgenden Klassenebenenvariablen hinzu:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Um Informationen über das Hochladen der Datei an der Konsole auszugeben, fügen Sie die folgende geschachtelte Klasse der **App**-Klasse hinzu:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Um den Thread zu starten, der auf Dateiuploadbenachrichtigungen lauscht, fügen Sie den folgenden Code in die **main**-Methode ein:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Speichern und schließen Sie die `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`-Datei.

11. Verwenden Sie den folgenden Befehl zum Erstellen der **read-file-upload-notification**-App, und prüfen Sie, ob Fehler vorliegen:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

Führen Sie an der Eingabeaufforderung im `read-file-upload-notification`-Ordner folgenden Befehl aus:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Führen Sie an der Eingabeaufforderung im `simulated-device`-Ordner folgenden Befehl aus:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Der folgende Screenshot zeigt die Ausgabe der **simulated-device**-App an:

![Ausgabe der simulated-device-App](media/iot-hub-java-java-upload/simulated-device.png)

Der folgende Screenshot zeigt die Ausgabe der **read-file-upload-notification**-App:

![Ausgabe der read-file-upload-notification-App](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Sie können das Verwaltungsportal verwenden, um die hochgeladene Datei im Speichercontainer anzuzeigen, den Sie konfiguriert haben:

![Hochgeladene Datei](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie die IoT Hub-Funktionen zum Hochladen von Dateien nutzen, um Dateiuploads zu vereinfachen. In den folgenden Artikeln werden weitere IoT Hub-Features und -Szenarien vorgestellt:

* [Erstellen einer IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage (PowerShell)](iot-hub-rm-template-powershell.md)
* [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md)
* [Azure IoT SDKs](iot-hub-devguide-sdks.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Simulieren eines Geräts mit IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)