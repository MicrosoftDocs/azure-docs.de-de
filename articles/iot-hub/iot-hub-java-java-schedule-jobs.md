---
title: Planen von Aufträgen mit Azure IoT Hub (Java) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um auf mehreren Geräten eine direkte Methode aufzurufen und eine gewünschte Eigenschaft festzulegen. Sie verwenden das Azure IoT-Geräte-SDK für Java, um die simulierten Geräte-Apps zu implementieren, und das Azure IoT-Dienst-SDK für Java, um eine Dienst-App für die Auftragsausführung zu implementieren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.openlocfilehash: ce7c70eef2d030a956ca5cc1ea85aff008074edb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572465"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Planen und Übertragen von Aufträgen (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Verwenden Sie Azure IoT Hub zum Planen und Nachverfolgen von Aufträgen, die Millionen von Geräte aktualisieren. Verwenden Sie Aufträge zum:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

Ein Auftrag umschließt eine dieser Aktionen und verfolgt die Ausführung für eine Gruppe von Geräten nach. Mit einer Gerätezwillingsabfrage definieren Sie die Gruppe der Geräte, für die der Auftrag ausgeführt wird. Eine Back-End-App kann z.B. einen Auftrag verwenden, um eine direkte Methode auf 10.000 Geräten aufzurufen, die die Geräte neu startet. Sie geben die Gruppe von Geräten mit einer Gerätezwillingsabfrage ein und planen die Ausführung des Auftrags zu einem späteren Zeitpunkt. Der Auftrag verfolgt den Fortschritt nach, während jedes der Geräte die Neustart-Direktmethode empfängt und ausführt.

Weitere Informationen zu diesen Funktionen finden Sie unter:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-java-java-twin-getstarted.md)

* Direkte Methoden: [Verstehen und Aufrufen direkter Methoden von IoT Hub](iot-hub-devguide-direct-methods.md) und [Tutorial: Verwenden von direkten Methoden](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen Sie eine Geräte-App, mit der die direkte **lockDoor**-Methode implementiert wird. Die Geräte-App empfängt auch die Änderungen der gewünschten Eigenschaft über die Back-End-App.

* Erstellen Sie eine Back-End-App, die einen Auftrag zum Aufrufen der direkten Methode **lockDoor** auf mehreren Geräten erstellt. Ein anderer Auftrag sendet die Updates der gewünschten Eigenschaft an mehrere Geräte.

Am Ende dieses Tutorials verfügen Sie über eine Java-Konsolen-Geräte-App und eine Java-Konsolen-Back-End-App:

Das Element **simulated-device**, mit dem eine Verbindung mit Ihrem IoT Hub hergestellt wird, implementiert die direkte **lockDoor**-Methode und führt die Änderungen der gewünschten Eigenschaft durch.

**schedule-jobs** verwendet Aufträge zum Aufrufen der direkten **lockDoor**-Methode und Aktualisieren der gewünschten Gerätezwillingseigenschaften auf mehreren Geräten.

> [!NOTE]
> Im Artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) finden Sie Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Geräte- als auch Back-End-Apps erstellen können.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Das neueste [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Sie können auch das Tool [IoT-Erweiterung für Azure CLI](https://github.com/Azure/azure-iot-cli-extension) verwenden, um Ihrem IoT Hub ein Gerät hinzuzufügen.

## <a name="create-the-service-app"></a>Erstellen der Dienst-App

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die Aufträge für folgende Zwecke nutzt:

* Aufrufen der direkten **lockDoor**-Methode auf mehreren Geräten

* Senden von gewünschten Eigenschaften an mehrere Geräte

Gehen Sie wie folgt vor, um die App zu erstellen:

1. Erstellen Sie auf dem Entwicklungscomputer einen leeren Ordner mit dem Namen `iot-java-schedule-jobs`.

2. Erstellen Sie im Ordner `iot-java-schedule-jobs` ein Maven-Projekt mit dem Namen **schedule-jobs**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. Navigieren Sie an der Eingabeaufforderung zum `schedule-jobs`-Ordner.

4. Öffnen Sie mit einem Text-Editor die Datei `pom.xml` im Ordner `schedule-jobs`, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit können Sie das Paket **iot-service-client** in Ihrer App zum Kommunizieren mit Ihrem IoT Hub verwenden:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-service-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Fügen Sie den Knoten **build** hinter dem Knoten **dependencies** hinzu. Diese Konfiguration weist Maven an, Java 1.8 zu verwenden, um die App zu erstellen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Speichern und schließen Sie die `pom.xml`-Datei.

7. Öffnen Sie in einem Text-Editor die Datei `schedule-jobs\src\main\java\com\mycompany\app\App.java`.

8. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{youriothubconnectionstring}` durch die IoT Hub-Verbindungszeichenfolge, die Sie im Abschnitt *Erstellen einer IoT Hub-Instanz* notiert haben:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Fügen Sie der **App**-Klasse die folgende Methode hinzu, um einen Auftrag zum Aktualisieren der gewünschten Eigenschaften **Building** und **Floor** im Gerätezwilling zu planen:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Fügen Sie der **App**-Klasse die folgende Methode hinzu, um einen Auftrag zum Aufrufen der **lockDoor**-Methode zu planen:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Fügen Sie der **App**-Klasse die folgende Methode hinzu, um einen Auftrag zu überwachen:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Fügen Sie die folgende Methode hinzu, um die Details zu den ausgeführten Aufträgen abzufragen:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Aktualisieren Sie die Signatur der **main**-Methode und schließen Sie die folgende `throws`-Klausel ein:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Fügen Sie der **main**-Methode den folgenden Code hinzu, um zwei Aufträge sequenziell auszuführen und zu überwachen:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Speichern und schließen Sie die `schedule-jobs\src\main\java\com\mycompany\app\App.java`-Datei.

17. Erstellen Sie die App **schedule-jobs**, und beheben Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum `schedule-jobs`-Ordner, und führen Sie den folgenden Befehl aus:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Erstellen einer Geräte-App

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, mit der die vom IoT Hub gesendeten gewünschten Eigenschaften verarbeitet und der Aufruf der direkten Methode implementiert wird.

1. Erstellen Sie im Ordner `iot-java-schedule-jobs` ein Maven-Projekt namens **simulated-device**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. Navigieren Sie an der Eingabeaufforderung zum `simulated-device`-Ordner.

3. Öffnen Sie mit einem Text-Editor die Datei `pom.xml` im Ordner `simulated-device`, und fügen Sie dem Knoten **dependencies** die folgenden Abhängigkeiten hinzu. Mit dieser Abhängigkeit können Sie das Paket **iot-device-client** in Ihrer App zum Kommunizieren mit Ihrem IoT Hub verwenden:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-device-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Fügen Sie den Knoten **build** hinter dem Knoten **dependencies** hinzu. Diese Konfiguration weist Maven an, Java 1.8 zu verwenden, um die App zu erstellen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Speichern und schließen Sie die `pom.xml`-Datei.

6. Öffnen Sie in einem Text-Editor die Datei `simulated-device\src\main\java\com\mycompany\app\App.java`.

7. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{youriothubname}` durch den Namen Ihres IoT Hubs und `{yourdevicekey}` durch den Geräteschlüsselwert, den Sie im Abschnitt *Erstellen einer Geräteidentität* generiert haben:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Diese Beispiel-App verwendet beim Instanziieren eines **DeviceClient**-Objekts die **protocol**-Variable.

9. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um Gerätezwillingsbenachrichtigungen auf der Konsole auszugeben:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um Benachrichtigungen über direkte Methoden auf der Konsole auszugeben:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um Aufrufe von direkten Methoden über den IoT Hub zu verarbeiten:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

12. Aktualisieren Sie die Signatur der **main**-Methode und schließen Sie die folgende `throws`-Klausel ein:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. Fügen Sie den folgenden Code der **main**-Methode hinzu:
    * Erstellen Sie einen Geräteclient zur Kommunikation mit IoT Hub.
    * Erstellen Sie ein **Device**-Objekt, das die Gerätezwillingseigenschaften speichert.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

14. Fügen Sie der **main**-Methode den folgenden Code hinzu, um die Dienste für den Geräteclient zu starten:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

15. Fügen Sie am Ende der **main**-Methode den folgenden Code hinzu, wenn vor dem Herunterfahren darauf gewartet werden soll, dass der Benutzer die **EINGABETASTE** drückt:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. Speichern und schließen Sie die `simulated-device\src\main\java\com\mycompany\app\App.java`-Datei.

17. Erstellen Sie die App **simulated-device**, und korrigieren Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum `simulated-device`-Ordner, und führen Sie den folgenden Befehl aus:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können nun die Konsolen-Apps ausführen.

1. Führen Sie an einer Eingabeaufforderung im Ordner `simulated-device` den folgenden Befehl aus, um die Geräte-App zu starten, mit der auf Änderungen von gewünschten Eigenschaften und Aufrufe von direkten Methoden gelauscht wird:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Geräteclient wird gestartet](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Führen Sie an der Eingabeaufforderung im Ordner `schedule-jobs` den folgenden Befehl aus, um die Dienst-App **schedule-jobs** für zwei Aufträge auszuführen. Mit dem ersten Auftrag werden die gewünschten Eigenschaftswerte festgelegt, und mit dem zweiten wird die direkte Methode aufgerufen:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java-IoT Hub-Dienst-App erstellt zwei Aufträge](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Die Geräte-App führt die Änderung der gewünschten Eigenschaften und den Aufruf der direkten Methode durch:

    ![Geräteclient reagiert auf Änderungen](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie im Azure-Portal einen neuen IoT-Hub konfiguriert und anschließend in der Identitätsregistrierung des IoT-Hubs eine Geräteidentität erstellt. Sie haben eine Back-End-App für die Ausführung von zwei Aufträgen erstellt. Mit dem ersten Auftrag wurden die gewünschten Eigenschaftswerte festgelegt, und mit dem zweiten Auftrag wurde eine direkte Methode aufgerufen.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* Senden von Telemetriedaten von Geräten im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-java.md).

* Interaktives Steuern von Geräten (z.B. Einschalten eines Lüfters über eine benutzergesteuerte App): [Tutorial: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Node.js)](quickstart-control-device-java.md)