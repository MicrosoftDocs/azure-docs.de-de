---
title: Remotezugriff auf lokale Apps – Azure AD-Anwendungsproxy
description: Der Azure Active Directory-Anwendungsproxy ermöglicht den sicheren Remotezugriff auf lokal gehostete Webanwendungen. Nach dem einmaligen Anmelden in Azure AD können Benutzer über eine externe URL oder ein internes Anwendungsportal auf Cloudanwendungen und lokale Anwendungen zugreifen. Der Anwendungsproxy kann z.B. Remotezugriff und einmaliges Anmelden bei Remotedesktop, SharePoint, Teams, Tableau, Qlik und Branchenanwendungen (LOB) bereitstellen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9d620342d53bdae0b3a520000f6d240eb0e28d15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180591"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy

Der Azure Active Directory-Anwendungsproxy ermöglicht den sicheren Remotezugriff auf lokal gehostete Webanwendungen. Nach dem einmaligen Anmelden in Azure AD können Benutzer über eine externe URL oder ein internes Anwendungsportal auf Cloudanwendungen und lokale Anwendungen zugreifen. Der Anwendungsproxy kann z.B. Remotezugriff und einmaliges Anmelden bei Remotedesktop, SharePoint, Teams, Tableau, Qlik und Branchenanwendungen (LOB) bereitstellen.

Azure AD-Anwendungsproxy ist:

- **Einfach zu verwenden.** Benutzer können auf die gleiche Weise wie auf Microsoft 365 und andere SaaS-Apps, die in Azure AD integriert sind, auf ihre lokalen Anwendungen zugreifen. Sie brauchen Ihre Anwendungen nicht ändern oder aktualisieren, damit Sie mit dem Anwendungsproxy funktionieren.

- **Sicher.** Lokale Anwendungen können die Autorisierungssteuerungen und Sicherheitsanalysen von Azure nutzen. So können lokale Anwendungen z.B. den bedingten Zugriff und die zweistufige Überprüfung verwenden. Für den Anwendungsproxy müssen Sie keine eingehenden Verbindungen über Ihre Firewall öffnen.

- **Kosteneffizient.** Für lokale Lösungen müssen Sie in der Regel demilitarisierte Zonen (DMZs), Edgeserver oder andere komplexe Infrastrukturen einrichten und verwalten. Der Anwendungsproxy wird in der Cloud ausgeführt, sodass seine Verwendung sehr einfach ist. Um den Anwendungsproxy verwenden zu können, müssen Sie weder die Netzwerkinfrastruktur ändern noch zusätzliche Appliances in Ihrer lokalen Umgebung installieren.

## <a name="what-is-application-proxy"></a>Was ist der Anwendungsproxy?
Der Anwendungsproxy ist ein Feature von Azure AD, mit dem Benutzer von einem Remoteclient aus auf lokale Webanwendungen zugreifen können. Der Anwendungsproxy umfasst den Anwendungsproxy-Dienst, der in der Cloud ausgeführt wird, und den Anwendungsproxy-Connector, der auf einem lokalen Server ausgeführt wird. Azure AD, der Anwendungsproxy-Dienst und der Anwendungsproxy-Connector arbeiten zusammen, um Benutzeranmeldetoken sicher von Azure AD zur Webanwendung weiterzuleiten.

Der Anwendungsproxy funktioniert mit:

* Webanwendungen, für die zur Authentifizierung die [integrierte Windows-Authentifizierung](application-proxy-configure-single-sign-on-with-kcd.md) verwendet wird
* Webanwendungen mit formularbasiertem oder [headerbasiertem](./application-proxy-configure-single-sign-on-with-headers.md) Zugriff
* Web-APIs, die Sie für umfassende Anwendungen auf unterschiedlichen Geräten verfügbar machen möchten
* Hinter einem [Remotedesktopgateway](application-proxy-integrate-with-remote-desktop-services.md) gehostete Anwendungen
* Rich Client-Apps, die in die Microsoft Authentication Library (MSAL) integriert sind

Der Anwendungsproxy unterstützt das einmalige Anmelden. Weitere Informationen zu unterstützten Methoden finden Sie unter [Auswählen einer Methode für einmaliges Anmelden](sso-options.md#choosing-a-single-sign-on-method).

Der Anwendungsproxy wird empfohlen, um Remotebenutzern Zugriff auf interne Ressourcen zu gewähren. Der Anwendungsproxy ersetzt die Notwendigkeit eines VPN- oder Reverseproxys. Er ist nicht für interne Benutzer im Unternehmensnetzwerk bestimmt.  Diese Benutzer, die den Anwendungsproxy unnötigerweise verwenden, können unerwartete und unerwünschte Leistungsprobleme verursachen.

## <a name="how-application-proxy-works"></a>Funktionsweise des Anwendungsproxys

Das folgende Diagramm zeigt, wie Azure AD und der Anwendungsproxy gemeinsam das einmalige Anmelden für lokale Anwendungen bereitstellen.

![Azure AD-Anwendungsproxy – Diagramm](./media/application-proxy/azureappproxxy.png)

1. Nachdem der Benutzer über einen Endpunkt auf die Anwendung zugegriffen hat, wird er an die Azure AD-Anmeldeseite umgeleitet.
2. Nach der erfolgreichen Anmeldung sendet Azure AD ein Token an das Clientgerät des Benutzers.
3. Der Client sendet das Token an den Anwendungsproxy-Dienst, der den Benutzerprinzipalnamen (UPN) und den Sicherheitsprinzipalnamen (SPN) aus dem Token abruft. Der Anwendungsproxy sendet die Anforderung dann zum Anwendungsproxy-Connector.
4. Wenn Sie das SSO konfiguriert haben, führt der Connector jede weitere erforderliche Authentifizierung im Namen des Benutzers durch.
5. Der Connector sendet die Anforderung an die lokale Anwendung.
6. Die Antwort wird über den Connector und den Anwendungsproxy-Dienst an den Benutzer gesendet.

> [!NOTE]
> Wie bei den meisten Azure AD Hybrid-Agents müssen Sie beim Anwendungsproxy-Connector keine eingehenden Verbindungen über Ihre Firewall öffnen. Der Benutzerdatenverkehr in Schritt 3 endet am Anwendungsproxy-Dienst (in Azure AD). Der Anwendungsproxy-Connector (lokal) ist für die restliche Kommunikation zuständig.
>


| Komponente | BESCHREIBUNG |
| --------- | ----------- |
| Endpunkt  | Der Endpunkt ist eine URL oder ein [Endbenutzerportal](end-user-experiences.md). Benutzer können außerhalb Ihres Netzwerks über eine externe URL auf Anwendungen zugreifen. Benutzer in Ihrem Netzwerk können über eine URL oder ein Endbenutzerportal auf die Anwendung zugreifen. Wenn Benutzer einen dieser Endpunkte erreichen, authentifizieren sie sich in Azure AD und werden dann über den Connector an die lokale Anwendung geleitet.|
| Azure AD | Azure AD führt die Authentifizierung mit dem Mandantenverzeichnis aus, das in der Cloud gespeichert ist. |
| Anwendungsproxy-Dienst | Dieser Anwendungsproxy-Dienst wird in der Cloud als Teil von Azure AD ausgeführt. Er übergibt das Anmeldetoken des Benutzers an den Anwendungsproxy-Connector. Der Anwendungsproxy leitet alle in der Anforderung verfügbaren Header weiter und legt die Header gemäß seinem Protokoll auf die Client-IP-Adresse fest. Enthält die eingehende Anforderung an den Proxy bereits diesen Header, wird die Client-IP-Adresse am Ende der durch Trennzeichen getrennten Liste hinzugefügt, die der Wert des Headers ist.|
| Anwendungsproxy-Connector | Der Connector ist ein einfacher Agent, der auf einem Windows-Server innerhalb Ihres Netzwerks ausgeführt wird. Der Connector verwaltet die Kommunikation zwischen dem Anwendungsproxydienst in der Cloud und der lokalen Anwendung. Der Connector verwendet nur ausgehende Verbindungen. Sie müssen also keine eingehenden Ports öffnen oder Ressourcen in die DMZ einfügen. Connectors sind zustandslos und rufen alle Informationen nach Bedarf aus der Cloud ab. Weitere Informationen zu Connectors, z.B. wie der Lastenausgleich und die Authentifizierung funktioniert, finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory wird lokal ausgeführt, um die Authentifizierung für Domänenkonten durchzuführen. Wenn einmaliges Anmelden konfiguriert ist, kommuniziert der Connector mit AD, um jede weitere erforderliche Authentifizierung auszuführen.
| Lokale Anwendung | Schließlich kann der Benutzer auf eine lokale Anwendung zugreifen.

## <a name="next-steps"></a>Nächste Schritte
Erste Schritte mit dem Anwendungsproxy finden Sie in [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy](application-proxy-add-on-premises-application.md).