---
title: Resilienz und Notfallwiederherstellung für Azure App Configuration | Microsoft-Dokumentation
description: Eine Übersicht über die Implementierung von Resilienz und Notfallwiederherstellung in Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394069"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienz und Notfallwiederherstellung

Derzeit ist Azure App Configuration ein regionaler Dienst. Jeder Konfigurationsspeicher wird in einer bestimmten Azure-Region erstellt. Ein regionsweiter Ausfall beeinträchtigt alle Speicher in dieser Region. App Configuration bietet kein automatisches Failover auf eine andere Region. Dieser Artikel enthält allgemeine Richtlinien zur Verwendung mehrerer Konfigurationsspeicher in mehreren Azure-Regionen, um die geografische Resilienz Ihrer Anwendung zu erhöhen.

## <a name="high-availability-architecture"></a>Hochverfügbarkeitsarchitektur

Um eine regionsübergreifende Redundanz zu erzielen, müssen Sie mehrere App Configuration-Speicher in verschiedenen Regionen erstellen. Mit diesem Setup kann die Anwendung auf mindestens einen zusätzlichen Konfigurationsspeicher zurückgreifen, wenn der primäre Speicher nicht mehr zugänglich ist. Unten sehen Sie ein Diagramm, das die Topologie zwischen Ihrer Anwendung und dem primären und dem sekundären Konfigurationsspeicher veranschaulicht.

![Georedundante Speicher](./media/geo-redundant-app-configuration-stores.png)

Ihre Anwendung lädt ihre Konfiguration sowohl aus dem primären als auch aus dem sekundären Speicher. Dadurch wird die Wahrscheinlichkeit, dass die Konfigurationsdaten erfolgreich abgerufen werden können, erheblich erhöht. Sie sind selbst dafür verantwortlich, die Daten in beiden Speichern zu synchronisieren. In den folgenden Abschnitten wird erläutert, wie Sie Georesilienz in Ihrer Anwendung implementieren können.

## <a name="failover-between-configuration-stores"></a>Failover zwischen Konfigurationsspeichern

In technischer Hinsicht führt Ihre Anwendung kein Failover aus. Es wird versucht, den gleichen Satz von Konfigurationsdaten gleichzeitig aus zwei App Configuration-Speichern abzurufen. Sie sollten Ihren Code so anordnen, dass die Daten zunächst aus dem sekundären Speicher und dann aus dem primären Speicher geladen werden. Durch diesen Ansatz wird sichergestellt, dass die Konfigurationsdaten im primären Speicher Vorrang haben, wenn sie verfügbar sind. Der folgende Codeausschnitt zeigt, wie Sie dies in .NET Core implementieren können.

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
    }
```

Beachten Sie den `optional`-Parameter, der an die `AddAzureAppConfiguration`-Funktion übergeben wird. Wenn er auf `true` festgelegt wird, sorgt dieser Parameter dafür, dass die Anwendung fortgesetzt wird, wenn die Funktion keine Konfigurationsdaten laden kann.

## <a name="synchronization-between-configuration-stores"></a>Synchronisierung zwischen Konfigurationsspeichern

Es ist wichtig, dass all Ihre georedundanten Konfigurationsspeicher denselben Satz von Daten verwenden. Mit der Funktion **Exportieren** in App Configuration können Sie Daten nach Bedarf aus dem primären Speicher in den sekundären Speicher kopieren. Diese Funktion ist sowohl über das Azure-Portal als auch über die CLI verfügbar.

Im Azure-Portal können Sie eine Änderung wie folgt an einen anderen Konfigurationsspeicher pushen:

1. Navigieren Sie zur Registerkarte **Importieren/Exportieren**, wählen Sie **Exportieren** aus, wählen Sie **App Configuration** als **Ziel** aus, und klicken Sie dann auf **Ressource auswählen**.

2. Auf dem neuen Blatt, das geöffnet wird, geben Sie das Abonnement, die Ressourcengruppe und den Ressourcennamen Ihres sekundären Speichers an, und klicken Sie dann auf **Anwenden**.

3. Die Benutzeroberfläche wird aktualisiert, sodass Sie auswählen können, welche Konfigurationsdaten Sie in den sekundären Speicher exportieren möchten. Sie können den Standardzeitwert unverändert lassen und Quelle und Ziel für **Label** **** auf den gleichen Wert festlegen. Klicken Sie auf **Anwenden**.

4. Wiederholen Sie die oben genannten Schritte für alle Konfigurationsänderungen.

Mithilfe der Azure-Befehlszeilenschnittstelle können Sie diesen Exportvorgang automatisieren. Der folgenden Befehl zeigt den Export einer einzelnen Konfigurationsänderung vom primären Speicher in den sekundären.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Ihre Anwendung zum Erzielen von Georesilienz für App Configuration während der Laufzeit erweitern. Alternativ dazu können Sie Konfigurationsdaten aus App Configuration zum Zeitpunkt der Erstellung oder Bereitstellung einbetten. Weitere Informationen finden Sie unter [Integrieren in eine CI/CD-Pipeline](./integrate-ci-cd-pipeline.md).

