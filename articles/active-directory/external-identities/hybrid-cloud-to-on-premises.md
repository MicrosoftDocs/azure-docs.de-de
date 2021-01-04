---
title: Gewähren des Zugriffs auf lokale Apps für B2B-Benutzer – Azure AD
description: Beschreibt, wie Sie B2B-Cloudbenutzern mit Azure AD B2B-Zusammenarbeit Zugriff auf lokale Apps gewähren.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131019"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD

Wenn Ihr Unternehmen Funktionen der Azure Active Directory B2B-Zusammenarbeit (Azure AD) verwendet, um Gastbenutzer aus Partnerunternehmen zu Ihrem Azure AD einzuladen, können Sie für diese B2B-Benutzer jetzt Zugriff auf lokale Apps bereitstellen. Diese lokalen Apps können die SAML-basierte Authentifizierung oder die integrierte Windows-Authentifizierung (Integrated Windows Authentication, IWA) mit eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) verwenden.

## <a name="access-to-saml-apps"></a>Zugriff auf SAML-Apps

Wenn Ihre lokalen Apps die SAML-basierte Authentifizierung verwenden, können Sie diese Apps ganz einfach über das Azure-Portal für Ihre Benutzer in Azure AD B2B-Zusammenarbeit verfügbar machen.

Dazu müssen Sie die folgenden zwei Schritte ausführen:

- Integrieren Sie die App unter Verwendung von SAML, wie unter [Konfigurieren des SAML-basierten einmaligen Anmeldens](../manage-apps/configure-saml-single-sign-on.md) beschrieben. Notieren Sie unbedingt den Wert, den Sie für **Anmelde-URL** angeben.
-  Verwenden Sie den Azure AD-Anwendungsproxy, um die lokale App mit **Azure Active Directory** als Authentifizierungsquelle zu veröffentlichen. Anweisungen dazu finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](../manage-apps/application-proxy-add-on-premises-application.md). 

   Verwenden Sie beim Konfigurieren der Einstellung **Interne URL** die Anmelde-URL, die Sie in der Vorlage „Nicht-Kataloganwendung“ angegeben haben. Auf diese Weise können Benutzer außerhalb der Unternehmensgrenzen auf die App zugreifen. Der Anwendungsproxy führt das SAML-basierte einmalige Anmelden für die lokale App aus.
 
   ![Zeigt die Einstellungen der lokalen App für die interne URL und Authentifizierung](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Zugriff auf IWA- und KCD-Apps

Um B2B-Benutzern den Zugriff auf lokale Anwendungen zu ermöglichen, die durch die integrierte Windows-Authentifizierung und eingeschränkte Kerberos-Delegierung geschützt sind, benötigen Sie die folgenden Komponenten:

- **Authentifizierung über den Azure AD-Anwendungsproxy**. B2B-Benutzer müssen sich bei der lokalen Anwendung authentifizieren können. Zu diesem Zweck müssen Sie die lokale App über den Azure AD-Anwendungsproxy veröffentlichen. Weitere Informationen finden Sie im [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy](../manage-apps/application-proxy-add-on-premises-application.md).
- **Autorisierung über ein B2B-Benutzerobjekt im lokalen Verzeichnis**. Die Anwendung muss Zugriffsüberprüfungen für Benutzer ausführen können und ihnen Zugriff auf die richtigen Ressourcen gewähren. IWA und KCD erfordern für diese Autorisierung ein Benutzerobjekt im lokalen Windows Server Active Directory-Verzeichnis. Der Anwendungsproxy benötigt dieses Benutzerobjekt, um die Identität des Benutzers anzunehmen und ein Kerberos-Token für die App abzurufen (siehe [So funktioniert das einmalige Anmelden mit KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)). 

   > [!NOTE]
   > Stellen Sie beim Konfigurieren des Azure AD-Anwendungsproxys sicher, dass **Delegierte Identität für Anmeldung** in der Konfiguration des einmaligen Anmeldens für die integrierte Windows-Authentifizierung (IWA) auf **Benutzerprinzipalname** (Standard) festgelegt ist.

   Für das B2B-Benutzerszenario sind zwei Methoden verfügbar, die Sie zum Erstellen der für die Autorisierung erforderlichen Gastbenutzerobjekte im lokalen Verzeichnis verwenden können:

   - Microsoft Identity Manager (MIM) und der MIM-Verwaltungs-Agent für Microsoft Graph. 
   - [Ein PowerShell-Skript](#create-b2b-guest-user-objects-through-a-script-preview). Die Verwendung des Skripts ist die einfachere Lösung, bei der kein MIM benötigt wird. 

Das folgende Diagramm bietet einen allgemeinen Überblick darüber, wie der Azure AD-Anwendungsproxy und die Generierung des B2B-Benutzerobjekts im lokalen Verzeichnis zusammenarbeiten, um B2B-Benutzern Zugriff auf Ihre lokalen IWA- und KCD-Apps zu gewähren. Die nummerierten Schritte werden unter dem Diagramm detailliert beschrieben.

![Diagramm von Lösungen mit MIM und B2B-Skript](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Ein Benutzer aus einem Partnerunternehmen (der Mandant „Fabrikam“) wird zum Mandanten „Contoso“ eingeladen.
2.  Ein Gastbenutzerobjekt wird im Mandanten „Contoso“ erstellt (beispielsweise ein Benutzerobjekt mit dem UPN „guest_fabrikam.com#EXT#“@contoso.onmicrosoft.com).
3.  Der Gast „Fabrikam“ wird über MIM oder das B2B-PowerShell-Skript aus Contoso importiert.
4.  Eine Darstellung bzw. ein „Profil“ des Gastbenutzerobjekts „Fabrikam“ (Guest#EXT#) wird im lokalen Verzeichnis (contoso.com) über MIM oder das B2B-PowerShell-Skript erstellt.
5.  Der Gastbenutzer greift auf die lokale Anwendung „app.contoso.com“ zu.
6.  Die Authentifizierungsanforderung wird mithilfe der eingeschränkten Kerberos-Delegierung über den Anwendungsproxy autorisiert. 
7.  Da das Gastbenutzerobjekt lokal vorhanden ist, ist die Authentifizierung erfolgreich.

### <a name="lifecycle-management-policies"></a>Richtlinien für die Lebenszyklusverwaltung

Sie können die lokalen B2B-Benutzerobjekte mithilfe von Richtlinien für die Lebenszyklusverwaltung verwalten. Beispiel:

- Sie können Richtlinien für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für den Gastbenutzer einrichten, damit während der Anwendungsproxy-Authentifizierung MFA verwendet wird. Weitere Informationen finden Sie unter [Bedingter Zugriff für Benutzer der B2B-Zusammenarbeit](conditional-access.md).
- Alle Sponsorships, Zugriffsüberprüfungen, Kontoüberprüfungen usw., die für die B2B-Cloudbenutzer ausgeführt werden, gelten für die lokalen Benutzer. Wenn der Cloudbenutzer beispielsweise durch Ihre Richtlinien für die Lebenszyklusverwaltung gelöscht wird, wird der lokale Benutzer ebenfalls durch die MIM-Synchronisierung oder Azure AD Connect-Synchronisierung gelöscht. Weitere Informationen finden Sie unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Erstellen von B2B-Gastbenutzerobjekten über MIM

Informationen zur Verwendung von MIM 2016 Service Pack 1 und des MIM-Verwaltungs-Agents für Microsoft Graph zum Erstellen von Gastbenutzerobjekten im lokalen Verzeichnis finden Sie unter [Azure AD-Business-to-Business-Kollaboration (B2B) mit Microsoft Identity Manager(MIM) 2016 SP1 mit Azure-Anwendungsproxy](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Erstellen von B2B-Gastbenutzerobjekten über ein Skript (Vorschau)

Es ist ein PowerShell-Beispielskript verfügbar, das Sie als Ausgangspunkt für die Erstellung von Gastbenutzerobjekten in Ihrem lokalen Active Directory-Verzeichnis verwenden können.

Sie können das Skript und die Infodatei unter [Connectors für Microsoft Identity Manager 2016 und Forefront Identity Manager 2010 R2](https://www.microsoft.com/download/details.aspx?id=51495) herunterladen. Wählen Sie im Downloadpaket die Datei **Script and Readme to pull Azure AD B2B users on-prem.zip** aus.

Lesen Sie vor der Verwendung des Skripts unbedingt die Voraussetzungen und wichtigen Hinweise in der zugehörigen Infodatei. Beachten Sie außerdem, dass das Skript nur als Beispiel zur Verfügung gestellt wird. Ihr Entwicklerteam oder ein Partner muss das Skript vor der Ausführung anpassen und überprüfen.

## <a name="license-considerations"></a>Hinweise zur Lizenz

Stellen Sie sicher, dass Sie über die richtigen Clientzugriffslizenzen (Client Access License, CAL) für externe Gastbenutzer verfügen, die auf lokale Apps zugreifen. Weitere Informationen finden Sie im Abschnitt zu externen Connector-Lizenzen unter [Client Access-Lizenzen und Management-Lizenzen](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Wenden Sie sich bezüglich Ihrer spezifischen Lizenzierungsanforderungen an den für Sie zuständigen Microsoft-Kundendienstmitarbeiter oder Ihren Handelspartner vor Ort.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Active Directory B2B-Zusammenarbeit für Hybridorganisationen](hybrid-organizations.md)

- Eine Übersicht zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).