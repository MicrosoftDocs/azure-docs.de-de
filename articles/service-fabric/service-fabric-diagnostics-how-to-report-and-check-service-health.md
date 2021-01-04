---
title: Melden und Überprüfen der Integrität mit Azure Service Fabric
description: Erfahren Sie, wie Sie Integritätsberichte aus Ihrem Dienstcode senden und die Integrität Ihres Diensts mithilfe von Integritätsüberwachungstools prüfen können, die von Azure Service Fabric bereitgestellt werden.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998242"
---
# <a name="report-and-check-service-health"></a>Melden und Überprüfen der Dienstintegrität
Wenn in Ihren Diensten Probleme auftreten, hängt Ihre Fähigkeit zum Reagieren auf und Beheben von Vorfällen und Ausfällen von Ihrer Fähigkeit ab, die Probleme schnell zu erkennen. Indem Ihr Dienstcode Probleme und Ausfälle dem Azure Service Fabric-Integritätsdienst meldet, können Sie standardmäßige Systemüberwachungstools nutzen, die Service Fabric zum Überprüfen des Integritätsstatus bereitstellt.

Es gibt drei Möglichkeiten, wie Sie Informationen zur Integrität über den Dienst melden können:

* Verwenden Sie [Partition](/dotnet/api/system.fabric.istatefulservicepartition)- oder [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext)-Objekte.  
  Mit den Objekten `Partition` und `CodePackageActivationContext` können Sie Informationen zur Integrität von Elementen melden, die Teil des aktuellen Kontexts sind. Zum Beispiel kann als Teil eines Replikats ausgeführter Code Informationen zur Integrität nur für dieses Replikat, die dazugehörige Partition und für die Anwendung melden, von der er ein Teil ist.
* Verwenden Sie `FabricClient`.   
  Sie können Integritätsinformationen über den Dienstcode mit `FabricClient` melden, wenn der Cluster nicht [sicher](service-fabric-cluster-security.md) ist, oder der Dienst mit Administratorrechten ausgeführt wird. In den meisten realen Szenarien werden keine unsicheren Cluster verwendet und keine Administratorrechte vergeben. Mit `FabricClient`können Sie Informationen zur Integrität jeder Entität melden, die Teil des Clusters ist. Im Idealfall sollte der Dienstcode jedoch nur Berichte senden, die im Zusammenhang mit seiner eigenen Integrität stehen.
* Verwenden Sie die REST-APIs auf den Ebenen von Cluster, Anwendung, bereitgestellter Anwendung, Dienst, Dienstpaket, Partition, Replikat oder Knoten. Damit können Sie die Integrität aus einem Container heraus melden.

Dieser Artikel führt Sie durch ein Beispiel, in dem Integritätsberichte auf Basis des Dienstcodes erstellt werden. Das Beispiel zeigt auch, wie die von Service Fabric bereitgestellten Tools verwendet werden können, um den Integritätsstatus zu überprüfen. Dieser Artikel soll als kurze Einführung in die Funktionen zur Integritätsüberwachung in Service Fabric dienen. Ausführlichere Informationen finden Sie in der Serie detaillierter Artikel zur Integrität, die mit dem Link am Ende dieses Artikels beginnt.

## <a name="prerequisites"></a>Voraussetzungen
Folgendes müssen Sie installiert haben:

* Visual Studio 2015 oder Visual Studio 2019
* Service Fabric-SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>So erstellen Sie einen lokalen, sicheren Entwicklungscluster
* Öffnen Sie PowerShell mit Administratorrechten, und führen Sie die folgenden Befehle aus:

![Befehle, die zeigen, wie ein sicherer Dev-Cluster erstellt wird](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>So stellen Sie eine Anwendung bereit und überprüfen ihre Integrität
1. Öffnen Sie Visual Studio als Administrator.
1. Erstellen Sie ein Projekt mithilfe der Vorlage **Zustandsbehafteter Dienst** .
   
    ![Erstellen einer Service Fabric-Anwendung mit einem zustandsbehafteten Dienst](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Drücken Sie **F5** , um die Anwendung im Debugmodus auszuführen. Die Anwendung wird im lokalen Cluster bereitgestellt.
1. Sobald die Anwendung ausgeführt wird, klicken Sie mit der rechten Maustaste im Infobereich auf das Symbol „Lokaler Cluster-Manager“, und wählen Sie **Lokalen Cluster verwalten** aus dem Kontextmenü, um Service Fabric Explorer zu öffnen.
   
    ![Öffnen von Service Fabric Explorer aus dem Infobereich heraus](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Die Anwendungsintegrität sollte wie in dieser Abbildung angezeigt werden. Zu diesem Zeitpunkt sollte die Anwendung stabil und fehlerfrei sein.
   
    ![Fehlerfreie Anwendung in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Sie können die Integrität auch mithilfe der PowerShell überprüfen. Sie können ```Get-ServiceFabricApplicationHealth``` zum Überprüfen der Integrität einer Anwendung und ```Get-ServiceFabricServiceHealth``` zum Überprüfen der Integrität eines Diensts verwenden. Der Integritätsbericht für die gleiche Anwendung in PowerShell ist in diesem Bild dargestellt.
   
    ![Fehlerfreie Anwendung in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>So fügen Sie dem Dienstcode benutzerdefinierte Integritätsereignisse hinzu
Die Service Fabric-Projektvorlagen in Visual Studio enthalten Beispielcode. Die folgenden Schritte zeigen, wie Sie Integritätsereignisse aus dem Dienstcode melden können. Diese Berichte werden automatisch in den Standardtools für die Überwachung der Integrität von Service Fabric angezeigt, z.B. Service Fabric Explorer, Integritätsanzeige im Azure-Portal und PowerShell.

1. Öffnen Sie erneut die Anwendung, die Sie zuvor in Visual Studio erstellt haben, oder erstellen Sie eine neue Anwendung, indem Sie die Visual Studio-Vorlage für einen **Zustandsbehafteten Dienst** verwenden.
1. Öffnen Sie die Datei „Stateful1.cs“, und suchen Sie den`myDictionary.TryGetValueAsync`-Aufruf in der `RunAsync`-Methode. Wie Sie sehen, gibt diese Methode den aktuellen Wert des Zählers als Ergebnis ( `result` ) zurück, da die Schlüssellogik dieser Anwendung einen laufenden Zähler vorsieht. Wenn diese Anwendung eine reale Anwendung wäre und das Fehlen des Ergebnisses einen Fehler darstellen würde, würden Sie dieses Ereignis kennzeichnen wollen.
1. Um ein Integritätsereignis zu melden, wenn das Fehlen des Ergebnisses einen Fehler darstellt, fügen Sie die folgenden Schritte hinzu.
   
    a. Fügen Sie der Datei „Stateful1.cs“ den Namespace `System.Fabric.Health` hinzu.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Fügen Sie nach dem `myDictionary.TryGetValueAsync` -Aufruf den folgenden Code hinzu.
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Wir melden Informationen zur Replikatintegrität, da die Meldung aus einem zustandsbehafteten Dienst erfolgt. Der Parameter `HealthInformation` speichert Informationen zum gemeldeten Integritätsproblem.
   
    Wenn Sie einen zustandslosen Dienst erstellt haben, verwenden Sie den folgenden Code.
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Wenn Ihr Dienst mit Administratorrechten ausgeführt wird oder der Cluster nicht [sicher](service-fabric-cluster-security.md) ist, können Sie auch `FabricClient` verwenden, um Informationen zur Integrität wie nachstehend beschrieben zu melden.  
   
    a. Erstellen Sie die `FabricClient`-Instanz nach der `var myDictionary`-Deklaration.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Fügen Sie nach dem `myDictionary.TryGetValueAsync` -Aufruf folgenden Code hinzu:
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Wir simulieren diesen Fehler nun und prüfen, ob er in den Integritätsüberwachungstools angezeigt wird. Zum Simulieren des Fehlers kommentieren Sie zunächst die erste Zeile im Integritätsberichtscode aus, den Sie zuvor hinzugefügt haben. Nach dem Auskommentieren der ersten Zeile sieht der Code wie im folgenden Beispiel aus.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Durch diesen Code wird nun bei jeder Ausführung von `RunAsync` dieser Integritätsbericht ausgelöst. Nachdem Sie die Änderung vorgenommen haben, drücken Sie **F5** , um die Anwendung auszuführen.
1. Sobald die Anwendung ausgeführt wird, öffnen Sie Service Fabric Explorer zum Überprüfen der Integrität der Anwendung. Dieses Mal zeigt Service Fabric Explorer die Anwendung als fehlerhaft an. Diese Anwendung wird aufgrund des Fehlers, der für den zuvor hinzugefügten Code gemeldet wurde, als fehlerhaft angezeigt.
   
    ![Fehlerhafte Anwendung in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Wenn Sie in Service Fabric Explorer in der Strukturansicht das primäre Replikat auswählen, erkennen Sie, dass auch hier der **Integritätsstatus** einen Fehler anzeigt. Außerdem zeigt Service Fabric Explorer Integritätsberichtsdetails an, die dem Parameter `HealthInformation` im Code hinzugefügt wurden. In PowerShell und im Azure-Portal werden die gleichen Integritätsberichte angezeigt.
   
    ![Replikatsintegrität in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Dieser Bericht verbleibt im Integritätsdienst, bis er durch einen anderen Bericht ersetzt oder dieses Replikat gelöscht wird. Da wir `TimeToLive` nicht für diesen Integritätsbericht im `HealthInformation`-Objekt festgelegt haben, läuft der Bericht nie ab.

Informationen zur Integrität sollten auf differenziertester Ebene gemeldet werden – in diesem Fall auf Replikatebene. Sie können Informationen zur Integrität auch zur `Partition`melden.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Um Integritätsberichte zu `Application`, `DeployedApplication` und `DeployedServicePackage` zu melden, verwenden Sie `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Nächste Schritte
* [Ausführlichere Informationen zur Service Fabric-Integrität](service-fabric-health-introduction.md)
* [REST-API für die Meldung der Dienstintegrität](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST-API für die Meldung der Anwendungsintegrität](/rest/api/servicefabric/report-the-health-of-an-application)
