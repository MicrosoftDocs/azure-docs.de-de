---
title: Einrichten und Verwenden von Metriken und Protokollen mit einer Azure IoT Hub-Instanz
description: Erfahren Sie, wie Sie Metriken und Protokolle mit einer Azure IoT Hub-Instanz einrichten und verwenden. Auf diese Weise werden zu analysierende Daten bereitgestellt, damit eventuelle Probleme Ihres Hubs diagnostiziert werden können.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: a16dbeedc2362f4a263d59a673dbb4358f7ba034
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436486"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Tutorial: Einrichten und Verwenden von Metriken und Protokollen mit einem IoT-Hub

Mithilfe von Azure Monitor können Sie Metriken und Protokolle für Ihren IoT-Hub erfassen, mit denen Sie den Betrieb Ihrer Lösung überwachen und auftretende Probleme beheben können. In diesem Artikel erfahren Sie, wie Diagramme basierend auf Metriken erstellt werden, wie Warnungen erstellt werden, die für Metriken ausgelöst werden, wie Sie IoT-Hub-Vorgänge und Fehler an Azure Monitor-Protokolle senden und wie Sie die Protokolle auf Fehler überprüfen.

In diesem Tutorial wird das Azure-Beispiel aus [Schnellstart: Senden von .NET-Telemetriedaten](quickstart-send-telemetry-dotnet.md) verwendet, um Nachrichten an den IoT-Hub zu senden. Sie können immer ein Gerät oder ein anderes Beispiel zum Senden von Nachrichten verwenden, aber Sie müssen möglicherweise einige Schritte entsprechend ändern.

Eine gewisse Vertrautheit mit den Konzepten von Azure Monitor kann hilfreich sein, bevor Sie mit diesem Tutorial beginnen. Weitere Informationen finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md). Weitere Informationen zu den vom IoT-Hub ausgegebenen Metriken und Ressourcenprotokollen finden Sie unter [Referenz: Überwachen von Daten](monitor-iot-hub-reference.md).

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
>
> * Verwenden der Azure CLI, um einen IoT-Hub zu erstellen, ein simuliertes Gerät zu registrieren und einen Log Analytics-Arbeitsbereich zu erstellen.  
> * Senden von IoT-Hub-Verbindungen und Gerätetelemetrie-Ressourcenprotokollen an Azure Monitor-Protokolle im Log Analytics-Arbeitsbereich.
> * Verwenden des Metrik-Explorers, um ein Diagramm auf Grundlage ausgewählter Metriken zu erstellen und an das Dashboard anzuheften.
> * Erstellen von Metrikwarnungen, damit Sie über E-Mail benachrichtigt werden können, wenn wichtige Bedingungen auftreten.
> * Herunterladen und Ausführen einer App, die ein IoT-Gerät simuliert, das Nachrichten an den IoT-Hub sendet.
> * Anzeigen der Warnungen, wenn Ihre Bedingungen auftreten.
> * Anzeigen des Metrikdiagramms in Ihrem Dashboard.
> * Anzeigen von IoT-Hub-Fehlern und -Vorgängen in Azure Monitor Protokollen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Sie benötigen auf Ihrem Entwicklungscomputer das .NET Core SDK 2.1 oder höher. Sie können das .NET Core SDK für mehrere Plattformen von [.NET](https://www.microsoft.com/net/download/all) herunterladen.

  Mit dem folgenden Befehl können Sie die aktuelle C#-Version auf Ihrem Entwicklungscomputer überprüfen:

  ```cmd/sh
  dotnet --version
  ```

- Ein E-Mail-Konto für den Empfang von E-Mails.

- Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in diesem Tutorial wird das MQTT-Protokoll verwendet, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Einrichten von Ressourcen

Für dieses Tutorial benötigen Sie einen IoT-Hub, einen Log Analytics-Arbeitsbereich und ein simuliertes IoT-Gerät. Diese Ressourcen können mithilfe der Azure-Befehlszeilenschnittstelle oder über Azure PowerShell erstellt werden. Verwenden Sie für alle Ressourcen die gleiche Ressourcengruppe und den gleichen Speicherort. Nachdem Sie dieses Tutorial durchgearbeitet haben, können Sie sämtliche Elemente in einem Schritt entfernen, indem Sie die Ressourcengruppe löschen.

Die folgenden Schritte sind erforderlich.

1. Erstellen Sie eine [Ressourcengruppe](../azure-resource-manager/management/overview.md).

2. Erstellen Sie einen IoT Hub.

3. Erstellen Sie einen Log Analytics-Arbeitsbereich.

4. Registrieren Sie eine Geräteidentität für das simulierte Gerät, das Nachrichten an Ihren IoT-Hub sendet. Speichern Sie die Geräteverbindungszeichenfolge, die zum Konfigurieren des simulierten Geräts verwendet wird.

### <a name="set-up-resources-using-azure-cli"></a>Einrichten der Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle

Kopieren Sie dieses Skript, und fügen Sie es in Cloud Shell ein. Sofern Sie bereits angemeldet sind, wird das Skript Zeile für Zeile ausgeführt. Die Ausführung einiger Befehle kann einige Zeit in Anspruch nehmen. Die neuen Ressourcen werden in der Ressourcengruppe *ContosoResources* erstellt.

Der Name für einige Ressourcen muss innerhalb von Azure eindeutig sein. Das Skript generiert einen Zufallswert mit der `$RANDOM`-Funktion und speichert ihn in einer Variablen. Für diese Ressourcen fügt das Skript diesen Zufallswert an einen Basisnamen für die Ressource an, sodass der Ressourcenname eindeutig ist.

Pro Abonnement ist immer nur ein kostenloser IoT-Hub zulässig. Wenn Sie bereits über einen kostenlosen IoT-Hub in Ihrem Abonnement verfügen, löschen Sie ihn vor dem Ausführen des Skripts, oder ändern Sie das Skript so, dass es Ihren kostenlosen IoT-Hub oder einen IoT-Hub verwendet, die den Tarif „Standard“ oder „Basic“ verwendet.

Das Skript gibt den Namen des IoT-Hubs, den Namen des Log Analytics-Arbeitsbereichs und die Verbindungszeichenfolge für das Gerät, das es registriert. Notieren Sie sich diese Angaben, weil Sie sie später in diesem Artikel benötigen.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Beim Erstellen der Geräteidentität tritt unter Umständen folgender Fehler auf: *No keys found for policy iothubowner of IoT Hub ContosoTestHub.* (Für die Richtlinie „iothubowner“ des IoT-Hubs „ContosoTestHub“ wurden keine Schlüssel gefunden.) Aktualisieren Sie zur Behebung dieses Fehlers die IoT-Erweiterung der Azure-Befehlszeilenschnittstelle, und führen Sie die letzten beiden Befehle des Skripts erneut aus. 
>
>Im Anschluss finden Sie den Befehl zum Aktualisieren der Erweiterung. Führen Sie diesen Befehl in Ihrer Cloud Shell-Instanz aus.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Erfassen von Protokollen für Verbindungen und Gerätetelemetrie

IoT Hub gibt Ressourcenprotokolle für verschiedene Vorgangskategorien aus. Damit Sie diese Protokolle anzeigen können, müssen Sie jedoch eine Diagnoseeinstellung erstellen, um sie an ein Ziel zu senden. Eines dieser Ziele sind Azure Monitor-Protokolle, die in einem Log Analytics-Arbeitsbereich erfasst werden. IoT Hub -Ressourcenprotokolle sind in verschiedene Kategorien unterteilt. Sie können in der Diagnoseeinstellung auswählen, welche Kategorien an Azure Monitor-Protokolle gesendet werden sollen. In diesem Artikel erfassen wir Protokolle für Vorgänge und Fehler, die bei Verbindungen und Gerätetelemetrie auftreten. Eine vollständige Liste der Kategorien, die für IoT Hub unterstützt werden, finden Sie unter [IoT Hub-Ressourcenprotokolle](monitor-iot-hub-reference.md#resource-logs).

Führen Sie die folgenden Schritte aus, um eine Diagnoseeinstellung zu erstellen, um IoT Hub-Ressourcenprotokolle an Azure Monitor-Protokolle zu senden:

1. Sollten Sie im Portal noch nicht zu Ihrem Hub navigiert sein, wählen Sie zunächst **Ressourcengruppen** und anschließend die Ressourcengruppe „ContosoResources“ aus. Wählen Sie Ihren IoT-Hub aus der angezeigten Liste der Ressourcen aus.

1. Suchen Sie auf dem Blatt des IoT-Hubs nach dem Abschnitt **Überwachung**. Wählen Sie **Diagnoseeinstellungen** aus. Wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Screenshot, auf dem „Diagnoseeinstellungen“ im Abschnitt „Überwachung“ hervorgehoben ist":::

1. Legen Sie im Bereich **Diagnoseeinstellung** einen beschreibenden Namen für Ihre Einstellung fest, z. B. „Verbindungen und Telemetriedaten an Protokolle senden“.

1. Wählen Sie unter **Kategoriedetails** die Optionen **Verbindungen** und **Gerätetelemetrie** aus.

1. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** aus, und wählen Sie dann mithilfe der Log Analytics-Arbeitsbereichsauswahl den Arbeitsbereich aus, den Sie zuvor notiert haben. Wenn Sie fertig sind, sollte die Diagnoseeinstellung etwa wie der folgende Screenshot aussehen:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Screenshot: Fertig eingerichtete Diagnoseprotokolleinstellungen":::

1. Klicken Sie auf **Speichern**, um die Einstellungen zu speichern. Schließen Sie den Bereich **Diagnoseeinstellungen**. Sie können die neue Einstellung in der Liste der Diagnoseeinstellungen sehen.

## <a name="set-up-metrics"></a>Einrichten von Metriken

Wir verwenden nun den Metrik-Explorer, um ein Diagramm zu erstellen, das Metriken anzeigt, die Sie nachverfolgen möchten. Sie heften dieses Diagramm an Ihr Standarddashboard im Azure-Portal an.

1. Wählen Sie im linken Bereich Ihres IoT-Hubs die Option **Metriken** im Abschnitt **Überwachen** aus.

1. Wählen Sie am oberen Bildschirmrand **Letzte 24 Stunden (automatisch)** aus. Wählen Sie im daraufhin angezeigten Dropdownfeld unter **Zeitbereich** die Option **Letzte 4 Stunden** aus, und legen Sie die **Zeitgranularität** auf **1 Minute** fest. Wählen Sie dann für **Zeit anzeigen als** die Option **Ortszeit** aus. Wählen Sie **Übernehmen** aus, um diese Einstellungen zu speichern. Die Einstellung sollte nun **Ortszeit: Letzte 4 Stunden (1 Minute)** lauten.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Screenshot: Zeiteinstellungen für Metriken":::

1. Im Diagramm wird eine partielle Metrikeinstellung angezeigt, die für Ihren IoT-Hub gilt. Belassen Sie die Werte für **Bereich** und **Metriknamespace** bei ihren Standardwerten. Wählen Sie die Einstellung **Metrik** aus, und geben Sie „Telemetrie“ ein. Wählen Sie dann aus der Dropdownliste **Gesendete Telemetrienachrichten** aus. **Aggregation** wird automatisch auf **Summe** festgelegt. Beachten Sie, dass sich auch der Titel des Diagramms ändert.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Screenshot, der zeigt, wie dem Diagramm die Metrik „Gesendete Telemetrienachrichten“ hinzugefügt wird.":::

1. Wählen Sie nun **Metrik hinzufügen** aus, um dem Diagramm eine weitere Metrik hinzuzufügen. Wählen Sie unter **Metrik** die Option **Total number of messages used** (Gesamtanzahl verwendeter Nachrichten) aus. **Aggregation** wird automatisch auf **Mittelwert** festgelegt. Beachten Sie, dass sich der Titel des Diagramms erneut geändert hat und nun diese Metrik enthält.

   Auf dem Bildschirm werden nun die minimierte Metrik für *Telemetry messages sent* (Gesendete Telemetrienachrichten) und die neue Metrik für *Total number of messages used* (Gesamtanzahl verwendeter Nachrichten) angezeigt.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Screenshot, der zeigt, wie dem Diagramm die Metrik „Gesamtzahl verwendeter Nachrichten“ hinzugefügt wird.":::

1. Wählen Sie in der oberen rechten Ecke des Diagramms **An Dashboard anheften** aus.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Screenshot, auf dem die Schaltfläche „An Dashboard anheften“ hervorgehoben ist":::

1. Wählen Sie im Bereich **An Dashboard anheften** die Registerkarte **Vorhandene** aus. Wählen Sie **Privat** aus, und wählen Sie dann aus der Dropdownliste die Option **Dashboard** aus. Wählen Sie dann **Anheften** aus, um das Diagramm an Ihr Standarddashboard im Azure-Portal anzuheften. Wenn Sie das Diagramm nicht an ein Dashboard anheften, werden Ihre Einstellungen nicht beibehalten, wenn Sie den Metrik-Explorer beenden.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Screenshot, der Einstellungen für das Anheften an das Dashboard zeigt.":::

## <a name="set-up-metric-alerts"></a>Einrichten von Metrikwarnungen

Jetzt richten wir Warnungen ein, die für zwei Metriken (*Gesendete Telemetrienachrichten* und *Gesamtzahl verwendeter Nachrichten*) ausgelöst werden.

*Gesendete Telemetrienachrichten* ist eine gute Metrik zum Nachverfolgen des Nachrichtendurchsatzes und Vermeiden von Drosselung. Für einen IoT-Hub im Free-Tarif beträgt der Drosselungsgrenzwert 100 Nachrichten/Sek. Mit einem einzelnen Gerät können wir diese Art von Durchsatz nicht erreichen. Daher richten wir die Warnung so ein, dass sie ausgelöst wird, wenn die Anzahl der Nachrichten in einem Zeitraum von fünf Minuten 1.000 überschreitet. In der Produktion können Sie das Signal basierend auf dem Tarif, der Edition und der Anzahl von Einheiten Ihres IoT-Hubs auf einen signifikanteren Wert festlegen.

*Gesamtzahl verwendeter Nachrichten* verfolgt die tägliche Anzahl der verwendeten Nachrichten nach. Diese Metrik wird jeden Tag um 00:00 Uhr UTC zurückgesetzt. Wenn Sie das Tageskontingent über einem bestimmten Schwellenwert hinaus überschreiten, akzeptiert Ihr IoT-Hub keine weiteren Nachrichten. Bei einem IoT-Hub im Free-Tarif beträgt das tägliche Nachrichtenkontingent 8.000. Wir richten die Warnung so ein, dass sie ausgelöst wird, wenn die Gesamtzahl der Nachrichten 4.000 (50 % des Kontingents) überschreitet. In der Praxis würden Sie diesen Prozentsatz wahrscheinlich auf einen höheren Wert festlegen. Der Wert für das tägliche Kontingent hängt vom Tarif, der Edition und der Anzahl von Einheiten Ihres IoT-Hubs ab.

Weitere Informationen zu Kontingenten und Drosselungsgrenzwerten für IoT Hub finden Sie unter [Kontingente und Drosselung](iot-hub-devguide-quotas-throttling.md).

Einrichten von Metrikwarnungen:

1. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub.

1. Wählen Sie unter **Überwachung** die Option **Warnungen** aus. Wählen Sie dann **Neue Warnungsregel** aus.  Der Bereich **Warnungsregel erstellen** wird geöffnet.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Screenshot des Bereichs „Warnungsregel erstellen“.":::

    Der Bereich **Warnungsregel erstellen** weist vier Abschnitte auf:

    * **Bereich** ist bereits auf Ihren IoT-Hub festgelegt, sodass wir diesen Abschnitt unverändert beibehalten.
    * **Bedingung** legt das Signal und die Bedingungen fest, mit denen die Warnung ausgelöst wird.
    * **Aktionen** konfiguriert, was geschieht, wenn die Warnung ausgelöst wird.
    * Unter **Benachrichtigungsregeldetails** können Sie einen Namen und eine Beschreibung für die Warnung festlegen.

1. Konfigurieren Sie zunächst die Bedingung, bei der die Warnung ausgelöst wird.

    1. Wählen Sie unter **Bedingung** die Option **Bedingung auswählen** aus. Geben Sie im Bereich **Signallogik konfigurieren** die Angabe „Telemetrie“ in das Suchfeld ein, und wählen Sie **Gesendete Telemetrienachrichten** aus.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Screenshot, der das Auswählen der Metrik zeigt.":::

    1. Legen Sie im Bereich **Signallogik konfigurieren** die folgenden Felder unter **Warnungslogik** fest, oder bestätigen Sie sie (Sie können das Diagramm ignorieren):

       **Schwellenwert**:  *Statisch*

       **Operator**: *Größer als*.

       **Aggregationstyp**: *Gesamt*.

       **Schwellenwert**: 1.000.

       **Aggregationsgranularität (Zeitraum)** : *5 Minuten*.

       **Häufigkeit der Auswertung**: *Jede Minute*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Screenshot der Einstellungen für Warnungsbedingungen.":::

       Mit diesen Einstellungen wird das Signal so festgelegt, dass die Gesamtzahl der Nachrichten in einem Zeitraum von 5 Minuten angezeigt wird. Diese Summe wird jede Minute ausgewertet, und wenn die Summe der vorangegangenen 5 Minuten den Wert von 1.000 Nachrichten überschreitet, wird die Warnung ausgelöst.

       Wählen Sie **Fertig** aus, um die Signallogik zu speichern.

1. Konfigurieren Sie nun die Aktion für die Warnung.

    1. Wählen Sie im Bereich **Warnungsregel erstellen** unter **Aktionen** die Option **Aktionsgruppe auswählen** aus. Wählen Sie im Bereich **Aktionsgruppe zum Anfügen an diese Warnungsregel auswählen** die Option **Aktionsgruppe erstellen** aus.

    1. Legen Sie auf der Registerkarte **Grundlagen** im Bereich **Aktionsgruppe erstellen** einen Namen und einen Anzeigenamen für Ihre Aktionsgruppe fest.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Screenshot der Registerkarte „Grundlagen“ des Bereichs „Aktionsgruppe erstellen“.":::

    1. Wählen Sie die Registerkarte **Benachrichtigungen** aus. Wählen Sie als **Benachrichtigungstyp** in der Dropdownliste **E-Mail/SMS/Push/Sprachanruf** aus. Der Bereich **E-Mail/SMS/Push/Sprachanruf** wird geöffnet.

    1. Wählen Sie im Bereich **E-Mail/SMS/Push/Sprachanruf** die Option „E-Mail“ aus, geben Sie Ihre E-Mail-Adresse ein, und wählen Sie dann **OK** aus.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Screenshot der Einstellung „E-Mail-Adresse“.":::

    1. Geben Sie im Bereich **Benachrichtigungen** einen Namen für die Benachrichtigung ein.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Screenshot des abgeschlossenen Benachrichtigungsbereichs.":::

    1. (Optional) Wenn Sie die Registerkarte **Aktionen** und dann in der Dropdownliste **Aktionstyp** auswählen, werden die Arten von Aktionen angezeigt, die mit einer Warnung ausgelöst werden können. Für diesen Artikel verwenden wir nur Benachrichtigungen, sodass Sie die Einstellungen auf dieser Registerkarte ignorieren können.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Screenshot, der Aktionstypen zeigt, die im Bereich „Aktionen“ verfügbar sind.":::

    1. Wählen Sie die Registerkarte **Überprüfen und erstellen** aus, überprüfen Sie die Einstellungen, und wählen Sie dann **Erstellen** aus.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Screenshot des Bereichs „Überprüfen und erstellen“.":::

    1. Beachten Sie im Bereich **Warnungsregel erstellen**, dass die neue Aktionsgruppe den Aktionen für die Warnung hinzugefügt wurde.  

1. Konfigurieren Sie schließlich die Details der Warnungsregel, und speichern Sie die Warnungsregel.

    1. Geben Sie im Bereich **Warnungsregel erstellen** unter „Warnungsregeldetails“ einen Namen und eine Beschreibung für die Warnung ein. Beispiel: „Warnen, wenn mehr als 1.000 Nachrichten in 5 Minuten eingehen“. Stellen Sie sicher, dass **Warnungsregel bei Erstellung aktivieren** aktiviert ist. Die fertiggestellte Warnungsregel sieht in etwa wie dieser Screenshot aus.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Screenshot des fertiggestellten Bereichs „Warnungsregel erstellen“.":::

    1. Wählen Sie **Warnungsregel erstellen** aus, um Ihre neue Regel zu speichern.

1. Richten Sie nun eine weitere Warnung für *Total number of messages used* (Gesamtanzahl verwendeter Nachrichten) ein. Diese Metrik ist hilfreich, um eine Warnung zu senden, wenn sich die Anzahl verwendeter Nachrichten dem Tageskontingent für den IoT-Hub nähert. Wenn dieses überschritten wird, weist der IoT-Hub Nachrichten zurück. Befolgen Sie die Schritte, die Sie zuvor ausgeführt haben, mit den folgenden Unterschieden.

    * Wählen Sie für das Signal im Bereich **Signallogik konfigurieren** die Option **Gesamtzahl verwendeter Nachrichten** aus.

    * Legen Sie im Bereich **Signallogik konfigurieren** die folgenden Felder fest, oder bestätigen Sie sie (Sie können das Diagramm ignorieren):

       **Schwellenwert**:  *Statisch*

       **Operator**: *Größer als*.

       **Aggregationstyp**: *Maximum*.

       **Schwellenwert**: 4.000.

       **Aggregationsgranularität (Zeitraum)** : *1 Minute*.

       **Häufigkeit der Auswertung**: *Jede Minute*

       Diese Einstellungen legen das Signal fest,das ausgelöst wird, wenn die Anzahl der Nachrichten 4.000 erreicht. Die Metrik wird jede Minute ausgewertet.

    * Wenn Sie die Aktion für Ihre Warnungsregel angeben, wählen Sie einfach die zuvor erstellte Aktionsgruppe aus.

    * Wählen Sie für die Warnungsdetails einen anderen Namen und eine andere Beschreibung als zuvor aus.

1. Wählen Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Warnungen** aus. Wählen Sie jetzt **Warnungsregeln verwalten** im Menü oben im Bereich **Warnungen** aus. Der Bereich **Regeln** wird geöffnet. Nun sollten Sie Ihre zwei Warnungen sehen:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Screenshot des Bereichs „Regeln“ mit den neuen Warnungsregeln.":::

1. Schließen Sie den Bereich **Regeln**.

Mit diesen Einstellungen wird eine Warnung ausgelöst, und Sie erhalten eine E-Mail-Benachrichtigung, wenn mehr als 1.000 Nachrichten innerhalb eines Zeitraums von 5 Minuten gesendet werden, bzw. wenn die Gesamtzahl der verwendeten Nachrichten 4.000 (50 % des täglichen Kontingents für einen IoT-Hub im Free-Tarif) überschreitet.

## <a name="run-the-simulated-device-app"></a>Ausführen der simulierten Geräte-App

Im Abschnitt [Ressourcen einrichten](#set-up-resources) haben Sie eine Geräteidentität registriert, die zum Simulieren mithilfe eines IoT-Geräts verwendet werden soll. In diesem Abschnitt laden Sie eine .NET-Konsolen-App herunter, die ein Gerät simuliert, das Gerät-zu-Cloud-Nachrichten an eine IoT Hub-Instanz sendet, konfigurieren das Senden dieser Nachrichten an Ihren IoT-Hub, und führen die App dann aus. 

> [!IMPORTANT]
>
> Es kann bis zu 10 Minuten dauern, bis Warnungen vollständig konfiguriert sind und durch IoT Hub aktiviert werden. Warten Sie mindestens 10 Minuten nach dem Konfigurieren der letzten Warnung, bis Sie die simulierte Geräte-App ausführen.

Laden Sie die Lösung für die [IoT-Gerätesimulation](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Dieser Link lädt ein Repository mit mehreren Anwendungen herunter. Die von Ihnen benötigte Anwendung befindet sich unter „iot-hub/Quickstarts/simulated-device/“.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner der Lösung. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device**.

1. Öffnen Sie die Datei **SimulatedDevice.cs** in einem Text-Editor Ihrer Wahl.

    1. Ersetzen Sie den Wert der `s_connectionString`-Variablen durch die Geräteverbindungszeichenfolge, die Sie notiert haben, als Sie das Skript zum Einrichten von Ressourcen ausgeführt haben.

    1. Ändern Sie in der `SendDeviceToCloudMessagesAsync`-Methode `Task.Delay` von 1.000 in 1, um das Sendeintervall für Nachrichten von einer Sekunde auf 0,001 Sekunden zu verringern. Die Verkürzung dieser Verzögerung führt zu einer höheren Anzahl gesendeter Nachrichten. (Sie erhalten wahrscheinlich keine Nachrichtenrate von 100 Nachrichten pro Sekunde.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Speichern Sie die Änderungen an der Datei **SimulatedDevice.cs**.

1. Führen Sie im lokalen Terminalfenster den folgenden Befehl aus, um die erforderlichen Pakete für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

1. Führen Sie im lokalen Terminalfenster den folgenden Befehl aus, um die simulierte Geräteanwendung zu erstellen und auszuführen:

    ```cmd/sh
    dotnet run
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Screenshot der simulierten Geräteausgabe.":::

Führen Sie die Anwendung mindestens 10 bis 15 Minuten lang aus. Im Idealfall führen Sie die Anwendung aus, bis sie das Senden von Nachrichten einstellt (ungefähr 20 bis 30 Minuten). Dies geschieht, wenn Sie das tägliche Nachrichtenkontingent für Ihren IoT-Hub überschritten haben und keine weiteren Nachrichten mehr akzeptiert werden.

> [!NOTE]
> Wenn Sie die Geräte-App für einen längeren Zeitraum ausführen, nachdem sie das Senden von Nachrichten beendet hat, wird möglicherweise eine Ausnahme ausgelöst. Sie können diese Ausnahme ignorieren und das App-Fenster schließen.

## <a name="view-metrics-chart-on-your-dashboard"></a>Anzeigen des Metrikdiagramms in Ihrem Dashboard

1. Öffnen Sie in der oberen linken Ecke des Azure-Portals das Portalmenü, und wählen Sie dann **Dashboard** aus.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Screenshot: Auswählen des Dashboards.":::

1. Suchen Sie das zuvor angeheftete Diagramm, und klicken Sie auf die Kachel außerhalb der Diagrammdaten, um sie zu erweitern. Daraufhin werden die Anzahl gesendeter Telemetrienachrichten und die Gesamtzahl verwendeter Nachrichten im Diagramm angezeigt. Unten im Diagramm werden die neuesten Werte angezeigt. Sie können den Cursor im Diagramm verschieben, um die Metrikwerte für bestimmte Zeiten anzuzeigen. Sie können auch den Zeitwert und die Granularität am oberen Rand des Diagramms ändern, um die Daten auf einen bestimmten Zeitraum einzugrenzen oder sie für diesen zu erweitern.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Screenshot: Metrikdiagramm.":::

   In diesem Szenario ist der Nachrichtendurchsatz des simulierten Geräts nicht groß genug, um zu bewirken, dass IoT Hub die Nachrichten drosselt. In einem Szenario, das tatsächlich Drosselung beinhaltet, kann es vorkommen, dass die gesendeten Telemetrienachrichten den Drosselungsgrenzwert für Ihren IoT-Hub für einen begrenzten Zeitraum überschreiten. Dies dient der Ermöglichung von Burstdatenverkehr. Weitere Informationen finden Sie unter [Datenverkehrsstrukturierung](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Anzeigen der Warnungen

Wenn die Anzahl gesendeter Nachrichten die Grenzwerte übersteigt, die Sie in den Warnungsregeln festgelegt haben, erhalten Sie E-Mail-Warnungen.

Um zu ermitteln, ob aktive Warnungen vorhanden sind, wählen Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Warnungen** aus. Im Bereich **Warnungen** wird die Anzahl der Warnungen nach Schweregrad sortiert angezeigt, die für den angegebenen Zeitraum ausgelöst wurden.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Screenshot: Zusammenfassung der Warnungen.":::

Wählen Sie die Zeile für den Schweregrad 3 aus. Der Bereich **Alle Warnungen** wird geöffnet, und die ausgelösten Warnungen mit dem Schweregrad 3 werden aufgelistet.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Screenshot: Bereich „Alle Warnungen“.":::

Wählen Sie eine der Warnungen aus, um die Warnungsdetails anzuzeigen.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Screenshot: „Warnungsdetails“.":::

Überprüfen Sie Ihren Posteingang auf E-Mails von Microsoft Azure. In der Betreffzeile wird die Warnung beschrieben, die ausgelöst wurde. Beispiel: *Azure: Aktivierter Schweregrad: 3. Warnung, wenn mehr als 1.000 Nachrichten innerhalb von 5 Minuten vorhanden sind*. Der Text sieht ähnlich wie die folgende Abbildung aus:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Screenshot: E-Mail zu den ausgelösten Warnungen":::

## <a name="view-azure-monitor-logs"></a>Anzeigen von Azure Monitor-Protokollen

Im Abschnitt [Protokolle für Verbindungen und Gerätetelemetrie erfassen](#collect-logs-for-connections-and-device-telemetry) haben Sie eine Diagnoseeinstellung zum Senden von Ressourcenprotokollen erstellt, die von Ihrem IoT-Hub für Verbindungen und Gerätetelemetrievorgänge an Azure Monitor-Protokolle ausgegeben werden. In diesem Abschnitt führen Sie eine Kusto-Abfrage für Azure Monitor-Protokolle aus, um aufgetretene Fehler zu beobachten.

1. Wählen Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** im Azure-Portal die Option **Protokolle** aus. Schließen Sie das zunächst angezeigte Fenster **Abfragen**, wenn es geöffnet werden sollte.

1. Wählen Sie im Bereich „Neue Abfrage“ die Registerkarte **Abfragen** aus, und erweitern Sie dann **IoT Hub**, um die Liste der Standardabfragen anzuzeigen.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Screenshot der Standardabfragen von IoT Hub.":::

1. Wählen Sie die Abfrage *Error Summary* (Fehlerzusammenfassung) aus. Die Abfrage wird im Bereich „Abfrage-Editor“ angezeigt. Wählen Sie im Editor-Bereich **Ausführen** aus, und beobachten Sie die Abfrageergebnisse. Erweitern Sie eine der Zeilen, um Details anzuzeigen.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Screenshot der Protokolle, die von der Fehlerzusammenfassungsabfrage zurückgegeben werden.":::

   > [!NOTE]
   > Wenn keine Fehler angezeigt werden, führen Sie die Abfrage *Recently connected devices* (Zuletzt verbundene Geräte) aus. Diese sollte eine Zeile für das simulierte Gerät zurückgeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle für dieses Tutorial erstellten Ressourcen entfernen möchten, löschen Sie die Ressourcengruppe. Diese Aktion löscht alle in der Gruppe enthaltenen Ressourcen. In diesem Fall werden der IoT-Hub, der Log Analytics-Arbeitsbereich und die Ressourcengruppe selbst entfernt. An das Dashboard angeheftete Metrikdiagramme müssen manuell entfernt werden. Klicken Sie hierzu jeweils in der rechten oberen Ecke jedes Diagramms auf die drei Punkte, und wählen Sie dann **Entfernen** aus. Stellen Sie sicher, dass Sie die Änderungen speichern, nachdem Sie die Diagramme gelöscht haben.

Um die Ressourcengruppe zu entfernen, verwenden Sie den Befehl [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie IoT Hub-Metriken und Protokolle verwenden. Hierzu haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
>
> * Verwenden der Azure CLI, um einen IoT-Hub zu erstellen, ein simuliertes Gerät zu registrieren und einen Log Analytics-Arbeitsbereich zu erstellen.  
> * Senden von IoT-Hub-Verbindungen und Gerätetelemetrie-Ressourcenprotokollen an Azure Monitor-Protokolle im Log Analytics-Arbeitsbereich.
> * Verwenden des Metrik-Explorers, um ein Diagramm auf Grundlage ausgewählter Metriken zu erstellen und an das Dashboard anzuheften.
> * Erstellen von Metrikwarnungen, damit Sie über E-Mail benachrichtigt werden können, wenn wichtige Bedingungen auftreten.
> * Herunterladen und Ausführen einer App, die ein IoT-Gerät simuliert, das Nachrichten an den IoT-Hub sendet.
> * Anzeigen der Warnungen, wenn Ihre Bedingungen auftreten.
> * Anzeigen des Metrikdiagramms in Ihrem Dashboard.
> * Anzeigen von IoT-Hub-Fehlern und -Vorgängen in Azure Monitor Protokollen.

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie den Status eines IoT-Geräts verwalten. 

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer Geräte über einen Back-End-Dienst](tutorial-device-twins.md)