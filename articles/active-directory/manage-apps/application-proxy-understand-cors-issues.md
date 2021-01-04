---
title: Verstehen und Beheben von CORS-Problemen für den Azure AD-Anwendungsproxy
description: Hier finden Sie grundlegende Informationen zu CORS im Azure AD-Anwendungsproxy sowie zur Erkennung und Behebung von CORS-Problemen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8836295e9f54260c4e9ff6c1da333ef2a86d58fb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651854"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Verstehen und Beheben von CORS-Problemen für den Azure Active Directory-Anwendungsproxy

Die Ressourcenfreigabe zwischen verschiedenen Ursprüngen ([Cross-Origin Resource Sharing, CORS](https://www.w3.org/TR/cors/)) kann bisweilen Herausforderungen für die Apps und APIs mit sich bringen, die Sie über den Azure Active Directory-Anwendungsproxy veröffentlichen. In diesem Artikel werden CORS-Probleme für den Azure AD-Anwendungsproxy sowie entsprechende Lösungen behandelt.

Die Browsersicherheit verhindert in der Regel, dass eine Webseite AJAX-Anforderungen an eine andere Domäne richtet. Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet und verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Manchmal sollen andere Websites jedoch unter Umständen Ihre Web-API aufrufen können. CORS ist ein W3C-Standard, der einem Server eine weniger strenge Anwendung der Richtlinie des gleichen Ursprungs ermöglicht und einige Anforderungen zwischen verschiedenen Ursprüngen zulässt, während andere abgelehnt werden.

## <a name="understand-and-identify-cors-issues"></a>Verstehen und Erkennen von CORS-Problemen

Zwei URLs haben den gleichen Ursprung, wenn sie über identische Schemas, Hosts und Ports verfügen ([RFC 6454](https://tools.ietf.org/html/rfc6454)). Beispiel:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Die folgenden URLs haben jeweils einen anderen Ursprung als die beiden obigen:

-   http:\//contoso.net (andere Domäne)
-   http:\//contoso.com:9000/foo.html (anderer Port)
-   https:\//contoso.com/foo.html (anderes Schema)
-   http:\//www.contoso.com/foo.html (andere Unterdomäne)

Die Richtlinie des gleichen Ursprungs sorgt dafür, dass Apps ohne korrekte Zugriffssteuerungsheader nicht auf Ressourcen eines anderen Ursprungs zugreifen können. Sind die CORS-Header nicht vorhanden oder nicht korrekt, tritt bei ursprungsübergreifenden Anforderungen ein Fehler auf. 

CORS-Probleme können mithilfe der Debugging-Tools des Browsers identifiziert werden:

1. Starten Sie den Browser, und navigieren Sie zur Web-App.
1. Drücken Sie **F12**, um die Debugging-Konsole zu öffnen.
1. Versuchen Sie, die Transaktion zu reproduzieren, und überprüfen Sie die Konsolenmeldung. Ein CORS-Verstoß erzeugt einen ursprungsbezogenen Konsolenfehler.

Das Auswählen der Schaltfläche **Try It** im folgenden Screenshot hat eine CORS-Fehlermeldung mit dem Hinweis zur Folge, dass „https:\//corswebclient-contoso.msappproxy.net“ im Header „Access-Control-Allow-Origin“ nicht gefunden wurde.

![CORS-Problem](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS-Herausforderungen im Zusammenhang mit dem Anwendungsproxy

Das folgende Beispiel zeigt ein typisches Szenario mit Azure AD-Anwendungsproxy und CORS. Der interne Server hostet einen Web-API-Controller (**CORSWebService**) sowie einen CORS-Webclient (**CORSWebClient**), der **CORSWebService** aufruft. **CORSWebClient** richtet eine AJAX-Anforderung an **CORSWebService**.

![Lokale Anforderung mit gleichem Ursprung](./media/application-proxy-understand-cors-issues/image1.png)

Die CORSWebClient-App funktioniert, wenn sie lokal gehostet wird. Wird sie dagegen über den Azure AD-Anwendungsproxy veröffentlicht, kann sie entweder nicht geladen werden, oder es tritt ein Fehler auf. Wenn Sie die Apps „CORSWebClient“ und „CORSWebService“ separat als unterschiedliche Apps über den Anwendungsproxy veröffentlicht haben, werden sie in unterschiedlichen Domänen gehostet. Eine von „CORSWebClient“ an „CORSWebService“ gerichtete AJAX-Anforderung ist eine ursprungsübergreifende Anforderung und somit nicht erfolgreich.

![CORS-Anforderung für den Anwendungsproxy](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Lösungen für CORS-Probleme mit dem Anwendungsproxy

Das oben genannte CORS-Problem lässt sich auf eine der folgenden Arten beheben:

### <a name="option-1-set-up-a-custom-domain"></a>Option 1: Einrichten einer benutzerdefinierten Domäne

Verwenden Sie eine [benutzerdefinierte Domäne](./application-proxy-configure-custom-domain.md) für den Azure AD-Anwendungsproxy, um bei der Veröffentlichung den gleichen Ursprung zu verwenden, ohne Änderungen an App-Ursprüngen, Code oder Headern vornehmen zu müssen. 

### <a name="option-2-publish-the-parent-directory"></a>Option 2: Veröffentlichen des übergeordneten Verzeichnisses

Veröffentlichen Sie das übergeordnete Verzeichnis beider Apps. Diese Lösung funktioniert besonders gut, wenn sich auf dem Webserver nur zwei Apps befinden. Anstatt jede App separat zu veröffentlichen, können Sie das gemeinsame übergeordnete Verzeichnis veröffentlichen, um den gleichen Ursprung zu erhalten.

Die folgenden Beispiele zeigen die Portalseite des Azure AD-Anwendungsproxys für die CORSWebClient-App.  Wenn die **interne URL** auf *contoso.com/CORSWebClient* festgelegt ist, kann die App keine erfolgreichen Anforderungen an das Verzeichnis *contoso.com/CORSWebService* richten, da es sich dabei um ursprungsübergreifende Anforderungen handelt. 

![Veröffentlichen der individuellen App](./media/application-proxy-understand-cors-issues/image4.png)

Legen Sie die **interne URL** stattdessen so fest, dass das übergeordnete Verzeichnis (enthält sowohl *CORSWebClient* als auch *CORSWebService*) veröffentlicht wird:

![Veröffentlichen des übergeordneten Verzeichnisses](./media/application-proxy-understand-cors-issues/image5.png)

Die resultierenden App-URLs beheben das CORS-Problem:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Option 3: Aktualisieren der HTTP-Header

Fügen Sie für den Webdienst einen benutzerdefinierten, der Ursprungsanforderung entsprechenden HTTP-Antwortheader hinzu. Verwenden Sie für Websites, die in Internetinformationsdienste (Internet Information Services, IIS) ausgeführt werden, den IIS-Manager, um den Header zu ändern:

![Hinzufügen eines benutzerdefinierten Antwortheaders im IIS-Manager](./media/application-proxy-understand-cors-issues/image6.png)

Für diese Änderung sind keinerlei Codeänderungen erforderlich. Sie kann in den Fiddler-Ablaufverfolgungen überprüft werden:

**Veröffentlichen der Headerergänzung**\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Content-Type: text/plain; charset=utf-8\
Expires: -1\
Vary: Accept-Encoding\
Server:  Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Option 4: Ändern der App

Sie können Ihre App ändern, um CORS zu unterstützen, indem Sie den Header „Access-Control-Allow-Origin“ mit entsprechenden Werten hinzufügen. Die Vorgehensweise zum Hinzufügen des Headers hängt von der Programmiersprache der App ab. Das Ändern des Codes ist die aufwendigste Option und daher am wenigsten empfehlenswert.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Option 5: Verlängern der Lebensdauer des Zugriffstokens

Einige CORS-Probleme können nicht behoben werden – etwa wenn Ihre App Datenverkehr zur Authentifizierung an *login.microsoftonline.com* umleitet und das Zugriffstoken abläuft. In diesem Fall ist der CORS-Aufruf nicht erfolgreich. Zur Umgehung dieses Problemszenarios können Sie die Lebensdauer des Zugriffstokens verlängern, um zu verhindern, dass es während einer Benutzersitzung abläuft. Weitere Informationen hierzu finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (Vorschau)](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Weitere Informationen
- [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planen der Bereitstellung eines Azure AD-Anwendungsproxys](application-proxy-deployment-plan.md) 
- [Remotezugriff auf lokale Anwendungen über den Azure Active Directory-Anwendungsproxy](application-proxy.md)