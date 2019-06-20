---
title: 'Tutorial: Entwickeln eines C-Moduls für Windows – Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit C-Code erstellen und auf einem Windows-Gerät bereitstellen, auf dem IoT Edge ausgeführt wird.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 49f853341edab7c7dc92f72472b81f7fb22c0ad8
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808758"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Entwickeln eines C-IoT Edge-Moduls für Windows-Geräte

Verwenden Sie Visual Studio zum Entwickeln von C-Code, und stellen Sie ihn auf einem Windows-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird. 

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen. In diesem Tutorial lernen Sie Folgendes:    

> [!div class="checklist"]
> * Verwenden von Visual Studio zum Erstellen eines IoT Edge-Moduls auf der Grundlage des C SDK
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Projektmappenbereich

In diesem Tutorial wird gezeigt, wie Sie ein Modul in **C** mit **Visual Studio 2019** entwickeln und auf einem **Windows-Gerät** bereitstellen. Wenn Sie Module für Linux-Geräte entwickeln, lesen Sie stattdessen [Entwickeln eines C-IoT Edge-Moduls für Linux-Geräte](tutorial-c-module.md). 

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von C-Modulen für Windows-Geräte: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Entwickeln von C-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Windows-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Windows-Geräte](tutorial-develop-for-windows.md) Nach Abschluss dieses Tutorials sollten Sie über die folgenden Komponenten verfügen: 

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Windows-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart.md)
* Eine Containerregistrierung wie [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio), mit der Erweiterung [Azure IoT Edge-Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) konfiguriert
* [Docker CE](https://docs.docker.com/install/), zur Ausführung von Windows-Containern konfiguriert
* Azure IoT SDK für C 

> [!TIP]
> Wenn Sie Visual Studio 2017 (Version 15.7 oder höher) verwenden, laden Sie die [Azure IoT Edge-Tools (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) für VS 2017 aus Visual Studio Marketplace herunter, und installieren Sie sie.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

Die folgenden Schritte dienen zum Erstellen eines auf dem C SDK basierenden IoT Edge-Moduls unter Verwendung von Visual Studio und der Erweiterung „Azure IoT Edge-Tools“. Sobald Sie eine Projektvorlage erstellt haben, fügen Sie ihr neuen Code hinzu, damit das Modul Nachrichten aufgrund der darin gemeldeten Eigenschaften herausfiltert. 

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Erstellen Sie eine C-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Starten Sie Visual Studio 2019, und wählen Sie **Neues Projekt erstellen** aus.

2. Suchen Sie im Fenster für das neue Projekt das Projekt **IoT Edge**, wählen Sie dann das Projekt **Azure IoT Edge (Windows amd64)** aus. Klicken Sie auf **Weiter**. 

   ![Erstellen eines neuen Azure IoT Edge-Projekts](./media/tutorial-c-module-windows/new-project.png)

3. Geben Sie im Fenster zum Konfigurieren des neuen Projekts dem Projekt und der Projektmappe einen aussagekräftigen Namen, etwa **CTutorialApp**. Klicken Sie auf **Erstellen** , um das Projekt zu erstellen. 

   ![Konfigurieren eines neuen Azure IoT Edge-Projekts](./media/tutorial-c-module-windows/configure-project.png)

4. Konfigurieren Sie im Fenster für die IoT Edge-Anwendung und das Modul Ihr Projekt mit den folgenden Werten: 

   | Feld | Wert |
   | ----- | ----- |
   | Vorlage auswählen | Wählen Sie die Option **C-Modul**. | 
   | Modulprojektname | Nennen Sie das Modul **CModule**. | 
   | Docker-Imagerepository | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für Ihr Containerimage ist der Wert des Modulprojektnamens bereits vorhanden. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br> Das endgültige Imagerepository sieht wie folgt aus: \<Registrierungsname\>.azurecr.io/cmodule. |

   ![Konfigurieren von Zielgerät, Modultyp und Containerregistrierung für Ihr Projekt](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Klicken Sie zum Übernehmen der Änderungen auf **OK**. 

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Das Bereitstellungsmanifest gibt die Anmeldeinformationen für Ihre Containerregistrierung an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. Verwenden Sie die Anmeldeinformationen aus dem Abschnitt **Zugriffsschlüssel** Ihrer Azure-Containerregistrierung. 

1. Öffnen Sie im Visual Studio-Projektmappen-Explorer die Datei **deployment.template.json**. 

2. Suchen Sie unter den gewünschten Eigenschaften von „$edgeAgent“ nach der **registryCredentials**-Eigenschaft. 

3. Aktualisieren Sie die Eigenschaft mit Ihren Anmeldeinformationen, und verwenden Sie das folgende Format: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Speichern Sie die Datei „deployment.template.json“. 

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Der Standardmodulcode empfängt Nachrichten in einer Eingabewarteschlange und leitet sie über eine Ausgabewarteschlange weiter. Fügen Sie einigen zusätzlichen Code hinzu, damit das Modul die Nachrichten auf Edge-Ebene verarbeitet, bevor es sie an IoT Hub weiterleitet. Aktualisieren Sie das Modul, sodass es die Temperaturdaten in jeder Nachricht analysiert und die Nachricht nur dann an IoT Hub sendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet. 


1. Die Sensordaten in diesem Szenario liegen im JSON-Format vor. Importieren Sie zum Filtern von Nachrichten im JSON-Format eine JSON-Bibliothek für C. In diesem Tutorial wird Parson verwendet.

   1. Laden Sie das [Parson-Repository von GitHub](https://github.com/kgabis/parson) herunter. Kopieren Sie die Dateien **parson.c** und **parson.h** in das Projekt **CModule**.

   2. Öffnen Sie in Visual Studio die Datei **CMakeLists.txt** aus dem CModule-Projektordner. Importieren Sie am Anfang der Datei die Parson-Dateien als Bibliothek namens **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Fügen Sie im Abschnitt **target_link_libraries** der Datei „CMakeLists.txt“ **my_parson** zur Liste der Bibliotheken hinzu.

   4. Speichern Sie die Datei **CMakeLists.txt**.

   5. Öffnen Sie **CModule** > **main.c**. Fügen Sie am Ende der Liste mit include-Anweisungen eine neue Anweisung hinzu, um `parson.h` zur JSON-Unterstützung einzuschließen:

      ```c
      #include "parson.h"
      ```

2.  Fügen Sie in der Datei **main.c** neben der Variablen „messagesReceivedByInput1Queue“ die globale Variable `temperatureThreshold` hinzu. Mit dieser Variable wird der Wert festgelegt, den die gemessene Temperatur übersteigen muss, damit die Daten an IoT Hub gesendet werden.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Suchen Sie die Funktion `CreateMessageInstance` in „main.c“. Ersetzen Sie die innere Anweisung „if-else“ durch den folgenden Code, der ein paar Zeilen mit Funktionen hinzufügt: 

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   Die neuen Codezeilen in der Anweisung vom Typ „else“ fügen der Nachricht eine neue Eigenschaft hinzu, wodurch sie als Warnung gekennzeichnet wird. Dieser Code kennzeichnet alle Nachrichten als Warnungen, weil Sie eine Funktionalität hinzufügen werden, die Nachrichten nur dann an IoT Hub sendet, wenn hohe Temperaturen gemeldet werden. 

4. Suchen Sie die Funktion `InputQueue1Callback`, und ersetzen Sie die gesamte Funktion durch den folgenden Code. Diese Funktion implementiert den Nachrichtenfilter. Wenn eine Nachricht empfangen wird, überprüft er, ob die gemeldete Temperatur den Schwellenwert überschreitet. Falls dies zutrifft, leitet er die Nachricht über seine Ausgabewarteschlange weiter. Andernfalls ignoriert er die Nachricht. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Fügen Sie die Funktion `moduleTwinCallback` hinzu. Diese Methode empfängt Aktualisierungen der gewünschten Eigenschaften aus dem Modulzwilling und aktualisiert die Variable **temperatureThreshold** entsprechend. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

6. Suchen Sie die Funktion `SetupCallbacksForModule`. Ersetzen Sie die Funktion durch den folgenden Code. Er fügt eine **else if**-Anweisung hinzu, mit der überprüft wird, ob der Modulzwilling aktualisiert wurde. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Speichern Sie die Datei „main.c“.

8. Öffnen Sie die Datei **deployment.template.json**. 

9. Fügen Sie den Modulzwilling „CModule“ zum Bereitstellungsmanifest hinzu. Fügen Sie am Ende des Abschnitts `moduleContent` nach dem Modulzwilling `$edgeHub` den folgenden JSON-Inhalt ein:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Hinzufügen des CModule-Zwillings zur Bereitstellungsvorlage](./media/tutorial-c-module-windows/module-twin.png)

1. Speichern Sie die Datei **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CModule** Code hinzugefügt, der Nachrichten herausfiltert, deren gemeldete Computertemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen. 

1. Verwenden Sie den folgenden Befehl, um sich auf Ihrem Entwicklungscomputer bei Docker anzumelden. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

2. Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, den Sie erstellen möchten. Der Standardname lautet **AzureIotEdgeApp1**. Da Sie ein Windows-Modul erstellen, sollte die Erweiterung **Windows.Amd64** lauten. 

3. Wählen Sie **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen) aus. 

   Mit dem Befehl zum Erstellen und Übertragen per Pushvorgang werden drei Vorgänge gestartet. Zuerst wird in der Projektmappe ein neuer Ordner mit dem Namen **config** erstellt. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen. 

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie den Visual Studio-Cloud-Explorer und die Erweiterung „Azure IoT-Tools“, um das Modulprojekt auf Ihrem IoT Edge-Gerät bereitzustellen. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät korrekt ausgeführt wird. 

1. Erweitern Sie im Visual Studio-Cloud-Explorer die Ressourcen, um die Liste der IoT-Geräte anzuzeigen. 

2. Klicken Sie mit der rechten Maustaste auf den Namen des IoT Edge-Geräts, das die Bereitstellung erhalten soll. 

3. Wählen Sie **Bereitstellung erstellen** aus.

4. Wählen Sie im Datei-Explorer im Ordner „config“ Ihrer Projektmappe die Datei **deployment.windows-amd64** aus. 

5. Aktualisieren Sie den Cloud-Explorer, um die bereitgestellten Module anzuzeigen, die unter Ihrem Gerät aufgeführt sind. 


## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet. 

Mit der Erweiterung „IoT Edge-Tools“ können Sie die bei Ihrer IoT Hub-Instanz eingehenden Nachrichten anzeigen. 

1. Wählen Sie im Cloud-Explorer von Visual Studio den Namen Ihres IoT Edge-Geräts aus. 

2. Wählen Sie in der Liste **Aktionen** die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus. 

3. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Möglicherweise dauert es eine Weile, bis die Nachrichten eintreffen, weil das IoT Edge-Gerät erst seine neue Bereitstellung empfangen und alle Module starten muss. Danach wird bei den am Code von „CModule“ vorgenommenen Änderungen gewartet, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu. 

   ![Anzeigen von Nachrichten, die in der IoT Hub-Instanz eingehen](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „CModule“ verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Öffnen Sie in Visual Studio die Datei **deployment.windows-amd64.json**. (Öffnen Sie nicht die Datei „deployment.template“. Wird das Bereitstellungsmanifest nicht in der Datei „config“ im Projektmappen-Explorer angezeigt, wählen Sie auf der Explorer-Symbolleiste das Symbol **Alle Dateien anzeigen** aus.)

2. Suchen Sie den Zwilling „CModule“, und ändern Sie den Wert des Parameters **temperatureThreshold** in eine neue Temperatur, die fünf bis zehn Grad über der zuletzt gemeldeten Temperatur liegt. 

3. Speichern Sie die Datei **deployment.windows-amd64.json**.

4. Führen Sie erneut die Bereitstellungsschritte aus, um das aktualisierte Bereitstellungsmanifest auf Ihr Gerät anzuwenden. 

5. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Jetzt sollten die Nachrichten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden. Wenn Sie jetzt Ihre eigenen Module entwickeln möchten, können Sie sich ausführlicher über das [Entwickeln Ihrer eigenen IoT Edge-Module](module-development.md) oder das [Entwickeln von Modulen mit Visual Studio](how-to-visual-studio-develop-module.md) informieren. Sie können die nächsten Tutorials durcharbeiten, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
