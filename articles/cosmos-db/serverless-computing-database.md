---
title: Serverloses Datenbankcomputing – Azure Functions und Azure Cosmos DB
description: Hier erfahren Sie, wie Sie mithilfe von Azure Cosmos DB und Azure Functions ereignisgesteuerte, serverlose Computing-Apps erstellen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: db85d02a4f5c6e0f644a03394b570aac46202e72
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256942"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Serverloses Datenbankcomputing mit Azure Cosmos DB und Azure Functions

Das serverlose Computing beruht im Wesentlichen auf der Fähigkeit, sich auf einzelne Logikelemente zu konzentrieren, die wiederholbar und zustandslos sind. Diese Elemente erfordern keine Infrastrukturverwaltung, und Ressourcen werden von ihnen lediglich für die Sekunden oder sogar Millisekunden ihrer Ausführung verbraucht. Den Kern der serverlosen Computing-Aktivitäten stellen Funktionen dar, die von [Azure Functions](https://azure.microsoft.com/services/functions) im Azure-Ökosystem verfügbar gemacht werden. Weitere Informationen zu anderen serverlosen Ausführungsumgebungen in Azure finden Sie auf der Seite [Serverlos in Azure](https://azure.microsoft.com/solutions/serverless/). 

Mit der nativen Integration zwischen [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) und Azure Functions können Sie Datenbanktrigger, Eingabebindungen und Ausgabebindungen direkt von Ihrem Azure Cosmos DB-Konto aus erstellen. Mithilfe von Azure Functions und Azure Cosmos DB können Sie ereignisgesteuerte serverlose Apps erstellen und bereitstellen, die Zugriff mit geringer Latenzzeit auf vielfältige Daten für eine globale Benutzerbasis bieten.

## <a name="overview"></a>Übersicht

Mit Azure Cosmos DB und Azure Functions können Sie Ihre Datenbanken und serverlosen Apps wie folgt integrieren:

* Erstellen Sie einen ereignisgesteuerten **Azure Cosmos DB-Trigger** in einer Azure-Funktion. Dieser Trigger stützt sich auf [Änderungsfeed](change-feed.md)-Datenströme, anhand derer Ihr Azure Cosmos DB-Container auf Änderungen überwacht wird. Werden Änderungen an einem Container vorgenommen, wird der Änderungsfeed-Datenstrom an den Trigger gesendet, wodurch die Azure-Funktion aufgerufen wird.
* Sie können aber auch eine Azure-Funktion mithilfe einer **Eingabebindung** an einen Azure Cosmos DB-Container binden. Eingabebindungen lesen Daten aus einem Container, wenn eine Funktion ausgeführt wird.
* Binden Sie eine Funktion mithilfe einer **Ausgabebindung** an einen Azure Cosmos DB-Container. Ausgabebindungen schreiben Daten in einen Container, wenn eine Funktion abgeschlossen wird.

> [!NOTE]
> Zurzeit werden Azure Cosmos DB-Trigger, Eingabebindungen und Ausgabebindungen nur für die Verwendung mit der SQL-API unterstützt. Für alle anderen Azure Cosmos DB-APIs müssen Sie für den Datenbankzugriff aus Ihrer Funktion den statischen Client für Ihre API verwenden.


In der folgenden Abbildung wird jede einzelne dieser drei Integrationen veranschaulicht: 

![Integration von Azure Cosmos DB und Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Der Azure Cosmos DB-Trigger, die Eingabebindung und die Ausgabebindung können in den folgenden Kombinationen verwendet werden:
* Eine Azure Cosmos DB-Trigger kann mit einer Ausgangsbindung an einen anderen Azure Cosmos DB-Container verwendet werden. Nachdem eine Funktion eine Aktion an einem Element im Änderungsfeed ausgeführt hat, können Sie es in einen anderen Container schreiben (beim Schreiben in den ursprünglichen Container würde im Endeffekt eine rekursive Schleife entstehen). Sie können jedoch auch einen Azure Cosmos DB-Trigger verwenden, um effektiv alle geänderten Elemente aus einem Container in einen anderen Container zu migrieren, unter Verwendung einer Ausgabebindung.
* Eingabebindungen und Ausgabebindungen für Azure Cosmos DB können in derselben Azure-Funktion verwendet werden. Dies funktioniert gut in Fällen, in denen Sie mit der Eingabebindung bestimmte Daten suchen, sie in der Azure-Funktion ändern und anschließend nach der Änderung im selben oder einem anderen Container speichern möchten.
* Eine Eingabebindung an einen Azure Cosmos DB-Container kann in derselben Funktion wie ein Azure Cosmos DB-Trigger verwendet werden; die Verwendung mit oder ohne eine Ausgabebindung ist ebenfalls möglich. Mit einer solchen Kombination können Sie z.B. aktuelle Wechselkursinformationen (abgerufen mit einer Eingangsbindung in einen Kurscontainer) auf den Änderungsfeed für neue Bestellungen in Ihrem Einkaufswagendienst anwenden. Die aktualisierte Einkaufswagensumme kann mit angewendetem aktuellen Wechselkurs mithilfe einer Ausgabebindung in einen dritten Container geschrieben werden.

## <a name="use-cases"></a>Anwendungsfälle

Die folgenden Anwendungsfälle veranschaulichen einige Möglichkeiten, wie Sie das Beste aus Ihren Azure Cosmos DB-Daten machen können – indem Sie Ihre Daten mit dem ereignisgesteuerten Azure Functions verbinden.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT-Anwendungsfall: Azure Cosmos DB-Trigger und Ausgabebindung

In IoT-Implementierungen können Sie eine Funktion aufrufen, wenn die Motorkontrollleuchte in einem angeschlossenen Auto angezeigt wird.

**Implementierung:** Verwenden eines Azure Cosmos DB-Triggers und einer Ausgabebindung

1. Mit einem **Azure Cosmos DB-Trigger** werden Ereignisse für Fahrzeugwarnungen ausgelöst, z.B. das Einschalten der Motorkontrollleuchte in einem angeschlossenen Auto.
2. Bei Einschalten der Motorkontrollleuchte werden die Sensordaten an Azure Cosmos DB gesendet.
3. Azure Cosmos DB erstellt oder aktualisiert Dokumente mit neuen Sensordaten. Anschließend werden diese Änderungen zum Azure Cosmos DB-Trigger gestreamt.
4. Der Trigger wird bei jeder Datenänderung an der Sensordatensammlung ausgelöst, da alle Änderungen über den Änderungsfeed gestreamt werden.
5. Anhand einer Schwellenwertbedingung in der Funktion werden die Sensordaten an die Garantieabteilung gesendet.
6. Steigt auch die Temperatur über einen bestimmten Wert, wird auch eine Warnung an den Fahrzeughalter gesendet.
7. Die **Ausgabebindung** in der Funktion aktualisiert den Fahrzeugdatensatz in einem weiteren Azure Cosmos DB-Container, um Informationen über das Motorkontrollereignis zu speichern.

Die folgende Abbildung zeigt den Code, der für diesen Trigger in das Azure-Portal geschrieben wird.

![Erstellen eines Azure Cosmos DB-Triggers im Azure-Portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finanzieller Anwendungsfall – Trigger mit Timer und Eingabebindung

Bei finanziellen Implementierungen können Sie beispielsweise eine Funktion aufrufen, wenn der Saldo eines Bankkontos unter einen bestimmten Betrag fällt.

**Implementierung:** Ein Trigger mit Timer mit einer Azure Cosmos DB-Eingabebindung

1. Mithilfe eines [Triggers mit Timer](../azure-functions/functions-bindings-timer.md) können Sie die in einem Azure Cosmos DB-Container gespeicherten Kontosaldoinformationen unter Verwendung einer **Eingabebindung** in festgelegten Zeitintervallen abrufen.
2. Wenn der Saldo unter dem vom Benutzer festgelegten unteren Schwellenwert für Salden liegt, wird durch die Azure-Funktion eine bestimmte Nachverfolgungsaktion ausgeführt.
3. Bei der Ausgabebindung kann es sich um eine [SendGrid-Integration](../azure-functions/functions-bindings-sendgrid.md) handeln, die eine E-Mail von einem Dienstkonto an die E-Mail-Adressen versendet, die für jedes der Konten mit geringem Saldo angegeben sind.

In den folgenden Abbildungen wird der Code im Azure-Portal für dieses Szenario gezeigt.

![Datei „Index.js“ für einen Trigger mit Timer für ein finanzielles Szenario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Datei „Run.csx“ für einen Trigger mit Timer für ein finanzielles Szenario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Gaming-Anwendungsfall: Azure Cosmos DB-Trigger und Ausgabebindung

Wenn in einer Gaming-Anwendung ein neuer Benutzer erstellt wird, können Sie mithilfe der [Azure Cosmos DB-Gremlin-API](graph-introduction.md) nach anderen Benutzern suchen, die diesen möglicherweise kennen. Anschließend können Sie die Ergebnisse in eine [Azure Cosmos DB-SQL-Datenbank] schreiben, aus der sie leicht abgerufen werden können.

**Implementierung:** Verwenden eines Azure Cosmos DB-Triggers und einer Ausgabebindung

1. Mithilfe einer Azure Cosmos DB-[Grafikdatenbank](graph-introduction.md), in der alle Benutzer gespeichert werden, können Sie eine neue Funktion mit einem Azure Cosmos DB-Trigger erstellen. 
2. Bei Einfügen eines neuen Benutzers wird die Funktion aufgerufen, und das Ergebnis wird mithilfe einer **Ausgabebindung** gespeichert.
3. Die Funktion fragt die Grafikdatenbank ab und sucht nach allen Benutzern, die in direkter Beziehung zum neuen Benutzer stehen. Das resultierende Dataset wird an die Funktion zurückgegeben.
4. Diese Daten werden dann in einer Azure Cosmos DB gespeichert, aus der sie von jeder Front-End-Anwendung abgerufen werden können, die den neuen Benutzer für seine verknüpften Freunde anzeigt.

### <a name="retail-use-case---multiple-functions"></a>Einzelhandel-Anwendungsfall: Mehrere Funktionen

Wenn ein Benutzer in Einzelhandelsimplementierungen seinem Einkaufskorb einen Artikel hinzufügt, sind Sie nun flexibel genug, um Funktionen für optionale Business-Pipelinekomponenten zu erstellen und aufzurufen.

**Implementierung:** Mehrere Azure Cosmos DB-Trigger überwachen einen Container

1. Sie können mehrere Azure Functions erstellen, indem Sie jeder davon Azure Cosmos DB-Trigger hinzufügen – diese überwachen alle denselben Änderungsfeed von Einkaufswagendaten. Beachten Sie, dass eine neue Leasesammlung für jede Funktion erforderlich ist, wenn mehrere Funktionen auf den gleichen Änderungsfeed lauschen. Weitere Informationen über Leasesammlungen finden Sie unter [Grundlegendes zur Change Feed Processor-Bibliothek](change-feed-processor.md).
2. Wenn dem Einkaufswagen eines Benutzers ein neuer Artikel hinzugefügt wird, wird jede Funktion unabhängig vom Änderungsfeed aus dem Einkaufswagen-Container aufgerufen.
   * Eine Funktion kann anhand des Inhalts des aktuellen Einkaufswagens die Anzeige weiterer Artikel ändern, an denen der Benutzer möglicherweise auch interessiert ist.
   * Von einer anderen Funktion können die Gesamtzahlen des Bestands aktualisiert werden.
   * Eine weitere Funktion kann Kundeninformationen zu bestimmten Produkten an die Marketingabteilung senden, die sie wiederum in einem Werbeverteiler versendet. 

     Jede Abteilung kann einen Azure Cosmos DB-Trigger durch Überwachen des Änderungsfeeds erstellen. Damit ist sichergestellt, dass wichtige Bestellverarbeitungsereignisse im Prozess nicht verzögert werden.

Da die Funktion in allen diesen Anwendungsfällen die App selbst entkoppelt hat, müssen Sie nicht andauernd neue App-Instanzen starten. Stattdessen ruft Azure Functions einzelne Funktionen auf, um einzelne Prozesse je nach Bedarf abzuschließen.

## <a name="tooling"></a>Tools

Die native Integration zwischen Azure Cosmos DB und Azure Functions ist im Azure-Portal und in Visual Studio 2019 verfügbar.

* Im Azure Functions-Portal können Sie einen Azure Cosmos DB-Trigger erstellen. Schnellstartanweisungen finden Sie unter [Erstellen eines Azure Cosmos DB-Triggers im Azure-Portal](https://aka.ms/cosmosdbtriggerportalfunc).
* Im Azure Cosmos DB-Portal können Sie einer vorhandenen Azure-Funktionen-App in derselben Ressourcengruppe einen Azure Cosmos DB-Trigger hinzufügen.
* In Visual Studio 2019 können Sie einen Azure Cosmos DB-Trigger mithilfe der [Azure Functions-Tools](../azure-functions/functions-develop-vs.md) erstellen:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Was spricht für die Integration von Azure Functions beim serverlosen Computing?

Azure Functions bietet die Möglichkeit, skalierbare Arbeitseinheiten bzw. präzise Logikelemente zu erstellen, die bei Bedarf ausgeführt werden, ohne dass eine Infrastruktur bereitgestellt und verwaltet werden muss. Mit Azure Functions müssen Sie keine umfassende App erstellen, um auf Änderungen in Ihrer Azure Cosmos DB-Datenbank zu reagieren. Stattdessen können Sie kleine, wiederverwendbare Funktionen für konkrete Aufgaben erstellen. Darüber hinaus können Sie Azure Cosmos DB-Daten auch als Eingabe oder Ausgabe für eine Azure-Funktion als Reaktion auf ein Ereignis (z.B. eine HTTP-Anforderung oder ein Trigger mit Timer) verwenden.

Azure Cosmos DB empfiehlt sich als folgenden Gründen als Datenbank für Ihre Architektur für serverloses Computing:

* **Sofortiger Zugriff auf alle Ihre Daten**: Sie verfügen über präzisen Zugriff auf jeden gespeicherten Wert, da Azure Cosmos DB sämtliche Daten standardmäßig [automatisch indiziert](index-policy.md) und die Indizes sofort verfügbar macht. Das heißt, dass Sie laufend in der Lage sind, Ihre Datenbank abzufragen, zu aktualisieren und der Datenbank neue Elemente hinzuzufügen, und der sofortige Zugriff über Azure Functions ist möglich.

* **Ohne Schema**. Azure Cosmos DB verfügt über kein Schema, daher kann jede Datenausgabe von einer Azure-Funktion verarbeitet werden. Durch diesen Ansatz der „uneingeschränkten Verarbeitung“ kann eine Vielzahl von Funktionen erstellt werden, die Daten in Azure Cosmos DB ausgeben.

* **Skalierbarer Durchsatz**. Durchsatz kann in Azure Cosmos DB sofort zentral hoch- und herunterskaliert werden. Wenn Sie über Hunderte oder sogar Tausende von Funktionen verfügen, die alle denselben Container abfragen und in diesen Container schreiben, können Sie Ihre [RU/s](request-units.md) zentral hochskalieren, sodass die Last bewältigt werden kann. Alle Funktionen arbeiten parallel mit den zugeordneten RU/s, und Ihre Daten sind garantiert [konsistent](consistency-levels.md).

* **Globale Replikation**. Sie können Azure Cosmos DB-Daten [weltweit](distribute-data-globally.md) replizieren, um die Latenz zu verringern, wobei eine Geolokalisierung Ihrer Daten in größtmöglicher Nähe zu Ihren Benutzern erfolgt. Wie bei allen Azure Cosmos DB-Abfragen sind Daten aus ereignisgesteuerten Triggern Daten, die aus der Azure Cosmos DB in größter Nähe zum Benutzer gelesen werden.

Wenn Sie die Integration mit Azure Functions zum Speichern von Daten einrichten möchten und keine tiefe Indizierung gewünscht wird, oder wenn Sie Anhänge und Mediendateien speichern müssen, ist der [Azure Blob Storage-Trigger](../azure-functions/functions-bindings-storage-blob.md) möglicherweise die bessere Wahl.

Vorteile von Azure Functions: 

* **Ereignisgesteuert**. Azure Functions ist ereignisgesteuert und kann einen Änderungsfeed von Azure Cosmos DB überwachen. Das bedeutet, dass Sie keine Überwachungslogik schreiben müssen. Sie müssen lediglich ein Auge auf die überwachten Änderungen haben. 

* **Keine Einschränkungen**. Funktionen werden parallel ausgeführt, und vom Dienst werden so viele Funktionen aufgerufen, wie benötigt werden. Die Parameter legen Sie fest.

* **Geeignet für schnelle Aufgaben**. Bei jedem ausgelösten Ereignis ruft der Dienst neue Instanzen der Funktionen auf, und er schließt sie, sobald die Funktion abgeschlossen ist. Sie bezahlen lediglich für den Zeitraum, in dem Ihre Funktionen ausgeführt werden.

Wenn Sie nicht sicher sind, ob Flow, Logic Apps, Azure Functions oder WebJobs am besten für Ihre Implementierung geeignet sind, lesen Sie den Artikel [Auswahl zwischen Flow, Logic Apps, Functions und WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Nächste Schritte

Stellen wir nun eine echte Verbindung zwischen Azure Cosmos DB und Azure Functions her: 

* [Erstellen eines Azure Cosmos DB-Triggers im Azure-Portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Erstellen eines Azure Functions-HTTP-Triggers mit einer Azure Cosmos DB-Eingabebindung](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB-Bindungen und -Trigger](../azure-functions/functions-bindings-cosmosdb.md)


 



