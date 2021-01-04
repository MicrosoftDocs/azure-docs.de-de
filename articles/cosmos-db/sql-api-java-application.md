---
title: 'Tutorial: Erstellen einer Java-Web-App mit Azure Cosmos DB und der SQL-API'
description: 'Tutorial: In diesem Tutorial zu Java-Webanwendungen erfahren Sie, wie Sie Azure Cosmos DB und die SQL-API verwenden, um Daten einer auf Azure-Websites gehosteten Java-Anwendung zu speichern und abzurufen.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: b3cb6bf56820da84d17f0b981f461a545bbe5ab6
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549258"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Tutorial: Erstellen einer Java-Webanwendung mithilfe von Azure Cosmos DB und der SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

In diesem Tutorial zur Java-Webanwendung erfahren Sie, wie Sie den [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)-Dienst verwenden, um Daten einer in Azure App Service-Web-Apps gehosteten Java-Anwendung zu speichern und abzurufen. In diesem Artikel lernen Sie Folgendes:

* Erstellen einer einfachen JSP-Anwendung (JavaServer Pages) in Eclipse
* Verwenden des Azure Cosmos DB-Diensts mit dem [Azure Cosmos DB-Java-SDK](https://github.com/Azure/azure-documentdb-java)

In diesem Java-Anwendungslernprogramm erfahren Sie, wie Sie eine webbasierte Aufgabenverwaltungsanwendung erstellen, mit der Sie Aufgaben erstellen, abrufen und als abgeschlossen kennzeichnen können, wie in der folgenden Abbildung gezeigt. Die einzelnen Aufgaben in der Aufgabenliste sind als JSON-Dokumente in Azure Cosmos DB gespeichert.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Java-Anwendung „My ToDo List“":::

> [!TIP]
> In diesem Lernprogramm zur Anwendungsentwicklung wird davon ausgegangen, dass Sie bereits Erfahrung mit Java haben. Wenn Sie noch nicht mit Java oder den [erforderlichen Tools](#Prerequisites) vertraut sind, wird empfohlen, das vollständige [todo](https://github.com/Azure-Samples/documentdb-java-todo-app)-Projekt von GitHub herunterzuladen und [gemäß den Anweisungen am Ende dieses Artikels zu erstellen](#GetProject). Nachdem Sie das Projekt erstellt haben, können Sie den Artikel lesen, um Einblick in den Code im Kontext des Projekts zu erhalten.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Voraussetzungen für dieses Java-Webanwendungstutorial

Bevor Sie mit diesem Lernprogramm zur Anwendungsentwicklung beginnen, benötigen Sie Folgendes:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true).
* [Eclipse IDE für Java EE-Entwickler.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Eine Azure-Website mit aktivierter Java-Laufzeitumgebung (z.B. Tomcat oder Jetty).](../app-service/quickstart-java.md)

Falls Sie diese Tools zum ersten Mal installieren, finden Sie auf „coreservlets.com“ eine schrittweise Anleitung für den Installationsvorgang. Diese befindet sich im Abschnitt „Schnellstart“ im [Lernprogramm: TomCat7 installieren und mit Eclipse verwenden](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Erstellen eines Azure Cosmos DB-Kontos

Wir beginnen, indem wir ein Azure Cosmos DB-Konto erstellen. Falls Sie bereits ein Konto besitzen oder den Azure Cosmos DB-Emulator für dieses Tutorial verwenden, können Sie mit [Schritt 2: Erstellen der Java JSP-Anwendung](#CreateJSP) fortfahren.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Erstellen der Java JSP-Anwendung

So erstellen Sie eine JSP-Anwendung

1. Zunächst beginnen wir mit der Erstellung eines Java-Projekts. Starten Sie Eclipse, klicken Sie auf **Datei**, dann auf **Neu** und schließlich auf **Dynamisches Webprojekt**. Wenn **Dynamic Web Project** nicht als verfügbares Projekt aufgeführt ist, gehen Sie wie folgt vor: Klicken Sie auf **Datei**, dann auf **Neu** und anschließend auf **Projekt**. Erweitern Sie die Option **Web**, klicken Sie auf **Dynamic Web Project**, und klicken Sie anschließend auf **Weiter**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP-Java-Anwendungsentwicklung":::

1. Geben Sie einen **Projektnamen** in das Feld Projektname ein, wählen Sie optional im Dropdownmenü **Ziellaufzeit** einen Wert aus (z. B. Apache Tomcat v7.0), und klicken Sie dann auf **Fertig stellen**. Durch das Auswählen einer Ziellaufzeit können Sie das Projekt lokal über Eclipse ausführen.

1. Erweitern Sie in der Projektexplorer-Ansicht von Eclipse Ihr Projekt. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu** sowie auf **JSP-Datei**.

1. Geben Sie der Datei im Dialogfeld **Neue JSP-Datei** den Namen **index.jsp**. Behalten Sie für den übergeordneten Ordner **WebContent** bei, wie in der folgenden Abbildung gezeigt, und klicken dann auf **Weiter**.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Erstellen einer neuen JSP-Datei – Java-Webanwendungstutorial":::

1. Wählen Sie im Dialogfeld **JSP-Vorlage auswählen** im Rahmen dieses Lernprogramms **Neue JSP-Datei (HTML)** , und klicken Sie dann auf **Fertig stellen**.

1. Wenn in Eclipse die Datei *index.jsp* geöffnet wird, fügen Sie den Text **Hello World** hinzu, damit er dynamisch im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte Inhalt von `<body>` sollte wie im folgenden Code aussehen:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Speichern Sie die Datei *index.jsp*.

1. Wenn Sie in Schritt 2 eine Ziellaufzeitversion festgelegt haben, können Sie auf **Projekt** und dann auf **Ausführen** klicken, um die JSP-Anwendung lokal auszuführen:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World (Java-Anwendungstutorial)":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Installieren des SQL Java SDK

Das SQL Java SDK und seine Abhängigkeiten können am einfachsten über [Apache Maven](https://maven.apache.org/)einbezogen werden. Dazu müssen Sie mithilfe der folgenden Schritten das Projekt in ein Maven-Projekt konvertieren:

1. Klicken Sie mit der rechten Maustaste im Projektexplorer auf Ihr Projekt, klicken Sie dann auf **Konfigurieren** und anschließend auf die Option zur **Umwandlung in ein Maven-Projekt**.

1. Übernehmen Sie im Fenster **Neue POM erstellen** die Standardeinstellungen, und klicken Sie auf **Fertig stellen**.

1. Öffnen Sie die Datei „pom.xml“ im **Projektexplorer**.

1. Klicken Sie auf der Registerkarte **Abhängigkeiten** im Bereich **Abhängigkeiten** auf **Hinzufügen**.

1. Gehen Sie im Fenster **Abhängigkeit auswählen** wie folgt vor:
   
   * Geben Sie `com.azure` im Feld **Gruppen-ID** ein.
   * Geben Sie `azure-cosmos` im Feld **Artefakt-ID** ein.
   * Geben Sie `4.0.1-beta.1` im Feld **Version** ein.
  
   Alternativ können Sie den Abhängigkeits-XML-Code für die Gruppen-ID und die Artefakt-ID direkt in der Datei *pom.xml* hinzufügen:

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. Klicken Sie auf **OK**, damit Maven das SQL Java SDK installiert, oder speichern Sie die Datei „pom.xml“.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Verwenden des Azure Cosmos DB-Diensts in Ihrer Java-Anwendung

Fügen Sie Ihrer Webanwendung als Nächstes die Modelle, Ansichten und Controller hinzu.

### <a name="add-a-model"></a>Hinzufügen eines Modells

Zunächst definieren Sie ein Modell in einer neuen Datei namens *TodoItem.java*. Die `TodoItem`-Klasse definiert das Schema eines Elements zusammen mit der getter- und setter-Methode:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Hinzufügen der DAO-Klassen (Data Access Object)

Erstellen Sie ein Datenzugriffsobjekt (Data Access Object, DAO), um das Speichern der Aufgabenelemente in Azure Cosmos DB zu abstrahieren. Um die Aufgabenelemente in einer Sammlung zu speichern, muss der Client wissen, in welcher Datenbank und Sammlung die Speicherung erfolgen soll (auf die durch eigene Links verwiesen wird). Im Allgemeinen empfiehlt es sich, die Datenbank und Sammlung nach Möglichkeit zwischenzuspeichern, um zusätzliche Roundtrips zur Datenbank zu vermeiden.

1. Um den Azure Cosmos DB-Dienst aufzurufen, müssen Sie ein neues `cosmosClient`-Objekt instanziieren. Im Allgemeinen empfiehlt es sich, das `cosmosClient`-Objekt wiederzuverwenden, anstatt für jede nachfolgende Anforderung einen neuen Client zu erstellen. Sie können den Client wiederverwenden, indem Sie ihn in der `cosmosClientFactory`-Klasse definieren. Aktualisieren Sie die Werte für HOST und MASTER_KEY, die Sie in [Schritt 1](#CreateDB) gespeichert haben. Ersetzen Sie die Variable HOST durch Ihren URI und MASTER_KEY durch ihren Primärschlüssel (PRIMARY KEY). Verwenden Sie den folgenden Code, um die `CosmosClientFactory`-Klasse in der Datei *CosmosClientFactory.java* zu erstellen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. Erstellen Sie eine neue Datei namens *TodoDao.java*, und fügen Sie die `TodoDao`-Klasse hinzu, um die Aufgabenelemente zu erstellen, zu aktualisieren, zu lesen und zu löschen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. Erstellen Sie eine neue Datei namens *MockDao.java*, und fügen Sie die `MockDao`-Klasse hinzu. Diese Klasse implementiert die `TodoDao`-Klasse, um CRUD-Vorgänge für die Elemente auszuführen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. Erstellen Sie eine neue Datei namens *DocDbDao.java*, und fügen Sie die `DocDbDao`-Klasse hinzu. Diese Klasse definiert den Code zum Speichern der Aufgabenelemente im Container und zum Abrufen von Datenbank und Sammlung (sofern vorhanden). Falls diese nicht vorhanden ist, wird sie neu erstellt. In diesem Beispiel wird [Gson](https://code.google.com/p/google-gson/) verwendet, um die TodoItem-POJOs (Plain Old Java Objects) in JSON-Dokumenten zu serialisieren bzw. zu deserialisieren. Um die Aufgabenelemente in einer Sammlung zu speichern, muss der Client wissen, in welcher Datenbank und Sammlung die Speicherung erfolgen soll (auf die durch eigene Links verwiesen wird). Diese Klasse definiert auch die Hilfsfunktion zum Abrufen der Dokumente durch ein anderes Attribut (z. B. „ID“) und nicht mit einem seiteninternen Link. Mit der Hilfsmethode können Sie ein TodoItem-JSON-Dokument nach „ID“ abrufen und dann zu einem POJO deserialisieren.

   Sie können auch das `cosmosClient`-Clientobjekt verwenden, um eine Sammlung oder Liste mit Aufgabenelementen mit einer SQL-Abfrage abzurufen. Abschließend definieren Sie die delete-Methode, um ein Aufgabenelement aus der Liste zu löschen. Mit dem folgenden Code zeigen Sie den Inhalt der `DocDbDao`-Klasse an:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. Erstellen Sie als Nächstes eine neue Datei namens *ToDoDaoFactory.java*, und fügen Sie die `TodoDaoFactory`-Klasse hinzu, mit der ein neues DocDbDao-Objekt erstellt wird:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Hinzufügen eines Controllers

Fügen Sie den Controller *ToDoItemController* zu Ihrer Anwendung hinzu. In diesem Projekt verwenden Sie [Project Lombok](https://projectlombok.org/), um den Konstruktor, die Getter, die Setter und einen Generator zu generieren. Alternativ können Sie diesen Code manuell eingeben oder von der IDE generieren lassen:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Erstellen eines Servlets

Als Nächstes erstellen Sie ein Servlet zum Weiterleiten von HTTP-Anforderungen an den Controller. Erstellen Sie die Datei *ApiServlet.java*, und definieren Sie darin den folgenden Code:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Verbinden der übrigen Java-App

Nachdem nun der angenehme Teil erledigt ist, müssen Sie nur noch eine bequeme Benutzeroberfläche erstellen und mit Ihrem DAO verbinden.

1. Sie benötigen eine Webbenutzeroberfläche, die dem Benutzer angezeigt wird. Dazu wird die zuvor erstellte Datei *index.jsp* mit dem folgenden Code neu geschrieben:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Schreiben Sie abschließend ein clientseitiges JavaScript, um die Webbenutzeroberfläche mit dem Servlet zu verbinden:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Jetzt muss die Anwendung nur noch getestet werden. Führen Sie die Anwendung lokal aus, und fügen Sie einige Aufgabenelemente hinzu, indem Sie den Elementnamen und die Kategorie eingeben und auf **Add Task** klicken. Wenn das Element angezeigt wird, können Sie den Abschlussstatus aktualisieren, indem Sie das Kontrollkästchen aktivieren und auf **Aufgaben aktualisieren** klicken.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Bereitstellen Ihrer Java-Anwendung für Azure Web Sites

Azure-Websites gestaltet die Bereitstellung von Java-Anwendungen so einfach wie das Exportieren Ihrer Anwendung als WAR-Datei und das anschließende Hochladen über die Quellcodeverwaltung (z.B. Git) oder über FTP.

1. Zum Exportieren Ihrer Anwendung als WAR-Datei klicken Sie im **Projektexplorer** mit der rechten Maustaste auf das Projekt, klicken Sie anschließend auf **Exportieren** und dann auf **WAR-Datei**.

1. Gehen Sie im Fenster **WAR-Export** wie folgt vor:
   
   * Geben Sie in das Webprojektfeld azure-documentdb-java-sample ein.
   * Wählen Sie im Feld „Ziel“ ein Ziel aus, an dem die WAR-Datei gespeichert wird.
   * Klicken Sie auf **Fertig stellen**.

1. Nachdem Sie jetzt über eine WAR-Datei verfügen, können Sie diese ganz einfach in das Verzeichnis **webapps** Ihrer Azure-Website hochladen. Anweisungen zum Hochladen der Datei finden Sie unter [Hinzufügen einer Java-Anwendung zu Azure App Service-Web-Apps](../app-service/quickstart-java.md). Nachdem die WAR-Datei in das Verzeichnis „webapps“ hochgeladen wurde, erkennt die Laufzeitumgebung die hinzugefügte Datei und lädt sie automatisch.

1. Navigieren Sie zum Anzeigen Ihres fertigen Produkts zu `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/`, und fügen Sie Ihre Aufgaben hinzu.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Abrufen des Projekts von GitHub

Alle Beispiele in diesem Tutorial befinden sich im Projekt [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) auf GitHub. Zum Importieren des Todo-Projekts in Eclipse müssen Sie sicherstellen, dass Sie über die im Abschnitt [Voraussetzungen](#Prerequisites) aufgeführten Softwareanwendungen und Ressourcen verfügen. Gehen Sie anschließend folgendermaßen vor:

1. Installieren Sie das [Projekt Lombok](https://projectlombok.org/). Lombok wird verwendet, um Konstruktoren, Getter und Setter im Projekt zu generieren. Nachdem Sie die Datei „lombok.jar“ heruntergeladen haben, doppelklicken Sie auf die Datei, um sie zu installieren, installieren Sie sie über die Befehlszeile.

1. Wenn Eclipse geöffnet ist, schließen Sie es, und starten Sie es neu, um Lombok zu laden.

1. Klicken in Eclipse im Menü **Datei** auf **Importieren**.

1. Klicken Sie im Fenster **Importieren** auf **Git**, dann auf **Projekte aus Git** und schließlich auf **Weiter**.

1. Klicken Sie auf dem Bildschirm **Repositoryquelle auswählen** auf **URI klonen**.

1. Geben Sie im Bildschirm **Source Git Repository (Quell-Git-Repository)** im Feld **URI** „ https://github.com/Azure-Samples/documentdb-java-todo-app.git “ ein, und klicken Sie auf **Next (Weiter)** .

1. Stellen Sie auf dem Bildschirm **Verzweigungsauswahl** sicher, dass **main** ausgewählt ist, und klicken Sie dann auf **Weiter**.

1. Klicken Sie auf dem Bildschirm **Lokales Ziel** auf **Durchsuchen**, um einen Ordner auszuwählen, in den das Repository kopiert werden kann, und klicken Sie dann auf **Weiter**.

1. Stellen Sie auf dem Bildschirm **Assistent zum Importieren von Projekten auswählen** sicher, dass **Vorhandene Projekte importieren** ausgewählt ist, und klicken Sie dann auf **Weiter**.

1. Heben Sie auf dem Bildschirm **Projekte importieren** die Auswahl des Projekts **DocumentDB** auf, und klicken Sie dann auf **Fertig stellen**. Das DocumentDB-Projekt enthält das Azure Cosmos DB-Java-SDK, das wir stattdessen als Abhängigkeit hinzufügen.

1. Navigieren Sie im **Projektexplorer** zu „azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java“, und ersetzen Sie die Werte „HOST“ und „MASTER_KEY“ durch den URI und den PRIMÄRSCHLÜSSEL für Ihr Azure Cosmos DB-Konto. Speichern Sie dann die Datei. Weitere Informationen finden Sie unter [Schritt 1. Erstellen eines Azure Cosmos-Datenbankkontos](#CreateDB).

1. Klicken Sie im **Projektexplorer** mit der rechten Maustaste auf **azure-documentdb-java-sample**, klicken Sie auf **Buildpfad**, und klicken Sie dann auf **Buildpfad konfigurieren**.

1. Wählen Sie auf dem Bildschirm **Java-Buildpfad** im rechten Bereich die Registerkarte **Bibliotheken** aus, und klicken Sie dann auf **Externe JARs hinzufügen**. Navigieren Sie zum Speicherort der Datei „lombok.jar“, und klicken Sie auf **Öffnen** und dann auf **OK**.

1. Verwenden Sie Schritt 12, um das Fenster **Eigenschaften** erneut zu öffnen, und klicken Sie dann im linken Bereich auf **Vorgesehene Laufzeiten**.

1. Klicken Sie auf dem Bildschirm **Vorgesehene Laufzeiten** auf **Neu**, wählen Sie **Apache Tomcat v7.0** aus, und klicken Sie dann auf **OK**.

1. Verwenden Sie Schritt 12, um das Fenster **Eigenschaften** erneut zu öffnen, und klicken Sie dann im linken Bereich auf **Projektfacets**.

1. Wählen Sie auf dem Bildschirm **Projektfacets** die Optionen **Dynamisches Webmodul** und **Java** aus, und klicken Sie dann auf **OK**.

1. Klicken Sie auf der Registerkarte **Server** am unteren Bildschirmrand mit der rechten Maustaste auf **Tomcat v7.0 Server auf Localhost**, und klicken Sie dann auf **Hinzufügen und Entfernen**.

1. Verschieben Sie im Fenster **Hinzufügen und Entfernen** **azure-documentdb-java-sample** in das Feld **Konfiguriert**, und klicken Sie dann auf **Fertig stellen**.

1. Klicken Sie auf der Registerkarte **Server** mit der rechten Maustaste auf den **Server mit Tomcat v7.0 auf Localhost**, und klicken Sie dann auf **Neu starten**.

1. Wechseln Sie in einem Browser zu `http://localhost:8080/azure-documentdb-java-sample/`, und beginnen Sie mit den Hinzufügungen zu Ihrer Aufgabenliste. Beachten Sie, dass Sie, wenn Sie die Standardwerte für den Port geändert haben, 8080 auf den Wert von Ihnen ausgewählten Wert ändern müssen.

1. Informationen zum Bereitstellen Ihres Projekts auf einer Azure-Website finden Sie unter [Schritt 6: Bereitstellen der Anwendung auf Azure-Websites](#Deploy).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Node.js-Anwendung mit Azure Cosmos DB](sql-api-nodejs-application.md)