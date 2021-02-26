---
title: App-Schutzrichtlinien mit bedingtem Zugriff – Azure Active Directory
description: Erfahren Sie, wie Sie die App-Schutzrichtlinie für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs in Azure Active Directory erzwingen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575770"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Gewusst wie: Erzwingen einer App-Schutzrichtlinie und einer genehmigten Client-App für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs

Viele Menschen verwenden mobile Geräte sowohl für private als auch für berufliche Zwecke. Organisationen möchten natürlich sicherstellen, dass die Mitarbeiter produktiv arbeiten können, aber gleichzeitig auch verhindern, dass Datenverluste durch potenziell unsichere Anwendungen vermieden werden. Mit bedingtem Zugriff können Organisationen den Zugriff auf genehmigte (modern authentifizierungsfähige) Client-Apps mithilfe von Intune-App-Schutzrichtlinien einschränken, die auf solche Apps angewandt werden.

Dieser Artikel enthält drei Szenarien zum Konfigurieren von Richtlinien für den bedingten Zugriff auf Ressourcen wie Microsoft 365, Exchange Online und SharePoint.

- [Szenario 1: Microsoft 365-Apps erfordern genehmigte Apps mit App-Schutzrichtlinien.](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Szenario 2: Browser-Apps erfordern genehmigte Apps mit App-Schutzrichtlinien.](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Szenario 3: Exchange Online und SharePoint erfordern eine genehmigte Client-App und eine App-Schutzrichtlinie.](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

Bei bedingtem Zugriff sind diese Client-Apps bekanntermaßen durch eine App-Schutzrichtlinie geschützt. Weitere Informationen zu App-Schutzrichtlinien finden Sie im Artikel [Übersicht über App-Schutzrichtlinien](/intune/apps/app-protection-policy).

> [!WARNING]
> Nicht alle Anwendungen werden als genehmigte Anwendungen unterstützt oder unterstützen Anwendungsschutzrichtlinien. Eine Liste der qualifizierten Client-Apps finden Sie unter [App-Schutzrichtlinie als Voraussetzung](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> Die Option „Eine der ausgewählten Steuerungen anfordern“ unter den Zugriffserteilungssteuerungen funktioniert wie eine ODER-Klausel. Sie wird innerhalb einer Richtlinie verwendet, um Benutzern die Verwendung von Apps zu ermöglichen, welche die Zugriffserteilungssteuerungen **App-Schutzrichtlinie erforderlich** bzw. **Genehmigte Client-App erforderlich** unterstützen. **App-Schutzrichtlinie erforderlich** wird erzwungen, wenn die App dieses Gewährungssteuerungselement unterstützt. Weitere Informationen zu den Apps, welche die Zugriffserteilungssteuerung **App-Schutzrichtlinie erforderlich** unterstützen, finden Sie unter [App-Schutzrichtlinie als Voraussetzung](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Szenario 1: Microsoft 365-Apps erfordern genehmigte Apps mit App-Schutzrichtlinien.

In diesem Szenario hat Contoso beschlossen, dass der gesamte mobile Zugriff auf Microsoft 365-Ressourcen genehmigte Client-Apps wie Outlook Mobile oder OneDrive erfordert, die durch eine App-Schutzrichtlinie geschützt sind. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden Schritte ausführen, um die Verwendung einer genehmigten Client-App auf mobilen Geräten zu erzwingen.

**Schritt 1: Konfigurieren einer Azure AD-Richtlinie für bedingten Zugriff für Microsoft 365**

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Klicken Sie unter **Bedingungen** auf **Client-Apps**.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Mobile Apps und Desktopclients** aus, und deaktivieren Sie alle anderen Optionen.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die folgenden Optionen aus:
   - **Genehmigte Client-App erforderlich**
   - **App-Schutzrichtlinie erforderlich**
   - **Eine der ausgewählten Steuerungen anfordern**
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 2: Konfigurieren einer Azure AD-Richtlinie für bedingten Zugriff für Exchange Online mit ActiveSync (EAS)**

Für die Richtlinie zum bedingten Zugriff in diesem Schritt konfigurieren Sie folgende Komponenten:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365 Exchange Online** aus.
1. Klicken Sie unter **Bedingungen** auf **Client-Apps**:
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Exchange ActiveSync-Clients** aus, und deaktivieren Sie alle anderen Optionen.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die Option **Zugriff erteilen**, **App-Schutzrichtlinie erforderlich** aus, und wählen Sie dann **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 3: Konfigurieren der Intune-App-Schutzrichtlinie für iOS- und Android-Clientanwendungen**

Die Schritte zum Erstellen von App-Schutzrichtlinien für Android und iOS finden Sie im Artikel [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/apps/app-protection-policies). 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Szenario 2: Browser-Apps erfordern genehmigte Apps mit App-Schutzrichtlinien.

In diesem Szenario hat Contoso entschieden, dass der gesamte mobile Webbrowserzugriff auf Microsoft 365-Ressourcen genehmigte Client-Apps wie Edge für iOS und Android erfordert, die durch eine App-Schutzrichtlinie geschützt sind. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden Schritte ausführen, um die Verwendung einer genehmigten Client-App auf mobilen Geräten zu erzwingen.

**Schritt 1: Konfigurieren einer Azure AD-Richtlinie für bedingten Zugriff für Microsoft 365**

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Klicken Sie unter **Bedingungen** auf **Client-Apps**.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Browser** aus, und deaktivieren Sie alle anderen Optionen.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die folgenden Optionen aus:
   - **Genehmigte Client-App erforderlich**
   - **App-Schutzrichtlinie erforderlich**
   - **Eine der ausgewählten Steuerungen anfordern**
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 2: Konfigurieren der Intune-App-Schutzrichtlinie für iOS- und Android-Clientanwendungen**

Die Schritte zum Erstellen von App-Schutzrichtlinien für Android und iOS finden Sie im Artikel [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/apps/app-protection-policies). 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Szenario 3: Exchange Online und SharePoint erfordern eine genehmigte Client-App und eine App-Schutzrichtlinie.

In diesem Szenario hat Contoso beschlossen, dass Benutzer auf mobilen Geräten nur auf E-Mail- und SharePoint-Daten zugreifen dürfen, sofern sie eine genehmigte Client-App wie Outlook Mobile verwenden, die vor dem Erteilen des Zugriffs durch eine App-Schutzrichtlinie geschützt wird. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden drei Schritte ausführen, um die Verwendung einer genehmigten Client-App auf mobilen Geräten und von Exchange ActiveSync-Clients zu erzwingen.

**Schritt 1: Richtlinie für auf Android und iOS basierende moderne Authentifizierungsclients, die die Verwendung einer genehmigten Client-App-Schutzrichtlinie für den Zugriff auf Exchange Online und SharePoint erfordern.**

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Optionen **Office 365 Exchange Online** und **Office 365 SharePoint Online** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Klicken Sie unter **Bedingungen** auf **Client-Apps**.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Mobile Apps und Desktopclients** aus, und deaktivieren Sie alle anderen Optionen.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die folgenden Optionen aus:
   - **Genehmigte Client-App erforderlich**
   - **App-Schutzrichtlinie erforderlich**
   - **Eine der ausgewählten Steuerungen anfordern**
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 2: Richtlinie für Exchange ActiveSync-Clients, die die Verwendung einer genehmigten Client-App erfordern.**

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365 Exchange Online** aus.
1. Klicken Sie unter **Bedingungen** auf **Client-Apps**:
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Exchange ActiveSync-Clients** aus, und deaktivieren Sie alle anderen Optionen.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die Option **Zugriff erteilen**, **App-Schutzrichtlinie erforderlich** aus, und wählen Sie dann **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 3: Konfigurieren der Intune-App-Schutzrichtlinie für iOS- und Android-Clientanwendungen**

Die Schritte zum Erstellen von App-Schutzrichtlinien für Android und iOS finden Sie im Artikel [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/apps/app-protection-policies). 

## <a name="next-steps"></a>Nächste Schritte

[Was ist bedingter Zugriff?](overview.md)

[Bedingter Zugriff auf Komponenten](concept-conditional-access-policies.md)

[Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

