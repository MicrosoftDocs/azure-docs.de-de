---
title: Ausführen eines Massenimports von Daten in ein Azure Cosmos DB-SQL-API-Konto mithilfe des .NET SDK
description: Hier erfahren Sie, wie Sie Daten in Azure Cosmos DB importieren oder erfassen, um eine .NET-Konsolenanwendung zu erstellen, mit der der bereitgestellte Durchsatz (RU/s), der zum Importieren von Daten benötigt wird, optimiert wird.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 09/21/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cf0e77657175449b126eeca02a12c164478e568
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548068"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Ausführen eines Massenimports von Daten in ein Azure Cosmos DB-SQL-API-Konto mithilfe des .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieses Tutorial enthält eine Beschreibung der Erstellung einer .NET-Konsolenanwendung, mit der der bereitgestellte Durchsatz (RU/s), der zum Importieren von Daten in Azure Cosmos DB benötigt wird, optimiert wird. In diesem Artikel lesen Sie Daten aus einer Beispieldatenquelle aus und importieren diese in einen Azure Cosmos-Container.
In diesem Tutorial wird [mindestens Version 3.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) des Azure Cosmos DB .NET SDK verwendet, das auf .NET Framework oder .NET Core ausgerichtet werden kann.

In diesem Lernprogramm wird Folgendes behandelt:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos-Kontos
> * Konfigurieren des Projekts
> * Herstellen einer Verbindung mit einem Azure Cosmos-Konto mit aktivierter Unterstützung für Massenvorgänge
> * Durchführen eines Datenimports mit gleichzeitigen Erstellungsvorgängen

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über die folgenden Ressourcen verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [.NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Führen Sie `dotnet --version` aus, um zu überprüfen, welche Version in Ihrer Umgebung verfügbar ist.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Schritt 1: Erstellen eines Azure Cosmos DB-Kontos

[Erstellen Sie ein SQL-API-Konto für Azure Cosmos DB](create-cosmosdb-resources-portal.md) über das Azure-Portal, oder verwenden Sie hierfür den [Azure Cosmos DB-Emulator](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Schritt 2: Einrichten des .NET-Projekts

Öffnen Sie auf Ihrem lokalen Computer die Windows-Eingabeaufforderung oder ein Terminalfenster. Sie führen alle Befehle in den nächsten Abschnitten über die Eingabeaufforderung oder das Terminal aus. Führen Sie den folgenden „dotnet new“-Befehl aus, um eine neue App mit dem Namen *bulk-import-demo* zu erstellen. Mit dem Parameter `--langVersion` wird die *LangVersion*-Eigenschaft in der erstellten Projektdatei festgelegt.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Die erwartete Ausgabe aus dem Build sollte etwa wie folgt aussehen:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Schritt 3: Hinzufügen des Azure Cosmos DB-Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl „dotnet add package“ die Azure Cosmos DB-Clientbibliothek für .NET Core.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Schritt 4: Abrufen der Anmeldeinformationen für Ihr Azure Cosmos-Konto

Die Beispielanwendung muss sich bei Ihrem Azure Cosmos-Konto authentifizieren. Für die Authentifizierung müssen Sie die Anmeldeinformationen für das Azure Cosmos-Konto an die Anwendung übergeben. Führen Sie zum Abrufen der Anmeldeinformationen für Ihr Azure Cosmos-Konto die folgenden Schritte aus:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1.  Navigieren Sie zu Ihrem Azure Cosmos-Konto.
1.  Öffnen Sie den Bereich **Schlüssel**, und kopieren Sie den **URI** und den **PRIMÄRSCHLÜSSEL** Ihres Kontos.

Wenn Sie den Azure Cosmos DB-Emulator nutzen, können Sie die Anmeldeinformationen für den Emulator [in diesem Artikel](local-emulator.md#authenticate-requests) verwenden.

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Schritt 5: Initialisieren des CosmosClient-Objekts mit Unterstützung für Massenvorgänge

Öffnen Sie die generierte Datei `Program.cs` in einem Code-Editor. Sie erstellen eine neue Instanz von CosmosClient mit aktivierter Massenausführung und verwenden sie, um Vorgänge für Azure Cosmos DB durchzuführen. 

Zunächst überschreiben Sie die `Main`-Standardmethode und definieren die globalen Variablen. Diese globalen Variablen enthalten den Endpunkt und die Autorisierungsschlüssel, den Namen der Datenbank, den von Ihnen erstellten Container und die Anzahl von Elementen, die Sie per Massenvorgang einfügen. Ersetzen Sie die Werte für endpointURL und den Autorisierungsschlüssel gemäß Ihrer Umgebung. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Fügen Sie in der `Main`-Methode den folgenden Code hinzu, um das CosmosClient-Objekt zu initialisieren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Nach der Aktivierung von Massenausführungen werden gleichzeitige Vorgänge von CosmosClient intern zu gemeinsamen Dienstaufrufen gruppiert. Auf diese Weise wird die Durchsatznutzung optimiert, indem Dienstaufrufe auf Partitionen verteilt werden, und einzelne Ergebnisse werden dann den ursprünglichen Aufrufern zugewiesen.

Anschließend können Sie einen Container erstellen, in dem alle Elemente gespeichert werden.  Definieren Sie `/pk` als Partitionsschlüssel, 50.000 RU/s als bereitgestellten Durchsatz und eine benutzerdefinierte Indizierungsrichtlinie, mit der alle Felder ausgeschlossen werden, um den Schreibdurchsatz zu optimieren. Fügen Sie nach der CosmosClient-Initialisierungsanweisung den folgenden Code hinzu:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Schritt 6: Auffüllen einer Liste mit gleichzeitigen Aufgaben

Erstellen Sie zum Nutzen der Unterstützung für Massenausführungen eine Liste mit asynchronen Aufgaben, die auf der Quelle der Daten und den durchzuführenden Vorgängen basiert, und verwenden Sie `Task.WhenAll` für die gleichzeitige Ausführung.
Zunächst verwenden wir „falsche“ Daten, um aus unserem Datenmodell eine Liste mit Elementen zu generieren. In einer realen Anwendung stammen die Elemente aus Ihrer jeweiligen Datenquelle.

Fügen Sie zuerst der Lösung das falsche Paket hinzu, indem Sie den Befehl „dotnet add package“ verwenden.

   ```bash
   dotnet add package Bogus
   ```

Legen Sie die Definition der Elemente fest, die Sie speichern möchten. Sie müssen die `Item`-Klasse in der Datei `Program.cs` definieren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Erstellen Sie als Nächstes in der `Program`-Klasse eine Hilfsfunktion. Mit dieser Hilfsfunktion wird die von Ihnen definierte Anzahl von Elementen abgerufen, um zufällige Daten einzufügen und zu generieren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Lesen Sie die Elemente ein, und serialisieren Sie sie zu Datenstrominstanzen, indem Sie die `System.Text.Json`-Klasse verwenden. Aufgrund der Art und Weise der automatisch generierten Daten serialisieren Sie diese als Datenströme. Sie können die Elementinstanz auch direkt verwenden, aber durch die Konvertierung in Datenströme können Sie die Leistung der Datenstrom-APIs in CosmosClient nutzen. Normalerweise ist die direkte Nutzung der Daten möglich, sofern Sie im Besitz des Partitionsschlüssels sind. 


Fügen Sie in der `Main`-Methode direkt nach der Containererstellung den folgenden Code hinzu, um die Daten in Datenstrominstanzen zu konvertieren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Verwenden Sie als Nächstes die Datenströme, um gleichzeitige Aufgaben zu erstellen und die Aufgabenliste aufzufüllen, damit die Elemente in den Container eingefügt werden. Fügen Sie der `Program`-Klasse den folgenden Code hinzu, um diesen Vorgang durchzuführen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

All diese gleichzeitigen Vorgänge werden zusammen (als Massenvorgang) ausgeführt, wie dies im Einführungsabschnitt beschrieben ist.

## <a name="step-7-run-the-sample"></a>Schritt 7: Ausführen des Beispiels

Zum Ausführen des Beispiels können Sie einfach den Befehl `dotnet` verwenden:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Abrufen des vollständigen Beispiels

Falls Sie die Schritte in diesem Tutorial aus Zeitmangel nicht durchführen konnten oder nur die Codebeispiele herunterladen möchten, können Sie sie über [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer) beziehen.

Stellen Sie nach dem Klonen des Projekts sicher, dass Sie in der Datei [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25) die gewünschten Anmeldeinformationen aktualisieren.

Sie können das Beispiel ausführen, indem Sie in das Repositoryverzeichnis wechseln und `dotnet` verwenden:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Schritte ausgeführt:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos-Kontos
> * Konfigurieren des Projekts
> * Herstellen einer Verbindung mit einem Azure Cosmos-Konto mit aktivierter Unterstützung für Massenvorgänge
> * Durchführen eines Datenimports mit gleichzeitigen Erstellungsvorgängen

Sie können jetzt mit dem nächsten Tutorial fortfahren:

> [!div class="nextstepaction"]
>[Abfragen von Azure Cosmos DB mithilfe der SQL-API](tutorial-query-sql-api.md)