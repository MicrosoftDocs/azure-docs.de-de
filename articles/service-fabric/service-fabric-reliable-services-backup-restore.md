---
title: Sichern und Wiederherstellen mit Service Fabric | Microsoft Docs
description: Dokumentation zum Sichern und Wiederherstellen mit Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: cd40f59cfa7846911c68206c3bc1e85a770b0fcc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723847"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Sichern und Wiederherstellen von Reliable Services und Reliable Actors
Azure Service Fabric ist eine Plattform mit Hochverfügbarkeit, bei der der Status über mehrere Knoten repliziert wird, um diese Hochverfügbarkeit zu gewährleisten.  Auch wenn ein Knoten im Cluster ausfällt, bleiben die Dienste somit verfügbar. Diese von der Plattform bereitgestellte integrierte Redundanz reicht in manchen Fällen aus. In bestimmten Fällen wäre es jedoch wünschenswert, dass der Dienst Daten (auf einem externen Speicher) sichert.

> [!NOTE]
> Das Sichern und die Möglichkeit des Wiederherstellens Ihrer Daten (sowie die Überprüfung, ob diese Vorgänge wie erwartet ausgeführt werden) sind wichtig, damit Sie sie bei Datenverlust wiederherstellen können.
> 

> [!NOTE]
> Microsoft empfiehlt die Verwendung von [Regelmäßigen Sicherungen und Wiederherstellungen](service-fabric-backuprestoreservice-quickstart-azurecluster.md) für die Konfiguration der Datensicherung von Reliable Stateful Services und Reliable Actors. 
> 


Ein Dienst möchte Daten beispielsweise zum Schutz vor folgenden Szenarien sichern:

- Im Fall des dauerhaften Verlusts eines gesamten Service Fabric-Clusters
- Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition
- Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Das kann z. B. passieren, wenn ein Administrator mit ausreichenden Berechtigungen versehentlich den Dienst löscht.
- Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.
- Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Das Feature für die Sicherung/Wiederherstellung ermöglicht mit Reliable Services-API entwickelten Diensten die Erstellung und Wiederherstellung von Sicherungen. Die von der Plattform bereitgestellten Sicherungs-APIs ermöglichen Sicherungen des Status einer Dienstpartition, ohne Lese- oder Schreibvorgänge zu blockieren. Die Wiederherstellungs-APIs ermöglichen die Wiederherstellung des Status einer Dienstpartition aus einer Sicherung.

## <a name="types-of-backup"></a>Sicherungstypen
Es gibt zwei Sicherungsoptionen: „Vollständig“ und „Inkrementell“.
Eine vollständige Sicherung ist eine Sicherung, die alle Daten umfasst, die zum Neuerstellen des Status des Replikats erforderlich sind, d.h. Prüfpunkte und alle Protokolleinträge.
Da eine vollständige Sicherung die Prüfpunkte und das Protokoll umfasst, kann sie eigenständig wiederhergestellt werden.

Probleme mit vollständigen Sicherungen entstehen, wenn die Prüfpunkte umfangreich sind.
Ein Replikat mit einem Status von 16 GB weist beispielsweise Prüfpunkte auf, die sich auf ca. 16 GB belaufen.
Bei einer Recovery Point Objective von fünf Minuten muss das Replikat alle fünf Minuten gesichert werden.
Bei jeder Sicherung müssen neben Protokollen mit einer Gesamtgröße von 50 MB (konfigurierbar mit `CheckpointThresholdInMB`) Prüfpunkte mit einer Gesamtgröße von 16 GB kopiert werden.

![Beispiel für vollständige Sicherung.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Die Lösung für dieses Problem besteht darin, inkrementelle Sicherungen durchzuführen, die nur die seit der letzten Sicherung geänderten Protokolleinträge enthalten.

![Beispiel für inkrementelle Sicherung.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Da inkrementelle Sicherungen nur die seit der letzten Sicherung vorgenommenen Änderungen umfassen (ohne die Prüfpunkte), werden sie meistens schneller durchgeführt, können aber nicht eigenständig wiederhergestellt werden.
Zum Wiederherstellen einer inkrementellen Sicherung ist die gesamte Sicherungskette erforderlich.
Eine Sicherungskette ist eine Kette von Sicherungen, die mit einer vollständigen Sicherung beginnt, auf die dann mehrere zusammenhängende inkrementelle Sicherungen folgen.

## <a name="backup-reliable-services"></a>Sichern von Reliable Services
Der Dienstautor kann bestimmen, wann Sicherungen erstellt und wo sie gespeichert werden.

Der Dienst muss die geerbte Memberfunktion `BackupAsync` aufrufen, um die Sicherung zu starten.  
Sicherungen können nur von primären Replikaten erstellt werden und benötigen Schreibzugriff.

Wie Sie unten sehen, nimmt `BackupAsync` ein `BackupDescription`-Objekt an, in dem Sie eine vollständige oder inkrementelle Sicherung sowie die Rückruffunktion `Func<< BackupInfo, CancellationToken, Task<bool>>>` angeben können, die aufgerufen wird, wenn der Sicherungsordner lokal erstellt wurde und in einen externen Speicher verschoben werden kann.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Bei der Anforderung zum Durchführen einer inkrementellen Sicherung kann der Fehler `FabricMissingFullBackupException` auftreten. Diese Ausnahme gibt an, dass einer der folgenden Fälle vorliegt:

- Dies deutet darauf hin, dass entweder niemals eine vollständige Sicherung für das Replikat erstellt wurde,
- einige der Protokolleinträge seit der letzten Sicherung abgeschnitten wurden oder
- das Replikat den Grenzwert `MaxAccumulatedBackupLogSizeInMB` überschritten hat.

Durch Konfigurieren von `MinLogSizeInMB` oder `TruncationThresholdFactor` können Benutzer die Wahrscheinlichkeit erhöhen, dass inkrementelle Sicherungen durchgeführt werden können.
Durch eine Erhöhung dieser Werte wird die Datenträgerverwendung pro Replikat erhöht.
Weitere Informationen finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md).

`BackupInfo` liefert Informationen zur Sicherung. Hierzu zählt auch der Speicherort des Ordners, in dem die Runtime die Sicherung gespeichert hat (`BackupInfo.Directory`). Die Rückruffunktion kann `BackupInfo.Directory` in einen externen Speicher oder an einen anderen Speicherort verschieben.  Diese Funktion gibt auch einen booleschen Wert zurück, der angibt, ob der Sicherungsordner erfolgreich an den Zielspeicherort verschoben werden konnte.

Der folgende Code veranschaulicht, wie die Sicherung mithilfe der `BackupCallbackAsync`-Methode in Azure Storage hochgeladen werden kann:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Im Beispiel oben ist `ExternalBackupStore` die Beispielklasse, die als Schnittstelle mit Azure Blob Storage verwendet wird. `UploadBackupFolderAsync` ist die Methode, die den Ordner komprimiert und im Azure-Blobspeicher platziert.

Beachten Sie Folgendes:

  - Pro Replikat kann jeweils nur ein Sicherungsvorgang ausgeführt werden. Mehrere gleichzeitig ausgeführte `BackupAsync`-Aufrufe lösen `FabricBackupInProgressException` aus, um nur eine einzelne Inflight-Sicherung zuzulassen.
  - Wenn ein Replikat während einer Sicherung ausfällt, wird die Sicherung möglicherweise nicht abgeschlossen. Folglich muss der Dienst die Sicherung nach Abschluss des Failovers neu starten, indem bei Bedarf `BackupAsync` aufgerufen wird.

## <a name="restore-reliable-services"></a>Wiederherstellen von Reliable Services
Im Allgemeinen können die Fälle, in denen Sie möglicherweise eine Wiederherstellung durchführen müssen, einer der folgenden Kategorien zugeordnet werden:

  - Die Dienstpartition hat Daten verloren. Beispielsweise wird die Festplatte für zwei von drei Replikaten einer Partition (einschließlich des primären Replikats) beschädigt oder gelöscht. Das neue primäre Replikat muss möglicherweise Daten aus einer Sicherung wiederherstellen.
  - Der gesamte Dienst geht verloren. Beispielsweise entfernt ein Administrator den gesamten Dienst, sodass Dienst und Daten wiederhergestellt werden müssen.
  - Der Dienst hat beschädigte Anwendungsdaten repliziert (z.B. aufgrund eines Anwendungsfehlers). In diesem Fall muss der Dienst aktualisiert oder zurückgesetzt werden, um die Ursache der Beschädigung zu entfernen, und nicht beschädigte Daten müssen wiederhergestellt werden.

Da viele Ansätze möglich sind, nennen wir einige Beispiele zum Verwenden von `RestoreAsync` zur Wiederherstellung in den obigen Szenarien.

## <a name="partition-data-loss-in-reliable-services"></a>Verlust von Partitionsdaten in Reliable Services
In diesem Fall würde die Runtime automatisch den Datenverlust erkennen und die `OnDataLossAsync`-API aufrufen.

Der Dienstautor muss Folgendes wiederherstellen:

  - Überschreiben Sie die Methode `OnDataLossAsync` der virtuellen Basisklasse.
  - Suchen Sie am externen Speicherort, der die Sicherungen des Diensts enthält, nach den neuesten Sicherungen.
  - Laden Sie die neueste Sicherung herunter (und dekomprimieren Sie die Sicherung in den Sicherungsordner, wenn sie komprimiert wurde).
  - Die `OnDataLossAsync`-Methode stellt ein `RestoreContext`-Element bereit. Rufen Sie die `RestoreAsync`-API für das bereitgestellte `RestoreContext`-Element auf.
  - Geben Sie „true“ zurück, wenn die Wiederherstellung erfolgreich war.

Im Folgenden finden Sie ein Beispiel für das Implementieren der `OnDataLossAsync`-Methode:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` wird an den `RestoreContext.RestoreAsync`-Aufruf übergeben und enthält ein Element mit dem Namen `BackupFolderPath`.
Beim Wiederherstellen einer einzelnen vollständigen Sicherung muss `BackupFolderPath` auf den lokalen Pfad des Ordners festgelegt werden, der die vollständige Sicherung enthält.
Beim Wiederherstellen einer vollständigen Sicherung und mehrerer inkrementeller Sicherungen muss `BackupFolderPath` auf den lokalen Pfad des Ordners festgelegt werden, der die vollständige Sicherung sowie alle inkrementellen Sicherungen enthält.
Der `RestoreAsync`-Aufruf kann `FabricMissingFullBackupException` auslösen, wenn das angegebene `BackupFolderPath`-Element keine vollständige Sicherung enthält.
Außerdem kann `ArgumentException` ausgelöst werden, wenn `BackupFolderPath` eine unterbrochene Kette von inkrementellen Sicherungen enthält,
beispielsweise die vollständige Sicherung, die erste inkrementelle und die dritte inkrementelle Sicherung, jedoch nicht die zweite inkrementelle Sicherung.

> [!NOTE]
> Für die RestorePolicy ist standardmäßig „sicher“ eingestellt.  Das bedeutet, dass bei der `RestoreAsync`-API der Fehler „ArgumentException“ auftritt, wenn sie erkennt, dass der Sicherungsordner einen Zustand enthält, der älter ist als der Zustand in diesem Replikat bzw. gleich alt ist.  `RestorePolicy.Force` kann verwendet werden, um diese Sicherheitsprüfung zu überspringen. Dies wird als Teil von `RestoreDescription` angegeben.
> 

## <a name="deleted-or-lost-service"></a>Gelöschter oder verlorener Dienst
Wenn ein Dienst entfernt wird, muss der Dienst erst neu erstellt werden, bevor die Daten wiederhergestellt werden können.  Der Dienst muss unbedingt mit der gleichen Konfiguration erstellt werden, z.B. dem Partitionierungsschema, damit die Daten problemlos wiederhergestellt werden können.  Sobald der Dienst wieder aktiv ist, muss die API zum Wiederherstellen von Daten (`OnDataLossAsync` oben) für jede Partition dieses Diensts aufgerufen werden. Hierzu kann beispielsweise [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) für jede Partition verwendet werden.  

Ab diesem Zeitpunkt erfolgt die Implementierung wie im oben aufgeführten Szenario. Jede Partition muss die letzte relevante Sicherung aus dem externen Speicher wiederherstellen. Ein Nachteil ist, dass die Partitions-ID sich geändert haben kann, da die Runtime Partitions-IDs dynamisch erstellt. Daher muss der Dienst die entsprechenden Partitionsinformationen und den Dienstnamen speichern, um die aktuelle Sicherung zum Speichern für jede Partition zu finden.

> [!NOTE]
> Es wird davon abgeraten, `FabricClient.ServiceManager.InvokeDataLossAsync` auf jeder Partition zu verwenden, um den gesamten Dienst wiederherzustellen, da dadurch der Clusterstatus beschädigt werden kann.
> 

## <a name="replication-of-corrupt-application-data"></a>Replizieren beschädigter Anwendungsdaten
Wenn das neu bereitgestellte Anwendungsupgrade fehlerhaft ist, werden ggf. Daten beschädigt. Beispielsweise kann ein Anwendungsupgrade damit beginnen, jede Telefonnummer in einem Reliable Dictionary mit einer ungültigen Vorwahl zu versehen.  In diesem Fall werden die ungültigen Telefonnummern repliziert, da Service Fabric die Art der gespeicherten Daten nicht kennt.

Treten solche Fehler auf, bei denen Daten beschädigt werden, frieren Sie den Dienst am besten auf Anwendungsebene ein und führen, falls möglich, ein Upgrade auf die Version des Anwendungscodes durch, in der dieser Fehler nicht auftritt.  Auch nach dem Korrigieren des Dienstcodes sind die Daten möglicherweise immer noch beschädigt und müssen daher wiederhergestellt werden.  In solchen Fällen reicht es möglicherweise nicht aus, die neueste Sicherung wiederherzustellen, da die neuesten Sicherungen möglicherweise ebenfalls beschädigt sind.  Daher müssen Sie die letzte Sicherung vor Beschädigung der Daten finden.

Wenn Sie nicht sicher sind, welche Sicherungen fehlerhaft sind, können Sie einen neuen Service Fabric-Cluster bereitstellen und die Sicherungen der betroffenen Partitionen wie im oben beschriebenen Szenario „Gelöschter oder verlorener Dienst“ wiederherstellen.  Beginnen Sie für jede Partition mit der Wiederherstellung der Sicherungen, beginnend mit der neuesten bis hin zur ältesten. Sobald Sie eine Sicherung ohne Beschädigung gefunden haben, verschieben/löschen Sie alle Sicherungen dieser Partition, die aktueller sind als diese Sicherung. Wiederholen Sie diesen Vorgang für jede Partition. Wenn nun `OnDataLossAsync` auf der Partition im Produktionscluster aufgerufen wird, handelt es sich bei der letzten im externen Speicher gefundenen Sicherung um die im oben beschriebenen Verfahren verwendete Sicherung.

Mit den Schritten im Abschnitt „Gelöschter oder verlorener Dienst“ kann der Status des Diensts wieder auf den Status vor der Beschädigung der Datei gesetzt werden.

Beachten Sie Folgendes:

  - Bei einer Wiederherstellung besteht die Möglichkeit, dass die wiederherzustellende Sicherung älter als der Status der Partition vor dem Verlust der Daten ist. Deshalb sollte die Wiederherstellung nur als letzter Ausweg verwendet werden, um so viele Daten wie möglich wiederherzustellen.
  - Die Zeichenfolge, die den Sicherungsorderpfad und die Dateipfade im Sicherungsordner repräsentiert, kann je nach FabricDataRoot-Pfad und Namenslänge des Anwendungstyps länger als 255 Zeichen sein. Einige .NET-Methoden, z.B. `Directory.Move`, lösen daraufhin unter Umständen die Ausnahme `PathTooLongException` aus. Als Problemumgehung können beispielsweise kernel32-APIs wie `CopyFile` direkt aufgerufen werden.

## <a name="back-up-and-restore-reliable-actors"></a>Sichern und Wiederherstellen von Reliable Actors


Das Reliable Actors-Framework basiert auf Reliable Services. Der ActorService, der die Actors hostet, ist ein zustandsbehafteter zuverlässiger Dienst. Daher stehen sämtliche Sicherungs- und Wiederherstellungsfunktionen, die in Reliable Services verfügbar sind, auch für Reliable Actors zur Verfügung (mit Ausnahme von Verhaltensweisen, die für den Zustandsanbieter spezifisch sind). Da Sicherungen jeweils pro Partition erstellt werden, werden Zustände für alle Actors in einer bestimmten Partition gesichert. (Die Wiederherstellung erfolgt ähnlich und jeweils pro Partition.) Um eine Sicherung/Wiederherstellung durchzuführen, muss der Dienstbesitzer eine benutzerdefinierte Actordienstklasse erstellen, die von der ActorService-Klasse abgeleitet ist, und dann Sicherungen bzw. Wiederherstellungen wie bei Reliable Services durchführen, wie in den vorherigen Abschnitten beschrieben.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Wenn Sie eine benutzerdefinierte Actordienstklasse erstellen, müssen Sie beim Registrieren des Actors auch diese Klasse registrieren.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Der Standardzustandsanbieter für Reliable Actors ist `KvsActorStateProvider`. Die inkrementelle Sicherung ist für `KvsActorStateProvider` nicht standardmäßig aktiviert. Sie können die inkrementelle Sicherung aktivieren, indem Sie `KvsActorStateProvider` mit der entsprechenden Einstellung im zugehörigen Konstruktor erstellen und dann wie im folgenden Codeausschnitt gezeigt an den ActorService-Konstruktor weiterleiten:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Nachdem die inkrementelle Sicherung aktiviert wurde, kann beim Erstellen einer inkrementellen Sicherung aus einem der folgenden Gründe ein Fehler „FabricMissingFullBackupException“ auftreten. Sie müssen dann eine vollständige Sicherung ausführen, bevor Sie inkrementelle Sicherungen erstellen:

  - Für das Replikat wurde nie eine vollständige Sicherung durchgeführt, seit es als primäres Replikat festgelegt wurde.
  - Einige Protokolldatensätze wurden seit der Erstellung der letzten Sicherung abgeschnitten.

Wenn die inkrementelle Sicherung aktiviert ist, verwendet `KvsActorStateProvider` keinen zirkulären Puffer zum Verwalten der Protokolleinträge und schneidet das Protokoll in regelmäßigen Abständen ab. Wenn für einen Zeitraum von 45 Minuten keine Sicherung von einem Benutzer erstellt wird, schneidet das System die Protokolldatensätze automatisch ab. Dieses Intervall kann konfiguriert werden, indem Sie `logTruncationIntervalInMinutes` im `KvsActorStateProvider`-Konstruktor angeben (ähnlich wie beim Aktivieren der inkrementellen Sicherung). Die Protokolldatensätze werden möglicherweise auch abgeschnitten, wenn das primäre Replikat durch Senden sämtlicher Daten ein weiteres Replikat erstellen muss.

Bei der Wiederherstellung aus einer Sicherungskette wie bei Reliable Services muss der BackupFolderPath Unterverzeichnisse enthalten, wobei ein Unterverzeichnis die vollständige Sicherung enthält und weitere Unterverzeichnissen die inkrementellen Sicherungen enthalten. Die Wiederherstellungs-API löst eine FabricException mit der entsprechenden Fehlermeldung aus, wenn die Überprüfung der Sicherungskette einen Fehler hervorruft. 

> [!NOTE]
> Derzeit ignoriert `KvsActorStateProvider` die Option „RestorePolicy.Safe“. Unterstützung für dieses Feature ist in einer künftigen Version geplant.
> 

## <a name="testing-back-up-and-restore"></a>Testen von Sichern und Wiederherstellen
Es muss sichergestellt werden, dass wichtige Daten gesichert werden und wiederhergestellt werden können. Dies kann durch Aufrufen des Cmdlets `Start-ServiceFabricPartitionDataLoss` in PowerShell erfolgen, mit dem Datenverlust in einer bestimmten Partition ausgelöst werden kann, um zu überprüfen, ob die Funktionen zum Sichern und Wiederherstellen der Daten wie erwartet ausgeführt werden.  Es ist auch möglich, den Datenverlust programmgesteuert auszulösen und die Wiederherstellung infolge dieses Ereignisses durchzuführen.

> [!NOTE]
> Ein Beispiel für die Implementierung der Sicherungs- und Wiederherstellungsfunktionalität finden Sie in der Beschreibung zur Web Reference-App auf GitHub. Ausführliche Informationen finden Sie in der Beschreibung zum `Inventory.Service`-Dienst.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Weitere Informationen zum Sichern und Wiederherstellen
Im Folgenden erhalten Sie weitere ausführliche Informationen zum Sichern und Wiederherstellen.

### <a name="backup"></a>Backup
Mit dem Reliable State Manager können konsistente Sicherungen erstellt werden, ohne Lese- oder Schreibvorgänge zu blockieren. Zu diesem Zweck verwendet er einen Mechanismus für Prüfpunkt- und Protokollbeständigkeit.  Der Reliable State Manager entlastet durch Prüfpunkte an bestimmten Punkten das Transaktionsprotokoll und verbessert Wiederherstellungszeiten.  Wenn `BackupAsync` aufgerufen wird, weist der Reliable State Manager alle Reliable Objects an, ihre neuesten Prüfpunktdateien in einen lokalen Sicherungsordner zu kopieren.  Der Reliable State Manager kopiert dann alle Protokolleinträge ab dem „Start“-Zeiger bis zum aktuellen Protokolleintrag in den Sicherungsordner.  Da alle Protokolleinträge einschließlich des aktuellen Eintrags in der Sicherung enthalten sind und der Reliable State Manager die Write-Ahead-Protokollierung beibehält, garantiert der Reliable State Manager, dass alle bestätigten Transaktionen (`CommitAsync` wurde erfolgreich zurückgegeben) in der Sicherung enthalten sind.

Sämtliche nach dem Aufrufen von `BackupAsync` bestätigten Transaktionen können in der Sicherung enthalten oder nicht enthalten sein.  Sobald der lokale Sicherungsordner der Plattform von der Plattform aufgefüllt wurde (z.B. Abschluss der lokalen Sicherung durch die Runtime), wird der Rückruf der Dienstsicherung aufgerufen.  Durch diesen Rückruf wird der Sicherungsordner in einen externen Speicherort wie den Azure-Speicher verschoben.

### <a name="restore"></a>Restore
Der Reliable State Manager ermöglicht das Wiederherstellen aus einer Sicherung mit der `RestoreAsync`-API.  
Die `RestoreAsync`-Methode für `RestoreContext` kann nur innerhalb der `OnDataLossAsync`-Methode aufgerufen werden.
Der von `OnDataLossAsync` zurückgegebene Boolean-Wert gibt an, ob der Dienst seinen Status aus einer externen Quelle wiederhergestellt hat.
Wenn für `OnDataLossAsync` „TRUE“ zurückgegeben wird, erstellt Service Fabric alle anderen Replikate aus diesem primären Replikat neu. Service Fabric stellt sicher, dass Replikate, die `OnDataLossAsync` empfangen, zunächst die primäre Rolle annehmen, aber keinen Lese- oder Schreibstatus erhalten.
Das bedeutet, dass `RunAsync` bei StatefulService-Implementierungen erst aufgerufen wird, wenn `OnDataLossAsync` erfolgreich abgeschlossen wurde.
`OnDataLossAsync` wird dann für das neue primäre Replikat aufgerufen.
Bis ein Dienst diese API erfolgreich (durch Rückgabe von true oder false) abschließt und die relevanten Neukonfigurationen fertigstellt, wird weiterhin die API nacheinander aufgerufen.

`RestoreAsync` löscht zuerst jeden bestehenden Status in dem primären Replikat, für das es aufgerufen wurde. Danach erstellt der Reliable State Manager alle Reliable Objects, die im Sicherungsordner vorhanden sind. Als Nächstes werden die Reliable Objects angewiesen, ihre Prüfpunkte im Sicherungsordner wiederherzustellen. Schließlich stellt der Reliable State Manager seinen eigenen Status aus den Protokolldatensätzen im Sicherungsordner wieder her und führt die Wiederherstellung aus. Im Rahmen des Wiederherstellungsprozesses werden ab dem „Startpunkt“ beginnende Vorgänge, die Protokolldatensätze in den Sicherungsordner ausgeführt haben, in den Reliable Objects wiederholt. Dadurch wird sichergestellt, dass der wiederhergestellte Status konsistent ist.

## <a name="next-steps"></a>Nächste Schritte
  - [Zuverlässige Auflistungen](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services – Benachrichtigungen](service-fabric-reliable-services-notifications.md)
  - [Konfigurieren von Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

