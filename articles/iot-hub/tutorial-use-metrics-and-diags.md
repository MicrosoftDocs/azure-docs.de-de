---
title: Einrichten und Verwenden von Metriken und Diagnoseprotokollen mit einer Azure IoT Hub-Instanz | Microsoft-Dokumentation
description: Einrichten und Verwenden von Metriken und Diagnoseprotokollen mit einer Azure IoT Hub-Instanz
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 40e54daa60efedd84b32c72f29d1e2a8858c27da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162283"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutorial: Einrichten und Verwenden von Metriken und Diagnoseprotokollen mit einem IoT-Hub

Wenn Sie in Ihrer Produktionsumgebung über eine IoT Hub-Lösung verfügen, empfiehlt es sich, einige Metriken einzurichten und Diagnoseprotokolle zu aktivieren. So stehen Ihnen im Falle eines Problems Daten zur Verfügung, die Ihnen bei der Diagnose und schnelleren Behebung des Problems helfen. In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokolle aktivieren und auf Fehler überprüfen. Darüber hinaus werden in diesem Artikel einige zu überwachende Metriken sowie Warnungen eingerichtet, die ausgelöst werden, wenn bei den Metriken ein bestimmter Grenzwert erreicht wird. Sie können beispielsweise festlegen, dass Sie eine E-Mail erhalten möchten, wenn die Anzahl gesendeter Telemetrienachrichten einen bestimmten Grenzwert übersteigt oder wenn die Anzahl verwendeter Nachrichten sich dem zulässigen Tageskontingent für den IoT-Hub nähert. 

Ein möglicher Anwendungsfall wäre beispielsweise eine Tankstelle, deren Zapfsäulen als IoT-Geräte mit einem IoT-Hub kommunizieren. Kreditkarten werden überprüft, und die abschließende Transaktion wird in einen Datenspeicher geschrieben. Wenn die IoT-Geräte keine Verbindung mehr mit dem Hub herstellen und keine Nachrichten mehr senden, lässt sich dieses Problem deutlich schwieriger beheben, wenn Sie nicht nachvollziehen können, was vor sich geht.

In diesem Tutorial wird das Azure-Beispiel aus [Tutorial: Konfigurieren der Nachrichtenweiterleitung mit IoT Hub](tutorial-routing.md) verwendet, um Nachrichten an den IoT-Hub zu senden.

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen eines IoT-Hubs, eines simulierten Geräts und eines Speicherkontos mithilfe der Azure-Befehlszeilenschnittstelle  
> * Aktivieren von Diagnoseprotokollen
> * Aktivieren von Metriken
> * Einrichten von Warnungen für diese Metriken 
> * Herunterladen und Ausführen einer App, die ein IoT-Gerät simuliert, das Nachrichten an den Hub sendet 
> * Ausführen der App, bis die Warnungen ausgelöst werden 
> * Anzeigen der Metrikergebnisse und Überprüfen der Diagnoseprotokolle 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Installieren Sie [Visual Studio](https://www.visualstudio.com/). 

- Ein E-Mail-Konto für den Empfang von E-Mails.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Einrichten von Ressourcen

Für dieses Tutorial benötigen Sie einen IoT-Hub, ein Speicherkonto und ein simuliertes IoT-Gerät. Diese Ressourcen können mithilfe der Azure-Befehlszeilenschnittstelle oder über Azure PowerShell erstellt werden. Verwenden Sie für alle Ressourcen die gleiche Ressourcengruppe und den gleichen Speicherort. Am Ende dieses Tutorials können Sie dann sämtliche Elemente in einem Schritt entfernen, indem Sie die Ressourcengruppe löschen.

Erforderliche Schritte:

1. Erstellen Sie eine [Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). 

2. Erstellen Sie einen IoT Hub.

3. Erstellen Sie ein Speicherkonto des Typs „Standard V1“ mit Standard_LRS-Replikation.

4. Erstellen Sie eine Geräteidentität für das simulierte Gerät, das Nachrichten an Ihren Hub sendet. Speichern Sie den Schlüssel für die Testphase.

### <a name="set-up-resources-using-azure-cli"></a>Einrichten der Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle

Kopieren Sie dieses Skript, und fügen Sie es in Cloud Shell ein. Sofern Sie bereits angemeldet sind, wird das Skript Zeile für Zeile ausgeführt. Die neuen Ressourcen werden in der Ressourcengruppe „ContosoResources“ erstellt.

Die Variablen, die global eindeutig sein müssen, verfügen über den Zusatz `$RANDOM`. Wenn das Skript ausgeführt und die Variablen festgelegt werden, wird eine zufällige numerische Zeichenfolge generiert und am Ende der feststehenden Zeichenfolge angefügt, um sie eindeutig zu machen.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Beim Erstellen der Geräteidentität tritt unter Umständen folgender Fehler auf: *No keys found for policy iothubowner of IoT Hub ContosoTestHub.* (Für die Richtlinie „iothubowner“ des IoT-Hubs „ContosoTestHub“ wurden keine Schlüssel gefunden.) Aktualisieren Sie zur Behebung dieses Fehlers die IoT-Erweiterung der Azure-Befehlszeilenschnittstelle, und führen Sie die letzten beiden Befehle des Skripts erneut aus. 
>
>Im Anschluss finden Sie den Befehl zum Aktualisieren der Erweiterung. Führen Sie diesen Befehl in Ihrer Cloud Shell-Instanz aus.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Aktivieren der Diagnoseprotokolle 

[Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md) sind standardmäßig deaktiviert, wenn Sie einen neuen IoT-Hub erstellen. In diesem Abschnitt werden die Diagnoseprotokolle für Ihren Hub aktiviert.

1. Sollten Sie im Portal noch nicht zu Ihrem Hub navigiert sein, klicken Sie zunächst auf **Ressourcengruppen** und anschließend auf die Ressourcengruppe „Contoso-Resources“. Wählen Sie den Hub in der angezeigten Ressourcenliste aus. 

2. Suchen Sie auf dem Blatt des IoT-Hubs nach dem Abschnitt **Überwachung**. Klicken Sie auf **Diagnoseeinstellungen**. 

   ![Screenshot: Diagnoseeinstellungen auf dem Blatt des IoT-Hubs](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Vergewissern Sie sich, dass Abonnement und Ressourcengruppe korrekt sind. Deaktivieren Sie unter **Ressourcentyp** das Kontrollkästchen **Alle auswählen**. Suchen Sie anschließend nach **IoT Hub**, und aktivieren Sie das entsprechende Kontrollkästchen. (Dadurch wird auch wieder *Alle auswählen* aktiviert, das können Sie aber einfach ignorieren.) Wählen Sie unter **Ressource** den Namen des Hubs aus. Ihr Bildschirm sollte nun wie auf der folgenden Abbildung aussehen: 

   ![Screenshot: Diagnoseeinstellungen auf dem Blatt des IoT-Hubs](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Klicken Sie nun auf **Diagnose aktivieren**. Der Bereich „Diagnoseeinstellungen“ wird angezeigt. Geben Sie als Name für Ihre Diagnoseprotokolleinstellungen die Zeichenfolge „diags-hub“ an.

5. Aktivieren Sie **In einem Speicherkonto archivieren**. 

   ![Screenshot: Konfigurieren der Diagnose für die Archivierung in einem Speicherkonto](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Klicken Sie auf **Konfigurieren**, um den Bildschirm **Speicherkonto auswählen** anzuzeigen. Wählen Sie das korrekte Konto (*contosostoragemon*) aus, und klicken Sie auf **OK**, um zum Bereich mit den Diagnoseeinstellungen zurückzukehren. 

   ![Screenshot: Konfigurieren der Diagnoseprotokolle für die Archivierung in einem Speicherkonto](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Aktivieren Sie unter **PROTOKOLLIEREN** die Kontrollkästchen **Verbindungen** und **Gerätetelemetrie**, und legen Sie **Aufbewahrung (Tage)** jeweils auf sieben Tage fest. Der Bildschirm mit den Diagnoseeinstellungen sollte nun wie auf der folgenden Abbildung aussehen:

   ![Screenshot: Fertig eingerichtete Diagnoseprotokolleinstellungen](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Klicken Sie auf **Speichern** , um die Einstellungen zu speichern. Schließen Sie den Bereich mit den Diagnoseeinstellungen.

In den Diagnoseprotokollen können Sie sich später die Protokollierung der hergestellten und getrennten Verbindungen für das Gerät ansehen. 

## <a name="set-up-metrics"></a>Einrichten von Metriken 

Richten Sie als Nächstes einige Metriken ein, um zu überwachen, wann Nachrichten an den Hub gesendet werden. 

1. Klicken Sie im Einstellungsbereich für den IoT-Hub im Abschnitt **Überwachung** auf die Option **Metriken**.

2. Klicken Sie am oberen Bildschirmrand auf **Letzte 24 Stunden (Automatisch)** . Wählen Sie im daraufhin angezeigten Dropdownfeld unter **Zeitbereich** die Option **Letzte 4 Stunden** aus, und legen Sie die **Zeitgranularität** auf **1 Minute** (Ortszeit) fest. Klicken Sie auf **Anwenden**, um die Einstellungen zu speichern. 

   ![Screenshot: Zeiteinstellungen für Metriken](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Standardmäßig ist bereits ein Metrikeintrag vorhanden. Lassen Sie den Standardwert für die Ressourcengruppe sowie den Metriknamespace unverändert. Wählen Sie in der Dropdownliste **Metrik** die Option **Telemetry messages sent** (Gesendete Telemetrienachrichten) aus. Legen Sie **Aggregation** auf **Summe** fest.

   ![Screenshot: Hinzufügen einer Metrik für gesendete Telemetrienachrichten](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Klicken Sie nun auf **Metrik hinzufügen**, um dem Diagramm eine weitere Metrik hinzuzufügen. Wählen Sie Ihre Ressourcengruppe (**ContosoTestHub**) aus. Wählen Sie unter **Metrik** die Option **Total number of messages used** (Gesamtanzahl verwendeter Nachrichten) aus. Wählen Sie unter **Aggregation** die Option **Durchschn.** aus. 

   Auf dem Bildschirm werden nun die minimierte Metrik für *Telemetry messages sent* (Gesendete Telemetrienachrichten) und die neue Metrik für *Total number of messages used* (Gesamtanzahl verwendeter Nachrichten) angezeigt.

   ![Screenshot: Hinzufügen einer Metrik für gesendete Telemetrienachrichten](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klicken Sie auf **An Dashboard anheften**. Dadurch wird sie an das Dashboard des Azure-Portals angeheftet, damit Sie erneut darauf zugreifen können. Wenn Sie sie nicht an das Dashboard anheften, werden Ihre Einstellungen nicht gespeichert.

## <a name="set-up-alerts"></a>Einrichten von Warnungen

Navigieren Sie im Portal zum Hub. Klicken Sie auf **Ressourcengruppen**, wählen Sie *ContosoResources* aus, und wählen Sie anschließend den IoT-Hub *ContosoTestHub* aus. 

IoT Hub wurde noch nicht zu den [Metriken in Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics) migriert. Daher müssen [klassische Warnungen](/azure/azure-monitor/platform/alerts-classic.overview) verwendet werden.

1. Klicken Sie unter **Überwachung** auf **Warnungen**. Daraufhin wird der Hauptbildschirm für Warnungen angezeigt. 

   ![Screenshot: Navigieren zu klassischen Warnungen](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Klicken Sie hier auf **Klassische Warnungen anzeigen**, um zu den klassischen Warnungen zu gelangen. 

    ![Screenshot: Bildschirm für klassische Warnungen](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Füllen Sie die Felder aus: 

    **Abonnement**: Behalten Sie in diesem Feld Ihr aktuelles Abonnement bei.

    **Quelle**: Legen Sie dieses Feld auf *Metriken* fest.

    **Ressourcengruppe**: Legen Sie dieses Feld auf Ihre aktuelle Ressourcengruppe (*ContosoResources*) fest. 

    **Ressourcentyp:** Legen Sie dieses Feld auf „IoT Hub“ fest. 

    **Ressource:** Wählen Sie Ihren IoT-Hub (*ContosoTestHub*) aus.

3. Klicken Sie auf **Metrikwarnung hinzufügen (klassisch)** , um eine neue Warnung einzurichten.

    Füllen Sie die Felder aus:

    **Name**: Geben Sie einen Namen für Ihre Warnungsregel ein (beispielsweise *telemetry-messages*).

    **Beschreibung:** Geben Sie eine Beschreibung für Ihre Warnung ein. Beispiel: *alert when there are 1000 telemetry messages sent* (Warnen, wenn mehr als 1.000 Telemetrienachrichten gesendet wurden) 

    **Quelle**: Legen Sie diese Option auf *Metriken* fest.

    **Abonnement**, **Ressourcengruppe** und **Ressource** müssen auf die Werte festgelegt werden, die Sie auf dem Bildschirm **Klassische Warnungen anzeigen** ausgewählt haben. 

    Legen Sie **Metrik** auf *Telemetry messages sent* (Gesendete Telemetrienachrichten) fest.

    ![Screenshot: Einrichten einer klassischen Warnung für gesendete Telemetrienachrichten](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Legen Sie nach dem Diagramm die folgenden Felder fest:

   **Bedingung:** Legen Sie diese Option auf *Ist größer als* fest.

   **Schwellenwert**: Legen Sie diesen Wert auf „1.000“ fest.

   **Zeitraum:** Legen Sie diesen Wert auf *In den letzten 5 Minuten* fest.

   **Empfänger für E-Mail-Benachrichtigungen:** Geben Sie hier Ihre E-Mail-Adresse ein. 

   ![Screenshot: Untere Hälfte des Warnungsbildschirms](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Klicken Sie auf **OK**, um die Warnung zu speichern. 

5. Richten Sie nun eine weitere Warnung für *Total number of messages used* (Gesamtanzahl verwendeter Nachrichten) ein. Diese Metrik ist hilfreich, um eine Warnung zu senden, wenn sich die Anzahl verwendeter Nachrichten dem Kontingent für den IoT-Hub nähert, damit Sie wissen, dass der Hub bald keine Nachrichten mehr akzeptiert.

   Klicken Sie auf dem Bildschirm **Klassische Warnungen anzeigen** auf **Metrikwarnung hinzufügen (klassisch)** , und füllen Sie im Bereich **Regel hinzufügen** die folgenden Felder aus:

   **Name**: Geben Sie einen Namen für Ihre Warnungsregel ein (beispielsweise *number-of-messages-used*).

   **Beschreibung:** Geben Sie eine Beschreibung für Ihre Warnung ein. Beispiel: *alert when getting close to quota* (Warnen, wenn das Kontingent fast erreicht ist)

   **Quelle**: Legen Sie dieses Feld auf *Metriken* fest.

    **Abonnement**, **Ressourcengruppe** und **Ressource** müssen auf die Werte festgelegt werden, die Sie auf dem Bildschirm **Klassische Warnungen anzeigen** ausgewählt haben. 

    Legen Sie **Metrik** auf *Total number of messages used* (Gesamtanzahl verwendeter Nachrichten) fest.

6. Füllen Sie unter dem Diagramm die folgenden Felder aus:

   **Bedingung:** Legen Sie diese Option auf *Ist größer als* fest.

   **Schwellenwert**: Legen Sie diesen Wert auf „1.000“ fest.

   **Zeitraum:** Legen Sie dieses Feld auf *In den letzten 5 Minuten* fest. 

   **Empfänger für E-Mail-Benachrichtigungen:** Geben Sie hier Ihre E-Mail-Adresse ein. 

   Klicken Sie auf **OK**, um die Regel zu speichern. 

5. Im Bereich mit den klassischen Warnungen sollten nun zwei Warnungen angezeigt werden: 

   ![Screenshot: Bildschirm für klassische Warnungen mit den neuen Warnungsregeln](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Schließen Sie den Warnungsbereich. 
    
    Mit diesen Einstellungen erhalten Sie eine Warnung, wenn die Anzahl gesendeter Nachrichten größer ist als 400 und wenn die Gesamtanzahl verwendeter Nachrichten „NUMBER“ übersteigt.

## <a name="run-simulated-device-app"></a>Ausführen der App zur Simulation von Geräten

Weiter oben im Abschnitt zum Skriptsetup haben Sie ein Gerät für die Simulation mithilfe eines IoT-Geräts eingerichtet. In diesem Abschnitt laden Sie eine .NET-Konsolen-App herunter, die ein Gerät simuliert, das Gerät-zu-Cloud-Nachrichten an eine IoT Hub-Instanz sendet.  

Laden Sie die Lösung für die [IoT-Gerätesimulation](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Dieser Link lädt ein Repository mit zahlreichen Anwendungen herunter. Die von Ihnen benötigte Lösung befindet sich unter „iot-hub/Tutorials/Routing/“.

Doppelklicken Sie auf die Lösungsdatei (SimulatedDevice.sln), um den Code in Visual Studio zu öffnen. Öffnen Sie anschließend „Program.cs“. Ersetzen Sie `{iot hub hostname}` durch den IoT Hub-Hostnamen. Das Format des IoT Hub-Hostnamens ist **{iot-hub-name}.azure-devices.net**. Für dieses Tutorial lautet der Hubhostname **ContosoTestHub.azure-devices.net**. Ersetzen Sie jetzt `{device key}` durch den Geräteschlüssel, den Sie zuvor beim Einrichten des simulierten Geräts gespeichert haben. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Ausführen und Testen 

Ändern Sie in „Program.cs“ die Aufgabenverzögerung (`Task.Delay`) von 1.000 in 10, um das Sendeintervall für Nachrichten von einer Sekunde auf 0,01 Sekunden zu verringern. Die Verkürzung dieser Verzögerung führt zu einer höheren Anzahl gesendeter Nachrichten.

```csharp
await Task.Delay(10);
```

Führen Sie die Konsolenanwendung aus. Warten Sie etwa zehn bis 15 Minuten. Die Nachrichten, die das simulierte Gerät an den Hub sendet, sehen Sie auf dem Konsolenbildschirm der Anwendung.

### <a name="see-the-metrics-in-the-portal"></a>Anzeigen der Metriken im Portal

Öffnen Sie Ihre Metriken über das Dashboard. Legen Sie die Zeitwerte auf *Letzte 30 Minuten* mit einer Zeitgranularität von *1 Minute* fest. Daraufhin werden die Anzahl gesendeter Telemetrienachrichten und die Gesamtanzahl verwendeter Nachrichten im Diagramm angezeigt (mit den neuesten Zahlen am unteren Rand des Diagramms).

   ![Screenshot: Metriken](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Anzeigen der Warnungen

Kehren Sie zu den Warnungen zurück. Klicken Sie auf **Ressourcengruppen**, wählen Sie *ContosoResources* aus, und wählen Sie anschließend den Hub *ContosoTestHub* aus. Wählen Sie auf der für den Hub angezeigten Eigenschaftenseite **Warnungen** und anschließend **Klassische Warnungen anzeigen** aus. 

Wenn die Anzahl gesendeter Nachrichten den Grenzwert übersteigt, erhalten Sie E-Mail-Warnungen. Navigieren Sie zu Ihrem Hub, und wählen Sie **Warnungen** aus, um zu ermitteln, ob aktive Benachrichtigungen vorliegen. Daraufhin werden die aktiven Benachrichtigungen sowie mögliche Warnungen angezeigt. 

   ![Screenshot: Ausgelöste Warnungen](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klicken Sie auf die Warnung für Telemetrienachrichten. Daraufhin werden das Metrikergebnis und ein Diagramm mit den Ergebnissen angezeigt. Die E-Mail, die gesendet wurde, um Sie auf die ausgelöste Warnung aufmerksam zu machen, sieht wie auf der folgenden Abbildung aus:

   ![Screenshot: E-Mail zu den ausgelösten Warnungen](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Anzeigen der Diagnoseprotokolle

In diesem Abschnitt richten Sie Ihre Diagnoseprotokolle für den Export in Blob Storage ein. Navigieren Sie zu Ihrer Ressourcengruppe, und wählen Sie Ihr Speicherkonto (*contosostoragemon*) aus. Wählen Sie „Blobs“ aus, und öffnen Sie den Container *insights-logs-connections*. Navigieren Sie zum aktuellen Datum, und wählen Sie die neueste Datei aus. 

   ![Screenshot: Navigieren zu den Diagnoseprotokollen im Speichercontainer](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Klicken Sie auf **Herunterladen**, um sie herunterzuladen, und öffnen Sie sie. Daraufhin werden die Protokolle für das Herstellen und Trennen von Verbindungen des Geräts angezeigt, während es Nachrichten an den Hub sendet. Beispiel:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Wenn Sie alle für dieses Tutorial erstellten Ressourcen entfernen möchten, löschen Sie die Ressourcengruppe. Diese Aktion löscht alle in der Gruppe enthaltenen Ressourcen. In diesem Fall werden der IoT-Hub, das Speicherkonto und die Ressourcengruppe selbst entfernt. An das Dashboard angeheftete Metriken müssen manuell entfernt werden. Klicken Sie hierzu jeweils in der rechten oberen Ecke auf die drei Punkte, und wählen Sie **Entfernen** aus.

Um die Ressourcengruppe zu entfernen, verwenden Sie den Befehl [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Metriken und Diagnoseprotokolle verwenden. Hierzu haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen eines IoT-Hubs, eines simulierten Geräts und eines Speicherkontos mithilfe der Azure-Befehlszeilenschnittstelle  
> * Aktivieren von Diagnoseprotokollen 
> * Aktivieren von Metriken
> * Einrichten von Warnungen für diese Metriken 
> * Herunterladen und Ausführen einer App, die ein IoT-Gerät simuliert, das Nachrichten an den Hub sendet 
> * Ausführen der App, bis die Warnungen ausgelöst werden 
> * Anzeigen der Metrikergebnisse und Überprüfen der Diagnoseprotokolle 

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie den Status eines IoT-Geräts verwalten. 

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer Geräte über einen Back-End-Dienst](tutorial-device-twins.md)