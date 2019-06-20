---
title: Verwenden des Objektspeichers (Blob) in Xamarin | Microsoft-Dokumentation
description: Die Azure Storage-Clientbibliothek für Xamarin ermöglicht Entwicklern das Erstellen von iOS-, Android- und Windows Store-Apps mit ihren nativen Benutzeroberflächen. In diesem Tutorial wird erläutert, wie mithilfe von Xamarin eine Anwendung erstellt wird, in der Azure Blob Storage verwendet wird.
services: storage
documentationcenter: xamarin
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 1953a0af89e5ea018bd3654c3b82087331b3d565
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141280"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Verwenden des Blobspeichers mit Xamarin

Xamarin ermöglicht Entwicklern die Verwendung einer gemeinsamen C#-Codebasis zum Erstellen von iOS-, Android- und Windows Store-Apps mit ihren systemeigenen Benutzeroberflächen. In diesem Tutorial wird erläutert, wie Sie Azure Blob Storage mit einer Xamarin-Anwendung verwenden können. Wenn Sie vor dem Einstieg in den Code mehr über Azure Storage erfahren möchten, lesen Sie [Einführung in Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Erstellen einer neuen Xamarin-Anwendung
Für dieses Tutorial erstellen wir eine App, die auf Android, iOS und Windows abzielt. Diese App erstellt einfach einen Container und lädt ein Blob in diesen Container hoch. Wir verwenden Visual Studio unter Windows, aber beim Erstellen einer App mit Xamarin Studio unter macOS können Sie ähnlich vorgehen.

Führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Falls noch nicht geschehen, laden Sie [Xamarin für Visual Studio](https://www.xamarin.com/download)herunter, und installieren Sie die Anwendung.
2. Öffnen Sie Visual Studio, und erstellen Sie eine lLeere App (nativ portabel): **Datei > Neu > Projekt > Plattformübergreifend > Leere App (nativ portabel)** .
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Lösung, und wählen Sie **NuGet-Pakete verwalten**aus. Suchen Sie nach **WindowsAzure.Storage** , und installieren Sie die neueste stabile Version für alle Projekte in der Lösung.
4. Erstellen Sie Ihr Projekt, und führen Sie es aus.

Es sollte nun eine Anwendung verfügbar sein, in der Sie auf eine Schaltfläche klicken können, die einen Zähler schrittweise heraufsetzt.

## <a name="create-container-and-upload-blob"></a>Erstellen des Containers und Hochladen des Blobs
Fügen Sie anschließend unter Ihrem `(Portable)`-Projekt `MyClass.cs` Code hinzu. Dieser Code erstellt einen Container und lädt ein Blob in diesen Container hoch. `MyClass.cs` sollte wie folgt aussehen:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Stellen Sie sicher, dass Sie „Your_account_name_here“ und „Your_account_key_here“ durch Ihren tatsächlichen Kontonamen und Kontoschlüssel ersetzen. 

Ihre iOS-, Android- und Windows Phone-Projekte enthalten Verweise auf Ihr portables Projekt – was bedeutet, dass Sie Ihren sämtlichen freigegebenen Code an einer Stelle schreiben und in allen Ihren Projekten verwenden können. Sie können jetzt jedem Projekt die folgende Codezeile hinzufügen, um diesen Vorteil zu nutzen: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.Xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung
Sie können diese Anwendung jetzt in einem Android- oder Windows Phone-Emulator ausführen. Sie können diese Anwendung auch in einem iOS-Emulator ausführen, aber dazu benötigen Sie einen Mac. Spezifische Anweisungen hierzu finden Sie in der Dokumentation zum [Verbinden von Visual Studio mit einem Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Wenn Sie Ihre App ausführen, erstellt sie den Container `mycontainer` in Ihrem Speicherkonto. Es sollte das Blob `myblob` mit dem Text `Hello, world!` enthalten. Sie können dies mit dem [Microsoft Azure Storage-Explorer](https://storageexplorer.com/)überprüfen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie eine plattformübergreifende Anwendung in Xamarin erstellt wird, die Azure Storage verwendet und besonders auf ein Szenario in Blob Storage fokussiert ist. Allerdings können Sie sie nicht nur auf Blob Storage, sondern auch auf Table Storage, File Storage und Queue Storage anwenden. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure Blob Storage mit .NET](storage-dotnet-how-to-use-blobs.md)
* [Einführung in Azure Files](../files/storage-files-introduction.md)
* [Entwickeln für Azure Files mit .NET](../files/storage-dotnet-how-to-use-files.md)
* [Erste Schritte mit Azure Table Storage mit .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Erste Schritte mit Azure Queue Storage mit .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]