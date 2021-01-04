---
title: Probleme beim Anmelden bei für SAML-basiertes einmaliges Anmelden konfigurierten Apps
description: Leitfaden zu möglichen Fehlern beim Anmelden bei einer Anwendung, die für SAML-basiertes einmaliges Anmelden im Verbund mit Azure Active Directory konfiguriert wurde
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: e6d3674aaa4c89f42c2e7b87a6be6cdc8ecabf64
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028273"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Probleme beim Anmelden bei für SAML-basiertes einmaliges Anmelden konfigurierten Apps
Zur Behandlung der unten aufgeführten Anmeldeprobleme werden folgende Vorgehensweisen empfohlen, um bessere Diagnosen zu erzielen und Lösungsschritte zu automatisieren:

- Installieren Sie die [Browsererweiterung zur sicheren Anmeldung für„Meine Apps“](./access-panel-deployment-plan.md), um Azure Active Directory (Azure AD) dabei zu unterstützen, bessere Diagnosen und Lösungen bereitzustellen, wenn Sie die Testumgebung im Azure-Portal nutzen.
- Reproduzieren Sie den Fehler mithilfe der Testfunktionen auf der App-Konfigurationsseite im Azure-Portal. Weitere Informationen finden Sie unter [Debuggen von SAML-basierten Anwendungen für einmaliges Anmelden](./debug-saml-sso-issues.md).

Wenn Sie die [Testumgebung](./debug-saml-sso-issues.md) im Azure-Portal mit der sicheren Browsererweiterung für „Meine Apps“ verwenden, müssen Sie die folgenden Schritte nicht manuell ausführen, um die Seite zur Konfiguration des SAML-basierten einmaligen Anmeldens zu öffnen.

So öffnen Sie die Seite für die Konfiguration des SAML-basierten einmaligen Anmeldens:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Admin** an.
1.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
1.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.
1.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.
1.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.
    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.
1.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.
1. Nachdem die Anwendung geladen wurde, wählen Sie im linken Navigationsmenü der Anwendung die Option **Einmaliges Anmelden** aus.
1. Wählen Sie die Option für SAML-basiertes einmaliges Anmelden aus.

## <a name="application-not-found-in-directory"></a>Die Anwendung wurde im Verzeichnis nicht gefunden.
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Mögliche Ursache**

Das `Issuer`-Attribut, das in der SAML-Anforderung von der Anwendung an Azure AD gesendet wird, entspricht nicht dem Bezeichnerwert, der für die Anwendung in Azure AD konfiguriert wurde.

**Lösung**

Stellen Sie sicher, dass das `Issuer`-Attribut in der SAML-Anforderung mit dem in Azure AD konfigurierten Bezeichnerwert übereinstimmt.

Überprüfen Sie auf der Konfigurationsseite für das SAML-basierte einmalige Anmelden im Abschnitt **Grundlegende SAML-Konfiguration**, ob der Wert im Textfeld „Bezeichner“ mit dem im Fehler angezeigten Bezeichnerwert übereinstimmt.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Die Antwortadresse stimmt nicht mit den für die Anwendung konfigurierten Antwortadressen überein
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Mögliche Ursache**

Der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung stimmt nicht mit dem in Azure AD konfigurierten Wert der Antwort-URL oder mit dem Muster überein. Der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung ist die URL in der Fehlermeldung.

**Lösung**

Stellen Sie sicher, dass der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung dem in Azure AD konfigurierten Wert der Antwort-URL entspricht. 

Vergewissern Sie sich, dass der Wert im Textfeld für die Antwort-URL dem `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung entspricht, oder aktualisieren Sie ihn entsprechend.   
    
Nachdem Sie den Wert der Antwort-URL in Azure AD aktualisiert haben und er mit dem Wert übereinstimmt, der in der SAML-Anforderung von der Anwendung gesendet wurde, sollten Sie sich bei der Anwendung anmelden können.

## <a name="user-not-assigned-a-role"></a>Benutzer wurde keine Rolle zugewiesen
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Mögliche Ursache**

Dem Benutzer wurde kein Zugriff auf die Anwendung in Azure AD erteilt.

**Lösung**

Um einer Anwendung Benutzer direkt zuzuweisen, lesen Sie folgenden Artikel: [Schnellstart: Zuweisen von Benutzern zu einer App](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Keine gültige SAML-Anforderung
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Mögliche Ursache**

Azure AD unterstützt die von der Anwendung für einmaliges Anmelden gesendete SAML-Anforderung nicht. Häufige Probleme sind beispielsweise die folgenden:
- Fehlende erforderliche Felder in der SAML-Anforderung
- Codierte Methode in der SAML-Anforderung

**Lösung**

1. Erfassen Sie die SAML-Anforderung. Im Tutorial [Debug SAML-based single sign-on to applications in Azure Active Directory (Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure AD)](./debug-saml-sso-issues.md) erhalten Sie Informationen zum Erfassen der SAML-Anforderung.
1. Wenden Sie sich an den Anwendungshersteller, und teilen Sie diesem folgende Informationen mit:
    - SAML-Anforderung
    - [Anforderungen des SAML-Protokolls für einmaliges Anmelden bei Azure AD](../develop/single-sign-on-saml-protocol.md)

Der Anwendungshersteller sollte bestätigen, dass er die SAML-Implementierung in Azure AD für einmaliges Anmelden unterstützt.

## <a name="misconfigured-application"></a>Falsch konfigurierte Anwendung
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Mögliche Ursache**

Das `Issuer`-Attribut, das in der SAML-Anforderung von der Anwendung an Azure AD gesendet wird, entspricht nicht dem Bezeichnerwert, der für die Anwendung in Azure AD konfiguriert wurde.

**Lösung**

Stellen Sie sicher, dass das `Issuer`-Attribut in der SAML-Anforderung mit dem in Azure AD konfigurierten Bezeichnerwert übereinstimmt. 

Stellen Sie sicher, dass der Wert im Textfeld „Bezeichner“ mit dem in der Fehlermeldung angezeigten Bezeichnerwert übereinstimmt.

## <a name="certificate-or-key-not-configured"></a>Zertifikat oder Schlüssel nicht konfiguriert
`Error AADSTS50003: No signing key configured.`

**Mögliche Ursache**

Das Anwendungsobjekt ist beschädigt, und Azure AD erkennt das für die Anwendung konfigurierte Zertifikat nicht.

**Lösung**

Um das Zertifikat zu löschen und ein neues zu erstellen, führen Sie die folgenden Schritte aus:
1. Wählen Sie im Konfigurationsbildschirm für das SAML-basierte einmalige Anmelden im Abschnitt **SAML-Signaturzertifikat** die Option **Neues Zertifikat erstellen** aus.
1. Wählen Sie das Ablaufdatum aus, und klicken Sie anschließend auf **Speichern**.
1. Aktivieren Sie **Neues Zertifikat aktivieren**, um das aktive Zertifikat außer Kraft zu setzen. Klicken Sie oben im Bereich auf **Speichern**, und akzeptieren Sie die Aktivierung des Rolloverzertifikats.
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Entfernen**, um das **nicht verwendete** Zertifikat zu entfernen.

## <a name="saml-request-not-present-in-the-request"></a>SAML-Anforderung nicht in der Anforderung vorhanden
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Mögliche Ursache**

Azure AD konnte die SAML-Anforderung in den URL-Parametern in der HTTP-Anforderung nicht identifizieren. Dies kann vorkommen, wenn die Anwendung keine HTTP-Umleitungsbindung beim Senden der SAML-Anforderung an Azure AD verwendet.

**Lösung**

Die Anwendung muss die SAML-Anforderung codiert im Adressheader unter Verwendung der HTTP-Umleitungsbindung senden. Weitere Informationen zur Implementierung finden Sie im Abschnitt zur HTTP-Umleitungsbindung in den [SAML-Protokollspezifikationen](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD sendet das Token an einen falschen Endpunkt
**Mögliche Ursache**

Wenn die Anmeldeanforderung während des einmaligen Anmeldens keine explizite Antwort-URL (Assertionsverbraucherdienst-URL) enthält, wählt Azure AD eine der konfigurierten Antwort-URLs für diese Anwendung aus. Auch wenn für die Anwendung eine explizite Antwort-URL konfiguriert wurde, wird der Benutzer möglicherweise zu https://127.0.0.1:444 umgeleitet. 

Wenn die Anwendung als Nicht-Katalog-App hinzugefügt wurde, hat Azure Active Directory die Antwort-URL als Standardwert erstellt. Dieses Verhalten wurde geändert, und Azure Active Directory fügt die URL nicht mehr standardmäßig hinzu. 

**Lösung**

Löschen Sie nicht verwendete Antwort-URLs, die für die Anwendung konfiguriert sind.

Löschen Sie auf der Konfigurationsseite für das SAML-basierte einmalige Anmelden im Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** nicht verwendete oder standardmäßige Antwort-URLs, die vom System erstellt wurden. Beispiel: `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Die Authentifizierungsmethode, mit der sich der Benutzer beim Dienst authentifiziert hat, stimmt nicht mit der angeforderten Authentifizierungsmethode überein.
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Mögliche Ursache**

Der `RequestedAuthnContext` ist in der SAML-Anforderung enthalten. Das bedeutet, dass in der Anwendung der von `AuthnContextClassRef` angegebene `AuthnContext` erwartet wird. Der Benutzer hat sich jedoch bereits vor dem Zugriff auf die Anwendung authentifiziert, und der `AuthnContext` (Authentifizierungsmethode), der für die vorherige Authentifizierung verwendet wurde, unterscheidet sich von der angeforderten Authentifizierungsmethode. Beispielsweise ist der Zugriff eines Verbundbenutzers auf „myapps“ und „WIA“ erfolgt. `AuthnContextClassRef` ist `urn:federation:authentication:windows`. In AAD wird keine neue Authentifizierungsanforderung durchgeführt. Stattdessen wird der Authentifizierungskontext verwendet, der über den Identitätsanbieter (in diesem Fall AD FS oder ein anderer Verbunddienst) weitergeleitet wurde. Daher besteht keine Übereinstimmung, wenn die Anwendung nicht `urn:federation:authentication:windows` anfordert. Ein anderes Szenario ist die Verwendung von MultiFactor: `'X509, MultiFactor`.

**Auflösung**


`RequestedAuthnContext` ist ein optionaler Wert. Überprüfen Sie in der Anwendung, ob dieser Wert entfernt werden kann.

Eine andere Möglichkeit besteht darin, dafür zu sorgen, dass `RequestedAuthnContext` berücksichtigt wird. Dies erfolgt durch Anfordern einer neuen Authentifizierung. Bei der Verarbeitung der SAML-Anforderung wird dadurch eine neue Authentifizierung durchgeführt, bei der `AuthnContext` berücksichtigt wird. Damit eine neue Authentifizierung angefordert wird, muss die SAML-Anforderung den Wert `forceAuthn="true"` enthalten. 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem beim Anpassen der SAML-Ansprüche, die an eine Anwendung gesendet werden
Informationen zum Anpassen der SAML-Attributsansprüche, die an Ihre Anwendung gesendet werden, finden Sie unter [Anspruchszuordnung in Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Fehler im Zusammenhang mit falsch konfigurierten Apps
Stellen Sie sicher, dass die Konfigurationen im Portal mit der App übereinstimmen. Vergleichen Sie insbesondere Client-/Anwendungs-ID, Antwort-URLs, Clientgeheimnisse/-schlüssel und App-ID-URI.

Vergleichen Sie die Ressource, auf die Sie zugreifen möchten, im Code mit den konfigurierten Berechtigungen auf der Registerkarte **Erforderliche Ressourcen**, um sicherzustellen, dass Sie nur Ressourcen anfordern, die Sie konfiguriert haben.

## <a name="next-steps"></a>Nächste Schritte
- [Schnellstartserie zur Anwendungsverwaltung](add-application-portal-assign-users.md)
- [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure AD](./debug-saml-sso-issues.md)
- [Anforderungen des SAML-Protokolls für einmaliges Anmelden bei Azure AD](../develop/single-sign-on-saml-protocol.md)