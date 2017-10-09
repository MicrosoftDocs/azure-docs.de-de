---
title: Verarbeiten von D2C-Nachrichten mit Azure IoT Hub (Java) | Microsoft Docs
description: "Hier wird erläutert, wie IoT Hub-D2C-Nachrichten mithilfe von Routingregeln und benutzerdefinierten Endpunkten verarbeitet werden, um Nachrichten an andere Back-End-Dienste zu senden."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: d1aca8f39e305105d4ec9f63fbe7bee95487e294
ms.contentlocale: de-de
ms.lasthandoff: 07/24/2017

---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Verarbeiten von D2C-Nachrichten mit IoT Hub (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Weitere Tutorials ([Erste Schritte mit IoT Hub] und [Senden von C2D-Nachrichten mit IoT Hub][lnk-c2d]) veranschaulichen, wie Sie die grundlegenden Device-to-Cloud- und Cloud-to-Device-Messagingfunktionen von IoT Hub verwenden.

Dieses Tutorial stützt sich auf den Code, der im Tutorial [Erste Schritte mit IoT Hub] vorgestellt wird, und zeigt Ihnen, wie Sie das Nachrichtenrouting für das skalierbare Verarbeiten von D2C-Nachrichten einsetzen. Das Tutorial veranschaulicht das Verarbeiten von Nachrichten, die sofortiges Eingreifen vom Lösungs-Back-End erfordern. Beispielsweise könnte ein Gerät eine Alarmnachricht senden, die das Einfügen eines Tickets in ein CRM-System auslöst. Im Gegensatz dazu werden Datenpunktnachrichten einfach in ein Analysemodul eingegeben. Temperaturtelemetriedaten von einem Gerät, die zur späteren Analyse gespeichert werden, sind beispielsweise Datenpunktnachrichten.

Am Ende dieses Tutorials führen Sie drei Java-Konsolen-Apps aus:

* **simulated-device**, eine geänderte Version der im Tutorial [Erste Schritte mit IoT Hub] erstellten App, die jede Sekunde Datenpunkt-D2C-Nachrichten und alle 10 Sekunden interaktive D2C-Nachrichten sendet. Für diese App wird das AMQP-Protokoll für die Kommunikation mit IoT Hub verwendet.
* **read-d2c-messages** zeigt die Telemetrie an, die Ihre Geräte-App sendet.
* **read-critical-queue** holt die kritischen Nachrichten aus der Service Bus-Warteschlange, die mit dem IoT Hub verbunden ist.

> [!NOTE]
> IoT Hub verfügt über SDK-Unterstützung für zahlreiche Geräteplattformen und Sprachen, z.B. C, Java und JavaScript. Anleitungen zum Ersetzen des Geräts in diesem Tutorial durch ein physisches Gerät und Informationen zum Verbinden von Geräten mit einem IoT Hub finden Sie im [Azure IoT Developer Center].

Für dieses Tutorial benötigen Sie Folgendes:

* Eine vollständig funktionierende Version des Tutorials [Erste Schritte mit IoT Hub] .
* Das neueste [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Ein aktives Azure-Konto. (Wenn Sie noch kein Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Konto][lnk-free-trial] erstellen.)

Sie sollten über grundlegende Kenntnisse zu [Azure Storage] und [Azure Service Bus] verfügen.

## <a name="send-interactive-messages-from-a-device-app"></a>Senden interaktiver Nachrichten von einer Geräte-App aus
In diesem Abschnitt ändern Sie die Geräte-App, die Sie im Tutorial [Erste Schritte mit IoT Hub] erstellt haben, sodass sie gelegentlich Nachrichten sendet, die sofort verarbeitet werden müssen.

1. Öffnen Sie die Datei „simulated-device\src\main\java\com\mycompany\app\App.java“ mit einem Text-Editor. Diese Datei enthält den Code für die **simulated-device** -App, die Sie im Tutorial [Erste Schritte mit IoT Hub] erstellt haben.

2. Ersetzen Sie die **MessageSender**-Klasse durch den folgenden Code:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Mit dieser Methode wird vom Gerät gesendeten Nachrichten nach dem Zufallsprinzip die Eigenschaft `"level": "critical"` hinzugefügt, wodurch eine Nachricht simuliert wird, die vom Anwendungs-Back-End sofortiges Eingreifen erfordert. Die Anwendung übergibt diese Information den Eigenschaften der Nachricht statt dem Nachrichtentext, sodass IoT Hub die Nachricht an das richtige Nachrichtenziel weiterleiten kann.
   
   > [!NOTE]
   > Sie können Nachrichteneigenschaften zum Weiterleiten von Nachrichten für verschiedene Szenarien zusätzlich zu dem hier gezeigten Beispiel des langsamsten Pfads verwenden – einschließlich der Cold-Path-Verarbeitung.

2. Speichern und schließen Sie die Datei „simulated-device\src\main\java\com\mycompany\app\App.java“.

    > [!NOTE]
    > Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie eine Wiederholungsrichtlinie implementieren (etwa einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler]beschrieben.

3. Führen Sie zum Erstellen der App **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Hinzufügen einer Warteschlange zu Ihrem IoT Hub und Weiterleiten von Nachrichten an sie

In diesem Abschnitt erstellen Sie eine Service Bus-Warteschlange, verbinden sie mit Ihrem IoT Hub und konfigurieren Ihren IoT Hub zum Senden von Nachrichten an die Warteschlange, sofern eine Eigenschaft in der Nachricht vorhanden ist. Weitere Informationen zum Verarbeiten von Nachrichten von der Service Bus-Warteschlange finden Sie in [Erste Schritte mit Event Hubs][lnk-sb-queues-java].

1. Erstellen Sie eine Service Bus-Warteschlange wie in [Erste Schritte mit Warteschlangen][lnk-sb-queues-java] beschrieben. Notieren Sie den Namespace und den Warteschlangennamen.

2. Öffnen Sie Ihren IoT-Hub im Azure-Portal, und klicken Sie auf **Endpunkte**.

    ![Endpunkte in IoT Hub][30]

3. Klicken Sie oben auf dem Blatt **Endpunkte** auf **Hinzufügen**, um die Warteschlange Ihrem IoT-Hub hinzuzufügen. Benennen Sie den Endpunkt **CriticalQueue**, und wählen Sie mithilfe der Dropdownlisten **Service Bus-Warteschlange**, den Service Bus-Namespace, in dem sich die Warteschlange befindet, und den Namen der Warteschlange. Klicken Sie anschließend am unteren Rand auf **Speichern**.

    ![Hinzufügen eines Endpunkts][31]

4. Klicken Sie jetzt in Ihrem IoT-Hub auf **Routen**. Klicken Sie am oberen Rand des Blatts auf **Hinzufügen**, um eine Routingregel zu erstellen, die Nachrichten an die gerade von Ihnen hinzugefügte Warteschlange leitet. Wählen Sie **DeviceTelemetry** als Quelle der Daten. Geben Sie `level="critical"` als Bedingung ein, und wählen Sie die Warteschlange, die Sie gerade als benutzerdefinierten Endpunkt hinzugefügt haben, als Routingregelendpunkt. Klicken Sie anschließend am unteren Rand auf **Speichern**.

    ![Hinzufügen einer Route][32]

    Stellen Sie sicher, dass die Fallbackroute auf **EIN** festgelegt ist. Diese Einstellung ist die Standardkonfiguration eines IoT-Hubs.

    ![Fallbackroute][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Optional) Lesen aus dem Warteschlangen-Endpunkt

Sie können optional die Nachrichten aus dem Warteschlangenendpunkt anhand der Anweisungen unter [Erste Schritte mit Warteschlangen][lnk-sb-queues-java] lesen. Benennen Sie die App **read-critical-queue**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können jetzt die drei Anwendungen ausführen.

1. Um die **read-d2c-messages** -Anwendung auszuführen, navigieren Sie in einer Befehlszeile oder Shell zum Ordner „read-d2c“, und geben Sie folgenden Befehl ein:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Ausführen von „read-d2c-messages“][readd2c]

2. Um die **read-critical-queue**-Anwendung auszuführen, navigieren Sie in einer Befehlszeile oder Shell zum Ordner „read-critical-queue“, und geben Sie folgenden Befehl ein:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Ausführen von „read-critical-messages“][readqueue]

3. Um die **simulated-device**-App auszuführen, navigieren Sie in einer Befehlszeile oder Shell zum Ordner „simulated-device“, und geben Sie folgenden Befehl ein:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Ausführen von „simulated-device“][simulateddevice]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, D2C-Nachrichten zuverlässig mit der Nachrichtenroutingfunktion von IoT Hub zu versenden.

Im Tutorial [Gewusst wie: Senden von C2D-Nachrichten mithilfe von IoT Hub][lnk-c2d] erfahren Sie, wie Sie Nachrichten aus Ihrem Lösungs-Back-End an Ihre Geräte senden.

Beispiele vollständiger Lösungen, die IoT Hub nutzen, finden Sie unter [Azure IoT Suite][lnk-suite].

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [IoT Hub-Entwicklerhandbuch].

Weitere Informationen zum Nachrichtenrouting in IoT Hub finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Erste Schritte mit IoT Hub]: iot-hub-java-java-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[Behandeln vorübergehender Fehler]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Behandeln vorübergehender Fehler]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/de-de/free/

