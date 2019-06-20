---
title: Erfassen von Daten aus Event Hub in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten aus Event Hub in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: cafceb0c7ee6b165ee03727e1dff76db00d2863b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077337"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Erfassen von Daten aus Event Hub in Azure Data Explorer

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure-Daten-Explorer ermöglicht die Datenerfassung (das Laden von Daten) aus Event Hubs. Dabei handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. [Event Hubs](/azure/event-hubs/event-hubs-about) kann Millionen von Ereignissen pro Sekunde nahezu in Echtzeit verarbeiten. In diesem Artikel erstellen Sie einen Event Hub, stellen damit eine Verbindung über Azure Data Explorer her und zeigen den Datenfluss durch das System an.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

* [Ein Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

* [Eine Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), die Daten generiert und an einen Event Hub sendet. Laden Sie die Beispiel-App auf Ihr System herunter.

* [Visual Studio-2019](https://visualstudio.microsoft.com/vs/) zum Ausführen der Beispielapp

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

In diesem Artikel generieren Sie Beispieldaten und senden sie an einen Event Hub. Der erste Schritt besteht im Erstellen eines Event Hubs. Dazu verwenden Sie eine Azure Resource Manager-Vorlage im Azure-Portal.

1. Verwenden Sie für die Event Hub-Erstellung die folgende Schaltfläche, um die Bereitstellung zu starten. Klicken Sie mit der rechten Maustaste, und wählen Sie **In neuem Fenster öffnen**, damit Sie die restlichen Schritte in diesem Artikel ausführen können.

    [![In Azure bereitstellen](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Über die Schaltfläche **In Azure bereitstellen** gelangen Sie zum Azure-Portal, um ein Bereitstellungsformular auszufüllen.

    ![Bereitstellen in Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Wählen Sie das Abonnement aus, in dem Sie den Event Hub erstellen möchten, und erstellen Sie eine Ressourcengruppe mit dem Namen *test-hub-rg*.

    ![Erstellen einer Ressourcengruppe](media/ingest-data-event-hub/create-resource-group.png)

1. Füllen Sie das Formular mit den folgenden Informationen aus.

    ![Bereitstellungsformular](media/ingest-data-event-hub/deployment-form.png)

    Verwenden Sie für alle nicht in der folgenden Tabelle aufgeführten Einstellungen die jeweiligen Standardwerte.

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Abonnement | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Event Hub verwenden möchten.|
    | Ressourcengruppe | *test-hub-rg* | Erstellen Sie eine neue Ressourcengruppe. |
    | Location | *USA, Westen* | Wählen Sie für diesen Artikel die Option *USA, Westen* aus. Wählen Sie für ein Produktionssystem die Region aus, die Ihre Anforderungen am besten erfüllt. Erstellen Sie den Event Hub-Namespace am gleichen Standort wie den Kusto-Cluster, um eine optimale Leistung zu erzielen (besonders wichtig für Event Hub-Namespaces mit hohem Durchsatz).
    | Namespacename | Ein eindeutiger Namespacename | Wählen Sie einen eindeutigen Namen, der Ihren Namespace identifiziert. Beispiel: *mytestnamespace*. Der Domänenname *servicebus.windows.net* wird an den von Ihnen angegebenen Namen angefügt. Der Name darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben beginnen und mit einem Buchstaben oder einer Zahl enden. Der Wert muss zwischen 6 und 50 Zeichen umfassen.
    | Event Hub-Name | *test-hub* | Der Event Hub befindet sich unter dem Namespace, der einen eindeutigen Bereichscontainer bereitstellt. Der Name des Event Hubs muss innerhalb des Namespaces eindeutig sein. |
    | Name der Consumergruppe | *test-group* | Durch Consumergruppen können mehrere verarbeitende Anwendungen jeweils über eine separate Ansicht des Ereignisdatenstroms verfügen. |
    | | |

1. Wählen Sie **Kauf** aus, wodurch bestätigt wird, dass Sie Ressourcen in Ihrem Abonnement erstellen.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um den Bereitstellungsvorgang zu überwachen. Es kann einige Minuten dauern, bis die Bereitstellung erfolgreich abgeschlossen ist, aber Sie können jetzt mit dem nächsten Schritt fortfahren.

    ![Benachrichtigungen](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Erstellen einer Zieltabelle im Azure-Daten-Explorer

Sie können jetzt eine Tabelle im Azure-Daten-Explorer erstellen, an die Event Hubs Daten sendet. Erstellen Sie die Tabelle in dem Cluster und der Datenbank, den/die Sie unter **Voraussetzungen** bereitgestellt haben.

1. Navigieren Sie im Azure-Portal zu Ihrem Cluster, und wählen Sie **Abfrage** aus.

    ![Anwendungslink „Abfrage“](media/ingest-data-event-hub/query-explorer-link.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die Tabelle (TestTable) zu erstellen, die die erfassten Daten erhalten soll.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Abfrage erstellen, „Ausführen“](media/ingest-data-event-hub/run-create-query.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Herstellen einer Verbindung mit dem Event Hub

Als Nächstes stellen Sie über Azure Data Explorer eine Verbindung mit dem Event Hub her. Nach Einrichtung dieser Verbindung werden beim Event Hub eingehende Daten an die Tabelle gestreamt, die Sie weiter oben in diesem Artikel erstellt haben.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um zu überprüfen, ob die Bereitstellung des Event Hubs erfolgreich verlaufen ist.

1. Wählen Sie unter dem von Ihnen erstellten Cluster zuerst **Datenbanken** und dann **TestDatabase** aus.

    ![Testdatenbank auswählen](media/ingest-data-event-hub/select-test-database.png)

1. Wählen Sie **Datenerfassung** und dann **Datenverbindung hinzufügen** aus. Füllen Sie dann das Formular mit den folgenden Informationen aus. Wählen Sie **Erstellen**, wenn Sie fertig sind.

    ![Event Hub-Verbindung](media/ingest-data-event-hub/event-hub-connection.png)

    Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Name der Datenverbindung | *test-hub-connection* | Der Name der Verbindung, die Sie im Azure-Daten-Explorer erstellen möchten.|
    | Event Hub-Namespace | Ein eindeutiger Namespacename | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
    | Event Hub | *test-hub* | Der von Ihnen erstellte Event Hub. |
    | Consumergruppe | *test-group* | Die Consumergruppe, die in dem von Ihnen erstellten Event Hub definiert ist. |
    | | |

    Zieltabelle:

    Es stehen zwei Routingoptionen für erfasste Daten zur Verfügung: *statisch* und *dynamisch*. 
    In diesem Artikel verwenden Sie statisches Routing, für das der Tabellenname, das Datenformat und die Zuordnung angegeben werden müssen. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) daher deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Table | *TestTable* | Die Tabelle, die Sie unter **TestDatabase** erstellt haben. |
    | Datenformat | *JSON* | Folgende Formate werden unterstützt: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV und TXT. |
    | Spaltenzuordnung | *TestMapping* | Die Zuordnung, die Sie in **TestDatabase** erstellt haben, um eingehende JSON-Daten den Spaltennamen und Datentypen von **TestTable** zuzuordnen. Für JSON, MULTILINE JSON oder AVRO erforderlich, für andere Formate optional|
    | | |

    > [!NOTE]
    > Wählen Sie **My data includes routing info** (Meine Daten enthalten Routinginformationen) aus, um dynamisches Routing zu verwenden. Dabei enthalten Ihre Daten die erforderlichen Routinginformationen, wie in den Kommentaren der [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) zu sehen. Werden sowohl statische als auch dynamische Eigenschaften festgelegt, setzen die dynamischen Eigenschaften die statischen außer Kraft. 

## <a name="copy-the-connection-string"></a>Verbindungszeichenfolge kopieren

Wenn Sie die in den Voraussetzungen angegebene [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) ausführen, benötigen Sie die Verbindungszeichenfolge für den Event Hub-Namespace.

1. Wählen Sie unter dem von Ihnen erstellten Event Hub-Namespace zuerst **Freigegebene Zugriffsrichtlinien** und dann **RootManageSharedAccessKey** aus.

    ![Freigegebene Zugriffsrichtlinien](media/ingest-data-event-hub/shared-access-policies.png)

1. Kopieren Sie den Wert für **Verbindungszeichenfolge – Primärschlüssel**. Dieser wird im nächsten Abschnitt eingefügt.

    ![Verbindungszeichenfolge](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Verwenden Sie die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), die Sie heruntergeladen haben, um die Daten zu generieren.

1. Öffnen Sie die Beispiel-App-Projektmappe in Visual Studio.

1. Ändern Sie in der Datei *program.cs* die Konstante `connectionString` in die Verbindungszeichenfolge, die Sie aus dem Event Hub-Namespace kopiert haben.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Erstellen Sie die App, und führen Sie sie aus. Die App sendet Nachrichten an den Event Hub, und alle zehn Sekunden wird der Status ausgegeben.

1. Nachdem die App ein paar Nachrichten gesendet hat, fahren Sie mit dem nächsten Schritt fort: Überprüfen Sie die in den Event Hub und in die Testtabelle fließenden Daten.

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

Anhand der von der App generierten Daten können Sie den Datenfluss vom Event Hub zur Tabelle in Ihrem Cluster nachvollziehen.

1. Im Azure-Portal sehen Sie unter Ihrem Event Hub den Anstieg der Aktivität während der App-Ausführung.

    ![Diagramm des Event Hubs](media/ingest-data-event-hub/event-hub-graph.png)

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```Kusto
    TestTable
    | count
    ```

1. Führen Sie die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```Kusto
    TestTable
    ```

    Das Resultset sollte wie folgt aussehen:

    ![Nachrichten-Resultset](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > Azure Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist. Da die Richtlinie auf fünf Minuten konfiguriert wird, kann es zu Latenz kommen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht vorhaben, Ihren Event Hub erneut zu verwenden, bereinigen Sie **test-hub-rg**, um Kosten zu vermeiden.

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe.  

    Wenn das linke Menü reduziert ist, wählen Sie ![Schaltfläche „Erweitern“](media/ingest-data-event-hub/expand.png) , um es zu erweitern.

   ![Auswählen einer zu löschenden Ressourcengruppe](media/ingest-data-event-hub/delete-resources-select.png)

1. Wählen Sie unter **test-resource-group** die Option **Ressourcengruppe löschen** aus.

1. Geben Sie im neuen Fenster den Namen der zu löschenden Ressourcengruppe (*test-hub-rg*) ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
