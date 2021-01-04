---
title: Resilienz und Notfallwiederherstellung für Azure App Configuration
description: Erfahren Sie, wie Sie Resilienz und Notfallwiederherstellung mit Azure App Configuration implementieren.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930482"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienz und Notfallwiederherstellung

Azure App Configuration ist momentan ein regionaler Dienst. Jeder Konfigurationsspeicher wird in einer bestimmten Azure-Region erstellt. Ein regionsweiter Ausfall betrifft alle Speicher in dieser Region. App Configuration bietet kein automatisches Failover auf eine andere Region. Dieser Artikel enthält allgemeine Richtlinien zur Verwendung mehrerer Konfigurationsspeicher in mehreren Azure-Regionen, um die geografische Resilienz Ihrer Anwendung zu erhöhen.

## <a name="high-availability-architecture"></a>Hochverfügbarkeitsarchitektur

Um eine regionsübergreifende Redundanz zu erzielen, müssen Sie mehrere App Configuration-Speicher in verschiedenen Regionen erstellen. Mit diesem Setup kann die Anwendung auf mindestens einen zusätzlichen Konfigurationsspeicher zurückgreifen, wenn der primäre Speicher nicht mehr zugänglich ist. Das folgende Diagramm veranschaulicht die Topologie zwischen Ihrer Anwendung und dem primären und sekundären Konfigurationsspeicher:

![Georedundante Speicher](./media/geo-redundant-app-configuration-stores.png)

Ihre Anwendung lädt ihre Konfiguration parallel aus dem primären und sekundären Speicher. Dadurch erhöht sich die Wahrscheinlichkeit, dass die Konfigurationsdaten erfolgreich abgerufen werden können. Sie sind selbst dafür verantwortlich, die Daten in beiden Speichern zu synchronisieren. In den folgenden Abschnitten wird erläutert, wie Sie Georesilienz in Ihrer Anwendung implementieren können.

## <a name="failover-between-configuration-stores"></a>Failover zwischen Konfigurationsspeichern

In technischer Hinsicht führt Ihre Anwendung kein Failover aus. Sie versucht, den gleichen Satz von Konfigurationsdaten gleichzeitig aus zwei App Configuration-Speichern abzurufen. Gestalten Sie Ihren Code so, dass die Daten zuerst aus dem sekundären Speicher und anschließend aus dem primären Speicher geladen werden. Dadurch wird sichergestellt, dass die Konfigurationsdaten im primären Speicher Vorrang haben, wenn sie verfügbar sind. Der folgende Codeausschnitt zeigt, wie Sie dies über .NET Core implementieren:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Beachten Sie den `optional`-Parameter, der an die `AddAzureAppConfiguration`-Funktion übergeben wird. Wenn dieser Parameter auf `true` festgelegt wird, sorgt er dafür, dass die Anwendung fortgesetzt wird, wenn die Funktion keine Konfigurationsdaten laden kann.

## <a name="synchronization-between-configuration-stores"></a>Synchronisierung zwischen Konfigurationsspeichern

Es ist wichtig, dass all Ihre georedundanten Konfigurationsspeicher denselben Satz von Daten verwenden. Es gibt zwei Möglichkeiten, dies zu erreichen:

### <a name="backup-manually-using-the-export-function"></a>Manuelle Sicherung mithilfe der Exportfunktion

Mit der Funktion **Exportieren** in App Configuration können Sie Daten nach Bedarf aus dem primären Speicher in den sekundären Speicher kopieren. Diese Funktion ist sowohl über das Azure-Portal als auch über die CLI verfügbar.

Im Azure-Portal können Sie eine Änderung wie folgt an einen anderen Konfigurationsspeicher pushen:

1. Wählen Sie auf der Registerkarte **Import/Export** Folgendes aus: **Exportieren** > **App-Konfiguration** > **Ziel** > **Ressource auswählen**.

1. Geben Sie auf dem neuen Blatt, das geöffnet wird, das Abonnement, die Ressourcengruppe und den Ressourcennamen Ihres sekundären Speichers an, und wählen Sie anschließend **Anwenden** aus.

1. Die Benutzeroberfläche wird aktualisiert, sodass Sie auswählen können, welche Konfigurationsdaten Sie in den sekundären Speicher exportieren möchten. Sie können den Standardzeitwert unverändert lassen und **Quellbezeichnung** und **Bezeichnung** auf den gleichen Wert festlegen. Wählen Sie **Übernehmen**. Wiederholen Sie diesen Vorgang für alle Bezeichnungen in Ihrem primären Speicher.

1. Wiederholen Sie die vorherigen Schritte jedes Mal, wenn sich die Konfiguration ändert.

Der Exportvorgang kann auch über die Azure CLI ausgeführt werden. Der folgenden Befehl zeigt, wie Sie alle Konfigurationen aus dem primären in den sekundären Speicher exportieren:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Automatische Sicherung mithilfe von Azure Functions

Der Sicherungsvorgang kann mit Azure Functions automatisiert werden. Dabei wird die Integration von Azure Event Grid in App Configuration genutzt. Nach der Einrichtung veröffentlicht App Configuration bei allen Änderungen, die an Schlüsselwerten in einem Konfigurationsspeicher vorgenommen wurden, Ereignisse in Event Grid. Daher kann eine Azure Functions-App auf diese Ereignisse lauschen und die Daten entsprechend sichern. Details dazu finden Sie im Tutorial zur [automatischen Sicherung von App Configuration-Speichern](./howto-backup-config-store.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Ihre Anwendung zum Erzielen von Georesilienz für App Configuration während der Laufzeit erweitern. Konfigurationsdaten aus App Configuration können auch zur Erstellungs- oder Bereitstellungszeit eingebettet werden. Weitere Informationen finden Sie unter [Integrieren in eine CI/CD-Pipeline](./integrate-ci-cd-pipeline.md).
