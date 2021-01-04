---
title: Messagingausnahmen von Azure Service Bus | Microsoft-Dokumentation
description: Dieser Artikel stellt eine Liste von Azure Service Bus-Messagingausnahmen und vorgeschlagenen Aktionen zur Verfügung, wenn eine Ausnahme auftritt.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e4aa6d82c20e21caabf0205d7446cf88ed8b7f34
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409313"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus-Messagingausnahmen
In diesem Artikel werden die von .NET Framework-APIs generierten .NET-Ausnahmen aufgelistet. 

## <a name="exception-categories"></a>Ausnahmekategorien
Die von den Messaging-APIs generierten Ausnahmen können – zusammen mit den zugehörigen Korrekturmaßnahmen – zu den folgenden Kategorien gehören. Die Bedeutung und die Ursachen einer Ausnahme können je nach Art der Messagingentität variieren:

1. Fehler der Benutzercodierung ([System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true), [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true), [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true), [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)). Allgemeine Maßnahme: Korrigieren Sie den Code, bevor Sie fortfahren.
2. Fehler bei der Einrichtung oder Konfiguration: ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true). Allgemeine Maßnahme: Überprüfen Sie die Konfiguration, und ändern Sie diese gegebenenfalls.
3. Vorübergehende Ausnahmen ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allgemeine Maßnahme: Wiederholen Sie den Vorgang, oder benachrichtigen Sie die Benutzer. Beachten Sie, dass die `RetryPolicy`-Klasse im Client-SDK so konfiguriert werden kann, dass Wiederholungsversuche automatisch verarbeitet werden. Weitere Informationen finden Sie in im [Leitfaden zu Wiederholungsversuchen](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Andere Ausnahmen ([System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true), [System.TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allgemeine Aktion: spezifisch für den Typ der Ausnahme, weitere Informationen finden Sie in der Tabelle im folgenden Abschnitt. 

## <a name="exception-types"></a>Ausnahmetypen
In der folgenden Tabelle werden die Typen von Messagingausnahmen, ihre Ursachen und Vorschläge für Korrekturmaßnahmen aufgelistet.

| **Ausnahmetyp** | **Beschreibung/Ursache/Beispiele** | **Vorgeschlagene Maßnahme** | **Hinweis zur automatischen/sofortigen Wiederholung** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |Der Server hat nicht innerhalb der von [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) vorgegebenen Zeit auf den angeforderten Vorgang reagiert. Unter Umständen hat der Server den angeforderten Vorgang abgeschlossen. Dies kann aufgrund von Verzögerungen im Netzwerk oder in der Infrastruktur auftreten. |Überprüfen Sie den Systemzustand auf Konsistenz, und wiederholen Sie den Vorgang bei Bedarf. Siehe [Timeoutausnahmen](#timeoutexception). |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |Der angeforderte Benutzervorgang ist auf dem Server oder innerhalb des Diensts unzulässig. Sehen Sie sich die Details in der Ausnahmemeldung an. Beispielsweise generiert [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) diese Ausnahme, wenn die Meldung im [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)-Modus empfangen wurde. |Überprüfen Sie den Code und die Dokumentation. Stellen Sie sicher, dass der angeforderte Vorgang gültig ist. |Der Wiederholungsversuch ist nicht hilfreich. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) |Es wurde versucht, einen Vorgang für ein Objekt aufzurufen, das bereits geschlossen, abgebrochen oder verworfen wurde. In seltenen Fällen wurde die Ambient-Transaktion bereits verworfen. |Überprüfen Sie den Code, und stellen Sie sicher, dass keine Vorgänge für verworfene Objekte aufgerufen werden. |Der Wiederholungsversuch ist nicht hilfreich. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) |Das [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider)-Objekt konnte kein Token abrufen, das Token ist ungültig, oder das Token enthält nicht die Ansprüche, die zum Ausführen des Vorgangs erforderlich sind. |Stellen Sie sicher, dass der Tokenanbieter mit den richtigen Werten erstellt wird. Überprüfen Sie die Konfiguration für den Access Control Service. |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) |Mindestens eines der für die Methode bereitgestellten Argumente ist ungültig.<br /> Der für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI enthält Pfadsegmente.<br /> Das für [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oder für [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bereitgestellte URI-Schema ist ungültig. <br />Der Eigenschaftswert ist größer als 32 KB. |Überprüfen Sie den aufrufenden Code, und stellen Sie sicher, dass die Argumente richtig sind. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Die dem Vorgang zugeordnete Entität ist nicht vorhanden oder wurde gelöscht. |Stellen Sie sicher, dass die Entität vorhanden ist. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Es wurde versucht, eine Nachricht mit einer bestimmten Sequenznummer zu empfangen. Diese Nachricht wurde nicht gefunden. |Stellen Sie sicher, dass die Nachricht nicht bereits empfangen wurde. Überprüfen Sie in der Warteschlange für unzustellbare Nachrichten, ob die Nachricht als unzustellbar gekennzeichnet wurde. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Der Client kann keine Verbindung mit Service Bus herstellen. |Stellen Sie sicher, dass der angegebene Hostname richtig und der Host erreichbar ist. <p>Falls Ihr Code in einer Umgebung mit einer Firewall oder einem Proxy ausgeführt wird, stellen Sie sicher, dass der für die Service Bus-Domäne/IP-Adresse und -Ports bestimmte Datenverkehr nicht blockiert wird.</p>|Eine Wiederholung kann helfen, wenn zeitweilige Verbindungsprobleme vorliegen. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Der Dienst kann die Anforderung derzeit nicht verarbeiten. |Der Client kann eine gewisse Zeit warten und dann den Vorgang wiederholen. |Der Client kann den Vorgang nach einer gewissen Zeitspanne wiederholen. Wenn die Wiederholung zu einer anderen Ausnahme führt, überprüfen Sie das Wiederholungsverhalten dieser Ausnahme. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Eine allgemeine Messagingausnahme, die in folgenden Fällen ausgelöst werden kann:<p>Es wird versucht, ein [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)-Element mit einem Namen oder einem Pfad zu erstellen, der zu einem anderen Entitätstyp gehört (z.B. zu einem Thema).</p><p>Es wird versucht, eine Nachricht zu senden, die größer als 256 KB ist. </p>Der Server oder der Dienst hat beim Verarbeiten der Anforderung einen Fehler festgestellt. Sehen Sie sich die Details in der Ausnahmemeldung an. Dies ist in der Regel eine vorübergehende Ausnahme.</p><p>Die Anforderung wurde beendet, da die Entität gedrosselt wird. Fehlercode: 50001, 50002, 50008. </p> | Überprüfen Sie den Code, und stellen Sie sicher, dass nur serialisierbare Objekte für den Nachrichtentext verwendet werden (oder verwenden Sie ein benutzerdefiniertes Serialisierungsprogramm). <p>Überprüfen Sie die Dokumentation für die unterstützten Werttypen der Eigenschaften, und verwenden Sie nur unterstützte Typen.</p><p> Überprüfen Sie die [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) -Eigenschaft. Wenn sie den Wert **TRUE** aufweist, können Sie versuchen, den Vorgang zu wiederholen. </p>| Ist die Ausnahme auf eine Drosselung zurückzuführen, wiederholen Sie den Vorgang nach einigen Sekunden. Das Wiederholungsverhalten ist nicht definiert und in anderen Szenarien unter Umständen nicht hilfreich.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Es wurde versucht, eine Entität mit einem Namen zu erstellen, der bereits von einer anderen Entität in diesem Dienstnamespace verwendet wird. |Löschen Sie die vorhandene Entität, oder wählen Sie einen anderen Namen für die zu erstellende Entität. |Der Wiederholungsversuch ist nicht hilfreich. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Die Messagingentität hat die maximal zulässige Größe erreicht, oder die maximale Anzahl von Verbindungen zu einem Namespace wurde überschritten. |Schaffen Sie Platz in der Entität, indem Sie Nachrichten aus der Entität oder ihren Unterwarteschlangen empfangen. Siehe [QuotaExceededException](#quotaexceededexception). |Eine Wiederholung kann helfen, wenn in der Zwischenzeit Nachrichten entfernt wurden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie versuchen, eine ungültige Regelaktion zu erstellen. Service Bus fügt diese Ausnahme zu einer unzustellbaren Nachricht hinzu, wenn beim Verarbeiten der Regelaktion für diese Nachricht ein Fehler festgestellt wird. |Überprüfen Sie die Regelaktion auf ihre Richtigkeit. |Der Wiederholungsversuch ist nicht hilfreich. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie versuchen, einen ungültigen Filter zu erstellen. Service Bus fügt diese Ausnahme zu einer unzustellbaren Nachricht hinzu, wenn beim Verarbeiten des Filters für diese Nachricht ein Fehler festgestellt wird. |Überprüfen Sie den Filter auf Richtigkeit. |Der Wiederholungsversuch ist nicht hilfreich. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Es wurde versucht, eine Sitzung mit einer bestimmten Sitzungs-ID zu akzeptieren, aber die Sitzung wird derzeit durch einen anderen Client gesperrt. |Stellen Sie sicher, dass die Sperre für die Sitzung von den anderen Clients aufgehoben wird. |Eine Wiederholung kann helfen, wenn die Sitzung in der Zwischenzeit freigegeben wurde. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Die Transaktion umfasst zu viele Vorgänge. |Reduzieren Sie die Anzahl der Vorgänge, die Teil dieser Transaktion sind. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Es wurde ein Laufzeitvorgang für eine deaktivierte Entität angefordert. |Aktivieren Sie die Entität. |Eine Wiederholung kann helfen, wenn die Entität in der Zwischenzeit aktiviert wurde. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus gibt diese Ausnahme zurück, wenn Sie eine Nachricht zu einem Thema senden, für das eine Vorfilterung aktiviert wurde. |Stellen Sie sicher, dass mindestens ein Filter übereinstimmt. |Der Wiederholungsversuch ist nicht hilfreich. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Eine Nachrichtennutzlast überschreitet den Grenzwert von 256 KB. Der Grenzwert von 256 KB gilt für die Gesamtgröße der Nachricht, zu der auch Systemeigenschaften und .NET-Mehraufwand gehören. |Reduzieren Sie die Größe der Nachrichtennutzlast, und wiederholen Sie den Vorgang. |Der Wiederholungsversuch ist nicht hilfreich. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |Die Ambient-Transaktion ( *Transaction.Current* ) ist ungültig. Sie wurde möglicherweise abgeschlossen oder abgebrochen. Unter Umständen enthält die innere Ausnahme weitere Informationen. | |Der Wiederholungsversuch ist nicht hilfreich. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |Es wurde versucht, einen Vorgang für eine unsichere Transaktion auszuführen, oder es wurde versucht, ein Commit für die Transaktion auszuführen, und die Transaktion wurde unsicher. |Die Anwendung muss diese Ausnahme (als Sonderfall) behandeln, da für die Transaktion u. U. bereits ein Commit ausgeführt wurde. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) gibt an, dass das Kontingent für eine bestimmte Entität überschritten wurde.

### <a name="queues-and-topics"></a>Warteschlangen und Themen
Für Warteschlangen und Themen ist dies häufig die Größe der Warteschlange. Die Fehlermeldungseigenschaft enthält weitere Details, wie das folgende Beispiel zeigt:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Die Meldung besagt, dass das Thema seine maximale Größe überschritten hat, in diesem Fall 1GB (Standardgrenzwert). 

### <a name="namespaces"></a>Namespaces

Für Namespaces kann [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) angeben, dass eine Anwendung die maximale Anzahl der Verbindungen zu einem Namespace überschritten hat. Beispiel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Häufige Ursachen
Es gibt zwei häufige Ursachen für diesen Fehler: die Warteschlange für unzustellbare Nachrichten und nicht funktionierende Nachrichtenempfänger.

1. **[Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md)** : Ein Leser kann Nachrichten nicht abschließen, und die Nachrichten werden nach Ablauf der Sperre an die Warteschlange/das Thema zurückgegeben. Dies kann auftreten, wenn der Leser auf eine Ausnahme trifft, die den Aufruf von [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) verhindert. Nachdem eine Nachricht 10 Mal gelesen wurde, wird sie standardmäßig in die Warteschlange für unzustellbare Nachrichten verschoben. Dieses Verhalten wird von der Eigenschaft [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) gesteuert, die standardmäßig den Wert 10 aufweist. Wenn sich Nachrichten in der Warteschlange für unzustellbare Nachrichten anhäufen, belegen sie Speicherplatz.
   
    Um das Problem zu beheben, lesen und schließen Sie die Nachrichten in der Warteschlange für unzustellbare Nachrichten ab – genau wie in jeder anderen Warteschlange. Sie können die Methode [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) verwenden, die Sie beim Formatieren des Pfads für die Warteschlange für unzustellbare Nachrichten unterstützt.
2. **Empfänger beendet**. Ein Empfänger hat den Empfang von Nachrichten aus einer Warteschlange oder einem Abonnement beendet. Dies können Sie anhand der Eigenschaft [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) feststellen, die die vollständige Aufschlüsselung der Nachrichten zeigt. Wenn die Eigenschaft [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) hoch ist oder dieser wert anwächst, werden die Nachrichten nicht so schnell gelesen, wie sie geschrieben werden.

## <a name="timeoutexception"></a>TimeoutException
Eine [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) zeigt an, dass ein von einem Benutzer initiierter Vorgang länger als das Timeout des Vorgangs dauert. 

Überprüfen Sie den Wert der [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netcore-3.1&preserve-view=true)-Eigenschaft, da das Erreichen dieses Limits auch eine [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) auslösen kann.

Es wird erwartet, dass es während oder zwischen Wartungsarbeiten, z. B. bei Service Bus-Dienstupdates (oder) Betriebssystemaktualisierungen für Ressourcen, die den Dienst ausführen, zu Timeouts kommt. Bei Betriebssystemupdates werden Entitäten verschoben, und Knoten werden aktualisiert oder neu gestartet. Dies kann zu Timeouts führen. Details zur Vereinbarung zum Service Level (SLA) für den Azure Service Bus-Dienst finden Sie unter [SLA für Service Bus](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Warteschlangen und Themen
Für Warteschlangen und Themen wird das Zeitlimit entweder in der [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)-Eigenschaft als Teil der Verbindungszeichenfolge oder über [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) angegeben. Die Fehlermeldung selbst kann variieren, aber sie enthält immer den Timeoutwert für den aktuellen Vorgang. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Ursache

Die Ausnahme **MessageLockLostException** wird ausgelöst, wenn eine Nachricht über den Empfangsmodus [PeekLock](message-transfers-locks-settlement.md#peeklock) empfangen wird und die Sperre des Clients auf Dienstseite abgelaufen ist.

Die Sperre einer Nachricht kann aufgrund verschiedener Gründe ablaufen: 

  * Der Timer der Sperre ist abgelaufen, bevor die Clientanwendung ihn erneuert hat.
  * Die Clientanwendung hat die Sperre abgerufen, diese in einem dauerhaften Speicher gespeichert und dann neu gestartet. Nach diesem Neustart hat die Clientanwendung die In-Flight-Nachrichten abgerufen und versucht, diese abzuschließen.

### <a name="resolution"></a>Lösung

Wenn die Ausnahme **MessageLockLostException** auftritt, kann die Clientanwendung die Nachricht nicht mehr verarbeiten. Die Clientanwendung kann die Ausnahme optional zur Analyse protokollieren, jedoch *muss* der Client die Nachricht verwerfen.

Da die Sperre der Nachricht abgelaufen ist, wird sie wieder in die Warteschlange (oder das Abonnement) eingereiht, sodass die Clientanwendung sie beim nächsten Empfangsaufruf verarbeiten kann.

Wenn **MaxDeliveryCount** überschritten wurde, kann die Nachricht in **DeadLetterQueue** verschoben werden.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Ursache

Die Ausnahme **SessionLockLostException** wird ausgelöst, wenn eine Sitzung akzeptiert wird und die Sperre des Clients auf Dienstseite abläuft.

Die Sperre einer Sitzung kann aufgrund verschiedener Gründe ablaufen: 

  * Der Timer der Sperre ist abgelaufen, bevor die Clientanwendung ihn erneuert hat.
  * Die Clientanwendung hat die Sperre abgerufen, diese in einem dauerhaften Speicher gespeichert und dann neu gestartet. Nach diesem Neustart hat die Clientanwendung die In-Flight-Sitzungen abgerufen und versucht, die Nachrichten in diesen Sitzungen abzuschließen.

### <a name="resolution"></a>Lösung

Wenn die Ausnahme **SessionLockLostException** auftritt, kann die Clientanwendung die Nachrichten in der Sitzung nicht mehr verarbeiten. Die Clientanwendung kann die Ausnahme zur Analyse protokollieren, jedoch *muss* der Client die Nachrichten verwerfen.

Da die Sperre der Sitzung abgelaufen ist, wird sie wieder in die Warteschlange (oder das Abonnement) eingereiht, sodass sie von der nächsten Clientanwendung gesperrt werden kann, die die Sitzung akzeptiert. Da die Sitzungssperre immer von jeweils einer einzelnen Clientanwendung aufrechterhalten wird, wird die Reihenfolge der Verarbeitung garantiert.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Ursache

In den folgenden Fällen wird die Ausnahme **SocketException** ausgelöst:
   * Wenn ein Verbindungsversuch fehlschlägt, weil der Host nach einem festgelegten Zeitraum nicht ordnungsgemäß reagiert hat (TCP-Fehlercode 10060).
   * Eine aktive Verbindung ist fehlgeschlagen, weil der verbundene Host nicht reagiert hat.
   * Ein Fehler ist beim Verarbeiten der Nachricht aufgetreten, oder es kam beim Remotehost zu einer Zeitüberschreitung.
   * Es liegt ein Problem mit der zugrunde liegenden Netzwerkressource vor.

### <a name="resolution"></a>Lösung

Die **SocketException** -Fehler geben an, dass die VM, die die Anwendungen hostet, den Namen `<mynamespace>.servicebus.windows.net` nicht in die entsprechende IP-Adresse konvertieren kann. 

Überprüfen Sie, ob der folgende Befehl erfolgreich eine IP-Adresse zuweisen kann.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

Dadurch sollte eine Ausgabe ähnlich der folgenden zurückgegeben werden:

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Wenn der obige Name **nicht in eine IP-Adresse und den Namespace-Alias aufgelöst wird** , wenden Sie sich zur weiteren Untersuchung an den Netzwerkadministrator. Die Namensauflösung erfolgt über einen DNS-Server. Diese Ressource befindet sich üblicherweise im Kundennetzwerk. Wenn die DNS-Auflösung über Azure DNS erfolgt, wenden Sie sich an den Azure-Support.

Wenn die Namensauflösung **erwartungsgemäß funktioniert** , überprüfen Sie [hier](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues), ob Verbindungen mit Azure Service Bus erlaubt sind.


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Ursache

**MessagingException** ist eine generische Ausnahme, die aus verschiedenen Gründen ausgelöst werden kann. Einige dieser Gründe werden im Folgenden aufgeführt.

   * Es wurde versucht, eine **QueueClient** -Instanz in einem **Thema** oder **Abonnement** zu erstellen.
   * Die Größe der gesendeten Nachricht ist überschreitet den Grenzwert für die jeweilige Stufe. Erfahren Sie mehr über die [Kontingente und Grenzwerte von Service Bus](service-bus-quotas.md).
   * Eine spezifische Datenebenenanforderung (Senden, Empfangen, Abschließen, Verwerfen) wurde aufgrund von Drosselung beendet.
   * Vorübergehende Probleme durch Dienstupgrades und Neustarts

> [!NOTE]
> Die obige Liste von Ausnahmen ist nicht vollständig.

### <a name="resolution"></a>Lösung

Die Lösungsschritte hängen davon ab, wodurch die Ausnahme **MessagingException** ausgelöst wurde.

   * Bei **vorübergehenden Problemen** (bei denen **_isTransient_*auf*_true_*festgelegt ist) oder* Drosselungsproblemen** lässt sich das Problem manchmal durch Wiederholen des Vorgangs lösen. Die Standardwiederholungsrichtlinie für das SDK kann hierfür verwendet werden.
   * Bei anderen Problemen geben die in der Ausnahme angegebenen Details Informationen zum Problem und den Lösungsschritten an.

## <a name="next-steps"></a>Nächste Schritte
Die vollständige .NET-API-Referenz für Service Bus finden Sie unter [Azure .NET API Referenz](/dotnet/api/overview/azure/service-bus).
Weitere Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung](service-bus-troubleshooting-guide.md).
