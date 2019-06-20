---
title: Erste Schritte mit der Authentifizierung für mobile Apps in Xamarin Android
description: Erfahren Sie, wie Sie mobile Apps zum Authentifizieren Ihrer Xamarin Android-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 0a2d964d60d13f0e71de5776112a4edbe3cdcc45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127912"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Azure Mobile Apps unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung in Mobile Apps wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Erstellen einer Xamarin.Android-App]abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

## <a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Hinzufügen Ihrer App zu den zulässigen externen Umleitungs-URLs

Eine sichere Authentifizierung erfordert, dass Sie ein neues URL-Schema für Ihre App definieren. Dies ermöglicht dem Authentifizierungssystem die erneute Umleitung an Ihre App, sobald der Authentifizierungsprozess abgeschlossen ist. In diesem Tutorial verwenden wir ausschließlich das URL-Schema _appname_. Sie können jedoch ein beliebiges URL-Schema auswählen und verwenden. Es sollte für Ihre mobile Anwendung eindeutig sein. So aktivieren Sie die Umleitung auf der Serverseite

1. Wählen Sie im [Azure-Portal] Ihre App Service-Instanz aus.

2. Klicken Sie auf die Menüoption **Authentifizierung/Autorisierung**.

3. Geben Sie in **Zulässige externe Umleitungs-URLs** `url_scheme_of_your_app://easyauth.callback` ein.  **url_scheme_of_your_app** steht in dieser Zeichenfolge für das URL-Schema für Ihre mobile Anwendung.  Es sollte der normalen URL-Spezifikation für ein Protokoll folgen (nur aus Buchstaben und Zahlen bestehen und mit einem Buchstaben beginnen).  Notieren Sie sich die gewählte Zeichenfolge, da Sie später Ihren mobilen Anwendungscode mehrfach mit dem URL-Schema anpassen müssen.

4. Klicken Sie auf **OK**.

5. Klicken Sie auf **Speichern**.

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Ihr Mobile App-Back-End zuzugreifen. Die Tabelle *TodoItem* erfordert jetzt eine Authentifizierung.

Als Nächstes aktualisieren Sie die Client-App, um Ressourcen vom mobilen App-Back-End mit einem authentifizierten Benutzer zu aktualisieren.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App
Die App wird so aktualisiert, dass Benutzer auf die Schaltfläche **Anmelden** tippen und sich authentifizieren müssen, bevor Daten angezeigt werden.

1. Fügen Sie der **TodoActivity** -Klasse den folgenden Code hinzu:
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Dies erstellt eine neue Methode zum Authentifizieren eines Benutzers und einen Methodenhandler für die neue Schaltfläche **Anmelden** . Der Benutzer wird im Codebeispiel oben mithilfe eines Facebook-Logins authentifiziert. Ein Dialogfeld wird verwendet, um die Benutzer-ID nach der Authentifizierung anzuzeigen.
   
   > [!NOTE]
   > Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** übergebenen Wert in einen der folgenden Werte: *MicrosoftAccount*, *Twitter*, *Google* oder *WindowsAzureActiveDirectory*.
   > 
   > 
2. Löschen Sie in der **OnCreate** -Methode die folgende Codezeile, oder kommentieren Sie sie aus:
   
        OnRefreshItemsSelected ();
3. Fügen Sie in der Datei „Activity_To_Do.axml“ die folgende Definition der Schaltfläche *LoginUser* vor der vorhandenen Schaltfläche *AddItem* hinzu:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Fügen Sie das folgende Element in die Ressourcendatei „Strings.xml“ ein:
   
        <string name="login_button_text">Sign in</string>
5. Öffnen Sie die Datei „AndroidManifest.xml“, und fügen Sie den folgenden Code im `<application>`-XML-Element hinzu:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus, und melden Sie sich mit dem ausgewählten Identitätsanbieter an. Nachdem Sie sich erfolgreich angemeldet haben, zeigt die App Ihre Login-ID und die Liste der zu erledigenden Elemente an, und Sie können die Daten ändern.

## <a name="troubleshooting"></a>Problembehandlung

**Die Anwendung ist mit dem Fehler `Java.Lang.NoSuchMethodError: No static method startActivity` abgestürzt.**

In einigen Fällen werden Konflikte in den Unterstützungspaketen nur als Warnung in Visual Studio angezeigt, aber die Anwendung stürzt zur Laufzeit mit dieser Ausnahme ab. In diesem Fall müssen Sie sicherstellen, dass alle Unterstützungspakete, auf die in Ihrem Projekt verwiesen wird, die gleiche Version aufweisen. Das [Azure Mobile Apps-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) hat eine `Xamarin.Android.Support.CustomTabs`-Abhängigkeit für die Android-Plattform. Wenn also das Projekt neuere Unterstützungspakete verwendet, müssen Sie dieses Paket direkt mit der erforderlichen Version installieren, um Konflikte zu vermeiden.

<!-- URLs. -->
[Erstellen einer Xamarin.Android-App]: app-service-mobile-xamarin-android-get-started.md
