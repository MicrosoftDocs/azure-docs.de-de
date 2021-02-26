---
title: 'Tutorial: Nutzen der dynamischen Konfiguration per Pushaktualisierung in einer .NET Core-App'
titleSuffix: Azure App Configuration
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für .NET Core-Apps per Pushaktualisierung dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 553c5081947ad784a8cdae6ad0eb92fc3e2a2c85
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981856"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutorial: Nutzen der dynamischen Konfiguration per Pushaktualisierung in einer .NET Core-App

Die .NET Core-Clientbibliothek von App Configuration unterstützt die bedarfsgesteuerte Aktualisierung der Konfigurationseinstellungen, ohne dass eine Anwendung neu gestartet werden muss. Eine Anwendung kann mit einem oder beiden der folgenden Ansätze so konfiguriert werden, dass App Configuration-Änderungen erkannt werden.

1. Abrufmodell: Dies ist das Standardverhalten, bei dem Änderungen an der Konfiguration per Abruf erkannt werden. Nachdem der zwischengespeicherte Wert einer Einstellung abgelaufen ist, wird beim nächsten Aufruf von `TryRefreshAsync` oder `RefreshAsync` eine Anforderung an den Server gesendet, um zu überprüfen, ob sich die Konfiguration geändert hat. Bei Bedarf wird die aktualisierte Konfiguration dann per Pullvorgang übertragen.

1. Pushmodell: Hierbei werden [App Configuration-Ereignisse](./concept-app-configuration-event.md) verwendet, um Änderungen an der Konfiguration zu erkennen. Nachdem für App Configuration das Senden von Ereignissen mit Schlüsselwertänderungen an Azure Event Grid eingerichtet wurde, können diese Ereignisse von der Anwendung genutzt werden, um die Gesamtzahl von Anforderungen zu optimieren, mit denen die Konfiguration auf dem aktuellen Stand gehalten werden kann. Diese können von Anwendungen entweder direkt von Event Grid abonniert werden, oder es können [unterstützte Ereignishandler](https://docs.microsoft.com/azure/event-grid/event-handlers), z. B. ein Webhook, eine Azure-Funktion oder ein Service Bus-Thema genutzt werden.

Von Anwendungen können diese Ereignisse entweder direkt aus Event Grid oder über einen Webhook abonniert werden, oder Ereignisse können an Azure Service Bus weitergeleitet werden. Das Azure Service Bus SDK verfügt über eine API zum Registrieren eines Meldungshandlers. Hiermit wird dieser Prozess für Anwendungen vereinfacht, die nicht über einen HTTP-Endpunkt verfügen oder bei denen nicht ständig Abfragen zur Ermittlung von Änderungen an Event Grid gesendet werden sollen.

In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code per Pushaktualisierung implementieren können. Dies baut auf der App auf, die in den Schnellstartanleitungen vorgestellt wurde. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer .NET Core-App mit Azure App Configuration](./quickstart-dotnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Abonnements zum Senden von Ereignissen zu Konfigurationsänderungen von App Configuration an ein Service Bus-Thema
> * Einrichten Ihrer .NET Code-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in App Configuration
> * Verwenden der aktuellen Konfiguration in Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie für dieses Tutorial das [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Einrichten eines Azure Service Bus-Themas und -Abonnements

In diesem Tutorial wird die Service Bus-Integration für Event Grid verwendet, um die Erkennung von Konfigurationsänderungen für Anwendungen zu vereinfachen, bei denen App Configuration nicht ständig nach Änderungen abgefragt werden soll. Das Azure Service Bus SDK verfügt über eine API zum Registrieren eines Meldungshandlers, mit dem die Konfiguration aktualisiert werden kann, wenn in App Configuration Änderungen erkannt werden. Befolgen Sie die Schritte im [Schnellstart: Verwenden Sie das Azure-Portal zum Erstellen eines Service Bus-Themas und eines -Abonnements](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal), um einen Namespace, ein Thema und ein Abonnement für Service Bus zu erstellen.

Fügen Sie nach der Erstellung der Ressourcen die folgenden Umgebungsvariablen hinzu. Sie werden verwendet, um einen Ereignishandler für Konfigurationsänderungen im Anwendungscode zu registrieren.

| Schlüssel | Wert |
|---|---|
| ServiceBusConnectionString | Verbindungszeichenfolge für den Service Bus-Namespace |
| ServiceBusTopic | Name des Service Bus-Themas |
| ServiceBusSubscription | Name des Service Bus-Abonnements |

## <a name="set-up-event-subscription"></a>Einrichten des Ereignisabonnements

1. Öffnen Sie die App Configuration-Ressource im Azure-Portal, und klicken Sie anschließend im Bereich `Events` (Ereignisse) auf `+ Event Subscription` (+ Ereignisabonnement).

    ![App Configuration-Ereignisse](./media/events-pane.png)

1. Geben Sie einen Namen für `Event Subscription` (Ereignisabonnement) und `System Topic` (Systemthema) ein.

    ![Erstellen eines Ereignisabonnements](./media/create-event-subscription.png)

1. Wählen Sie unter `Endpoint Type` (Endpunkttyp) die Option `Service Bus Topic` (Service Bus-Thema) und dann das Service Bus-Thema aus, und klicken Sie anschließend auf `Confirm Selection` (Auswahl bestätigen).

    ![Ereignisabonnements: Service Bus-Endpunkt](./media/event-subscription-servicebus-endpoint.png)

1. Klicken Sie auf `Create` (Erstellen), um das Ereignisabonnement zu erstellen.

1. Klicken Sie im Bereich `Events` (Ereignisse) auf `Event Subscriptions` (Ereignisabonnements), um sich zu vergewissern, dass die Erstellung des Abonnements erfolgreich war.

    ![App Configuration-Ereignisabonnements](./media/event-subscription-view.png)

> [!NOTE]
> Beim Abonnieren von Konfigurationsänderungen können ein oder mehrere Filter genutzt werden, um die Anzahl von Ereignissen zu reduzieren, die an Ihre Anwendung gesendet werden. Diese können entweder als [Event Grid-Abonnementfilter](https://docs.microsoft.com/azure/event-grid/event-filtering) oder [Service Bus-Abonnementfilter](https://docs.microsoft.com/azure/service-bus-messaging/topic-filters) konfiguriert werden. Beispielsweise kann ein Abonnementfilter nur verwendet werden, um Ereignisse zu Änderungen in einem Schlüssel zu abonnieren, der mit einer bestimmten Zeichenfolge beginnt.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registrieren eines Ereignishandlers zum erneuten Laden von Daten aus App Configuration

Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie sie mit folgendem Code.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

Die [SetDirty](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty)-Methode wird verwendet, um den zwischengespeicherten Wert für Schlüsselwerte, die für die Aktualisierung registriert sind, auf „Dirty“ (Modifiziert) festzulegen. Hierdurch wird sichergestellt, dass beim nächsten Aufruf von `RefreshAsync` oder `TryRefreshAsync` die zwischengespeicherten Werte mit App Configuration erneut überprüft und bei Bedarf aktualisiert werden.

Eine zufällige Verzögerung wird hinzugefügt, bevor der zwischengespeicherte Wert als „Dirty“ gekennzeichnet wird, um eine potenzielle Drosselungsmaßnahme zu reduzieren, falls mehrere Instanzen gleichzeitig aktualisiert werden. Die maximale Standardverzögerung vor der Kennzeichnung des zwischengespeicherten Werts als „Dirty“ beträgt 30 Sekunden. Dieser Wert kann aber außer Kraft gesetzt werden, indem ein optionaler `TimeSpan`-Parameter an die `SetDirty`-Methode übergeben wird.

> [!NOTE]
> Für die Reduzierung der Anzahl von Anforderungen an App Configuration bei Verwendung der Pushaktualisierung ist es wichtig, `SetCacheExpiration(TimeSpan cacheExpiration)` mit einem passenden Wert für den Parameter `cacheExpiration` aufzurufen. Hiermit die Cacheablaufzeit für die Pullaktualisierung gesteuert. Dies ist eine Absicherung für den Fall, dass ein Problem mit dem Ereignisabonnement oder dem Service Bus-Abonnement besteht. Empfohlener Wert: `TimeSpan.FromDays(30)`.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **AppConfigurationConnectionString** auf den Zugriffsschlüssel für Ihren App Configuration-Speicher fest. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Führen Sie den folgenden Befehl aus, um die Konsolen-App zu erstellen:

    ```console
     dotnet build
    ```

1. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die App lokal auszuführen:

    ```console
     dotnet run
    ```

    ![Ausführung der Pushaktualisierung vor dem Update](./media/dotnet-core-app-pushrefresh-initial.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Klicken Sie auf **Alle Ressourcen**, und wählen Sie dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration: Aktualisiert |

1. Warten Sie 30 Sekunden lang ab, bis das Ereignis verarbeitet und die Konfiguration aktualisiert wurde.

    ![Ausführung der Pushaktualisierung nach dem Update](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre .NET Core-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
