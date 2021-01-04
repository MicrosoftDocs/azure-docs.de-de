---
title: Authentifizierung und Autorisierung
description: Lernen Sie die verschiedenen Möglichkeiten kennen, mit denen eine App oder ein Dienst sich bei Azure Spatial Anchors authentifizieren kann, und erfahren Sie, auf welchen Ebenen Sie den Zugriff auf Spatial Anchors steuern können.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 0166a3b6031f9e1d364a37db99be5bc5a65267df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484609"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Authentifizierung und Autorisierung bei Azure Spatial Anchors

In diesem Artikel erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie sich aus Ihrer App oder Ihren Webdienst bei Azure Spatial Anchors authentifizieren können. Außerdem erfahren Sie mehr über die Möglichkeiten, wie Sie die rollenbasierte Zugriffssteuerung (Azure RBC) in Azure Active Directory (Azure AD) verwenden können, um den Zugriff auf Ihre Spatial Anchors-Konten zu steuern.

## <a name="overview"></a>Übersicht

![Abbildung der Übersicht über die Authentifizierung bei Azure Spatial Anchors](./media/spatial-anchors-authentication-overview.png)

Um auf ein bestimmtes Azure Spatial Anchors-Konto zuzugreifen, müssen Clients zuerst ein Zugriffstoken aus dem Azure Mixed Reality-Sicherheitstokendienst (Security Token Service, STS) abrufen. Von STS abgerufene Token haben eine Gültigkeitsdauer von 24 Stunden. Sie enthalten Informationen, mit denen Spatial Anchors-Dienste Autorisierungsentscheidungen für ein Konto treffen können. Diese Informationen stellen sicher, dass nur autorisierte Prinzipale auf das Konto zugreifen können.

Zugriffstoken können im Austausch gegen Kontoschlüssel oder von Azure AD ausgestellte Token abgerufen werden.

Kontoschlüssel ermöglichen Ihnen den schnellen Einstieg in die Verwendung des Azure Spatial Anchors-Diensts. Aber bevor Sie Ihre Anwendung in der Produktionsumgebung bereitstellen, empfiehlt es sich, dass Sie Ihre App für die Verwendung von Azure AD-Authentifizierung aktualisieren.

Azure AD-Authentifizierungstoken können Sie auf zwei Arten abrufen:

- Wenn Sie eine Unternehmensanwendung entwickeln und Ihr Unternehmen Azure AD als Identitätssystem verwendet, können Sie die benutzerbasierte Azure AD-Authentifizierung in Ihrer App verwenden. Sie gewähren dann den Zugriff auf Ihre Spatial Anchors-Konten mithilfe Ihrer vorhandenen Azure AD-Sicherheitsgruppen. Sie können Benutzern in Ihrer Organisation Zugriff auch direkt gewähren.
- Andernfalls empfiehlt es sich, Azure AD-Token von einem Webdienst abzurufen, der Ihre App unterstützt. Wir empfehlen diese Methode für Produktionsanwendungen, weil diese es Ihnen ermöglicht, die Einbettung der Anmeldeinformationen für den Zugriff auf Azure Spatial Anchors in Ihre Clientanwendung zu vermeiden.

## <a name="account-keys"></a>Kontoschlüssel

Die einfachste Möglichkeit für den Einstieg besteht in der Verwendung von Kontoschlüsseln für den Zugriff auf Ihr Azure Spatial Anchors-Konto. Ihre Kontoschlüssel können Sie im Azure-Portal abrufen. Wechseln Sie zu Ihrem Konto, und wählen Sie die Registerkarte **Schlüssel** aus:

![Screenshot, der die Registerkarte „Schlüssel“ mit hervorgehobener Schaltfläche „Kopieren“ für den „Primärschlüssel“ zeigt.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Zwei Schlüssel sind verfügbar. Beide sind gleichzeitig für den Zugriff auf das Spatial Anchors-Konto gültig. Wir empfehlen, dass Sie den Schlüssel, den Sie für den Zugriff auf das Konto verwenden, regelmäßig aktualisieren. Wenn zwei separate gültige Schlüssel vorhanden sind, können Sie diese Updates ohne Ausfallzeiten vornehmen. Sie müssen nur den Primärschlüssel aktualisieren und den sekundären ersatzweise.

Das SDK verfügt über integrierte Unterstützung für die Authentifizierung mittels Kontoschlüsseln. Sie müssen nur die `AccountKey`-Eigenschaft für Ihr `cloudSession`-Objekt festlegen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Nachdem Sie diese Eigenschaft festgelegt haben, verarbeitet das SDK den Austausch des Kontoschlüssels gegen ein Zugriffstoken und die notwendige Zwischenspeicherung der Token für Ihre App.

> [!WARNING]
> Wir empfehlen, dass Sie Kontoschlüssel für ein schnelles Onboarding verwenden, allerdings nur in den Phasen der Entwicklung und der Prototyperstellung. Es wird nicht empfohlen, Ihre Anwendung mit einem darin eingebetteten Kontoschlüssel an die Produktion auszuliefern. Verwenden Sie stattdessen die benutzerbasierten oder dienstbasierten Azure AD-Authentifizierungsansätze, die als Nächstes beschrieben werden.

## <a name="azure-ad-user-authentication"></a>Azure AD-Benutzerauthentifizierung

Für Anwendungen, die auf Azure Active Directory-Benutzer abzielen, empfehlen wir, dass Sie ein Azure AD-Token für den Benutzer verwenden. Sie können dieses Token mithilfe der [MSAL](../../active-directory/develop/msal-overview.md) abrufen. Führen Sie die Schritte in der [Schnellstartanleitung zum Registrieren einer App](../../active-directory/develop/quickstart-register-app.md)aus. Hierzu gehören:

**Im Azure-Portal**
1.    Registrieren Sie Ihre Anwendung in Azure AD als native Anwendung. Im Rahmen der Registrierung müssen Sie festlegen, ob Ihre Anwendung mehrinstanzenfähig sein soll. Außerdem müssen Sie die Umleitungs-URLs angeben, die für Ihre Anwendung zulässig sind.
1.  Wechseln Sie zur Registerkarte **API-Berechtigungen**.
2.  Wählen Sie **Berechtigung hinzufügen** aus.
    1.  Wählen Sie auf der Registerkarte **Von meiner Organisation verwendete APIs** die Option **Mixed Reality-Ressourcenanbieter** aus.
    2.  Wählen Sie **Delegierte Berechtigungen** aus.
    3.  Wählen Sie **mixedreality.signin** unter **mixedreality** aus.
    4.  Wählen Sie **Berechtigungen hinzufügen** aus.
3.  Wählen Sie **Administratoreinwilligung erteilen** aus.

2. Gewähren Sie Ihrer Anwendung bzw. den Benutzern Zugriff auf Ihre Ressource:
   1.    Wechseln Sie im Azure-Portal zu Ihrer Spatial Anchors-Ressource.
   2.    Wechseln Sie zur Registerkarte **Zugriffssteuerung (IAM)** .
   3.    Wählen Sie **Rollenzuweisung hinzufügen** aus.
   1.    [Wählen Sie eine Rolle aus](#azure-role-based-access-control).
   2.    Geben Sie im Feld **Auswählen** die Namen der Benutzer, Gruppen und/oder Anwendungen ein, denen Sie Zugriff gewähren möchten.
   3.    Wählen Sie **Speichern** aus.

**In Ihrem Code**
1.    Stellen Sie sicher, dass Sie die Anwendungs-ID und den Umleitungs-URI Ihrer eigenen Azure AD-Anwendung als Parameter für **Client-ID** und **RedirectUri** (Umleitungs-URI) in MSAL verwenden.
2.    Legen Sie die Informationen zum Mandanten fest:
        1.    Wenn Ihre Anwendung **Nur meine Organisation** unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen**. Beispiel: „contoso.microsoft.com“.
        2.    Wenn Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch **Organizations** (Organisationen).
        3.    Wenn Ihre Anwendung **Alle Microsoft-Kontobenutzer** unterstützt, ersetzen Sie diesen Wert durch **Common** (Allgemein).
3.    Legen Sie für Ihre Tokenanforderung für **scope** den Wert **"`https://sts.<account-domain>//.default`"** fest, wobei `<account-domain>` durch die **Kontodomäne** für Ihr Azure Spatial Anchors-Konto ersetzt wird. Ein Beispiel für ein Azure Spatial Anchors-Konto in der Kontodomäne „USA, Osten 2“ ist **"`https://sts.mixedreality.azure.com//.default`"** . Dieser Bereich informiert Azure AD darüber, dass Ihre Anwendung ein Token für den Sicherheitstokendienst (Security Token Service, STS) Mixed Reality anfordert.

Nachdem Sie diese Schritte ausgeführt haben, sollte Ihre Anwendung in der Lage sein, ein Azure AD-Token von MSAL abzurufen. Sie können dieses Azure AD-Token als `authenticationToken` für Ihr Cloudsitzungs-Konfigurationsobjekt festlegen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD-Dienstauthentifizierung

Um Apps, die Azure Spatial Anchors verwenden, in der Produktion bereitzustellen, empfehlen wir, dass Sie einen Back-End-Dienst als Broker für Authentifizierungsanforderungen verwenden. Übersicht über den Prozess:

![Diagramm, das eine Übersicht über die Authentifizierung bei Azure Spatial Anchors bereitstellt.](./media/spatial-anchors-aad-authentication.png)

Hier wird davon ausgegangen, dass Ihre App einen eigenen Mechanismus zur Authentifizierung ihres Back-End-Diensts verwendet. (Beispielsweise ein Microsoft-Konto, PlayFab, Facebook, eine Google-ID oder einen benutzerdefinierten Benutzernamen und ein Kennwort.)  Nachdem Ihre Benutzer bei Ihrem Back-End-Dienst authentifiziert wurden, kann der Dienst ein Azure AD-Token abrufen, dieses gegen ein Zugriffstoken für Azure Spatial Anchors austauschen und es dann an die Clientanwendung zurückgeben.

Das Azure AD-Zugriffstoken wird mithilfe von [MSAL](../../active-directory/develop/msal-overview.md) abgerufen. Befolgen Sie die Schritte im [Schnellstart zum Registrieren einer App](../../active-directory/develop/quickstart-register-app.md), einschließlich der folgenden:

**Im Azure-Portal**
1.    Registrieren Ihrer Anwendung bei Azure AD:
        1.    Wählen Sie im Azure-Portal **Azure Active Directory** und anschließend **App-Registrierungen** aus.
        2.    Wählen Sie **Neue Registrierung** aus.
        3.    Geben Sie den Namen Ihrer Anwendung ein, wählen Sie **Web-App/API** als Anwendungstyp aus, und geben Sie die Authentifizierungs-URL für Ihren Dienst ein. Klicken Sie auf **Erstellen**.
2.    Wählen Sie in der Anwendung **Einstellungen** und dann die Registerkarte **Zertifikate und Geheimnisse** aus. Erstellen Sie einen neuen geheimen Clientschlüssel, wählen Sie eine Dauer aus, und wählen Sie **Hinzufügen** aus. Stellen Sie sicher, dass Sie den Geheimniswert speichern. Sie müssen diesen in den Code Ihres Webdiensts aufnehmen.
3.    Gewähren Sie Ihrer Anwendung bzw. den Benutzern Zugriff auf Ihre Ressource:
        1.    Wechseln Sie im Azure-Portal zu Ihrer Spatial Anchors-Ressource.
        2.    Wechseln Sie zur Registerkarte **Zugriffssteuerung (IAM)** .
        3.    Wählen Sie **Rollenzuweisung hinzufügen** aus.
        4.    [Wählen Sie eine Rolle aus](#azure-role-based-access-control).
        5.    Geben Sie im Feld **Auswählen** den oder die Namen der Benutzer der Anwendungen ein, denen Sie Zugriff gewähren möchten. Wenn Sie möchten, dass die Benutzer Ihrer App unterschiedliche Rollen im Spatial Anchors-Konto einnehmen, registrieren Sie mehrere Anwendungen in Azure AD, und weisen Sie jeder eine separate Rolle zu. Implementieren Sie dann Ihre Autorisierungslogik, damit die richtige Rolle für Ihre Benutzer verwendet wird.

              > [!NOTE]
              > Wählen Sie im Bereich **Rollenzuweisung hinzufügen** in **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus.

        6.    Wählen Sie **Speichern** aus.

**In Ihrem Code**

>[!NOTE]
> Sie können das auf GitHub verfügbare Dienstbeispiel verwenden.

1.    Stellen Sie sicher, dass Sie die Anwendungs-ID, das Anwendungsgeheimnis und den Umleitungs-URI Ihrer eigenen Azure AD-Anwendung als Parameter für **Client-ID**, **Geheimnis** und **RedirectUri** (Umleitungs-URI) in MSAL verwenden.
2.    Legen Sie die Mandanten-ID im **authority**-Parameter in MSAL auf Ihre eigene Azure AD-Mandanten-ID fest.
3.    Legen Sie für Ihre Tokenanforderung für **scope** den Wert **"`https://sts.<account-domain>//.default`"** fest, wobei `<account-domain>` durch die **Kontodomäne** für Ihr Azure Spatial Anchors-Konto ersetzt wird. Ein Beispiel für ein Azure Spatial Anchors-Konto in der Kontodomäne „USA, Osten 2“ ist **"`https://sts.mixedreality.azure.com//.default`"** .

Nachdem Sie diese Schritte durchgeführt haben, kann Ihr Back-End-Dienst ein Azure AD-Token abrufen. Der Dienst kann dieses Token gegen ein MR-Token austauschen, das er dann an den Client zurückgibt. Der Abruf eines MR-Tokens mithilfe eines Azure AD-Tokens erfolgt über einen REST-Aufruf. Hier sehen Sie einen Beispielaufruf:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Der Autorisierungsheader ist hierbei als `Bearer <Azure_AD_token>` formatiert.

Die Antwort enthält das MR-Token im Klartext.

Dieses MR-Token wird an den Client zurückgegeben. Ihre Client-App kann dieses dann als Zugriffstoken in der Cloudsitzungskonfiguration festlegen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Damit Sie die Zugriffsebene für Anwendungen, Dienste oder Azure AD-Benutzer Ihres Diensts besser steuern können, können Sie diese bereits vorhandenen Rollen nach Bedarf für Ihre Azure Spatial Anchors-Konten zuweisen:

- **Spatial Anchors-Kontobesitzer**. Anwendungen oder Benutzer mit dieser Rolle können Raumanker erstellen, abfragen und löschen. Wenn Sie sich mit Kontoschlüsseln bei Ihrem Konto authentifizieren, wird dem authentifizierten Prinzipal die Rolle „Spatial Anchors-Kontobesitzer“ zugewiesen.
- **Spatial Anchors-Kontomitwirkender**. Anwendungen oder Benutzer mit dieser Rolle können Raumanker erstellen, abfragen, aber nicht löschen.
- **Spatial Anchors-Kontoleser**. Anwendungen oder Benutzer mit dieser Rolle können Raumanker nur abfragen. Sie können keine neuen erstellen, vorhandene löschen oder deren Metadaten aktualisieren. Diese Rolle wird in der Regel für Anwendungen verwendet, bei denen einige Benutzer die Umgebung zusammenstellen, während andere nur Anker zurückrufen können, die zuvor in dieser Umgebung platziert waren.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihre erste App mit Azure Spatial Anchors:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
