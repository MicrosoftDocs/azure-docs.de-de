---
title: Verwalten des Zugriffs auf SaaS-Cloudanwendungen mithilfe von Mandanteneinschränkungen – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Mandanteneinschränkungen auf der Grundlage des verwendeten Azure AD-Mandanten steuern, welcher Benutzer auf Apps zugreifen kann.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824380"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Verwalten des Zugriffs auf SaaS-Cloudanwendungen mithilfe von Mandanteneinschränkungen

Große Organisationen mit hohen Sicherheitsanforderungen, die Clouddienste wie etwa Office 365 nutzen möchten, müssen sich darauf verlassen können, dass ihre Benutzer nur auf genehmigte Ressourcen zugreifen können. Zur Steuerung des Zugriffs schränken Unternehmen in der Regel Domänennamen oder IP-Adressen ein. In einer Welt, in der SaaS-Apps (Software-as-a-Service) in einer öffentlichen Cloud gehostet und unter freigegebenen Domänennamen wie [outlook.office.com](https://outlook.office.com/) und [login.microsoftonline.com](https://login.microsoftonline.com/) ausgeführt werden, ist diese Vorgehensweise jedoch zum Scheitern verurteilt. Die Blockierung der Adressen würde dazu führen, dass Benutzer gar nicht mehr über das Web auf Outlook zugreifen können, anstatt lediglich ihren Zugriff auf genehmigte Identitäten und Ressourcen zu beschränken.

Zur Lösung dieses Problems bietet Azure Active Directory (Azure AD) ein Feature namens Mandanteneinschränkungen. Mithilfe von Mandanteneinschränkungen können Organisationen den Zugriff auf SaaS-Cloudanwendungen in Abhängigkeit von dem Azure AD-Mandanten steuern, den die Anwendungen für einmaliges Anmelden verwenden. So können Sie beispielsweise den Zugriff auf die Office 365-Anwendungen Ihrer Organisation ermöglichen und den Zugriff auf Instanzen der gleichen Anwendungen anderer Organisationen verhindern.  

Mit Mandanteneinschränkungen können Organisationen eine Liste mit Mandanten angeben, für die ihre Benutzer zugriffsberechtigt sind. Azure AD gewährt daraufhin nur Zugriff auf die zugelassenen Mandanten.

In diesem Artikel konzentrieren wir uns auf Mandanteneinschränkungen für Office 365, das Feature sollte aber auch für jede andere SaaS-Cloud-App verwendet werden können, die moderne Authentifizierungsprotokolle mit Azure AD für einmaliges Anmelden verwendet. Falls Sie SaaS-Apps mit einem Azure AD-Mandanten verwenden, der nicht dem von Office 365 verwendeten Mandanten entspricht, müssen Sie sicherstellen, dass alle erforderlichen Mandanten zugelassen sind. Weitere Informationen zu SaaS-Cloud-Apps finden Sie im [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>So funktioniert's

Die Lösung umfasst folgende Komponenten:

1. **Azure AD**: Wenn `Restrict-Access-To-Tenants: <permitted tenant list>` vorhanden ist, stellt Azure AD nur Sicherheitstoken für die zugelassenen Mandanten aus.

2. **Lokale Proxyserver-Infrastruktur**: Bei dieser Infrastruktur handelt es sich um ein Proxygerät, das zur SSL-Überprüfung (Secure Sockets Layer) geeignet ist. Sie müssen den Proxy so konfigurieren, dass er den Header mit der Liste der zulässigen Mandanten in den für Azure AD bestimmten Datenverkehr einfügt.

3. **Clientsoftware**: Zur Unterstützung von Mandanteneinschränkungen muss Clientsoftware Token direkt von Azure AD anfordern, damit Datenverkehr von der Proxyinfrastruktur abgefangen werden kann. Browserbasierte Office 365-Anwendungen unterstützen derzeit Mandanteneinschränkungen, ebenso wie Office-Clients, die eine moderne Authentifizierung verwenden (wie OAuth 2.0).

4. **Moderne Authentifizierung**: Clouddienste müssen eine moderne Authentifizierung verwenden, um Mandanteneinschränkungen nutzen und den Zugriff auf nicht zugelassene Mandanten blockieren zu können. Sie müssen Office 365-Clouddienste standardmäßig für die Verwendung moderner Authentifizierungsprotokolle konfigurieren. Aktuelle Informationen zur Unterstützung von moderner Authentifizierung durch Office 365 finden Sie unter [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Aktualisierte moderne Authentifizierung für Office 365).

Das folgende Diagramm veranschaulicht den allgemeinen Datenverkehrsfluss. Mandanteneinschränkungen erfordern die SSL-Überprüfung nur bei Datenverkehr zu Azure AD, nicht bei Datenverkehr zu den Office 365-Clouddiensten. Diese Unterscheidung ist wichtig, da der durch die Authentifizierung bedingte Datenverkehr für Azure AD in der Regel erheblich geringer ausfällt als der Datenverkehr für SaaS-Anwendungen wie Exchange Online und SharePoint Online.

![Datenverkehrsfluss für Mandanteneinschränkungen – Diagramm](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Einrichten von Mandanteneinschränkungen

Für die Verwendung von Mandanteneinschränkungen sind zunächst zwei Schritte erforderlich. Stellen Sie zunächst sicher, dass Ihre Clients eine Verbindung mit den richtigen Adressen herstellen können. Konfigurieren Sie dann Ihre Proxyinfrastruktur.

### <a name="urls-and-ip-addresses"></a>URLs und IP-Adressen

Um Mandanteneinschränkungen verwenden zu können, müssen Ihre Clients für die Authentifizierung eine Verbindung mit den folgenden Azure AD-URLs herstellen können: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) und [login.windows.net](https://login.windows.net/). Für den Zugriff auf Office 365 müssen Ihre Clients außerdem eine Verbindung mit den unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) definierten vollqualifizierten Domänennamen (FQDNs), URLs und IP-Adressen herstellen können. 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration und Anforderungen

Die folgende Konfiguration ist erforderlich, um Mandanteneinschränkungen über Ihre Proxyinfrastruktur zu aktivieren. Hierbei handelt es sich um einen allgemeinen Leitfaden. Spezifische Implementierungsschritte finden Sie in der Dokumentation Ihres Proxyanbieters.

#### <a name="prerequisites"></a>Voraussetzungen

- Der Proxy muss SSL abfangen, HTTP-Header einfügen und Ziele unter Verwendung von FQDNs/URLs filtern können.

- Clients müssen der Zertifikatkette vertrauen, die vom Proxy für die SSL-Kommunikation präsentiert wird. Wenn also z. B. Zertifikate einer internen [PKI (Public Key-Infrastruktur)](/windows/desktop/seccertenroll/public-key-infrastructure) verwendet werden, muss dem Zertifikat der internen ausstellenden Stammzertifizierungsstelle vertraut werden.

- In Office 365-Abonnements ist dieses Feature bereits enthalten. Wenn Sie mit Mandanteneinschränkungen allerdings den Zugriff auf andere SaaS-Apps steuern möchten, benötigen Sie Azure AD Premium 1-Lizenzen.

#### <a name="configuration"></a>Konfiguration

Fügen Sie für jede eingehende, an „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ gerichtete Anforderung zwei HTTP-Header ein: *Restrict-Access-To-Tenants* und *Restrict-Access-Context*.

Die Header müssen folgende Elemente enthalten:

- *Restrict-Access-To-Tenants:* Verwenden Sie einen Wert für \<Liste mit zugelassenen Mandanten\>. Hierbei handelt es sich um eine kommagetrennte Liste mit Mandanten, auf die Benutzer zugreifen können sollen. Der Mandant in dieser Liste kann mithilfe jeder bei einem Mandanten registrierten Domäne identifiziert werden. Wenn Sie beispielsweise den Zugriff auf die Mandanten Contoso und Fabrikam zulassen möchten, sieht das Name/Wert-Paar wie folgt aus: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- *Restrict-Access-Context:* Verwenden Sie einen Wert für eine einzelne Verzeichnis-ID, um zu deklarieren, welcher Mandant die Mandanteneinschränkungen festlegt. Wenn Sie z. B. Contoso als den Mandanten deklarieren möchten, der die Mandanteneinschränkungsrichtlinie festlegt, sieht das Name/Wert-Paar wie folgt aus: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Ihre Verzeichnis-ID finden Sie im [Azure Active Directory-Portal](https://aad.portal.azure.com/). Melden Sie sich als Administrator an, und wählen Sie **Azure Active Directory** und anschließend **Eigenschaften** aus.

Um zu verhindern, dass Benutzer ihre eigenen HTTP-Header mit nicht genehmigten Mandanten einfügen, muss der Proxy den *Restrict-Access-To-Tenants*-Header ersetzen, falls er in der eingehenden Anforderung bereits vorhanden ist.

Für Clients muss bei allen Anforderungen an „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ die Verwendung des Proxys erzwungen werden. Wenn Clients also z. B. mithilfe von PAC-Dateien zur Verwendung des Proxys angewiesen werden, sollten Endbenutzer die PAC-Dateien nicht bearbeiten oder deaktivieren können.

## <a name="the-user-experience"></a>Die Benutzererfahrung

In diesem Abschnitt wird die Erfahrung für Endbenutzer und Administratoren erläutert.

### <a name="end-user-experience"></a>Endbenutzererfahrung

Ein Beispielbenutzer im Netzwerk von Contoso versucht, online auf die Fabrikam-Instanz einer freigegebenen SaaS-Anwendung wie Outlook zuzugreifen. Wenn Fabrikam ein nicht zugelassener Mandant für die Contoso-Instanz ist, wird dem Benutzer eine Meldung zur Zugriffsverweigerung angezeigt. Diese besagt, dass Sie versuchen, auf eine Ressource zuzugreifen, die zu einer Organisation gehört, die von Ihrer IT-Abteilung nicht genehmigt wurde.

### <a name="admin-experience"></a>Administratorerfahrung

Die Konfiguration der Mandanteneinschränkungen erfolgt zwar in der Proxyinfrastruktur des Unternehmens, Administratoren können jedoch über das Azure-Portal direkt auf die Mandanteneinschränkungsberichte zugreifen. So zeigen Sie die Berichte an

1. Melden Sie sich am [Azure Active Directory-Portal](https://aad.portal.azure.com/) an. Das Dashboard **Azure Active Directory Admin Center** wird angezeigt.

2. Wählen Sie im linken Bereich **Azure Active Directory** aus. Die Übersichtsseite „Azure Active Directory Admin Center“ wird angezeigt.

3. Wählen Sie in der Überschrift **Weitere Funktionen** die Option **Mandanteneinschränkungen** aus.

Der Administrator für den Mandanten, der als Restricted-Access-Context-Mandant angegeben ist, kann sich anhand dieses Berichts über Anmeldungen informieren, die aufgrund der Mandanteneinschränkungsrichtlinie blockiert wurden (einschließlich der jeweils verwendeten Identität und der Zielverzeichnis-ID). Anmeldungen sind enthalten, wenn es sich beim Mandanten, der die Einschränkung festlegt, entweder um den Benutzermandanten oder den Ressourcenmandanten für die Anmeldung handelt.

Genau wie bei anderen Berichten im Azure-Portal können Sie auch hier mithilfe von Filtern den gewünschten Umfang des Berichts angeben. Der Bericht kann nach einem bestimmten Zeitintervall, einem Benutzer, einer Anwendung, einem Client oder einem Status gefiltert werden. Wenn Sie die Schaltfläche **Spalten** auswählen, können Sie Daten mit einer beliebigen Kombination der folgenden Felder anzeigen:

- **Benutzer**
- **Anwendung**
- **Status**
- **Date**
- **Datum (UTC)** (UTC bedeutet Coordinated Universal Time)
- **MFA-Authentifizierungsmethode** (Methode für Multi-Factor Authentication)
- **MFA-Authentifizierungsdetails** (Details zur Multi-Factor Authentication)
- **MFA-Ergebnis**
- **IP-Adresse**
- **Client**
- **Benutzername**
- **Location**
- **Zielmandanten-ID**

## <a name="office-365-support"></a>Unterstützung für Office 365

Zur vollständigen Unterstützung von Mandanteneinschränkungen müssen Office 365-Anwendungen zwei Kriterien erfüllen:

1. Der verwendete Client muss die moderne Authentifizierung unterstützen.
2. Die moderne Authentifizierung muss als Standardauthentifizierungsprotokoll für den Clouddienst aktiviert sein.

Aktuelle Informationen zu den Office-Clients, die die moderne Authentifizierung unterstützen, finden Sie unter [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Aktualisierte moderne Authentifizierung für Office 365). Diese Seite enthält außerdem Links zu Schritten, mit denen Sie die moderne Authentifizierung für bestimmte Exchange Online- und Skype for Business Online-Mandanten aktivieren können. SharePoint Online ermöglicht bereits standardmäßig die moderne Authentifizierung.

Browserbasierte Office 365-Anwendungen (das Office-Portal, Yammer, SharePoint-Websites, Outlook im Web und andere) unterstützen derzeit Mandanteneinschränkungen. Thick Clients (Outlook, Skype for Business, Word, Excel, PowerPoint und andere) können Mandanteneinschränkungen nur bei Verwendung moderner Authentifizierung erzwingen.  

Outlook- und Skype for Business-Clients, die die moderne Authentifizierung unterstützen, können für Mandanten, bei denen keine moderne Authentifizierung aktiviert ist, unter Umständen weiterhin ältere Protokolle verwenden und dadurch die Mandanteneinschränkungen umgehen. Anwendungen, die ältere Protokolle verwenden, werden unter Umständen durch Mandanteneinschränkungen blockiert, wenn sie bei der Authentifizierung „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ kontaktieren.

Bei Outlook unter Windows können Kunden ggf. Einschränkungen implementieren, die verhindern, dass Endbenutzer ihren Profilen nicht genehmigte E-Mail-Konten hinzufügen. Informationen hierzu finden Sie beispielsweise unter der Gruppenrichtlinieneinstellung [Das Hinzufügen nicht standardmäßiger Exchange-Konten verhindern](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Testen

Wenn Sie die Mandanteneinschränkungen ausprobieren möchten, bevor Sie sie für Ihre gesamte Organisation implementieren, können Sie entweder einen hostbasierten Ansatz mit einem Tool wie Fiddler oder einen gestaffelten Rollout von Proxyeinstellungen verwenden.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler für einen hostbasierten Ansatz

Fiddler ist ein kostenloser Web Debugging Proxy, mit dem Sie HTTP/HTTPS-Datenverkehr erfassen und modifizieren können, um beispielsweise HTTP-Header einzufügen. Gehen Sie wie folgt vor, um Fiddler zum Testen von Mandanteneinschränkungen zu konfigurieren:

1. [Laden Sie Fiddler herunter, und installieren Sie es.](https://www.telerik.com/fiddler)

2. Konfigurieren Sie Fiddler wie in der [Hilfedokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) beschrieben für die Entschlüsselung von HTTPS-Datenverkehr.

3. Konfigurieren Sie Fiddler mithilfe von benutzerdefinierten Regeln so, dass die Header *Restrict-Access-To-Tenants* und *Restrict-Access-Context* eingefügt werden:

   1. Wählen Sie im Fiddler Web Debugger-Tool das Menü **Rules** (Regeln) und anschließend **Customize Rules...** (Regeln anpassen...) aus, um die Datei „CustomRules“ zu öffnen.

   2. Fügen Sie am Anfang der `OnBeforeRequest`-Funktion die folgenden Zeilen hinzu: Ersetzen Sie \<tenant domain\> durch eine bei Ihrem Mandanten registrierte Domäne (z. B. `contoso.onmicrosoft.com`). Ersetzen Sie \<directory ID\> durch den Azure AD-GUID Ihres Mandanten.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Falls Sie mehrere Mandanten zulassen möchten, trennen Sie die einzelnen Mandantennamen jeweils durch ein Komma. Beispiel: 

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Speichern und schließen Sie die Datei „CustomRules“.

Nachdem Sie Fiddler konfiguriert haben, können Sie Datenverkehr erfassen, indem Sie im Menü **File** (Datei) die Option **Capture Traffic** (Datenverkehr erfassen) auswählen.

### <a name="staged-rollout-of-proxy-settings"></a>Gestaffelter Rollout von Proxyeinstellungen

Abhängig von den Funktionen Ihrer Proxyinfrastruktur können Sie unter Umständen einen gestaffelten Rollout der Einstellungen für Ihre Benutzer durchführen. Dabei sollten Sie folgende allgemeine Optionen berücksichtigen:

1. Verwenden Sie PAC-Dateien, um Testbenutzer an eine Testproxyinfrastruktur weiterzuleiten, während normale Benutzer weiterhin die Produktionsproxyinfrastruktur verwenden.
2. Einige Proxyserver unterstützen möglicherweise verschiedene Konfigurationen mit Gruppen.

Spezifische Details finden Sie in der Dokumentation Ihres Proxyservers.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Aktualisierte moderne Authentifizierung für Office 365).
- Informieren Sie sich über die [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).
