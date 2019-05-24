---
title: Ausführen von Unittests für zustandsbehaftete Dienste in Azure Service Fabric | Microsoft-Dokumentation
description: Informationen zu den Konzepten und Verfahren von Unittests für zustandsbehaftete Service Fabric-Dienste.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ad7cf3a1dfcef8795ceb378a59a1cf0b2010293e
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595502"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Ausführen von Unittests für zustandsbehaftete Dienste in Service Fabric

In diesem Artikel werden die Konzepte und Verfahren von Unittests für zustandsbehaftete Service Fabric-Dienste behandelt. Für Unittests in Service Fabric sind besondere Aspekte zu berücksichtigen, da der Anwendungscode aktiv unter mehreren verschiedenen Kontexten ausgeführt wird. In diesem Artikel werden die Verfahren beschrieben, mit denen sichergestellt wird, dass Anwendungscode unter jedem der unterschiedlichen Kontexte abgedeckt wird, in denen er ausgeführt werden kann.

## <a name="unit-testing-and-mocking"></a>Unittests und Simulation
Unittests im Rahmen dieses Artikels sind automatisierte Tests, die im Kontext eines Test Runners wie MSTest oder NUnit ausgeführt werden können. Mit den Komponententests in diesem Artikel werden keine Vorgänge für Remoteressourcen wie z.B. eine Datenbank oder RESTful-API ausgeführt. Diese Remoteressourcen müssen simuliert werden. Bei der Simulation im Rahmen dieses Artikels werden die Rückgabewerte für Remoteressourcen vorgetäuscht, protokolliert und gesteuert.

### <a name="service-fabric-considerations"></a>Überlegungen zu Service Fabric
Beim Ausführen von Unittests für einen zustandsbehafteten Service Fabric-Dienst sind mehrere Aspekte zu berücksichtigen. Erstens wird der Dienstcode auf mehreren Knoten, jedoch unter unterschiedlichen Rollen ausgeführt. Zur vollständigen Abdeckung sollten Komponententests den Code unter jeder Rolle überprüfen. Die unterschiedlichen Rollen sind „Primary“, „ActiveSecondary“, „IdleSecondary“ und „Unknown“. Die Rolle „None“ muss in der Regel nicht speziell abgedeckt werden, da diese Rolle in Service Fabric als leerer oder NULL-Dienst eingestuft wird. Zweitens ändert jeder Knoten irgendwann die Rolle. Zur vollständigen Abdeckung sollten Codeausführungspfade mit auftretenden Rollenänderungen getestet werden.

## <a name="why-unit-test-stateful-services"></a>Wozu dienen Komponententests für zustandsbehaftete Dienste? 
Bei Unittests für zustandsbehaftete Dienste können einige häufige Fehler aufgedeckt werden, die durch herkömmliche anwendungs- oder domänenspezifische Unittests nicht unbedingt erkannt werden. Wenn der zustandsbehaftete Dienst beispielsweise einen In-Memory-Zustand aufweist, kann bei dieser Testart überprüft werden, ob der In-Memory-Zustand für alle Replikate synchron bleibt. Bei dieser Testart kann zudem geprüft werden, ob ein zustandsbehafteter Dienst entsprechend auf Abbruchtoken reagiert, die von der Service Fabric-Orchestrierung übergeben werden. Wenn Abbrüche ausgelöst werden, sollte der Dienst alle Vorgänge mit langer Ausführungszeit und/oder asynchronen Vorgänge anhalten.  

## <a name="common-practices"></a>Gängige Verfahren

Im folgenden Abschnitt werden die gängigsten Verfahren für Unittests für zustandsbehaftete Dienste empfohlen. Zudem wird erläutert, wie eine Simulationsebene aussehen soll, damit sie eng an die Service Fabric-Orchestrierung und die Zustandsverwaltung angelehnt ist. Eine dieser Bibliotheken ist [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) ab Version 3.3.0. Diese Bibliothek umfasst die empfohlenen Simulationsfunktionen und entspricht den nachfolgend beschriebenen Verfahren.

### <a name="arrangement"></a>Vorbereitung

#### <a name="use-multiple-service-instances"></a>Verwenden mehrerer Dienstinstanzen
Bei Komponententests sollten mehrere Instanzen eines zustandsbehafteten Diensts ausgeführt werden. Dadurch wird die tatsächliche Vorgehensweise im Cluster simuliert, bei der Service Fabric mehrere Replikate bereitstellt, die den Dienst auf verschiedenen Knoten ausführen. Jede dieser Instanzen wird jedoch unter einem jeweils anderen Kontext ausgeführt. Für die Testausführung sollte jede Instanz mit der im Cluster erwarteten Rollenkonfiguration vorbereitet werden. Wenn der Dienst beispielsweise 3 Zielreplikate haben soll, werden in Service Fabric drei Replikate auf unterschiedlichen Knoten bereitgestellt. Dabei ist eines der Replikate das primäre Replikat, und die anderen beiden sind aktive sekundäre Replikate.

In den meisten Fällen variieren die Dienstausführungspfade für jede dieser Rollen geringfügig. Wenn der Dienst z.B. keine Anforderungen von einem aktiven sekundären Replikat akzeptieren soll, enthält der Dienst möglicherweise eine Überprüfung für diesen Fall, um eine Informationsausnahme auszulösen, die angibt, dass eine Anforderung an ein sekundäres Replikat gesendet wurde. Durch Festlegen mehrerer Instanzen kann dieser Fall getestet werden.

Darüber hinaus wird so ermöglicht, dass bei den Tests die Rollen jeder dieser Instanzen gewechselt werden können, um zu überprüfen, ob die Antworten trotz der Rollenänderungen konsistent sind.

#### <a name="mock-the-state-manager"></a>Simulieren des Zustands-Managers
Der Zustands-Manager sollte als Remoteressource behandelt und daher simuliert werden. Bei der Simulation des Zustands-Managers muss zugrunde liegender In-Memory-Speicher vorhanden sein, damit nachverfolgt werden kann, welche Daten im Zustands-Manager gespeichert werden, und damit diese Daten gelesen und überprüft werden können. Eine einfache Möglichkeit hierfür besteht darin, Pseudoinstanzen der einzelnen Typen von zuverlässigen Sammlungen zu erstellen. Verwenden Sie in diesen Simulationen einen Datentyp, der eng an die Vorgänge angelehnt ist, die für die jeweilige Sammlung durchgeführt werden. Im Folgenden sind einige empfohlene Datentypen für die einzelnen zuverlässigen Sammlungen aufgeführt.

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Mehrere Zustands-Manager-Instanzen, ein Speicher
Wie zuvor erwähnt sollten der Zustands-Manager und die zuverlässigen Sammlungen als Remoteressourcen behandelt werden. Daher sollten und werden diese Ressourcen in den Komponententests simuliert. Wenn jedoch mehrere Instanzen eines zustandsbehafteten Diensts ausgeführt werden, gestaltet es sich schwierig, jeden simulierten Zustands-Manager für die unterschiedlichen zustandsbehafteten Dienstinstanzen zu synchronisieren. Wenn der zustandsbehaftete Dienst im Cluster ausgeführt wird, wird in Service Fabric sichergestellt, dass der Zustands-Manager der einzelnen sekundären Replikate mit dem des primären Replikats konsistent ist. Daher sollten sich die Tests gleich verhalten, sodass sie Rollenänderungen simulieren können.

Diese Synchronisierung kann auf einfache Weise erreicht werden, indem ein Singletonmuster für das zugrunde liegende Objekt verwendet wird, in dem die in jede zuverlässige Sammlung geschriebenen Daten gespeichert werden. Beispiel: Ein zustandsbehafteter Dienst verwendet ein `IReliableDictionary<string, string>`-Objekt. Der Pseudo-Zustands-Manager sollte eine Simulation von `IReliableDictionary<string, string>` zurückgeben. Diese Simulation kann ein `ConcurrentDictionary<string, string>`-Objekt verwenden, um die geschriebenen Schlüssel-Wert-Paare nachzuverfolgen. Das `ConcurrentDictionary<string, string>`-Objekt sollte ein Singleton sein, das von allen Instanzen der an den Dienst übergebenen Zustands-Manager verwendet wird.

#### <a name="keep-track-of-cancellation-tokens"></a>Nachverfolgen von Abbruchtoken
Abbruchtoken sind ein wichtiger, aber häufig übersehener Aspekt bei zustandsbehafteten Diensten. Wenn in Service Fabric ein primäres Replikat für einen zustandsbehafteten Dienst gestartet wird, wird ein Abbruchtoken bereitgestellt. Dieses Abbruchtoken soll dem Dienst signalisieren, wenn das Replikat entfernt oder auf eine andere Rolle heruntergestuft wird. Der zustandsbehaftete Dienst soll alle Vorgänge mit langer Ausführungszeit oder asynchronen Vorgänge beenden, damit der Rollenänderungsworkflow in Service Fabric abgeschlossen werden kann.

Beim Ausführen der Komponententests sollten alle für RunAsync, ChangeRoleAsync, OpenAsync und CloseAsync bereitgestellten Token während der Testausführung beibehalten werden. Die Beibehaltung dieser Token ermöglicht, dass beim Test das Herunterfahren des Diensts oder das Herunterstufen simuliert und dann überprüft werden kann, ob der Dienst richtig reagiert.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Umfassende Tests mit simulierten Remoteressourcen
Bei Komponententests sollte ein möglichst großer Teil des Anwendungscodes ausgeführt werden, der den Zustand des zustandsbehafteten Diensts ändern kann. Es wird empfohlen, durchgängige Tests auszuführen. Die einzigen vorhandenen Simulationen betreffen das Aufzeichnen, Simulieren und/oder Überprüfen von Interaktionen der Remoteressourcen. Dies schließt Interaktionen mit dem Zustands-Manager und den zuverlässigen Sammlungen ein. Der folgende Codeausschnitt ist ein Gherkin-Beispiel für einen durchgängigen Test:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Dieser Test bestätigt, dass die in einem Replikat erfassten Daten für ein sekundäres Replikat verfügbar sind, wenn dieses auf ein primäres Replikat hochgestuft wird. Vorausgesetzt, dass eine zuverlässige Sammlung als Sicherungsspeicher für die Mitarbeiterdaten dient, kann mit diesem Test ein potenzieller Fehler aufgedeckt werden, bei dem der Ausführungscode `CommitAsync` für den Übergang nicht ausgeführt hat, um den neuen Mitarbeiter zu speichern. In diesem Fall gibt die zweite Anforderung zum Abrufen der Mitarbeiter den mit der ersten Anforderung hinzugefügten Mitarbeiter nicht zurück.

### <a name="acting"></a>Aktionen
#### <a name="mimic-service-fabric-replica-orchestration"></a>Simulieren der Service Fabric-Orchestrierung von Replikaten
Beim Verwalten mehrerer Dienstinstanzen sollten die Tests diese Dienste auf die gleiche Weise wie bei der Service Fabric-Orchestrierung initialisieren und beenden. Wenn ein Dienst z.B. für ein neues primäres Replikat erstellt wird, ruft Service Fabric CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync und RunAsync auf. Die Lebenszyklusereignisse sind in den folgenden Artikeln dokumentiert:

- [Starten zustandsbehafteter Dienste](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Herunterfahren zustandsbehafteter Dienste](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Tausch des primären Replikats bei zustandsbehafteten Diensten](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Ausführen von Rollenänderungen bei Replikaten
Bei den Komponententests sollten die Rollen der Dienstinstanzen auf die gleiche Weise wie bei der Service Fabric-Orchestrierung geändert werden. Der Zustandsautomat für Rollen wird im folgenden Artikel beschrieben:

[Zustandsautomat für Replikatrollen](service-fabric-concepts-replica-lifecycle.md#replica-role)

Die Simulation von Rollenänderungen ist einer der kritischeren Aspekte bei den Tests und kann Probleme aufdecken, bei denen die Zustände der Replikate nicht konsistent sind. Inkonsistente Replikatzustände können auftreten, weil In-Memory-Zustände in statischen Instanzvariablen oder Instanzvariablen auf Klassenebene gespeichert werden. Beispiele hierfür sind Abbruchtoken, Enumerationen sowie Konfigurationsobjekte und -werte. Dadurch wird auch sichergestellt, dass der Dienst die während RunAsync bereitgestellten Abbruchtoken berücksichtigt, sodass die Rollenänderung erfolgen kann. Durch Simulieren von Rollenänderungen können zudem Probleme erkannt werden, die auftreten können, wenn kein Code geschrieben wurde, um mehrere Aufrufe von RunAsync zu ermöglichen.

#### <a name="cancel-cancellation-tokens"></a>Abbrechen von Abbruchtoken
Es sollten Komponententests festgelegt werden, bei denen das für RunAsync bereitgestellte Abbruchtoken abgebrochen wird. Dadurch kann überprüft werden, ob der Dienst ordnungsgemäß heruntergefahren wird. Beim Herunterfahren sollten alle Vorgänge mit langer Ausführungszeit oder asynchronen Vorgänge beendet werden. Ein mögliches Beispiel für einen Prozess mit langer Ausführungszeit für einen Dienst ist ein Prozess, der auf Nachrichten in einer zuverlässigen Warteschlange lauscht. Dieser kann direkt in RunAsync oder einem Hintergrundthread enthalten sein. Die Implementierung sollte Logik zum Beenden des Vorgangs enthalten, wenn das Abbruchtoken abgebrochen wird.

Wenn die zustandsbehafteten Dienste einen Cache- oder In-Memory-Zustand verwenden, der nur für das primäre Replikat vorhanden sein soll, sollte er zu diesem Zeitpunkt entfernt werden. Dadurch wird sichergestellt, dass dieser Zustand konsistent ist, wenn der Knoten später wieder zum primären Knoten wird. Bei Abbruchtests kann geprüft werden, ob dieser Zustand ordnungsgemäß entfernt wird.

#### <a name="execute-requests-against-multiple-replicas"></a>Ausführen von Anforderungen für mehrere Replikate
Bei Bestätigungstests sollte die gleiche Anforderung für mehrere Replikate ausgeführt werden. In Kombination mit Rollenänderungen können so Konsistenzprobleme aufgedeckt werden. In einem Beispieltest werden möglicherweise folgende Schritte ausgeführt:
1. Ausführen einer Schreibanforderung für das aktuelle primäre Replikat
2. Ausführen einer Leseanforderung, die die in Schritt 1 geschriebenen Daten für das aktuelle primäre Replikat zurückgibt
3. Hochstufen eines sekundären Replikats auf ein primäres Replikat. Dadurch sollte auch das aktuelle primäre Replikat auf ein sekundäres Replikat heruntergestuft werden.
4. Ausführen der gleichen Leseanforderung aus Schritt 2 für das neue sekundäre Replikat.

Im letzten Schritt kann der Test bestätigen, dass die zurückgegebenen Daten konsistent sind. Als potenzielles Problem kann dabei aufgedeckt werden, dass die vom Dienst zurückgegebenen Daten sich im Speicher befinden, jedoch letztendlich von einer zuverlässigen Sammlung gesichert werden. Diese In-Memory-Daten werden möglicherweise nicht ordnungsgemäß mit den in der zuverlässigen Sammlung enthaltenen Daten synchronisiert.

In-Memory-Daten werden normalerweise verwendet, um sekundäre Indizes oder Aggregationen von Daten zu erstellen, die in einer zuverlässigen Sammlung enthalten sind.

### <a name="asserting"></a>Bestätigung
#### <a name="ensure-responses-match-across-replicas"></a>Sicherstellen, dass Antworten in allen Replikaten übereinstimmen
Bei Komponententests sollte bestätigt werden, dass eine Antwort für eine bestimmte Anforderung in mehreren Replikaten konsistent ist, nachdem diese in ein primäres Replikat geändert wurden. Hierbei können mögliche Probleme auftreten, bei denen die in der Antwort angegebenen Daten entweder nicht von einer zuverlässigen Sammlung gesichert werden oder im Speicher beibehalten werden, ohne dass ein Mechanismus zum Synchronisieren dieser Daten in allen Replikaten definiert ist. Dadurch wird sichergestellt, dass der Dienst konsistente Antworten zurücksendet, nachdem in Service Fabric ein Lastenausgleich oder ein Failover zu einem neuen primären Replikat durchgeführt wurde.

#### <a name="verify-service-respects-cancellation"></a>Überprüfen, ob der Dienst einen Abbruch einhält
Prozesse mit langer Ausführungszeit oder asynchrone Prozesse, die bei Abbruch eines Abbruchtokens beendet werden sollen, sollten daraufhin überprüft werden, ob sie nach dem Abbruch tatsächlich beendet werden. Dadurch wird sichergestellt, dass Prozesse, die für nicht primäre Replikate nicht weiter ausgeführt werden sollen, trotz der Rollenänderung der Replikate beendet werden, bevor der Zustandsübergang abgeschlossen ist. Hierbei können zudem Probleme gefunden werden, bei denen ein solcher Prozess den Abschluss einer Rollenänderung oder eine Anforderung zum Herunterladen von Service Fabric blockiert.

#### <a name="verify-which-replicas-should-serve-requests"></a>Überprüfen, welche Replikate Anforderungen verarbeiten sollen
Die Tests sollten das erwartete Verhalten für den Fall bestätigen, dass eine Anforderung zu einem nicht primären Replikat weitergeleitet wird. In Service Fabric ist es möglich, dass sekundäre Replikate Anforderungen verarbeiten. Schreibvorgänge für zuverlässige Sammlungen können jedoch nur über das primäre Replikat erfolgen. Wenn in Ihrer Anwendung nur primäre Replikate Anforderungen verarbeiten sollen oder nur eine Teilmenge von Anforderungen von einem sekundären Replikat verarbeitet werden kann, sollten die Tests das erwartete Verhalten für die positiven und negativen Fälle bestätigen. Dabei wird bei einem negativen Fall eine Anforderung an ein Replikat weitergeleitet, das die Anforderung nicht verarbeiten soll. Das Gegenteil davon ist ein positiver Fall.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie [Komponententests für zustandsbehaftete Dienste](service-fabric-how-to-unit-test-stateful-services.md) ausgeführt werden.
