---
title: Erstellen von Apps, die Azure AD-Benutzer anmelden
titleSuffix: Microsoft identity platform
description: Es wird veranschaulicht, wie Sie eine mehrinstanzenfähige Anwendung erstellen, mit der ein Benutzer über jeden Azure Active Directory-Mandanten angemeldet werden kann.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 22d6cacc36363b17f9bc32d354982eb71974b31d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779752"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Gewusst wie: Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster

Wenn Sie vielen Organisationen eine Software-as-a-Service-Anwendung (SaaS) anbieten, können Sie Ihre Anwendung so konfigurieren, dass Anmeldungen von beliebigen Azure Active Directory-Mandanten (Azure AD) akzeptiert werden. Diese Konfiguration wird als *mehrinstanzenfähige Anwendung* bezeichnet. Benutzer eines Azure AD-Mandanten können sich bei Ihrer Anwendung anmelden, nachdem sie zugestimmt haben, ihr Konto mit Ihrer Anwendung zu verwenden.

Wenn Sie eine Anwendung besitzen, die mit einem eigenen Kontosystem ausgestattet ist oder andere Arten von Anmeldungen über andere Cloudanbieter unterstützt, erweist sich das Hinzufügen der Azure AD-Anmeldung über beliebige Mandanten als einfach. Registrieren Sie einfach Ihre App, fügen Sie den Anmeldecode über OAuth2, OpenID Connect oder SAML hinzu, und implementieren Sie die [Schaltfläche „Mit Microsoft anmelden“][AAD-App-Branding] in Ihrer Anwendung.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie mit der Erstellung einer Anwendung für einen Mandanten für Azure AD bereits vertraut sind. Falls nicht, beginnen Sie mit einer der Schnellstartanleitungen, die auf der [Startseite des Entwicklerhandbuchs][AAD-Dev-Guide] verfügbar sind.

Sie müssen vier Schritte zum Konvertieren der Anwendung in eine mehrinstanzenfähige Azure AD-App ausführen:

1. [Aktualisieren der Anwendungsregistrierung, sodass sie mehrinstanzenfähig ist](#update-registration-to-be-multi-tenant)
2. [Aktualisieren des Codes zum Senden von Anforderungen an den Endpunkt „/common“](#update-your-code-to-send-requests-to-common)
3. [Aktualisieren des Codes zum Verarbeiten mehrerer Ausstellerwerte](#update-your-code-to-handle-multiple-issuer-values)
4. [Interpretieren der Benutzer- und Administratorzustimmung und Vornehmen der entsprechenden Codeänderungen](#understand-user-and-admin-consent)

Betrachten wir jeden Schritt im Detail. Sie können auch direkt zum Beispiel [Erstellen einer mehrinstanzenfähigen SaaS-Anwendung, die Microsoft Graph mithilfe von Azure AD und OpenID Connect aufruft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) wechseln.

## <a name="update-registration-to-be-multi-tenant"></a>Aktualisieren der Registrierung, sodass sie mehrinstanzenfähig ist

Standardmäßig gelten Web-App-/API-Registrierungen in Azure AD für einen einzelnen Mandanten. Sie können Ihre Registrierung mehrinstanzenfähig machen, indem Sie im [Azure-Portal][AZURE-portal] im Bereich **Authentifizierung** Ihrer Anwendungsregistrierung den Schalter **Unterstützte Kontotypen** auf **Konten in einem beliebigen Organisationsverzeichnis** festlegen.

Damit eine Anwendung mehrinstanzenfähig sein kann, muss der App-ID-URI der Anwendung in Azure AD global eindeutig sein. Der App-ID-URI ist eine der Methoden, mit der eine Anwendung in Protokollmeldungen identifiziert wird. Bei einer Anwendung mit einem einzelnen Mandanten muss der App-ID-URI nur in diesem Mandanten eindeutig sein. Bei einer mehrinstanzenfähigen Anwendung muss er global eindeutig sein, damit Azure AD die Anwendung in allen Mandanten finden kann. Globale Eindeutigkeit wird durch die Anforderung erzwungen, dass der App-ID-URI einen Hostnamen aufweisen muss, der mit einer überprüften Domäne des Azure AD-Mandanten übereinstimmt.

Standardmäßig verfügen Apps, die über das Azure-Portal erstellt werden, über einen global eindeutigen App-ID-URI, der bei der Erstellung der App festgelegt wird. Sie können diesen Wert aber ändern. Wenn der Name Ihres Mandanten beispielsweise „contoso.onmicrosoft.com“ lautet, wäre `https://contoso.onmicrosoft.com/myapp`ein gültiger App-ID-URI. Enthält Ihr Mandant die überprüfte Domäne `contoso.com`, wäre auch ein gültiger App-ID-URI `https://contoso.com/myapp`. Wenn der App-ID-URI nicht diesem Muster folgt, schlägt das Festlegen einer Anwendung als mehrinstanzenfähig fehl.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualisieren des Codes zum Senden von Anforderungen an „/common“

Bei einer Anwendung mit einem einzelnen Mandanten werden Anmeldeanforderungen an den Anmeldeendpunkt des Mandanten gesendet. Für „contoso.onmicrosoft.com“ würde der Endpunkt beispielsweise wie folgt lauten: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Mit Anforderungen, die an den Endpunkt eines Mandanten gesendet werden, können Benutzer (oder Gäste) in diesem Mandanten für Anwendungen dieses Mandanten angemeldet werden.

Bei einer mehrinstanzenfähigen Anwendung weiß die Anwendung zunächst nicht, von welchem Mandanten der Benutzer stammt. Deshalb können Sie keine Anforderungen an den Endpunkt eines Mandanten senden. Stattdessen werden Anforderungen an einen Endpunkt gesendet, der sie per Multiplexverfahren an alle Azure AD-Mandanten verteilt: `https://login.microsoftonline.com/common`

Wenn Microsoft Identity Platform eine Anforderung am Endpunkt „/common“ erhält, wird der Benutzer angemeldet, und dadurch wird der Mandant ermittelt, von dem der Benutzer stammt. Der Endpunkt „/common“ funktioniert mit allen von Azure AD unterstützten Authentifizierungsprotokollen:  OpenID Connect, OAuth 2.0, SAML 2.0 und WS-Verbund.

Die Anmeldeantwort an die Anwendung enthält dann ein Token, das den Benutzer darstellt. Anhand des Ausstellerwerts im Token erfährt eine Anwendung, von welchem Mandanten der Benutzer stammt. Wenn eine Antwort vom Endpunkt „/common“ zurückgegeben wird, entspricht der Ausstellerwert im Token dem Mandanten des Benutzers.

> [!IMPORTANT]
> Der Endpunkt „/common“ ist kein Mandant und kein Aussteller, sondern lediglich ein Multiplexer. Bei Verwendung von „/common“ muss die Logik zum Überprüfen von Token in Ihrer Anwendung aktualisiert werden, um dies zu berücksichtigen.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualisieren des Codes zum Verarbeiten mehrerer Ausstellerwerte

Webanwendungen und Web-APIs empfangen und überprüfen Token von Microsoft Identity Platform.

> [!NOTE]
> Wenn native Clientanwendungen Token von Microsoft Identity Platform anfordern und empfangen, werden diese zur Überprüfung an APIs weitergeleitet. Native Anwendungen überprüfen Zugriffstoken nicht und müssen sie als nicht transparent behandeln.

Sehen wir uns an, wie eine Anwendung Token überprüft, die sie von Microsoft Identity Platform erhält. Eine Anwendung mit einem einzigen Mandanten verwendet normalerweise einen Endpunktwert wie den folgenden:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

Hieraus wird eine Metadaten-URL (in diesem Fall OpenID Connect) wie die folgende erstellt:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

Damit können zwei wichtige Informationen heruntergeladen werden, die zum Überprüfen von Token verwendet werden: die Signaturschlüssel und der Ausstellerwert des Mandanten.

Jeder Azure AD-Mandant weist einen eindeutigen Ausstellerwert in folgender Form auf:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

Dabei ist der GUID-Wert die nicht änderbare Version der Mandanten-ID des Mandanten. Wenn Sie den vorhergehenden Metadatenlink für `contoso.onmicrosoft.com` wählen, können Sie diesen Ausstellerwert im Dokument sehen.

Wenn eine Anwendung mit einem einzelnen Mandanten ein Token überprüft, wird die Signatur des Tokens anhand der Signaturschlüssel aus dem Metadatendokument geprüft. Hierdurch kann sichergestellt werden, dass der Ausstellerwert im Token dem Wert im Metadatendokument entspricht.

Da der Endpunkt „/common“ keinem Mandanten entspricht und kein Aussteller ist, hat der Ausstellerwert in den Metadaten für „/common“ eine Vorlagen-URL anstatt eines tatsächlichen Werts:

```http
https://sts.windows.net/{tenantid}/
```

Aus diesem Grund kann eine mehrinstanzenfähige Anwendung keine Token überprüfen, indem einfach der Ausstellerwert in den Metadaten mit dem `issuer` -Wert im Token abgeglichen wird. Eine mehrinstanzenfähige Anwendung benötigt Logik, um basierend auf der Mandanten-ID im Ausstellerwert zu entscheiden, welche Ausstellerwerte zulässig sind.

Wenn eine mehrinstanzenfähige Anwendung nur Anmeldungen von bestimmten Mandanten zulässt, die sich für den Dienst registriert haben, muss der Ausstellerwert oder der Anspruchswert `tid` im Token überprüft werden. So wird sichergestellt, dass dieser Mandant in der Liste der Abonnenten enthalten ist. Wenn eine mehrinstanzenfähige Anwendung nur von Personen genutzt wird und keine Zugriffsentscheidungen basierend auf Mandanten trifft, kann der Ausstellerwert vollständig ignoriert werden.

In den [Beispielen für Mehrinstanzenfähigkeit][AAD-Samples-MT] ist die Überprüfung des Ausstellers deaktiviert, damit sich alle Azure AD-Mandanten anmelden können.

## <a name="understand-user-and-admin-consent"></a>Grundlegendes zur Benutzer- und Administratoreinwilligung

Damit sich ein Benutzer bei einer Anwendung in Azure AD anmelden kann, muss die Anwendung im Mandanten des Benutzers dargestellt werden. Dies ermöglicht es der Organisation beispielsweise, eindeutige Richtlinien anzuwenden, wenn sich Benutzer ihres Mandanten bei der Anwendung anmelden. Bei einer Anwendung mit einem einzelnen Mandanten ist diese Registrierung einfacher. Es handelt sich um die Registrierung, die erfolgt, wenn Sie die Anwendung im [Azure-Portal][AZURE-portal] registrieren.

Bei einer mehrinstanzenfähigen Anwendung erfolgt die erste Registrierung für die Anwendung in dem Azure AD-Mandanten, der vom Entwickler verwendet wurde. Wenn sich ein Benutzer von einem anderen Mandanten zum ersten Mal bei der Anwendung anmeldet, fordert Azure AD ihn auf, den von der Anwendung angeforderten Berechtigungen zuzustimmen. Wenn er zustimmt, wird eine Darstellung der Anwendung, die als *Dienstprinzipal* bezeichnet wird, im Mandanten des Benutzers erstellt, und die Anmeldung kann fortgesetzt werden. Im Verzeichnis, das die Zustimmung des Benutzers zur Anwendung erfasst, wird auch eine Delegierung erstellt. Ausführliche Informationen zu Anwendungsobjekten und Dienstprinzipalobjekten der Anwendung und deren Beziehungen zueinander finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte][AAD-App-SP-Objects].

![Darstellung der Zustimmung zur App mit einer Ebene][Consent-Single-Tier]

Dieser Zustimmungsprozess wird durch die von der Anwendung angeforderten Berechtigungen beeinflusst. Microsoft Identity Platform unterstützt zwei Arten von Berechtigungen – nur für eine App geltende und delegierte Berechtigungen.

* Eine delegierte Berechtigung gewährt einer Anwendung die Möglichkeit, für einen Teil der Funktionen, die der Benutzer ausführen kann, als angemeldeter Benutzer zu agieren. Sie können einer Anwendung z.B. die delegierte Berechtigung zum Lesen des Kalenders des angemeldeten Benutzers erteilen.
* Eine nur für die App geltende Berechtigung wird der Identität der Anwendung direkt gewährt. Beispielsweise können Sie einer Anwendung die nur für die App geltende Berechtigung zum Lesen der Liste der Benutzer in einem Mandanten erteilen, unabhängig davon, wer sich bei der Anwendung angemeldet hat.

Einigen Berechtigungen kann ein regulärer Benutzer zustimmen, während andere die Zustimmung eines Mandantenadministrators erfordern.

Weitere Informationen zur Benutzer- und Administratoreinwilligung finden Sie unter [Konfigurieren des Workflows für die Administratoreinwilligung](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Administratorzustimmung

Nur für die App geltende Berechtigungen erfordern immer die Zustimmung eines Mandantenadministrators. Wenn die Anwendung eine nur für die App geltende Berechtigung anfordert und ein Benutzer versucht, sich bei der Anwendung anzumelden, wird eine Fehlermeldung angezeigt, die besagt, dass der Benutzer nicht zustimmen kann.

Bestimmte delegierte Berechtigungen erfordern ebenfalls die Zustimmung eines Mandantenadministrators. Beispielsweise erfordert die Funktion zum Zurückschreiben in Azure AD als der angemeldete Benutzer die Zustimmung eines Mandantenadministrators. Wenn ein normaler Benutzer versucht, sich bei einer Anwendung anzumelden, die eine delegierte Berechtigung anfordert, für die die Zustimmung des Administrators erforderlich ist, wird in Ihrer Anwendung ein Fehler angezeigt, wie es auch bei nur für die App geltenden Berechtigungen der Fall ist. Ob für eine Berechtigung die Zustimmung des Administrators erforderlich ist, wird durch den Entwickler bestimmt, der die Ressource veröffentlicht hat. Sie können dies in der Dokumentation für die Ressource nachlesen. In der Berechtigungsdokumentation für die [Microsoft Graph-API][MSFT-Graph-permission-scopes] ist angegeben, für welche Berechtigungen die Zustimmung des Administrators erforderlich ist.

Wenn Ihre Anwendung Berechtigungen nutzt, die die Zustimmung des Administrators erfordern, müssen Sie z.B. eine Schaltfläche oder einen Link implementieren, damit der Administrator die Aktion initiieren kann. Die Anforderung, die die Anwendung für diese Aktion sendet, ist die reguläre OAuth2/OpenID Connect-Autorisierungsanforderung, die zusätzlich den Abfragezeichenfolgen-Parameter `prompt=admin_consent` enthält. Nachdem der Administrator zugestimmt hat und der Dienstprinzipal im Mandanten des Kunden erstellt wurde, ist für nachfolgende Anmeldeanforderungen der Parameter `prompt=admin_consent` nicht mehr erforderlich. Da der Administrator entschieden hat, dass die angeforderten Berechtigungen zulässig sind, werden von diesem Zeitpunkt an keine anderen Benutzer im Mandanten zur Zustimmung aufgefordert.

Ein Mandantenadministrator kann die Funktion deaktivieren, dass reguläre Benutzer Anwendungen zustimmen. Wenn diese Funktion deaktiviert ist, ist die Zustimmung des Administrators immer erforderlich, damit die Anwendung im Mandanten verwendet werden kann. Wenn Sie Ihre Anwendung mit deaktivierter Endbenutzerzustimmung testen möchten, können Sie die Konfigurationsoption im [Azure-Portal][AZURE-portal] im Abschnitt **[Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** unter **Unternehmensanwendungen** verwenden.

Der Parameter `prompt=admin_consent` kann auch von Anwendungen verwendet werden, die Berechtigungen anfordern, die nicht die Zustimmung des Administrators erfordern. Ein Beispiel für einen Anwendungsfall lautet wie folgt: Für die Anwendung ist ein Vorgang erforderlich, bei dem sich der Administrator des Mandanten einmal registriert, und danach werden keine anderen Benutzer mehr zur Zustimmung aufgefordert.

Wenn für eine Anwendung die Zustimmung des Administrators erforderlich ist und sich ein Administrator anmeldet, ohne dass der Parameter `prompt=admin_consent` gesendet wird, gilt die erfolgreiche Zustimmung des Administrators **nur für sein Benutzerkonto**. Reguläre Benutzer können sich weiterhin nicht anmelden und der Anwendung nicht zustimmen. Diese Funktion ist sinnvoll, wenn Sie dem Mandantenadministrator die Möglichkeit geben möchten, Ihre Anwendung zu untersuchen, bevor Sie anderen Benutzern Zugriff gewähren.

### <a name="consent-and-multi-tier-applications"></a>Zustimmung und Anwendungen mit mehreren Ebenen

Ihre Anwendung weist möglicherweise mehrere Ebenen auf, die in Azure AD jeweils durch eine eigene Registrierung dargestellt werden. Beispielsweise eine native Anwendung, die eine Web-API aufruft, oder eine Webanwendung, die eine Web-API aufruft. In beiden Fällen fordert der Client (native App oder Web-App) Berechtigungen an, um die Ressource (Web-API) aufzurufen. Damit dem Client erfolgreich die Zustimmung im Mandanten eines Kunden erteilt werden kann, müssen alle Ressourcen, für die Berechtigungen angefordert werden, bereits im Mandanten des Kunden vorhanden sein. Wenn diese Bedingung nicht erfüllt ist, gibt Azure AD einen Fehler zurück, der besagt, dass die Ressource zuerst hinzugefügt werden muss.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Mehrere Ebenen in einem einzelnen Mandanten

Dies kann ein Problem sein, wenn die logische Anwendung aus zwei oder mehr Anwendungsregistrierungen besteht, z.B. separate Clients und Ressourcen. Wie sorgen Sie zuerst dafür, dass die Ressource im Mandanten des Kunden vorhanden ist? Azure AD behandelt diesen Fall, indem der Client und die Ressource, der zugestimmt werden soll, in einem einzigen Schritt aktiviert werden. Der Benutzer sieht die Gesamtsumme der Berechtigungen, die sowohl vom Client als auch von der Ressource auf der Seite „Zustimmung“ angefordert werden. Um dieses Verhalten zu ermöglichen, muss die Anwendungsregistrierung der Ressource die App-ID des Clients als `knownClientApplications` im [Anwendungsmanifest][AAD-App-Manifest] enthalten. Beispiel:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Dies wird in einem Beispiel eines nativen Clients mit mehreren Ebenen, der eine Web-API aufruft, im Abschnitt [Verwandte Inhalte](#related-content) am Ende dieses Artikels veranschaulicht. Die folgende Abbildung zeigt eine Übersicht über die Zustimmung für eine Mehrparteien-App, die in einem einzigen Mandanten registriert wurde.

![Darstellung der Zustimmung zur bekannten Client-App mit mehreren Ebenen][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Mehrere Ebenen in mehreren Mandanten

Ein ähnlicher Fall tritt ein, wenn die verschiedenen Ebenen einer Anwendung in verschiedenen Mandanten registriert sind. Betrachten Sie z.B. die Erstellung einer nativen Clientanwendung, die die Exchange Online-API aufruft. Um die native Anwendung zu entwickeln und sie später im Mandanten eines Kunden auszuführen, muss der Exchange Online-Dienstprinzipal vorhanden sein. In diesem Fall müssen der Entwickler und der Kunde Exchange Online erwerben, damit der Dienstprinzipal in seinen Mandanten erstellt wird.

Falls die API von einer anderen Organisation als Microsoft erstellt wurde, muss der Entwickler der API für Kunden eine Möglichkeit bieten, der Anwendung in den Mandanten der Kunden die Zustimmung zu erteilen. Der empfohlene Entwurf gilt für externe Entwickler, um die API so zu erstellen, dass sie auch als Webclient zur Implementierung der Registrierung fungieren kann. Gehen Sie dazu folgendermaßen vor:

1. Führen Sie die Schritte in den vorherigen Abschnitten durch, um sicherzustellen, dass die API die Registrierungs-/Codeanforderungen für mehrinstanzenfähige Anwendungen erfüllt.
2. Stellen Sie neben der Bereitstellung der API-Bereiche/-Rollen sicher, dass die Registrierung die (standardmäßig bereitgestellte) Berechtigung „Anmelden und Benutzerprofil lesen“ beinhaltet.
3. Implementieren Sie eine Anmelde-/Registrierungsseite im Webclient gemäß den Schritten unter [Administratorzustimmung](#admin-consent).
4. Nach der Zustimmung des Benutzers bei der Anwendung werden die Dienstprinzipal- und Zustimmungsdelegierungsverknüpfungen in dessen Mandanten erstellt, und die systemeigene Anwendung kann Tokens für die API abrufen.

Die folgende Abbildung zeigt eine Übersicht über die Zustimmung für eine App mit mehreren Ebenen, die in verschiedenen Mandanten registriert wurde.

![Darstellung der Zustimmung zur Mehrparteien-App mit mehreren Ebenen][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Widerrufen der Zustimmung

Benutzer und Administratoren können die Zustimmung zu Ihrer Anwendung jederzeit widerrufen:

* Benutzer widerrufen den Zugriff auf einzelne Anwendungen, indem sie die jeweiligen Anwendungen aus der Liste [Zugriffspanel – Anwendungen][AAD-Access-Panel] entfernen.
* Administratoren widerrufen den Zugriff auf Anwendungen, indem sie diese über den Abschnitt [Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) im [Azure-Portal][AZURE-portal] entfernen.

Wenn ein Administrator einer Anwendung für alle Benutzer in einem Mandanten seine Zustimmung gibt, können Benutzer den Zugriff nicht einzeln widerrufen. Nur der Administrator kann den Zugriff widerrufen, und dies nur für die gesamte Anwendung.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Mehrinstanzenfähige Anwendungen und Zwischenspeichern von Zugriffstoken

Mehrinstanzenfähige Anwendungen können auch Zugriffstoken abrufen, um APIs aufzurufen, die von Azure AD geschützt sind. Ein häufiger Fehler bei der Verwendung der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) mit einer mehrinstanzenfähigen Anwendung ist, zuerst ein Token für einen Benutzer mithilfe von „/common“ anzufordern, eine Antwort zu erhalten und dann ein weiteres Token für denselben Benutzer ebenfalls mit „/common“ anzufordern. Da die Antwort von Azure AD von einem Mandanten stammt (und nicht von „/common“), speichert MSAL das Token als Token vom Mandanten zwischen. Beim nachfolgenden Aufruf von „/common“ zum Abrufen eines Zugriffstokens für den Benutzer wird der Cacheeintrag übersehen, und der Benutzer wird aufgefordert, sich erneut anzumelden. Um zu vermeiden, dass Cacheeinträge übersehen werden, stellen Sie sicher, dass nachfolgende Aufrufe für einen bereits angemeldeten Benutzer dem Endpunkt des Mandanten gelten.

## <a name="related-content"></a>Verwandte Inhalte

* [Beispiel für eine mehrinstanzenfähige Anwendung](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Brandingrichtlinien für Anwendungen][AAD-App-Branding]
* [Anwendungsobjekte und Dienstprinzipalobjekte][AAD-App-SP-Objects]
* [Integrieren von Anwendungen in Azure Active Directory][AAD-Integrating-Apps]
* [Azure Active Directory-Zustimmungsframework][AAD-Consent-Overview]
* [Microsoft Graph-API-Berechtigungsbereiche][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Anwendung erstellen, die einen Benutzer über einen beliebigen Azure AD-Mandanten anmelden kann. Nach der Aktivierung der einmaligen Anmeldung (SSO) zwischen Ihrer App und Azure AD können Sie auch Ihre Anwendung aktualisieren, um auf von Microsoft-Ressourcen bereitgestellte APIs wie Microsoft 365 zuzugreifen. Auf diese Weise können Sie in Ihrer Anwendung für ein personalisiertes Benutzererlebnis sorgen, um den Benutzern beispielsweise Kontextinformationen anzuzeigen, z.B. ihr Profilfoto oder ihren nächsten Kalendertermin.

Weitere Informationen zu API-Aufrufen von Azure AD und Microsoft 365-Diensten wie Exchange, SharePoint, OneDrive, OneNote usw. finden Sie unter [Microsoft Graph-API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken