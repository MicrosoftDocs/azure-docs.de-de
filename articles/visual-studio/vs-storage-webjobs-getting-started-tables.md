---
title: Erste Schritte mit Azure-Speicher und verbundenen Visual Studio-Diensten (WebJob-Projekte)
description: Erfahren Sie etwas über die ersten Schritte mit Azure-Tabellenspeicher in einem Azure WebJobs-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a9a4475465fefb01ec53e6e0eb814f9b8f192a1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390841"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Erste Schritte mit Azure Storage (Azure WebJob-Projekte)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel enthält C#-Codebeispiele, die zeigen, wie das Azure WebJobs SDK (Version 1.x) mit dem Azure-Tabellenspeicherdienst verwendet wird. In den Codebeispielen wird Version 1.x des [WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verwendet.

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.  Weitere Informationen finden Sie unter [Erste Schritte mit Azure Table Storage mit .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

Einige der Codeausschnitte zeigen das **Table** -Attribut in Funktionen, die manuell aufgerufen werden, also nicht mit einem der Triggerattribute.

## <a name="how-to-add-entities-to-a-table"></a>Hinzufügen von Entitäten zu einer Tabelle
Um einer Tabelle Entitäten hinzuzufügen, verwenden Sie das **Table**-Attribut mit einem **ICollector<T>** - oder **IAsyncCollector<T>** -Parameter, wobei **T** das Schema der Entitäten angibt, die Sie hinzufügen möchten. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Tabelle angibt.

Das folgende Codebeispiel fügt einer Tabelle namens **Ingress** *Person*-Entitäten hinzu.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

In der Regel ist der Typ, den Sie mit **ICollector verwenden**, von **TableEntity** abgeleitet oder implementiert **ITableEntity**, was aber nicht sein muss. Beide der folgenden **Person**-Klassen funktionieren mit dem Code in der vorangehenden **Ingress**-Methode.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Wenn Sie direkt mit dem Azure-Speicher-API arbeiten möchten, können Sie der Methodensignatur einen **CloudStorageAccount** -Parameter hinzufügen.

## <a name="real-time-monitoring"></a>Überwachung in Echtzeit
Da Dateneingangsfunktionen oft große Datenmengen verarbeiten, bietet das Dashboard des WebJobs-SDK Überwachungsdaten in Echtzeit. Der Abschnitt **Aufrufprotokoll** informiert Sie, ob die Funktion noch ausgeführt wird.

![Eingangsfunktion wird ausgeführt](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Die Seite **Aufrufdetails** meldet den Status der Funktion (Anzahl der geschriebenen Entitäten) während der Ausführung und bietet Ihnen die Möglichkeit, sie abzubrechen.

![Eingangsfunktion wird ausgeführt](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Wenn die Funktion abgeschlossen ist, meldet die Seite **Aufrufdetails** die Anzahl der geschriebenen Zeilen.

![Eingangsfunktion ist abgeschlossen](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Lesen mehrerer Entitäten aus einer Tabelle
Verwenden Sie zum Lesen einer Tabelle das **Table**-Attribut mit dem **IQueryable<T>** -Parameter, wobei der Typ **T** von **TableEntity** abgeleitet ist oder **ITableEntity** implementiert.

Das folgende Codebeispiel liest und protokolliert alle Zeilen aus der **Ingress**-Tabelle:

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Lesen einer einzelnen Entität aus einer Tabelle
Es gibt einen **Table** -Attributkonstruktor mit zwei zusätzliche Parametern, mit denen Sie den Partitionsschlüssel und Zeilenschlüssel angeben können, wenn Sie eine Bindung mit einer einzelnen Tabellenentität herstellen möchten.

Das folgende Codebeispiel liest eine Tabellenzeile für eine **Person** -Entität anhand der Partitions- und Zeilenschlüsselwerte, die in einer Warteschlange empfangen wurden:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


Die **Person**-Klasse in diesem Beispiel muss nicht zwingend **ITableEntity** implementieren.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Verwenden der .NET Storage API für das direkte Arbeiten mit der Tabelle
Sie können auch das **Table**-Attribut mit einem **CloudTable**-Objekt verwenden, um flexibler mit einer Tabelle arbeiten zu können.

Im folgenden Codebeispiel wird ein **CloudTable** -Objekt verwendet, um eine einzelne Entität zur Tabelle *Ingress* hinzuzufügen.

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Weitere Informationen zur Verwendung des **CloudTable** -Objekts finden Sie unter [Erste Schritte mit Azure Table Storage mit .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Verwandte in den Artikeln zu Warteschlangen behandelte Themen
Informationen zur Handhabung der durch eine Warteschlangennachricht ausgelösten Tabellenverarbeitung oder zu Szenarien für das WebJobs SDK, die nicht spezifisch für die Tabellenverarbeitung sind, finden Sie unter [Erste Schritte mit Azure Queue Storage und verbundenen Diensten in Visual Studio (WebJob-Projekte)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Tabellen behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und des WebJobs SDK finden Sie unter [Dokumentationsressourcen für Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

