---
title: Einrichten einer CI/CD-Pipeline mit dem Buildtask des Azure Cosmos DB-Emulators
description: Tutorial zum Einrichten des Build- und Releaseworkflows in Azure DevOps mithilfe des Buildtasks des Cosmos DB-Emulators
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: a5b8842718aa2d9f90ac06283abc5fe2fdd925cb
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95997000"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Einrichten einer CI/CD-Pipeline mit dem Buildtask des Azure Cosmos DB-Emulators in Azure DevOps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die zu Entwicklungszwecken den Azure Cosmos DB-Dienst emuliert. Mithilfe des Emulators können Sie Ihre Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen oder sonstige Kosten zu verursachen. 

Der Buildtask des Azure Cosmos DB-Emulators für Azure DevOps ermöglicht Ihnen die gleichen Schritte in einer CI-Umgebung. Mit dem Buildtask können Sie im Rahmen der Build- und Releaseworkflows Tests für den Emulator ausführen. Der Task startet einen Docker-Container, in dem der Emulator bereits ausgeführt wird, und stellt einen Endpunkt bereit, der von der übrigen Builddefinition verwendet werden kann. Sie können beliebig viele Emulatorinstanzen erstellen, die jeweils in einem separaten Container ausgeführt werden. 

In diesem Artikel wird gezeigt, wie Sie für eine ASP.NET-Anwendung eine CI-Pipeline in Azure DevOps einrichten, die den Buildtask des Cosmos DB-Emulators zum Ausführen von Tests nutzt. Sie können einen ähnlichen Ansatz zum Einrichten einer CI-Pipeline für eine Node.js- oder Python-Anwendung verwenden. 

## <a name="install-the-emulator-build-task"></a>Installieren des Emulator-Buildtasks

Damit der Buildtask verwendet werden kann, muss er zunächst in der Azure DevOps-Organisation installiert werden. Suchen Sie im [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) nach **Azure Cosmos DB-Emulator**, und klicken Sie auf **Kostenlos erhalten**.

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Suchen und Installieren des Buildtasks des Azure Cosmos DB-Emulators im Azure DevOps-Marketplace":::

Wählen Sie anschließend die Organisation aus, in der die Erweiterung installiert werden soll. 

> [!NOTE]
> Sie müssen Kontobesitzer oder Projektsammlungsadministrator sein, um eine Erweiterung in einer Azure DevOps-Organisation installieren zu können. Falls Sie keine Berechtigungen haben, aber Kontomitglied sind, können Sie stattdessen Erweiterungen anfordern. [Weitere Informationen.](/azure/devops/marketplace/faq-extensions?preserve-view=true&view=vsts)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Auswählen einer Azure DevOps-Organisation für die Installation einer Erweiterung":::

## <a name="create-a-build-definition"></a>Erstellen einer Builddefinition

Nachdem die Erweiterung installiert ist, melden Sie sich bei Ihrer Azure DevOps-Organisation an, und suchen Sie Ihr Projekt im Projektdashboard. Sie können Ihrem Projekt eine [Buildpipeline](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav&view=vsts) hinzufügen oder eine bereits vorhandenen Buildpipeline ändern. Wenn Sie bereits über eine Buildpipeline verfügen, können Sie mit [Hinzufügen des Emulator-Buildtasks zur Builddefinition](#addEmulatorBuildTaskToBuildDefinition) fortfahren.

1. Navigieren Sie zum Erstellen einer neuen Builddefinition in Azure DevOps zur Registerkarte **Builds**. Klicken Sie auf **+Neu.** \> **Neue Buildpipeline**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Erstellen einer neuen Buildpipeline":::

2. Wählen Sie die gewünschte **Quelle**, das **Teamprojekt**, das **Repository** und den **Standardbranch für manuelle und geplante Builds** aus. Wählen Sie **Weiter** aus, nachdem Sie alle erforderlichen Optionen ausgewählt haben.

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Auswählen von Teamprojekt, Repository und Branch für die Buildpipeline":::

3. Wählen Sie abschließend die gewünschte Vorlage für die Buildpipeline aus. In diesem Tutorial wird die Vorlage **ASP.NET** ausgewählt. Sie verfügen nun über eine Buildpipeline, die Sie für die Verwendung des Azure Cosmos DB-Emulator-Buildtasks einrichten können. 

> [!NOTE]
> Im Agent-Pool, der für diese CI ausgewählt werden soll, muss Docker für Windows installiert sein, es sei denn, die Installation erfolgt manuell in einer vorherigen Aufgabe als Teil der CI. Im Artikel [Von Microsoft gehostete Agents](/azure/devops/pipelines/agents/hosted?preserve-view=true&tabs=yaml&view=azure-devops) finden Sie eine Auswahl von Agent-Pools. Wir empfehlen, mit `Hosted VS2017` zu beginnen.

Vom Azure Cosmos DB-Emulator wird derzeit kein gehosteter VS2019-Agent-Pool unterstützt. VS2019 ist für den Emulator jedoch bereits installiert und kann verwendet werden, indem Sie den Emulator mit den folgenden PowerShell-Cmdlets starten. Sollten bei der Verwendung von VS2019 Probleme auftreten, wenden Sie sich an das [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html)-Team:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Hinzufügen des Tasks zu einer Buildpipeline

1. Bevor Sie der Buildpipeline einen Task hinzufügen, müssen Sie einen Agent-Auftrag hinzufügen. Navigieren Sie zu Ihrer Buildpipeline. Wählen Sie **...** und anschließend **Agentauftrag hinzufügen** aus.

1. Wählen Sie als Nächstes das Symbol **+** neben dem Agent-Auftrag aus, um den Emulator-Buildtask hinzuzufügen. Suchen Sie über das Suchfeld nach **cosmos**, wählen Sie den **Azure Cosmos DB-Emulator** aus, und fügen Sie ihn dem Agent-Auftrag hinzu. Der Buildtask startet einen Container, in dem bereits eine Instanz des Cosmos DB-Emulators ausgeführt wird. Der Azure Cosmos DB-Emulator-Task muss vor allen anderen Tasks platziert werden, die darauf angewiesen sind, dass der Emulator bereits aktiv ist.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Hinzufügen des Emulator-Buildtasks zur Builddefinition":::

In diesem Tutorial fügen Sie den Task am Anfang hinzu, um sicherzustellen, dass der Emulator vor der Testausführung verfügbar ist.

### <a name="add-the-task-using-yaml"></a>Hinzufügen des Tasks mithilfe von YAML

Dieser Schritt ist optional und nur erforderlich, wenn Sie die CI/CD-Pipeline mit einem YAML-Task einrichten. In solchen Fällen können Sie den YAML-Task definieren, wie im folgenden Code gezeigt:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurieren von Tests zur Verwendung des Emulators

Sie konfigurieren die Tests nun zur Verwendung des Emulators. Der Emulator-Buildtask exportiert die Umgebungsvariable „CosmosDbEmulator.Endpoint“. Diese kann von nachfolgenden Tasks in der Buildpipeline verwendet werden, um Anforderungen auszugeben. 

In diesem Tutorial führen Sie mithilfe des [Visual Studio-Testtasks](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) Komponententests aus, die über eine Datei vom Typ **.runsettings** konfiguriert wurden. Weitere Informationen zur Komponententesteinrichtung finden Sie in der [Dokumentation](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017). Das in diesem Dokument verwendete vollständige Codebeispiel für die To-Do-Anwendung ist auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) verfügbar.

Nachfolgend sehen Sie ein Beispiel einer Datei vom Typ **.runsettings**, die Parameter definiert, die an die Komponententests einer Anwendung übergeben werden sollen. Beachten Sie, dass die Variable `authKey` der [bekannte Schlüssel](./local-emulator.md#authenticate-requests) für den Emulator ist. Dieses `authKey`-Element ist der vom Emulator-Buildtask erwartete Schlüssel. Er muss in der Datei vom Typ **.runsettings** definiert sein.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Wenn Sie eine CI/CD-Pipeline für eine Anwendung einrichten, die die API für MongoDB von Azure Cosmos DB nutzt, enthält die Verbindungszeichenfolge standardmäßig die Portnummer 10255. Dieser Port ist derzeit jedoch nicht geöffnet. Verwenden Sie als Alternative Port 10250, um die Verbindung herzustellen. Die Verbindungszeichenfolge der API für MongoDB von Azure Cosmos DB bleibt gleich. Die unterstützte Portnummer ist jedoch 10250 anstelle von 10255.

Auf diese `TestRunParameters`-Parameter wird im Testprojekt der Anwendung über eine `TestContext`-Eigenschaft verwiesen. Hier sehen Sie ein Beispiel für einen Test, der für Cosmos DB ausgeführt wird:

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Navigieren Sie zu den Ausführungsoptionen im Visual Studio-Testtask. Geben Sie unter **Einstellungsdatei** an, dass die Tests mit der Datei **.runsettings** konfiguriert sind. Fügen Sie unter **Testlaufparameter überschreiben** die Zeichenfolge `-endpoint $(CosmosDbEmulator.Endpoint)` ein. Dadurch wird der Testtask so konfiguriert, dass er auf den Endpunkt des Emulator-Buildtasks und nicht auf den in der Datei **.runsettings** definierten Endpunkt verweist.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Überschreiben der Endpunktvariablen mit dem Endpunkt des Emulator-Buildtasks":::

## <a name="run-the-build"></a>Führen Sie den Buildvorgang durch.

Verwenden Sie nun die Option **Speichern und in Warteschlange einreihen**, um den Build zu speichern und in die Warteschlange einzureihen. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Screenshot eines Builds mit ausgewählter Option „Speichern und in Warteschlange einreihen“":::

Nach dem Start des Builds sehen Sie, dass der Cosmos DB-Emulatortask damit begonnen hat, das Docker-Image mit dem installierten Emulator abzurufen. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Screenshot des Abrufvorgangs des Cosmos DB-Emulatortasks":::

Nach Abschluss des Builds sehen Sie, dass Ihre Tests, die für den Cosmos DB-Emulator aus dem Buildtask ausgeführt wurden, erfolgreich waren.

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Screenshot des Fortschrittswerts auf der Registerkarte „Zusammenfassung“":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwenden des Emulators für lokale Entwicklungs- und Testvorgänge finden Sie unter [Verwenden des Azure Cosmos DB-Emulators für lokale Entwicklungs- und Testvorgänge](./local-emulator.md).

Informationen zum Exportieren von TLS-/SSL-Emulatorzertifikaten finden Sie unter [Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java, Python und Node.js](./local-emulator-export-ssl-certificates.md).
