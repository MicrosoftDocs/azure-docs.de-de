---
title: Erste Schritte der Authentifizierung für Mobile Apps in der Xamarin.Forms-App | Microsoft Docs
description: Erfahren Sie, wie Sie Mobile Apps zum Authentifizieren von Benutzern Ihrer Xamarin.Forms-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 2945cefc18a378b31700104049f1a14a1f320136
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66019787"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Hinzufügen von Authentifizierung zu Ihrer Xamarin Forms-App
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Übersicht
In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Tutorial fügen Sie dem Xamarin Forms-Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt, und Sie erhalten Zugriff auf beschränkte Tabellendaten.

## <a name="prerequisites"></a>Voraussetzungen
Wir empfehlen Ihnen, zuerst das Tutorial [Erstellen einer Xamarin.Forms-App][1] durchzuarbeiten, um für dieses Tutorial das bestmögliche Ergebnis zu erzielen. Nach Abschluss dieses Tutorials verfügen Sie über ein Xamarin Forms-Projekt, bei dem es sich um eine TodoList-App für mehrere Plattformen handelt.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Arbeiten Sie mit dem .NET-Back-End-Server SDK für Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Hinzufügen Ihrer App zu den zulässigen externen Umleitungs-URLs

Eine sichere Authentifizierung erfordert, dass Sie ein neues URL-Schema für Ihre App definieren. Dies ermöglicht dem Authentifizierungssystem die erneute Umleitung an Ihre App, sobald der Authentifizierungsprozess abgeschlossen ist. In diesem Tutorial verwenden wir ausschließlich das URL-Schema _appname_. Sie können jedoch ein beliebiges URL-Schema auswählen und verwenden. Es sollte für Ihre mobile Anwendung eindeutig sein. So aktivieren Sie die Umleitung auf der Serverseite

1. Wählen Sie im [Azure-Portal][8] Ihre App Service-Instanz aus.

2. Klicken Sie auf die Menüoption **Authentifizierung/Autorisierung**.

3. Geben Sie in **Zulässige externe Umleitungs-URLs** `url_scheme_of_your_app://easyauth.callback` ein.  **url_scheme_of_your_app** steht in dieser Zeichenfolge für das URL-Schema für Ihre mobile Anwendung.  Es sollte der normalen URL-Spezifikation für ein Protokoll folgen (nur aus Buchstaben und Zahlen bestehen und mit einem Buchstaben beginnen).  Notieren Sie sich die gewählte Zeichenfolge, da Sie später Ihren mobilen Anwendungscode mehrfach mit dem URL-Schema anpassen müssen.

4. Klicken Sie auf **OK**.

5. Klicken Sie auf **Speichern**.

## <a name="restrict-permissions-to-authenticated-users"></a>Einschränken von Berechtigungen für authentifizierte Benutzer
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Hinzufügen der Authentifizierung zur portablen Klassenbibliothek
Mobile Apps verwendet die [LoginAsync][3]-Erweiterungsmethode auf dem [MobileServiceClient][4], um für einen Benutzer die Anmeldung mit der App Service-Authentifizierung durchzuführen. In diesem Beispiel wird ein vom Server verwalteter Authentifizierungsfluss verwendet, bei dem in der App die Anmeldeoberfläche des Anbieters angezeigt wird. Weitere Informationen finden Sie unter [Vom Server verwaltete Authentifizierung][5]. Sie können stattdessen auch die [vom Client verwaltete Authentifizierung][6] verwenden, um in Ihrer Produktions-App eine bessere Benutzeroberfläche bereitzustellen.

Für die Authentifizierung mit einem Xamarin Forms-Projekt definieren Sie in der portablen Klassenbibliothek für die App eine **IAuthenticate**-Schnittstelle. Fügen Sie anschließend der Benutzeroberfläche, die in der portablen Klassenbibliothek definiert ist, die Schaltfläche **Anmelden** hinzu. Diese Schaltfläche wird zum Starten der Authentifizierung verwendet. Nach der erfolgreichen Authentifizierung werden die Daten aus dem mobilen App-Back-End geladen.

Implementieren Sie die **IAuthenticate**-Schnittstelle für jede Plattform, die von Ihrer App unterstützt wird.

1. Öffnen Sie in Visual Studio oder Xamarin Studio die Datei „App.cs“ im Projekt mit **Portable** im Namen, also das Projekt „Portable Klassenbibliothek“. Fügen Sie anschließend die folgende `using`-Anweisung hinzu:

        using System.Threading.Tasks;
2. Fügen Sie in „App.cs“ die folgende `IAuthenticate`-Schnittstellendefinition unmittelbar vor der `App`-Klassendefinition hinzu.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Fügen Sie der **App**-Klasse die folgenden statischen Member hinzu, um die Schnittstelle mit einer plattformspezifischen Implementierung zu initialisieren.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Öffnen Sie die Datei „TodoList.xaml“ des Projekts „Portable Klassenbibliothek“, und fügen Sie das folgende **Button** -Element im *buttonsPanel* -Layoutelement nach der vorhandenen Schaltfläche hinzu:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Diese Schaltfläche löst die vom Server verwaltete Authentifizierung mit Ihrem mobilen App-Back-End aus.
5. Öffnen Sie die Datei „TodoList.xaml.cs“ im Projekt „Portable Klassenbibliothek“, und fügen Sie der `TodoList` -Klasse dann das folgende Feld hinzu:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Ersetzen Sie die vorhandene **OnAppearing** -Methode durch den folgenden Code:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Mit diesem Code wird sichergestellt, dass die Daten für den Dienst erst aktualisiert werden, nachdem Sie authentifiziert wurden.
7. Fügen Sie der **TodoList**-Klasse den folgenden Handler für das **Clicked**-Ereignis hinzu:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Speichern Sie die Änderungen, und erstellen Sie das Projekt „Portable Klassenbibliothek“ erneut, um sicherzustellen, dass es fehlerfrei ist.

## <a name="add-authentication-to-the-android-app"></a>Hinzufügen der Authentifizierung zur Android-App
In diesem Abschnitt wird das Implementieren der **IAuthenticate** -Schnittstelle im Android-App-Projekt gezeigt. Überspringen Sie diesen Abschnitt, wenn Sie Android-Geräte nicht unterstützen.

1. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das Projekt **droid**, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Drücken Sie F5, um das Projekt im Debugger zu starten. Stellen Sie anschließend sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) ausgelöst wird, nachdem die App gestartet wurde. Der 401-Code wird angezeigt, weil der Zugriff auf dem Back-End nur auf autorisierte Benutzer beschränkt ist.
3. Öffnen Sie im Android-Projekt die Datei „MainActivity.cs“, und fügen Sie die folgenden `using` -Anweisungen hinzu:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualisieren Sie die **MainActivity**-Klasse wie folgt, um die **IAuthenticate**-Schnittstelle zu implementieren:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualisieren Sie die **MainActivity**-Klasse, indem Sie wie folgt ein **MobileServiceUser**-Feld und eine **Authenticate**-Methode hinzufügen, die für die **IAuthenticate**-Schnittstelle erforderlich ist:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, müssen Sie für [MobileServiceAuthenticationProvider][7] einen anderen Wert auswählen.

6. Aktualisieren Sie die Datei **AndroidManifest.xml**, indem Sie im `<application>`-Element folgenden XML-Code hinzufügen:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Ersetzen Sie `{url_scheme_of_your_app}` durch Ihr URL-Schema.
7. Fügen Sie der **OnCreate**-Methode der **MainActivity**-Klasse vor dem Aufruf von `LoadApplication()` den folgenden Code hinzu:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Mit diesem Code wird sichergestellt, dass der Authentifikator vor dem Laden der App initialisiert wird.
8. Erstellen Sie die App erneut, und führen Sie sie aus. Melden Sie sich dann über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf Daten zugreifen können.

### <a name="troubleshooting"></a>Problembehandlung

**Die Anwendung ist mit dem Fehler `Java.Lang.NoSuchMethodError: No static method startActivity` abgestürzt.**

In einigen Fällen werden Konflikte in den Unterstützungspaketen nur als Warnung in Visual Studio angezeigt, aber die Anwendung stürzt zur Laufzeit mit dieser Ausnahme ab. In diesem Fall müssen Sie sicherstellen, dass alle Unterstützungspakete, auf die in Ihrem Projekt verwiesen wird, die gleiche Version aufweisen. Das [Azure Mobile Apps-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) hat eine `Xamarin.Android.Support.CustomTabs`-Abhängigkeit für die Android-Plattform. Wenn also das Projekt neuere Unterstützungspakete verwendet, müssen Sie dieses Paket direkt mit der erforderlichen Version installieren, um Konflikte zu vermeiden.

## <a name="add-authentication-to-the-ios-app"></a>Hinzufügen der Authentifizierung zur iOS-App
In diesem Abschnitt wird das Implementieren der **IAuthenticate** -Schnittstelle im iOS-App-Projekt gezeigt. Überspringen Sie diesen Abschnitt, wenn Sie iOS-Geräte nicht unterstützen.

1. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das **iOS**-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Drücken Sie F5, um das Projekt im Debugger zu starten. Stellen Sie anschließend sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) ausgelöst wird, nachdem die App gestartet wurde. Die 401-Antwort wird angezeigt, weil der Zugriff auf dem Back-End nur auf autorisierte Benutzer beschränkt ist.
3. Öffnen Sie die Datei „AppDelegate.cs“ im iOS-Projekt, und fügen Sie die folgenden `using` -Anweisungen hinzu:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualisieren Sie die **AppDelegate**-Klasse wie folgt, um die **IAuthenticate**-Schnittstelle zu implementieren:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualisieren Sie die **AppDelegate**-Klasse, indem Sie wie folgt ein **MobileServiceUser**-Feld und eine **Authenticate**-Methode hinzufügen, die für die **IAuthenticate**-Schnittstelle erforderlich ist:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, müssen Sie für [MobileServiceAuthenticationProvider] einen anderen Wert auswählen.
    
6. Aktualisieren Sie die Klasse **AppDelegate**, indem Sie die **OpenUrl**-Methodenüberladung wie folgt hinzufügen:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Fügen Sie der **FinishedLaunching**-Methode vor dem Aufruf von `LoadApplication()` die folgende Codezeile hinzu:

        App.Init(this);

    So wird sichergestellt, dass der Authentifikator vor dem Laden der App initialisiert wird.

8. Öffnen Sie „Info.plist“, und fügen Sie einen **URL-Typ** hinzu. Legen Sie als **Bezeichner** einen Namen Ihrer Wahl fest. Legen Sie außerdem für **URL-Schemas** das URL-Schema für Ihre App und für **Rolle** die Option „Keine“ fest.

9. Erstellen Sie die App erneut, und führen Sie sie aus. Melden Sie sich dann über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf Daten zugreifen können.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Hinzufügen der Authentifizierung zu Windows 10-App-Projekten (einschließlich Windows Phone)
In diesem Abschnitt wird das Implementieren der **IAuthenticate**-Schnittstelle in Windows 10-App-Projekten gezeigt. Die gleichen Schritte gelten auch für UWP-Projekte (Universelle Windows-Plattform), nur eben entsprechend mit Verwendung des **UWP**-Projekts (mit den angegebenen Änderungen). Überspringen Sie diesen Abschnitt, wenn Sie Windows-Geräte nicht unterstützen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **UWP**, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Drücken Sie F5, um das Projekt im Debugger zu starten. Stellen Sie anschließend sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) ausgelöst wird, nachdem die App gestartet wurde. Die 401-Antwort wird angezeigt, weil der Zugriff auf dem Back-End nur auf autorisierte Benutzer beschränkt ist.
3. Öffnen Sie die Datei „MainPage.xaml.cs“ für das Windows-App-Projekt, und fügen Sie die folgenden `using` -Anweisungen hinzu:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Ersetzen Sie `<your_Portable_Class_Library_namespace>` durch den Namespace für Ihre portable Klassenbibliothek.
4. Aktualisieren Sie die **MainPage**-Klasse wie folgt, um die **IAuthenticate**-Schnittstelle zu implementieren:

        public sealed partial class MainPage : IAuthenticate
5. Aktualisieren Sie die **MainPage**-Klasse, indem Sie wie folgt ein **MobileServiceUser**-Feld und eine **Authenticate**-Methode hinzufügen, die für die **IAuthenticate**-Schnittstelle erforderlich ist:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, müssen Sie für [MobileServiceAuthenticationProvider][7] einen anderen Wert auswählen.

1. Fügen Sie im Konstruktor für die **MainPage**-Klasse vor dem Aufruf von `LoadApplication()` die folgende Codezeile hinzu:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Ersetzen Sie `<your_Portable_Class_Library_namespace>` durch den Namespace für Ihre portable Klassenbibliothek.

3. Fügen Sie bei Verwendung von **UWP** der **App**-Klasse die folgende **OnActivated**-Methodenüberschreibung hinzu:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Öffnen Sie „Package.appxmanifest“, und fügen Sie eine **Protokolldeklaration** hinzu. Legen Sie als **Anzeigename** einen Namen Ihrer Wahl und für **Name** das URL-Schema für Ihre App fest.

4. Erstellen Sie die App erneut, und führen Sie sie aus. Melden Sie sich dann über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf Daten zugreifen können.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie dieses einfache Tutorial zur Authentifizierung abgeschlossen haben, können Sie mit den folgenden Tutorials fortfahren:

* [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-xamarin-forms-get-started-push.md)

   Hier erfahren Sie, wie Sie Ihrer App die Unterstützung von Pushbenachrichtigungen hinzufügen und Ihr Mobile App-Back-End für die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen konfigurieren.
* [Aktivieren der Offlinesynchronisierung für Ihre App](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Erfahren Sie, wie Sie mithilfe eines Mobile App-Back-Ends Ihrer App Offlineunterstützung hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten) auch ohne Netzwerkverbindung.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
