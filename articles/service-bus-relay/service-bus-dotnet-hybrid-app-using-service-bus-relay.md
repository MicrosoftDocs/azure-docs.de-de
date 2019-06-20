---
title: Lokale oder cloudbasierte Hybridanwendung (.NET) mit Azure WCF Relay | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen lokalen Windows Communication Foundation-Anwendungsdienst (WCF-Dienst) für eine Webanwendung in der Cloud mit Azure Relay verfügbar machen.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 145960db27247a8535eb96640000b86d810619c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419908"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Machen Sie einen lokalen Windows Communication Foundation-Anwendungsdienst (WCF-Dienst) für eine Webanwendung in der Cloud mit Azure Relay verfügbar. 
In diesem Artikel wird gezeigt, wie Sie mit Microsoft Azure und Visual Studio eine Hybridcloudanwendung erstellen. Sie erstellen eine Anwendung, die mehrere in der Cloud ausgeführte Azure-Ressourcen nutzt.

* Erstellen oder Anpassen eines vorhandenen Webdiensts zur Verwendung durch eine Weblösung
* Verwenden des Azure WCF Relay-Diensts zum Freigeben von Daten zwischen einer Azure-Anwendung und einem an einem anderen Ort gehosteten Webdienst

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Überprüfen des Szenarios
> * Erstellen eines Namespaces
> * Erstellen eines lokalen Servers
> * Erstellen einer ASP.NET-Anwendung
> * Führen Sie die App lokal aus.
> * Bereitstellen der Web-App in Azure
> * Ausführen der App in Azure

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
- [Visual Studio 2015 oder eine höhere Version.](https://www.visualstudio.com) Für die Beispiele in diesem Tutorial wird Visual Studio 2017 verwendet.
- Azure SDK für .NET. Installieren Sie das SDK über die [SDK-Downloadseite](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>So unterstützt Sie Azure Relay bei hybriden Lösungen
Geschäftslösungen bestehen normalerweise aus einer Kombination von benutzerdefiniertem Code, der geschrieben wurde, um neue und individuelle Geschäftsanforderungen in Angriff zu nehmen und vorhandene Funktionen, die von bereits verwendeten Lösungen und Systemen bereitgestellt werden, zu integrieren.

Lösungsarchitekten setzen die Cloud inzwischen ein, um Skalierungsanforderungen leichter bewältigen zu können und die Betriebskosten zu senken. Dabei stellen sie fest, dass sich vorhandene Dienstressourcen, die sie als Bausteine für ihre Lösungen nutzen möchten, innerhalb der Unternehmensfirewall befinden und somit nicht problemlos von der Cloudlösung darauf zugegriffen werden kann. Viele interne Dienste werden nicht so erstellt oder gehostet, dass sie einfach am Rand des Unternehmensnetzwerks verfügbar gemacht werden können.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) wurde konzipiert, damit vorhandene WCF-Webdienste (Windows Communication Foundation) sicher von Lösungen aufgerufen werden können, die sich außerhalb des Unternehmensumkreises befinden, ohne dass aufwändige Änderungen an der Netzwerkinfrastruktur des Unternehmens vorgenommen werden müssen. Solche Relay-Dienste werden weiterhin in der vorhandenen Umgebung gehostet, das Lauschen an eingehenden Sitzungen und Anforderungen wird jedoch an den in der Cloud gehosteten Relay-Dienst delegiert. Azure Relay schützt diese Dienste per SAS-Authentifizierung ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)) außerdem vor unbefugtem Zugriff.

## <a name="review-the-scenario"></a>Überprüfen des Szenarios
In diesem Tutorial erstellen Sie eine ASP.NET-Website, mit der Sie eine Produktliste auf der Produktbestandsseite anzeigen können.

![Szenario][0]

In diesem Tutorial wird davon ausgegangen, dass Sie über Produktinformationen in einem vorhandenen lokalen System verfügen, und mithilfe von Azure Relay darauf zugegriffen. Dies wird durch einen Webdienst simuliert, der in einer einfachen Konsolenanwendung ausgeführt und durch einen im Arbeitsspeicher befindlichen Produktsatz unterstützt wird. Sie können diese Konsolenanwendung auf Ihrem eigenen Computer ausführen und die Webrolle in Azure bereitstellen. Dabei werden Sie feststellen, dass die im Azure-Datencenter ausgeführte Webrolle tatsächlich Aufrufe an Ihren Computer sendet, obwohl dieser sich mit ziemlicher Sicherheit hinter mindestens einer Firewall und einer NAT (Network Address Translation)-Ebene befindet.

## <a name="set-up-the-development-environment"></a>Einrichten der Entwicklungsumgebung

Bevor Sie mit der Entwicklung von Azure-Anwendungen beginnen können, müssen Sie die entsprechenden Tools herunterladen und die Entwicklungsumgebung einrichten:

1. Installieren Sie das Azure SDK für .NET über die [Downloadseite](https://azure.microsoft.com/downloads/) des SDK.
2. Klicken Sie in der Spalte **.NET** auf die von Ihnen verwendete Version von [Visual Studio](https://www.visualstudio.com). Für die Schritte in diesem Tutorial wird Visual Studio 2017 verwendet.
3. Wenn Sie aufgefordert werden, das Installationsprogramm auszuführen oder zu speichern, klicken Sie auf **Ausführen**.
4. Klicken Sie im **Webplattform-Installer** auf **Installieren**, und setzen Sie die Installation fort.
5. Nach Abschluss der Installation haben Sie alles zur Hand, was Sie benötigen, um mit der Entwicklung der App zu beginnen. Das SDK enthält Tools, mit denen Sie ganz leicht Azure-Anwendungen in Visual Studio entwickeln können.

## <a name="create-a-namespace"></a>Erstellen eines Namespace
Der erste Schritt umfasst die Einrichtung eines Namespaces und das Abrufen eines [Shared Access Signature](../service-bus-messaging/service-bus-sas.md)-Schlüssels (SAS). Ein Namespace stellt eine Anwendungsgrenze für jede Anwendung bereit, die über den Relaydienst verfügbar gemacht wird. Das System generiert automatisch einen SAS-Schlüssel, wenn ein Dienstnamespace erstellt wird. Dienstnamespace und SAS-Schlüssel bilden gemeinsam die Anmeldeinformationen, mit denen sich Azure für den Zugriff auf die Anwendung authentifiziert.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Erstellen eines lokalen Servers
Zunächst erstellen Sie ein simuliertes lokales Produktkatalogsystem.  Bei diesem Projekt handelt es sich um eine Visual Studio-Konsolenanwendung, bei dem das [Azure Service Bus-NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) verwendet wird, um die Service Bus-Bibliotheken und -Konfigurationseinstellungen einzubinden.

### <a name="create-the-project"></a>Erstellen des Projekts
1. Starten Sie Microsoft Visual Studio mit Administratorrechten. Klicken Sie hierzu mit der rechten Maustaste auf das Visual Studio-Programmsymbol und anschließend auf **Als Administrator ausführen**.
2. Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
3. Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf **Konsolen-App (.NET Framework)** . Geben Sie in das Feld **Name** den Namen **ProductsServer** ein:

   ![Dialogfeld "Neues Projekt"][11]
4. Klicken Sie auf **OK**, um das Projekt **ProductsServer** zu erstellen.
5. Wenn Sie den NuGet-Paket-Manager für Visual Studio bereits installiert haben, fahren Sie mit dem nächsten Schritt fort. Besuchen Sie andernfalls [NuGet][NuGet], und klicken Sie auf [NuGet installieren](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Folgen Sie den Anweisungen, um den NuGet-Paket-Manager zu installieren, und starten Sie Visual Studio dann neu.
6. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsServer**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.
7. Klicken Sie auf die Registerkarte **Durchsuchen**, und suchen Sie nach **WindowsAzure.ServiceBus**. Wählen Sie das Paket **WindowsAzure.ServiceBus** aus.
8. Klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

   ![Auswählen eines NuGet-Pakets][13]

   Nun wird auf die erforderlichen Clientassemblys verwiesen.
8. Fügen Sie eine neue Klasse für den Produktvertrag hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsServer**. Klicken Sie auf **Hinzufügen** und dann auf **Klasse**.
9. Geben Sie in das Feld **Name** den Namen **ProductsContract.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.
10. Ersetzen Sie in **ProductsContract.cs** die Namespacedefinition durch den folgenden Code, mit dem der Vertrag für den Dienst definiert wird.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. Ersetzen Sie in "Program.cs" die Namespacedefinition durch den folgenden Code, mit dem der Profildienst und der dafür vorgesehene Host hinzugefügt werden.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **App.config**, um sie im Visual Studio-Editor zu öffnen. Fügen Sie unten im `<system.ServiceModel>`-Element (aber noch innerhalb von `<system.ServiceModel>`) den folgenden XML-Code hinzu: Ersetzen Sie dabei yourServiceNamespace durch den Namen Ihres Namespace und *yourKey* durch den SAS-Schlüssel, den Sie zuvor aus dem Portal abgerufen haben:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
    Der von „transportClientEndpointBehavior“ verursachte Fehler ist nur eine Warnung und stellt für dieses Beispiel kein Problem dar.
    
13. Ersetzen Sie in „App.config“ im `<appSettings>`-Element den Wert der Verbindungszeichenfolge durch die Verbindungszeichenfolge, die Sie zuvor aus dem Portal abgerufen haben.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Drücken Sie **STRG+UMSCHALT+B** , oder klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Anwendung zu erstellen und Ihre bisherige Arbeit zu überprüfen.

## <a name="create-an-aspnet-application"></a>Erstellen einer ASP.NET-Anwendung

In diesem Abschnitt erstellen Sie eine einfache ASP.NET-Anwendung, in der aus Ihrem Produktdienst abgerufene Daten angezeigt werden.

### <a name="create-the-project"></a>Erstellen des Projekts

1. Stellen Sie sicher, dass Visual Studio mit Administratorberechtigungen ausgeführt wird.
2. Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
3. Klicken Sie in **Installierte Vorlagen** unter **Visual C#** auf **ASP.NET-Webanwendung (.NET Framework)** . Nennen Sie das Projekt **ProductsPortal**. Klicken Sie dann auf **OK**.

   ![Dialogfeld "Neues Projekt"][15]

4. Klicken Sie in der Liste **ASP.NET-Vorlagen** im Dialogfeld **Neue ASP.NET-Webanwendung** auf **MVC**.

   ![Auswählen einer ASP.NET-Webanwendung][16]

6. Klicken Sie auf die Schaltfläche **Authentifizierung ändern**. Stellen Sie sicher, dass im Dialogfeld **Authentifizierung ändern** die Option **Keine Authentifizierung** ausgewählt wurde, und klicken Sie dann auf **OK**. Für dieses Tutorial stellen Sie eine App bereit, für die keine Benutzeranmeldung erforderlich ist.

    ![Angeben des Authentifizierungstyps][18]

7. Wechseln Sie zurück zum Dialogfeld **Neue ASP.NET-Webanwendung**, und klicken Sie auf **OK**, um die MVC-App zu erstellen.
8. Als Nächstes müssen Sie die Azure-Ressourcen für eine neue Web-App konfigurieren. Führen Sie die Schritte im Abschnitt zum [Veröffentlichen in Azure](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard) in diesem Artikel aus. Kehren Sie anschließend zu diesem Tutorial zurück, und fahren Sie mit dem nächsten Schritt fort.
10. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Modelle**, und klicken Sie anschließend auf **Hinzufügen** und dann auf **Klasse**. Geben Sie in das Feld **Name** den Namen **Product.cs** ein. Klicken Sie anschließend auf **Hinzufügen**.

    ![Erstellen des Produktmodells][17]

### <a name="modify-the-web-application"></a>Ändern der Webanwendung

1. Ersetzen Sie in der Datei „Product.cs“ in Visual Studio die vorhandene Namespacedefinition durch den folgenden Code:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. Erweitern Sie im Projektmappen-Explorer den Ordner **Controller**, und doppelklicken Sie auf die Datei **HomeController.cs**, um sie in Visual Studio zu öffnen.
3. Ersetzen Sie in der Datei **HomeController.cs** die vorhandene Namespacedefinition durch den folgenden Code:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. Erweitern Sie im Projektmappen-Explorer den Ordner „Views\Shared“, und doppelklicken Sie dann auf **_Layout.cshtml**, um die Datei im Visual Studio-Editor zu öffnen.
5. Ändern Sie alle Vorkommnisse von **My ASP.NET Application** in **Northwind Traders Products**.
6. Entfernen Sie die Links **Home**, **About** und **Contact**. Löschen Sie im folgenden Beispiel den hervorgehobenen Code.

    ![Löschen der generierten Listenelemente][41]

7. Erweitern Sie im Projektmappen-Explorer den Ordner „Views\Home“, und doppelklicken Sie dann auf **Index.cshtml**, um die Datei im Visual Studio-Editor zu öffnen. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. Drücken Sie **STRG+UMSCHALT+B** , um das Projekt zu erstellen und Ihre bisherige Arbeit zu überprüfen.

### <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie die Anwendung aus, um sicherzustellen, dass sie funktioniert.

1. Stellen Sie sicher, dass **ProductsPortal** das aktive Projekt ist. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Als Startprojekt festlegen** aus.
2. Drücken Sie in Visual Studio die Taste **F5**.
3. Die Anwendung sollte ausgeführt und in einem Browser angezeigt werden.

   ![Webanwendung][21]

## <a name="put-the-pieces-together"></a>Zusammensetzen der Einzelteile

Im nächsten Schritt wird der lokale Produktserver mit der ASP.NET-Webanwendung zusammengeführt.

1. Falls noch nicht geschehen, öffnen Sie in Visual Studio erneut das Projekt **ProductsPortal**, das Sie im Abschnitt [Erstellen einer ASP.NET-Anwendung](#create-an-aspnet-application) erstellt haben.
2. Fügen Sie das NuGet-Paket den Projektverweisen hinzu, wie im Schritt „Erstellen eines lokalen Servers“ beschrieben. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.
3. Suchen Sie nach **WindowsAzure.ServiceBus**, und wählen Sie das Element **WindowsAzure.ServiceBus** aus. Schließen Sie die Installation ab, und schließen Sie das Dialogfeld.
4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**. Klicken Sie auf **Hinzufügen** und dann auf **Vorhandenes Element**.
5. Navigieren Sie im Konsolenprojekt **ProductsServer** zur Datei **ProductsContract.cs**. Klicken Sie, um "ProductsContract.cs" zu markieren. Klicken Sie auf den Pfeil nach unten neben **Hinzufügen** und dann auf **Als Link hinzufügen**.

   ![Als Link hinzufügen][24]

6. Öffnen Sie nun die Datei **HomeController.cs** im Visual Studio-Editor, und ersetzen Sie die Namespacedefinition durch den folgenden Code: Achten Sie darauf, yourServiceNamespace durch den Namen Ihres Dienstnamespaces und *yourKey* durch Ihren SAS-Schlüssel zu ersetzen. Der Client kann nun den lokalen Dienst aufrufen und das Ergebnis des Aufrufs zurückgeben.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ProductsPortal** (klicken Sie auf die Projektmappe, nicht auf das Projekt). Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Projekt**.
8. Navigieren Sie zum Projekt **ProductsServer**, und doppelklicken Sie dann auf die Projektmappendatei **ProductsServer.csproj**, um sie hinzuzufügen.
9. **ProductsServer** muss ausgeführt werden, damit die Daten unter **ProductsPortal** angezeigt werden. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ProductsPortal**, und klicken Sie dann auf **Eigenschaften**. Das Dialogfeld **Eigenschaftenseiten** wird angezeigt.
10. Klicken Sie auf der linken Seite auf **Startprojekt**. Klicken Sie auf der rechten Seite auf **Mehrere Startprojekte**. Vergewissern Sie sich, dass **ProductsServer** und **ProductsPortal** in dieser Reihenfolge angezeigt werden und jeweils **Start** als Aktion festgelegt ist.

      ![Mehrere Startprojekte][25]

11. Klicken Sie im Dialogfeld **Eigenschaften** auf der linken Seite auf **Projektabhängigkeiten**.
12. Klicken Sie in der Liste **Projekte** auf **ProductsServer**. Vergewissern Sie sich, dass **ProductsPortal** nicht ausgewählt ist.
13. Klicken Sie in der Liste **Projekte** auf **ProductsPortal**. Vergewissern Sie sich, dass **ProductsServer** ausgewählt ist.

    ![Projektabhängigkeiten][26]

14. Klicken Sie im Dialogfeld **Eigenschaftenseiten** auf **OK**.

## <a name="run-the-project-locally"></a>Lokales Ausführen des Projekts

Drücken Sie **F5** , um die Anwendung in Visual Studio lokal zu testen. Zuerst muss der lokale Server (**ProductsServer**) gestartet werden, gefolgt von der Anwendung **ProductsPortal** in einem Browserfenster. Dieses Mal werden Sie feststellen, dass in der Produktbestandsliste Daten aus dem lokalen Produktdienstsystem aufgeführt werden.

![Webanwendung][10]

Wählen Sie auf der Seite **ProductsPortal** die Option **Aktualisieren**. Bei jeder Aktualisierung der Seite zeigt die Server-App eine Meldung an, wenn `GetProducts()` von **ProductsServer** aufgerufen wird.

Schließen Sie beide Anwendungen, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Bereitstellen des Projekts ProductsPortal in einer Azure-Web-App

Im nächsten Schritt wird das **ProductsPortal**-Front-End der Azure-Web-App erneut veröffentlicht. Gehen Sie wie folgt vor:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProductsPortal**, und klicken Sie anschließend auf **Veröffentlichen**. Klicken Sie auf der Seite **Veröffentlichen** auf **Veröffentlichen**.

   > [!NOTE]
   > Im Browserfenster wird unter Umständen eine Fehlermeldung angezeigt, wenn das Webprojekt **ProductsPortal** nach der Bereitstellung automatisch gestartet wird. Dies ist zu erwarten und tritt auf, da die Anwendung **ProductsServer** noch nicht ausgeführt wird.
   >
   >

2. Kopieren Sie die URL der bereitgestellten Web-App, da Sie die URL im nächsten Schritt benötigen. Sie können diese URL auch in Visual Studio im Fenster „Aktivität von Azure App Service“ abrufen:

   ![URL der bereitgestellten App][9]

3. Schließen Sie das Browserfenster, um die ausgeführte Anwendung zu beenden.

### <a name="set-productsportal-as-web-app"></a>Festlegen von ProductsPortal als Web-App

Bevor Sie die Anwendung in der Cloud ausführen, müssen Sie dafür sorgen, dass **ProductsPortal** in Visual Studio als Web-App gestartet wird.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **ProductsPortal**, und klicken Sie dann auf **Eigenschaften**.
2. Klicken Sie in der linken Spalte auf **Web**.
3. Klicken Sie im Abschnitt **Aktion starten** auf die Schaltfläche **Start-URL**, und geben Sie im Textfeld die URL für die zuvor bereitgestellte Web-App ein (z.B. `http://productsportal1234567890.azurewebsites.net/`).

    ![Start-URL][27]

4. Klicken Sie im Menü **Datei** in Visual Studio auf **Alles speichern**.
5. Klicken Sie in Visual Studio im Menü „Build“ auf **Projektmappe neu erstellen**.

## <a name="run-the-application"></a>Ausführen der Anwendung

1. Drücken Sie F5, um die Anwendung zu erstellen und auszuführen. Zuerst muss der lokale Server (die Konsolenanwendung **ProductsServer**) gestartet werden, gefolgt von der Anwendung **ProductsPortal** in einem Browserfenster, wie im Screenshot weiter unten dargestellt: Beachten Sie auch hier wieder, dass im Produktbestand die aus dem lokalen Produktdienstsystem abgerufenen Daten aufgelistet und in der Web-App angezeigt werden. Vergewissern Sie sich anhand der URL, dass **ProductsPortal** in der Cloud als Azure-Web-App ausgeführt wird.

   ![Ausführen der Web-App in Azure][1]

   > [!IMPORTANT]
   > Die Konsolenanwendung **ProductsServer** muss ausgeführt werden, und sie muss die Daten für die Anwendung **ProductsPortal** bereitstellen können. Sollte im Browser ein Fehler angezeigt werden, warten Sie noch einige Sekunden, bis **ProductsServer** geladen wurde und die folgende Nachricht angezeigt wird. Wählen Sie im Browser dann die Option **Aktualisieren** aus.
   >
   >

   ![Ausgabe des Servers][37]
2. Wählen Sie im Browser auf der Seite **ProductsPortal** die Option **Aktualisieren** aus. Bei jeder Aktualisierung der Seite zeigt die Server-App eine Meldung an, wenn `GetProducts()` von **ProductsServer** aufgerufen wird.

    ![Aktualisierte Ausgabe][38]

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem folgenden Tutorial fort: 

> [!div class="nextstepaction"]
>[Verfügbarmachen eines lokalen WCF-Diensts für einen WCF-Client außerhalb Ihres Netzwerks](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
