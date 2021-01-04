---
title: 'Tutorial: Erstellen einer End-to-End-Lösung'
titleSuffix: Azure Digital Twins
description: Tutorial zur Erstellung einer Azure Digital Twins-End-to-End-Lösung, die auf Gerätedaten basiert.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: f788c9e78790e6872870869e2bc153e1b1451e51
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566536"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Tutorial: Erstellen einer End-to-End-Lösung

Für die Einrichtung einer vollständigen End-to-End-Lösung, die auf Livedaten aus Ihrer Umgebung basiert, können Sie für Ihre Azure Digital Twins-Instanz eine Verbindung mit anderen Azure-Diensten herstellen, um die Verwaltung von Geräten und Daten durchzuführen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten einer Azure Digital Twins-Instanz
> * Kennenlernen des Beispielszenarios für die Erstellung und Instanziieren der vorab geschriebenen Komponenten
> * Verwenden einer [Azure Functions](../azure-functions/functions-overview.md)-App zum Weiterleiten von simulierten Telemetriedaten von einem [IoT Hub](../iot-hub/about-iot-hub.md)-Gerät an Eigenschaften digitaler Zwillinge
> * Verteilen von Änderungen über den **Zwillingsgraphen** durch die Verarbeitung von Benachrichtigungen für digitale Zwillinge mit Azure Functions, Endpunkten und Routen

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Einrichten einer Cloud Shell-Sitzung
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Einstieg in die Erstellung

Im Beispielprojekt dieses Tutorials geht es um ein Szenario mit einem realen **Gebäude**, das über eine Etage, einen Raum und ein Thermostat verfügt. Diese Komponenten werden in einer Azure Digital Twins-Instanz digital dargestellt, die dann mit [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md) und zwei [Azure-Funktionen](../azure-functions/functions-overview.md) verbunden wird, um die Verschiebung von Daten zu ermöglichen.

Unten ist ein Diagramm mit dem vollständigen Szenario angegeben. 

Sie erstellen zuerst die Azure Digital Twins-Instanz (im Diagramm **Abschnitt A**) und richten dann den Fluss der Telemetriedaten zu den digitalen Zwillingen ein (**Pfeil B**). Anschließend richten Sie die Verteilung der Daten über den Zwillingsgraphen ein (**Pfeil C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Grafik mit dem vollständigen Gebäudeszenario. Enthält eine Darstellung des Datenflusses von einem Gerät an IoT Hub, über eine Azure-Funktion (Pfeil B) zu einer Azure Digital Twins-Instanz (Abschnitt A) und dann über Event Grid zu einer anderen Azure-Funktion für die Verarbeitung (Pfeil C).":::

Für dieses Szenario interagieren Sie mit den Komponenten der vorab geschriebenen Beispiel-App, die Sie weiter oben heruntergeladen haben.

Hier sind die Komponenten aufgeführt, die mit der Beispiel-App *AdtSampleApp* für das Gebäudeszenario implementiert werden:
* Geräte-Authentifizierung 
* Beispiele für die Nutzung des [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (in *CommandLoop.cs*)
* Konsolenschnittstelle zum Aufrufen der Azure Digital Twins-API
* *SampleClientApp*: Azure Digital Twins-Beispiellösung
* *SampleFunctionsApp*: Eine Azure Functions-App, mit der Ihr Azure Digital Twins-Graph basierend auf den Telemetriedaten von IoT Hub- und Azure Digital Twins-Ereignissen aktualisiert wird

Das Beispielprojekt enthält auch eine Komponente für die interaktive Autorisierung. Bei jedem Start des Projekts wird ein Browserfenster geöffnet, in dem Sie aufgefordert werden, sich mit Ihrem Azure-Konto anzumelden.

### <a name="instantiate-the-pre-created-twin-graph"></a>Instanziieren des vorab erstellten Zwillingsgraphen

Zunächst verwenden Sie die Lösung *AdtSampleApp* aus dem Beispielprojekt, um die Azure Digital Twins-Komponente des End-to-End-Szenarios zu erstellen (**Abschnitt A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Auszug aus der Grafik mit dem vollständigen Gebäudeszenario mit Hervorhebung von Abschnitt A (Azure Digital Twins-Instanz)":::

Führen Sie in Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, das Projekt mit dieser Symbolleistenschaltfläche aus:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio-Schaltfläche „Start“ (SampleClientApp-Projekt)":::

Ein Konsolenfenster wird geöffnet, die Authentifizierung wird durchgeführt, und dann wird auf einen Befehl gewartet. Führen Sie in dieser Konsole den nächsten Befehl aus, um die Azure Digital Twins-Beispiellösung zu instanziieren.

> [!IMPORTANT]
> Wenn Sie auf Ihrer Azure Digital Twins-Instanz bereits über digitale Zwillinge und Beziehungen verfügen, werden diese mit dem Ausführen dieses Befehls gelöscht und durch die Zwillinge und Beziehungen für das Beispielszenario ersetzt.

```cmd/sh
SetupBuildingScenario
```

Die Ausgabe dieses Befehls umfasst eine Reihe von Bestätigungsmeldungen, während auf Ihrer Azure Digital Twins-Instanz drei [**digitale Zwillinge**](concepts-twins-graph.md) erstellt und verbunden werden: die Etage *floor1*, der Raum *room21* und der Temperatursensor *thermostat67*. Diese digitalen Zwillinge stehen für die Entitäten, die auch in einer realen Umgebung vorhanden wären.

Sie werden über Beziehungen mit dem folgenden [**Zwillingsgraphen**](concepts-twins-graph.md) verbunden. Der Zwillingsgraph steht für die gesamte Umgebung, z. B. für die Interaktion der Entitäten und ihre Beziehung untereinander.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Graph: „floor1“ enthält „room21“, und „room21“ enthält „thermostat67“" border="false":::

Sie können die erstellten Zwillinge überprüfen, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden für die verbundene Azure Digital Twins-Instanz alle digitalen Zwillinge abgefragt, die sie enthält:

```cmd/sh
Query
```

>[!TIP]
> Diese vereinfachte Methode wird als Teil des _**AdtE2ESample**_-Projekts bereitgestellt. Außerhalb des Kontexts dieses Beispielcodes können Sie jederzeit alle Zwillinge in Ihrer Instanz abfragen, indem Sie die [Abfrage-APIs](/rest/api/digital-twins/dataplane/query) oder die [CLI-Befehle](how-to-use-cli.md) verwenden.
>
> Dies ist der vollständige Abfragetext, um alle digitalen Zwillinge in Ihrer Instanz abzurufen:
> 
> ```sql
> SELECT *
> FROM DIGITALTWINS
> ``` 

Anschließend können Sie die Ausführung des Projekts beenden. Lassen Sie die Projektmappe mit der Lösung aber in Visual Studio geöffnet, weil Sie sie für das Tutorial noch benötigen.

## <a name="set-up-the-sample-function-app"></a>Einrichten der Beispiel-Funktions-App

Der nächste Schritt umfasst die Einrichtung einer [Azure Functions-App](../azure-functions/functions-overview.md), die im gesamten Tutorial zum Verarbeiten von Daten verwendet wird. Die Funktions-App *SampleFunctionsApp* enthält zwei Funktionen:
* *ProcessHubToDTEvents*: Verarbeitet eingehende IoT Hub-Daten und aktualisiert Azure Digital Twins entsprechend.
* *ProcessDTRoutedData*: Verarbeitet Daten digitaler Zwillinge und aktualisiert die übergeordneten Zwillinge in Azure Digital Twins entsprechend.

In diesem Abschnitt veröffentlichen Sie die vorab geschriebene Funktions-App und stellen sicher, dass diese auf Azure Digital Twins zugreifen kann, indem Sie ihr eine Azure AD-Identität (Azure Active Directory) zuweisen. Wenn Sie diese Schritte ausführen, können im restlichen Teil des Tutorials die Funktionen in der Funktions-App verwendet werden. 

In Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, ist die Funktions-App in der Projektdatei _**SampleFunctionsApp**_ enthalten. Sie können sie im Bereich *Projektmappen-Explorer* anzeigen.

### <a name="update-dependencies"></a>Aktualisieren von Abhängigkeiten

Vor dem Veröffentlichen der App sollten Sie sicherstellen, dass Ihre Abhängigkeiten auf dem aktuellen Stand sind und dass Sie jeweils über die aktuelle Version aller enthaltenen Pakete verfügen.

Erweitern Sie im Bereich *Projektmappen-Explorer* die Option *SampleFunctionsApp > Abhängigkeiten*. Klicken Sie mit der rechten Maustaste auf *Pakete*, und wählen Sie *NuGet-Pakete verwalten...* aus.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: Verwalten von NuGet-Paketen für das Projekt „SampleFunctionsApp“" border="false":::

Der NuGet-Paket-Manager wird geöffnet. Wählen Sie die Registerkarte *Updates* aus. Falls zu aktualisierende Pakete vorhanden sind, aktivieren Sie das Kontrollkästchen *Alle Pakete auswählen*. Klicken Sie anschließend auf *Aktualisieren*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: Auswählen der Aktualisierung aller Pakete im NuGet-Paket-Manager":::

### <a name="publish-the-app"></a>Veröffentlichen der App

Wählen Sie im Visual Studio-Fenster mit dem geöffneten Projekt _**AdtE2ESample**_ im *Projektmappen-Explorer* mit der rechten Maustaste die Projektdatei _**SampleFunctionsApp**_ aus, und klicken Sie dann auf **Veröffentlichen**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: Projekt veröffentlichen":::

Behalten Sie auf der angezeigten Seite *Veröffentlichen* die Auswahl des Standardziels **Azure** bei, und wählen Sie die Option *Weiter* aus. 

Wenn Sie ein bestimmtes Ziel angeben möchten, müssen Sie **Azure-Funktions-App (Windows)** und dann *Weiter* auswählen.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Bestimmtes Ziel":::

Wählen Sie auf der Seite *Functions-Instanz* Ihr Abonnement aus. In einem Feld sollten die *Ressourcengruppen* Ihres Abonnements angezeigt werden.

Wählen Sie die Ressourcengruppe Ihrer Instanz aus, und klicken Sie auf *+ Neue Azure-Funktion erstellen...* .

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Functions-Instanz (vor Funktions-App)":::

Füllen Sie die Felder im Fenster *Funktions-App (Windows): Neu erstellen* wie folgt aus:
* **Name** ist der Name des Verbrauchsplans, der von Azure zum Hosten Ihrer Azure Functions-App verwendet wird. Dies ist dann auch der Name der Funktions-App, in der Ihre eigentliche Funktion enthalten ist. Sie können Ihren eigenen eindeutigen Wert auswählen oder den Standardvorschlag übernehmen.
* Achten Sie darauf, dass unter **Abonnement** das gewünschte Abonnement angegeben ist. 
* Achten Sie darauf, dass unter **Ressourcengruppe** die gewünschte Ressourcengruppe angegeben ist.
* Behalten Sie unter **Plantyp** die Option *Verbrauch* bei.
* Wählen Sie den **Standort** aus, der dem Standort Ihrer Ressourcengruppe entspricht.
* Erstellen Sie eine neue **Azure Storage**-Ressource, indem Sie den Link *Neu...* verwenden. Geben Sie denselben Standort wie für Ihre Ressourcengruppe an, behalten Sie die anderen Standardwerte bei, und klicken Sie auf „OK“.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Funktions-App (Windows): Neu erstellen":::

Wählen Sie anschließend **Erstellen**.

Sie sollten wieder auf die Seite *Functions-Instanz* gelangen, auf der Ihre neue Funktions-App jetzt unterhalb Ihrer Ressourcengruppe angezeigt wird. Klicken Sie auf *Fertig stellen*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Functions-Instanz (nach Funktions-App)":::

Vergewissern Sie sich im Bereich *Veröffentlichen*, der im Visual Studio-Fenster geöffnet wird, dass alle Informationen stimmen, und wählen Sie **Veröffentlichen** aus.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Veröffentlichen":::

> [!NOTE]
> Gehen Sie wie folgt vor, wenn dieses Popupfenster angezeigt wird: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Azure-Funktion „Veröffentlichen“ in Visual Studio: Anmeldeinformationen veröffentlichen" border="false":::
> Wählen Sie die Option **Attempt to retrieve credentials from Azure** (Versuchtes Abrufen von Anmeldeinformationen aus Azure) und dann **Speichern** aus.
>
> Gehen Sie wie folgt vor, falls Warnungen wie *Upgrade für Functions-Version in Azure* oder *Ihre Version der Functions-Runtime stimmt nicht mit der in Azure ausgeführten Version überein* angezeigt werden:
>
> Befolgen Sie die Anweisungen zum Durchführen des Upgrades auf die neueste Version der Azure Functions-Runtime. Dieses Problem kann auftreten, wenn Sie eine ältere Version von Visual Studio als die im Abschnitt *Voraussetzungen* am Anfang dieses Tutorials empfohlene verwenden.

### <a name="assign-permissions-to-the-function-app"></a>Zuweisen von Berechtigungen zur Funktions-App

Um für die Funktions-App den Zugriff auf Azure Digital Twins zu ermöglichen, müssen Sie als Nächstes die Schritte zum Konfigurieren einer App-Einstellung, Zuweisen einer vom System verwalteten Azure AD-Identität zur App und Erteilen der Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) für diese Identität in der Azure Digital Twins-Instanz ausführen. Diese Rolle ist für alle Benutzer oder Funktionen erforderlich, von denen auf der Instanz viele Datenebenenaktivitäten durchgeführt werden sollen. Weitere Informationen zu Sicherheits- und Rollenzuweisungen finden Sie unter [*Konzepte: Sicherheit für Azure Digital Twins-Lösungen*](concepts-security.md).

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Verwenden Sie in Azure Cloud Shell den folgenden Befehl, um eine Anwendungseinstellung festzulegen, die von Ihrer Funktions-App zum Verweisen auf Ihre Azure Digital Twins-Instanz verwendet wird.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

Verwenden Sie den folgenden Befehl, um die vom System verwaltete Identität zu erstellen. Beachten Sie in der Ausgabe das Feld *principalId*.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Verwenden Sie den Wert *principalId* aus der Ausgabe im folgenden Befehl, um die Identität der Funktions-App der Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) für Ihre Azure Digital Twins-Instanz zuzuweisen:

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

Die Ausgabe dieses Befehls enthält Informationen zur von Ihnen erstellten Rollenzuweisung. Die Funktions-App verfügt jetzt über Berechtigungen für den Zugriff auf Ihre Azure Digital Twins-Instanz.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Verarbeiten von simulierten Telemetriedaten von einem IoT Hub-Gerät

Ein Azure Digital Twins-Graph sollte auf Telemetriedaten von realen Geräten basieren. 

In diesem Schritt verbinden Sie ein simuliertes Thermostat, das unter [IoT Hub](../iot-hub/about-iot-hub.md) registriert ist, mit dem digitalen Zwilling, der das Gerät in Azure Digital Twins repräsentiert. Wenn das simulierte Gerät Telemetriedaten ausgibt, werden die Daten über die Azure-Funktion *ProcessHubToDTEvents* geleitet, die ein entsprechendes Update auf dem digitalen Zwilling auslöst. Auf diese Weise bleibt der digitale Zwilling über die Daten des realen Geräts auf dem Laufenden. In Azure Digital Twins wird das Leiten von Ereignisdaten von einem Ort an einen anderen als das [**Weiterleiten von Ereignissen**](concepts-route-events.md) bezeichnet.

Dies wird in diesem Teil des End-to-End-Szenarios durchgeführt (**Pfeil B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Auszug aus der Grafik mit dem vollständigen Gebäudeszenario mit Hervorhebung von Pfeil B (Elemente vor Azure Digital Twins: Gerät, IoT Hub und erste Azure-Funktion)":::

Hier sind die Aktionen aufgeführt, die Sie zum Einrichten dieser Geräteverbindung durchführen:
1. Erstellen eines IoT-Hubs zum Verwalten des simulierten Geräts
2. Verbinden des IoT-Hubs mit der entsprechenden Azure-Funktion, indem eine Ereignisabonnement eingerichtet wird
3. Registrieren des simulierten Geräts auf dem IoT-Hub
4. Ausführen des simulierten Geräts und Generieren von Telemetriedaten
5. Abfragen von Azure Digital Twins zum Anzeigen der Liveergebnisse

### <a name="create-an-iot-hub-instance"></a>Erstellen einer IoT Hub-Instanz

Azure Digital Twins ist für die Zusammenarbeit mit [IoT Hub](../iot-hub/about-iot-hub.md) (Azure-Dienst zum Verwalten von Geräten und den darauf enthaltenen Daten) ausgelegt. In diesem Schritt richten Sie einen IoT-Hub ein, mit dem das Beispielgerät in diesem Tutorial verwaltet wird.

Verwenden Sie in Azure Cloud Shell diesen Befehl, um einen neuen IoT-Hub zu erstellen:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Die Ausgabe dieses Befehls enthält Informationen zum erstellten IoT-Hub.

Speichern Sie den Namen, den Sie Ihrem IoT-Hub gegeben haben. Er wird später noch benötigt.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Verbinden des IoT-Hubs mit der Azure-Funktion

Verbinden Sie als Nächstes Ihren IoT-Hub mit der Azure-Funktion *ProcessHubToDTEvents* in der Funktions-App, die Sie weiter oben veröffentlicht haben. Dies ermöglicht den Datenfluss vom Gerät im IoT-Hub über die Funktion, mit der Azure Digital Twins aktualisiert wird.

Zu diesem Zweck erstellen Sie ein **Ereignisabonnement** auf Ihrem IoT-Hub mit der Azure-Funktion als Endpunkt. Hierdurch wird die Funktion für Ereignisse, die auf dem IoT-Hub auftreten, „abonniert“.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem neu erstellten IoT-Hub, indem Sie in der oberen Suchleiste nach dessen Namen suchen. Wählen Sie im Menü des Hubs die Option *Ereignisse* und dann *+ Ereignisabonnement* aus.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure-Portal: Ereignisabonnement für IoT Hub":::

Die Seite *Ereignisabonnement erstellen* wird angezeigt.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure-Portal: Ereignisabonnement erstellen":::

Füllen Sie die Felder wie folgt aus (standardmäßig ausgefüllte Felder werden nicht angegeben):
* *DETAILS ZUM EREIGNISABONNEMENT* > **Name**: Geben Sie Ihrem Ereignisabonnement einen Namen.
* *DETAILS ZUM THEMA* > **Name des Systemthemas**: Geben Sie einen Namen für das Systemthema an. 
* *EREIGNISTYPEN* > **Nach Ereignistypen filtern**: Wählen Sie im Menü die Option *Gerätetelemetrie* aus.
* *ENDPUNKTDETAILS* > **Endpunkttyp**: Wählen Sie im Menü die Option *Azure-Funktion* aus.
* *ENDPUNKTDETAILS* > **Endpunkt**: Klicken Sie auf den Link *Endpunkt auswählen*. Das Fenster *Azure-Funktion auswählen* wird geöffnet: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Ereignisabonnement im Azure-Portal: Azure-Funktion auswählen" border="false":::
    - Füllen Sie die Felder **Abonnement**, **Ressourcengruppe**, **Funktions-App** und **Funktion** (*ProcessHubToDTEvents*) aus. Einige Felder werden unter Umständen automatisch ausgefüllt, nachdem Sie das Abonnement ausgewählt haben.
    - Klicken Sie auf **Auswahl bestätigen**.

Wählen Sie auf der Seite *Ereignisabonnement erstellen* die Option **Erstellen** aus.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registrieren des simulierten Geräts für IoT Hub 

In diesem Abschnitt wird eine Darstellung des Geräts in IoT Hub mit der ID *thermostat67* erstellt. Das simulierte Gerät stellt hiermit eine Verbindung her. Auf diese Weise gelangen Telemetrieereignisse vom Gerät zu IoT Hub, wo die abonnierte Azure-Funktion aus dem vorherigen Schritt lauscht, um die Ereignisse auszuwählen und mit der Verarbeitung fortzufahren.

Erstellen Sie per Azure Cloud Shell ein Gerät in IoT Hub, indem Sie den folgenden Befehl verwenden:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Die Ausgabe enthält Informationen zum erstellten Gerät.

### <a name="configure-and-run-the-simulation"></a>Konfigurieren und Ausführen der Simulation

Konfigurieren Sie als Nächstes den Gerätesimulator für das Senden der Daten an Ihre IoT Hub-Instanz.

Rufen Sie zunächst mit dem folgenden Befehl die *IoT Hub-Verbindungszeichenfolge* ab:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Rufen Sie anschließend mit diesem Befehl die *Geräte-Verbindungszeichenfolge* ab:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Sie fügen diese Werte in den Gerätesimulatorcode Ihres lokalen Projekts ein, um den Simulator mit diesem IoT-Hub und dem IoT-Hub-Gerät zu verbinden.

Öffnen Sie in einem neuen Visual Studio-Fenster (im Ordner mit der heruntergeladenen Lösung) die Datei unter _Gerätesimulator > **DeviceSimulator.sln**_.

>[!NOTE]
> Sie sollten nun über zwei Visual Studio-Fenster verfügen: eins mit _**DeviceSimulator.sln**_ und ein bereits vorher geöffnetes mit _**AdtE2ESample.sln**_.

Wählen Sie im *Projektmappen-Explorer* in diesem neuen Visual Studio-Fenster _DeviceSimulator/**AzureIoTHub.cs**_ aus, um die Datei im Bearbeitungsfenster zu öffnen. Ändern Sie die folgenden Verbindungszeichenfolgen-Werte in die oben ermittelten Werte:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Speichern Sie die Datei.

Führen Sie nun das Projekt **DeviceSimulator** mit der folgenden Symbolleistenschaltfläche aus, um die Ergebnisse der von Ihnen eingerichteten Datensimulation anzuzeigen:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Schaltfläche „Start“ in Visual Studio (Projekt „DeviceSimulator“)":::

Im geöffneten Konsolenfenster werden Meldungen mit simulierten Telemetriedaten zur Temperatur angezeigt. Diese werden an IoT Hub gesendet, wo sie dann ausgewählt und von der Azure-Funktion verarbeitet werden.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Konsolenausgabe des Gerätesimulators mit gesendeten Telemetriedaten zur Temperatur":::

Sie müssen in dieser Konsole keine weiteren Schritte ausführen, aber Sie sollten sie während der nächsten Schritte geöffnet lassen.

### <a name="see-the-results-in-azure-digital-twins"></a>Anzeigen der Ergebnisse in Azure Digital Twins

Die von Ihnen weiter oben veröffentlichte Funktion *ProcessHubToDTEvents* lauscht auf die IoT Hub-Daten und ruft eine Azure Digital Twins-API auf, um die *Temperature*-Eigenschaft des Zwillings *thermostat67* zu aktualisieren.

Navigieren Sie zum Anzeigen der Daten aufseiten von Azure Digital Twins zu Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, und führen Sie das Projekt aus.

Führen Sie im geöffneten Konsolenfenster des Projekts den folgenden Befehl aus, um die Temperaturen abzurufen, die vom digitalen Zwilling *thermostat67* gemeldet werden:

```cmd
ObserveProperties thermostat67 Temperature
```

Sie sollten verfolgen können, wie die Liveaktualisierung der Temperaturen *von Ihrer Azure Digital Twins-Instanz* alle zehn Sekunden in der Konsole protokolliert wird.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Konsolenausgabe mit Protokollierung der Temperaturmeldungen des digitalen Zwillings „thermostat67“":::

Nachdem Sie überprüft haben, dass dies richtig funktioniert, können Sie die Ausführung beider Projekte beenden. Lassen Sie die Visual Studio-Fenster geöffnet, da Sie sie im weiteren Verlauf des Tutorials noch benötigen.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Verteilen von Azure Digital Twins-Ereignissen im Graphen

Bisher haben Sie in diesem Tutorial erfahren, wie Azure Digital Twins mit externen Gerätedaten aktualisiert werden kann. Als Nächstes wird beschrieben, wie Änderungen eines digitalen Zwillings über den Azure Digital Twins-Graphen verteilt werden können. Anders ausgedrückt: Wie Zwillinge anhand von internen Daten des Diensts aktualisiert werden.

Verwenden Sie hierfür die Azure-Funktion *ProcessDTRoutedData*, um einen Zwilling vom Typ *Room* zu aktualisieren, wenn der verbundene *Thermostat*-Zwilling aktualisiert wird. Dies wird in diesem Teil des End-to-End-Szenarios durchgeführt (**Pfeil C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Auszug aus der Grafik mit dem vollständigen Gebäudeszenario mit Hervorhebung von Pfeil C (Elemente nach Azure Digital Twins: Event Grid und zweite Azure-Funktion)":::

Hier sind die Aktionen angegeben, die Sie zum Einrichten dieses Datenflusses durchführen:
1. Erstellen eines Event Grid-Endpunkts in Azure Digital Twins zum Verbinden der Instanz mit Event Grid
2. Einrichten einer Route in Azure Digital Twins zum Senden von Ereignissen zur Änderung von Zwillingseigenschaften an den Endpunkt
3. Bereitstellen einer Azure Functions-App, die auf dem Endpunkt lauscht (über [Event Grid](../event-grid/overview.md)) und andere Zwillinge entsprechend aktualisiert
4. Ausführen des simulierten Geräts und Abfragen von Azure Digital Twins zum Anzeigen der Liveergebnisse

### <a name="set-up-endpoint"></a>Einrichten des Endpunkts

[Event Grid](../event-grid/overview.md) ist ein Azure-Dienst, mit dem Sie von Azure-Diensten stammende Ereignisse an andere Orte in Azure weiterleiten und dort bereitstellen können. Sie können ein [Event Grid-Thema](../event-grid/concepts.md) erstellen, um bestimmte Ereignisse von einer Quelle zu erfassen. Abonnenten können dann über das Thema lauschen, um Ereignisse zu erhalten, sobald sie verfügbar sind.

In diesem Abschnitt erstellen Sie ein Event Grid-Thema und erstellen dann in Azure Digital Twins einen Endpunkt, der auf dieses Thema verweist (bzw. Ereignisse sendet). 

Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um ein Event Grid-Thema zu erstellen:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Führen Sie den folgenden Befehl aus, um eine Liste mit Namen von Azure-Regionen auszugeben, die in der Azure CLI an Befehle übergeben werden können:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Die Ausgabe dieses Befehls enthält Informationen zum von Ihnen erstellten Event Grid-Thema.

Erstellen Sie als Nächstes einen Event Grid-Endpunkt in Azure Digital Twins, über den Ihre Instanz mit Ihrem Event Grid-Thema verbunden wird. Verwenden Sie den folgenden Befehl, und fügen Sie je nach Bedarf die Werte für die Platzhalter ein:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Die Ausgabe dieses Befehls enthält Informationen zum von Ihnen erstellten Endpunkt.

Sie können auch überprüfen, ob die Erstellung des Endpunkts erfolgreich war, indem Sie den folgenden Befehl zum Abfragen Ihrer Azure Digital Twins-Instanz für diesen Endpunkt ausführen:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Suchen Sie in der Ausgabe nach dem Feld `provisioningState`, und vergewissern Sie sich, dass der Wert „Succeeded“ lautet. Er kann auch „Provisioning“ lauten. Dies bedeutet, dass die Erstellung des Endpunkts noch nicht abgeschlossen ist. Warten Sie in diesem Fall einige Sekunden, und führen Sie den Befehl dann erneut aus, um sicherzustellen, dass er erfolgreich abgeschlossen wurde.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Ergebnis der Endpunktabfrage mit Anzeige von „Succeeded“ unter „provisioningState“ für den Endpunkt":::

Speichern Sie die Namen, die Sie Ihrem Event Grid-Thema und -Endpunkt in Azure Digital Twins gegeben haben. Sie benötigen sie später noch.

### <a name="set-up-route"></a>Einrichten der Route

Erstellen Sie als Nächstes eine Azure Digital Twins-Route, über die Ereignisse an den Event Grid-Endpunkt gesendet werden, den Sie gerade erstellt haben.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Die Ausgabe dieses Befehls enthält einige Informationen zur von Ihnen erstellten Route.

>[!NOTE]
>Die Bereitstellung der Endpunkte (aus dem vorherigen Schritt) muss abgeschlossen sein, bevor Sie eine Ereignisroute einrichten können, in der diese verwendet werden. Wenn die Routenerstellung fehlschlägt, weil die Endpunkte noch nicht bereit sind, sollten Sie einige Minuten warten und es dann erneut versuchen.

#### <a name="connect-the-function-to-event-grid"></a>Verbinden der Funktion mit Event Grid

Abonnieren Sie als Nächstes die Azure-Funktion *ProcessDTRoutedData* für das von Ihnen erstellte Event Grid-Thema. Dies ermöglicht den Fluss der Telemetriedaten vom Zwilling *thermostat67* über das Event Grid-Thema an die Funktion, mit der in Azure Digital Twins der Zwilling *room21* dann entsprechend aktualisiert wird.

Erstellen Sie hierfür ein **Event Grid-Abonnement** aus Ihrem Event Grid-Thema mit der Azure-Funktion *ProcessDTRoutedData* als Endpunkt.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Event Grid-Thema, indem Sie in der oberen Suchleiste nach dessen Namen suchen. Klicken Sie auf *+ Ereignisabonnement*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure-Portal: Event Grid-Ereignisabonnement":::

Die Schritte zum Erstellen dieses Ereignisabonnements ähneln den Schritten beim Abonnieren der ersten Azure-Funktion für IoT Hub weiter oben in diesem Tutorial. Dieses Mal müssen Sie nicht *Gerätetelemetrie* als Ereignistyp angeben, auf den gelauscht wird, und Sie verwenden für die Verbindungsherstellung eine andere Azure-Funktion.

Fügen Sie auf der Seite *Ereignisabonnement erstellen* die Felder wie folgt aus (standardmäßig ausgefüllte Felder werden nicht angegeben):
* *DETAILS ZUM EREIGNISABONNEMENT* > **Name**: Geben Sie Ihrem Ereignisabonnement einen Namen.
* *ENDPUNKTDETAILS* > **Endpunkttyp**: Wählen Sie im Menü die Option *Azure-Funktion* aus.
* *ENDPUNKTDETAILS* > **Endpunkt**: Klicken Sie auf den Link *Endpunkt auswählen*. Das Fenster *Azure-Funktion auswählen* wird geöffnet:
    - Füllen Sie die Felder **Abonnement**, **Ressourcengruppe**, **Funktions-App** und **Funktion** (*ProcessDTRoutedData*) aus. Einige Felder werden unter Umständen automatisch ausgefüllt, nachdem Sie das Abonnement ausgewählt haben.
    - Klicken Sie auf **Auswahl bestätigen**.

Wählen Sie auf der Seite *Ereignisabonnement erstellen* die Option **Erstellen** aus.

### <a name="run-the-simulation-and-see-the-results"></a>Ausführen der Simulation und Anzeigen der Ergebnisse

Nun können Sie den Gerätesimulator ausführen, um den neuen Ereignisfluss zu starten, den Sie eingerichtet haben. Navigieren Sie zu Ihrem Visual Studio-Fenster, in dem das Projekt _**DeviceSimulator**_ geöffnet ist, und führen Sie das Projekt aus.

Wie auch bei der Ausführung des Gerätesimulators wird ein Konsolenfenster geöffnet, in dem Meldungen mit simulierten Telemetriedaten zur Temperatur angezeigt werden. Diese Ereignisse durchlaufen den Datenfluss, den Sie weiter oben für die Aktualisierung des Zwillings *thermostat67* eingerichtet haben, und dann den Datenfluss, den Sie anschließend für die Aktualisierung des passenden Zwillings *room21* eingerichtet haben.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Konsolenausgabe des Gerätesimulators mit gesendeten Telemetriedaten zur Temperatur":::

Sie müssen in dieser Konsole keine weiteren Schritte ausführen, aber Sie sollten sie während der nächsten Schritte geöffnet lassen.

Navigieren Sie zum Anzeigen der Daten aufseiten von Azure Digital Twins zu Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, und führen Sie das Projekt aus.

Führen Sie im geöffneten Konsolenfenster des Projekts den folgenden Befehl aus, um die Temperaturen abzurufen, die **sowohl** vom digitalen Zwilling *thermostat67* als auch vom digitalen Zwilling *room21* gemeldet werden.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Sie sollten verfolgen können, wie die Liveaktualisierung der Temperaturen *von Ihrer Azure Digital Twins-Instanz* alle zehn Sekunden in der Konsole protokolliert wird. Beachten Sie, dass die Temperatur für *room21* aktualisiert wird, damit sie mit den Aktualisierungen für *thermostat67* übereinstimmt.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Konsolenausgabe mit Protokollierung der Temperaturmeldungen für ein Thermostat und einen Raum":::

Nachdem Sie überprüft haben, dass dies richtig funktioniert, können Sie die Ausführung beider Projekte beenden. Da das Tutorial nun abgeschlossen ist, können Sie auch die Visual Studio-Fenster schließen.

## <a name="review"></a>Überprüfung

Hier wird das in diesem Tutorial erstellte Szenario noch einmal zusammengefasst.

1. Eine Azure Digital Twins-Instanz verfügt über eine digitale Darstellung einer Etage, eines Raums und eines Thermostats (unten im Diagramm **Abschnitt A**).
2. Simulierte Gerätetelemetriedaten werden an die IoT Hub-Instanz gesendet, auf der die Azure-Funktion *ProcessHubToDTEvents* auf Telemetrieereignisse lauscht. Die Azure-Funktion *ProcessHubToDTEvents* nutzt die Informationen in diesen Ereignissen zum Festlegen der *Temperature*-Eigenschaft für *thermostat67* (**Pfeil B** im Diagramm).
3. Ereignisse zu Änderungen von Eigenschaften in Azure Digital Twins werden an ein Event Grid-Thema geleitet, unter dem die Azure-Funktion *ProcessDTRoutedData* auf Ereignisse lauscht. Die Azure-Funktion *ProcessDTRoutedData* nutzt die Informationen in diesen Ereignissen, um die *Temperature*-Eigenschaft für *room21* festzulegen (**Pfeil C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Grafik mit dem vollständigen Gebäudeszenario. Enthält eine Darstellung des Datenflusses von einem Gerät an IoT Hub, über eine Azure-Funktion (Pfeil B) zu einer Azure Digital Twins-Instanz (Abschnitt A) und dann über Event Grid zu einer anderen Azure-Funktion für die Verarbeitung (Pfeil C).":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcen nicht mehr benötigen, können Sie die folgenden Schritte zum Löschen ausführen. 

Bei Verwendung von [Azure Cloud Shell](https://shell.azure.com) können Sie alle Azure-Ressourcen in einer Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) löschen. Hierdurch werden die Ressourcengruppe, die Azure Digital Twins-Instanz, der IoT-Hub und die Registrierung des Hubgeräts, das Event Grid-Thema und die zugehörigen Abonnements sowie beide Azure Functions-Apps einschließlich beider Funktionen zugeordneter Ressourcen wie Speicher entfernt.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Löschen Sie abschließend den Projektbeispielordner, den Sie auf Ihren lokalen Computer heruntergeladen haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein End-to-End-Szenario erstellt, mit dem veranschaulicht wird, wie eine Azure Digital Twins-Instanz auf Livedaten von Geräten basiert.

Sehen Sie sich als Nächstes die Konzeptdokumentation an, um mehr über die Elemente zu erfahren, mit denen Sie in diesem Tutorial gearbeitet haben:

> [!div class="nextstepaction"]
> [*Konzepte: Grundlegendes zu Zwillingsmodellen in Azure Digital Twins*](concepts-models.md)