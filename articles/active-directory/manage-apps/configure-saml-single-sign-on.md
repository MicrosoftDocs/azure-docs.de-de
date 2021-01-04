---
title: Grundlegendes zum SAML-basierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory
description: Grundlegendes zum SAML-basierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: ec020ecd4c2bcf6e9186afb3d2c4a79ef235c371
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658909"
---
# <a name="understand-saml-based-single-sign-on"></a>Grundlegendes zum SAML-basierten einmaligen Anmelden

In der [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung haben Sie gelernt, wie Sie Azure AD als Identitätsanbieter (Identity Provider, IdP) für eine Anwendung verwenden. In diesem Artikel erfahren Sie mehr über die SAML-basierte Option für einmaliges Anmelden. 


## <a name="before-you-begin"></a>Voraussetzungen

Die Verwendung von Azure AD als Identitätsanbieter und das Konfigurieren des einmaligen Anmeldens (Single Sign-on, SSO) kann je nach verwendeter Anwendung einfach oder komplex sein. Einige Anwendungen können mit nur wenigen Aktionen konfiguriert werden. Andere erfordern eine umfassende Konfiguration. Führen Sie die [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung durch, um Ihre Kenntnisse zügig zu vertiefen. Wenn die hinzugefügte Anwendung einfach ist, müssen Sie diesen Artikel wahrscheinlich nicht lesen. Wenn die hinzugefügte Anwendung eine benutzerdefinierte Konfiguration für SAML-basiertes SSO erfordert, ist dieser Artikel für Sie vorgesehen.

In der [Schnellstartserie](add-application-portal-setup-sso.md) finden Sie einen Artikel zum Konfigurieren des einmaligen Anmeldens. Darin erfahren Sie, wie Sie auf die SAML-Konfigurationsseite für eine App zugreifen. Die SAML-Konfigurationsseite enthält fünf Abschnitte. Diese Abschnitte werden in diesem Artikel erläutert.

> [!IMPORTANT] 
> Es gibt einige Szenarien, in denen die Option **Einmaliges Anmelden** in der Navigation für eine Anwendung in **Unternehmensanwendungen** nicht vorhanden ist. 
>
> Wenn die Anwendung mithilfe von **App-Registrierungen** registriert wurde, wird die Funktion zum einmaligen Anmelden so konfiguriert, dass standardmäßig OIDC OAuth verwendet wird. In diesem Fall wird die Option **Einmaliges Anmelden** in der Navigation unter **Unternehmensanwendungen** nicht angezeigt. Wenn Sie **App-Registrierungen** zum Hinzufügen Ihrer benutzerdefinierten App verwenden, konfigurieren Sie die Optionen in der Manifestdatei. Weitere Informationen zur Manifestdatei finden Sie unter [Azure Active Directory-App-Manifest](../develop/reference-app-manifest.md). Weitere Informationen zu SSO-Standards finden Sie unter [Authentifizierung und Autorisierung mit der Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Zu den Szenarien, in denen ebenfalls **Einmaliges Anmelden** in der Navigation fehlt, zählen auch folgende Beispiele: Eine Anwendung wird in einem anderen Mandanten gehostet. Ihr Konto verfügt nicht über die erforderlichen Berechtigungen (globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals). Berechtigungen können auch zu einem Szenario führen, in dem Sie die Option **Einmaliges Anmelden**  zwar öffnen, aber nicht speichern können. Weitere Informationen zu administrativen Rollen in Azure AD finden Sie unter https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).


## <a name="basic-saml-configuration"></a>Grundlegende SAML-Konfiguration

Sie sollten die Werte vom Hersteller der Anwendung erhalten. Sie können die Werte manuell eingeben oder eine Metadatendatei hochladen, um die Werte der Felder zu extrahieren.

> [!TIP]
> Viele Apps wurden bereits für die Zusammenarbeit mit Azure AD vorkonfiguriert. Diese Apps werden im App-Katalog aufgelistet, den Sie durchsuchen können, wenn Sie eine App in Ihrem Azure AD-Mandanten hinzufügen. In der [Schnellstartserie](add-application-portal-setup-sso.md) werden Sie auch durch diesen Vorgang geführt. Für die Apps im Katalog erhalten Sie ausführliche Schrittanleitungen. Um die Schritte anzuzeigen, klicken Sie auf den Link auf der SAML-Konfigurationsseite für die App, wie in der Schnellstartserie beschrieben. Sie können auch eine Liste mit allen Tutorials zur App-Konfiguration unter [Tutorials zur SaaS-App-Konfiguration](../saas-apps/tutorial-list.md) durchsuchen.

| Einstellung für die grundlegende SAML-Konfiguration | Vom Dienstanbieter initiiert | Vom Identitätsanbieter initiiert | BESCHREIBUNG |
|:--|:--|:--|:--|
| **Bezeichner (Entitäts-ID)** | Für einige Apps erforderlich | Für einige Apps erforderlich | Hiermit wird die Anwendung eindeutig identifiziert. Azure AD sendet den Bezeichner als Audience-Parameter des SAML-Tokens an die Anwendung. Von der Anwendung wird erwartet, dass sie diesen Parameter überprüft. Dieser Wert ist auch als Entitäts-ID in SAML-Metadaten enthalten, die von der Anwendung bereitgestellt werden. Geben Sie eine URL ein, die das folgende Muster verwendet: https://<subdomain>.contoso.com. *Sie finden diesen Wert als Element vom Typ **Issuer** (Aussteller) in der SAML-Anforderung **AuthnRequest**, die von der Anwendung gesendet wurde.* |
| **Antwort-URL** | Erforderlich | Erforderlich | Gibt an, ob die Anwendung den Empfang des SAML-Tokens erwartet. Die Antwort-URL wird auch als „Assertionsverbraucherdienst-URL“ (Assertion Consumer Service, ACS) bezeichnet. Sie können die zusätzlichen Antwort-URL-Felder verwenden, um mehrere Antwort-URLs anzugeben. Zusätzliche Antwort-URLs werden möglicherweise für mehrere Unterdomänen benötigt. Sie können auch zu Testzwecken mehrere Antwort-URLs gleichzeitig angeben (URL des lokalen Host und öffentliche URL). |
| **Anmelde-URL** | Erforderlich | Nicht angeben | Wenn ein Benutzer diese URL öffnet, wird er vom Dienstanbieter zur Authentifizierung und Anmeldung an Azure AD umgeleitet. Azure AD verwendet die URL, um die Anwendung über Microsoft 365 oder über die Azure AD-Funktion „Meine Apps“ zu starten. Ist das Feld leer, führt Azure AD das vom Identitätsanbieter initiierte einmalige Anmelden aus, wenn ein Benutzer die Anwendung über Microsoft 365, über die Azure AD-Funktion „Meine Apps“ oder über die Azure AD-SSO-URL startet.|
| **Relayzustand** | Optional | Optional | Mit dieser Option wird die Anwendung darüber informiert, wohin der Benutzer nach der Authentifizierung umgeleitet werden soll. In der Regel ist der Wert eine für die Anwendung gültige URL. Einige Anwendungen verwenden dieses Feld jedoch anders. Weitere Informationen erhalten Sie vom Anwendungshersteller.
| **Abmelde-URL** | Optional | Optional | Wird verwendet, um die SAML-Abmeldeantworten an die Anwendung zurückzusenden.

## <a name="user-attributes-and-claims"></a>Benutzerattribute und Ansprüche 

Wenn sich ein Benutzer bei der Anwendung authentifiziert, stellt Azure AD der Anwendung ein SAML-Token aus, das Informationen (so genannte „Ansprüche“) über den Benutzer enthält, die ihn eindeutig identifizieren. Zu diesen Informationen gehören standardmäßig der Benutzername, die E-Mail-Adresse, der Vorname und der Nachname des Benutzers. Möglicherweise müssen Sie diese Ansprüche anpassen, wenn die Anwendung beispielsweise bestimmte Anspruchswerte oder für **Name** ein anderes Format als den Benutzernamen erfordert. 

> [!IMPORTANT]
> Viele Apps sind bereits vorkonfiguriert und befinden sich im App-Katalog. Sie müssen sich bei diesen Apps keine Gedanken über das Festlegen von Benutzer- und Gruppenansprüchen machen. Die [Schnellstartserie](add-application-portal.md) führt Sie durch das Hinzufügen und Konfigurieren von Apps.


Der Bezeichnerwert der **eindeutigen Benutzer-ID (Namens-ID)** ist ein erforderlicher Anspruch und sehr wichtig. Der Standardwert ist *user.userprincipalname*. Mit der Benutzer-ID wird jeder Benutzer in der Anwendung eindeutig identifiziert. Beispiel: Ist die E-Mail-Adresse sowohl der Benutzername als auch der eindeutige Bezeichner, legen Sie den Wert auf *user.mail* fest.

Weitere Informationen zum Anpassen von SAML-Ansprüchen finden Sie unter [Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).

Weitere Informationen zum Hinzufügen neuer Ansprüche finden Sie unter [Hinzufügen anwendungsspezifischer Ansprüche](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) und zum Hinzufügen von Gruppenansprüchen unter [Konfigurieren von Gruppenansprüchen](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Weitere Möglichkeiten zum Anpassen des SAML-Tokens, das von Azure AD an Ihre Anwendung gesendet wird, finden Sie in den folgenden Ressourcen:
>- Informationen zum Erstellen benutzerdefinierter Rollen über das Azure-Portal finden Sie unter [Gewusst wie: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md).
>- Informationen zum Anpassen der Ansprüche über PowerShell finden Sie unter [Gewusst wie: Anpassen von in Token ausgegebenen Ansprüchen für eine bestimmte App in einem Mandanten (Vorschau)](../develop/active-directory-claims-mapping.md).
>- Informationen zum Konfigurieren optionaler Ansprüche für Ihre Anwendung durch Anpassen des Anwendungsmanifests finden Sie unter [Gewusst wie: Bereitstellen optionaler Ansprüche für Ihre Azure AD-App](../develop/active-directory-optional-claims.md).
>- Informationen zum Festlegen von Tokengültigkeitsdauer-Richtlinien für Aktualisierungstoken, Zugriffstoken, Sitzungstoken und ID-Token finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (Vorschau)](../develop/active-directory-configurable-token-lifetimes.md). Informationen zum Einschränken von Authentifizierungssitzungen mithilfe des bedingten Azure AD-Zugriffs finden Sie unter [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>SAML-Signaturzertifikat

Azure AD verwendet ein Zertifikat zum Signieren der SAML-Token, die an die Anwendung gesendet werden. Sie benötigen dieses Zertifikat, um die Vertrauensstellung zwischen Azure AD und der Anwendung zu konfigurieren. Einzelheiten zum Zertifikatsformat finden Sie in der SAML-Dokumentation der Anwendung. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) und [Erweiterte Optionen für die Zertifikatsignatur im SAML-Token für Katalog-Apps in Azure Active Directory](certificate-signing-options.md).

> [!IMPORTANT]
> Viele Apps sind bereits vorkonfiguriert und befinden sich im App-Katalog, sodass Sie sich keine Gedanken über Zertifikate machen müssen. Die [Schnellstartserie](add-application-portal.md) führt Sie durch das Hinzufügen und Konfigurieren von Apps.

In Azure AD können Sie das aktive Zertifikat im Base64- oder Raw-Format direkt von der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** herunterladen. Außerdem können Sie das aktive Zertifikat abrufen, indem Sie die XML-Datei mit den Anwendungsmetadaten herunterladen oder die App-Verbundmetadaten-URL verwenden. Führen Sie diese Schritte aus, um Ihre Zertifikate (aktiv oder inaktiv) anzuzeigen, zu erstellen oder herunterzuladen.

Beim Überprüfen eines Zertifikats sind einige allgemeine Aspekte zu berücksichtigen: 
   - *Das richtige Ablaufdatum.* Sie können ein Ablaufdatum konfigurieren, das bis zu drei Jahre in der Zukunft liegt.
   - *Ein aktiver Status des richtigen Zertifikats.* Wenn der Status **Inaktiv** lautet, ändern Sie diesen zu **Aktiv**. Um den Status zu ändern, klicken Sie mit der rechten Maustaste auf die Zeile des Zertifikats, und wählen Sie **Zertifikat als aktiv festlegen** aus.
   - *Die richtige Anmeldeoption und der entsprechende Algorithmus*.
   - *Die richtige(n) E-Mail-Adresse(n) für Benachrichtigungen*. Wenn sich das aktive Zertifikat dem Ablaufdatum nähert, sendet Azure AD eine Benachrichtigung an die in diesem Feld konfigurierte E-Mail-Adresse.

Manchmal müssen Sie das Zertifikat herunterladen. Achten Sie genau darauf, wo Sie es speichern! Um das Zertifikat herunterzuladen, wählen Sie eine der Optionen für Base64-Format, Raw-Format oder Verbundmetadaten-XML aus. Azure AD bietet auch die **App-Verbundmetadaten-URL**, unter der Sie auf die anwendungsspezifischen Metadaten zugreifen können (im Format `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`).

> [!NOTE]
> Bei Verwendung von https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} muss die Anwendung in der Lage sein, die Bytereihenfolge-Marke in der gerenderten XML-Datei zu verarbeiten. Bei Überprüfung der XML-Daten wird die Bytereihenfolge-Marke als nicht druckbares ASCII-Zeichen „»¿“ und im Hexadezimalformat als EF BB BF dargestellt.

Wenn Sie Änderungen am Zertifikat vornehmen möchten, wählen Sie die Schaltfläche „Bearbeiten“ aus. Sie können auf der Seite **SAML-Signaturzertifikat** verschiedene Aktionen ausführen:
   - Wenn Sie ein neues Zertifikat erstellen möchten, wählen Sie **Neues Zertifikat**, anschließend unter **Ablaufdatum** das Ablaufdatum und dann **Speichern** aus. Wenn Sie das Zertifikat aktivieren möchten, wählen Sie das Kontextmenü ( **...** ) und anschließend **Zertifikat als aktiv festlegen** aus.
   - Wenn Sie ein Zertifikat mit privatem Schlüssel und PFX-Anmeldeinformationen hochladen möchten, wählen Sie **Zertifikat importieren** aus, und navigieren Sie zu dem Zertifikat. Geben Sie unter **PFX-Kennwort** das PFX-Kennwort ein, und wählen Sie anschließend **Speichern** aus.  
   - Konfigurieren Sie die erweiterten Einstellungen für das Signieren des Zertifikats. Weitere Informationen zu diesen Optionen finden Sie unter [Erweiterte Optionen für die Zertifikatsignierung](certificate-signing-options.md).
   - Wenn weitere Personen darüber informiert werden sollen, dass das Ablaufdatum des aktiven Zertifikats bald erreicht ist, geben Sie die entsprechenden E-Mail-Adressen in die Felder für die **Benachrichtigungs-E-Mail-Adressen** ein.

## <a name="set-up-the-application-to-use-azure-ad"></a>Einrichten der Anwendung für die Verwendung von Azure AD

Im Abschnitt **Einrichten \<applicationName>** werden die Werte aufgeführt, die in der Anwendung konfiguriert werden müssen, damit Azure AD als SAML-Identitätsanbieter verwendet wird. Sie legen die Werte auf der Konfigurationsseite der Anwendungswebsite fest. Wenn Sie beispielsweise GitHub konfigurieren, wechseln Sie zur Website github.com und legen dort die Werte fest. Wenn die Anwendung bereits vorkonfiguriert ist und sich im Azure AD-Katalog befindet, finden Sie dort einen Link **Schrittanleitung anzeigen**. Andernfalls müssen Sie die Dokumentation für die Anwendung suchen, die Sie konfigurieren. 

Die Werte für **Anmelde-URL** und **Abmelde-URL** werden in den gleichen Endpunkt aufgelöst. Dabei handelt es sich um den Endpunkt, auf dem die SAML-Anforderungen für Ihren Azure AD-Mandanten verarbeitet werden. 

**Azure AD-Bezeichner** ist der Wert für den **Issuer** (Aussteller) in dem SAML-Token, das für die Anwendung ausgestellt wird.

## <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Sobald Sie Ihre Anwendung für die Verwendung von Azure AD als SAML-basiertem Identitätsanbieter konfiguriert haben, können Sie die Einstellungen testen, um zu überprüfen, ob das einmalige Anmelden für Konto funktioniert. 

Wählen Sie **Testen** aus, und verwenden Sie dann den aktuell angemeldeten Benutzer oder einen anderen Benutzer zum Testen. 

Ist die Anmeldung erfolgreich, können Sie Ihrer SAML-Anwendung Benutzer und Gruppen zuweisen. Glückwunsch!

Sollte eine Fehlermeldung angezeigt werden, gehen Sie wie folgt vor:

1. Kopieren Sie die Details, und fügen Sie sie ins Feld **Wie äußert sich der Fehler?** ein.

    ![Leitfaden zur Problemlösung abrufen](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Klicken Sie auf **Leitfaden zur Problemlösung abrufen**. Die Grundursache und Informationen zur Problemlösung werden angezeigt.  In diesem Beispiel wurde der Benutzer nicht der Anwendung zugewiesen.

3. Lesen Sie den Leitfaden zur Problemlösung, und beheben Sie nach Möglichkeit das Problem.

4. Führen Sie den Test erneut aus, bis der Vorgang erfolgreich abgeschlossen wird.

Weitere Informationen finden Sie unter [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Nächste Schritte

- [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
- [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md)
- [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md)