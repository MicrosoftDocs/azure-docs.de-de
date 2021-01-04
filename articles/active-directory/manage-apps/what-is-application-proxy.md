---
title: Veröffentlichen lokaler Apps mit dem Azure AD-Anwendungsproxy
description: Erfahren Sie, warum zum externen Veröffentlichen lokaler Webanwendungen für Remotebenutzer der Anwendungsproxy verwendet werden sollte. Sie erhalten Informationen über die Architektur, Connectors, Authentifizierungsmethoden und Sicherheitsvorteile des Anwendungsproxys.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/31/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d73cf3336afad83fadf6f56606161f523e202280
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180539"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Veröffentlichen von lokalen Apps für Remotebenutzer mit dem Azure AD-Anwendungsproxy

Azure Active Directory (Azure AD) bietet viele Funktionen für den Schutz von Benutzern, Apps und Daten – in der Cloud und lokal. Der Azure AD-Anwendungsproxy kann insbesondere von IT-Experten implementiert werden, die lokale Webanwendungen extern veröffentlichen möchten. Remotebenutzer, die Zugriff auf interne Apps benötigen, können dann auf sichere Weise auf sie zugreifen.

Die Fähigkeit zum sicheren Zugriff auf interne Apps von außerhalb des Netzwerks wird in der modernen Arbeitswelt noch wichtiger. In Szenarien wie BYOD (Bring Your Own Device) und mobilen Geräten müssen IT-Experten zwei Ziele erreichen:

* Ermöglichen von zeit- und ortsunabhängigem produktiven Arbeiten für Endbenutzer
* Lückenloses Schützen der Assets eines Unternehmens

Viele Organisationen glauben, die Kontrolle zu haben und geschützt zu sein, wenn Ressourcen innerhalb der Grenzen ihres Unternehmensnetzwerks vorhanden sind. In der modernen digitalen Arbeitswelt mit verwalteten mobilen Geräten sowie Ressourcen und Diensten in der Cloud wurde diese Grenze jedoch durchlässig. Jetzt müssen Sie den komplexen Schutz der Identitäten der Benutzer und ihrer auf Geräten und in Apps gespeicherten Daten sicherstellen.

Vielleicht verwenden Sie bereits Azure AD zum Verwalten von Benutzern in der Cloud, die auf Microsoft 365 und andere SaaS-Anwendungen sowie lokal gehostete Web-Apps zugreifen müssen. Wenn Sie bereits über Azure AD verfügen, können Sie dies als eine Steuerungsebene nutzen, um den nahtlosen und sicheren Zugriff auf die lokalen Anwendungen sicherzustellen. Oder Sie erwägen möglicherweise noch die Migration zur Cloud. Wenn dies der Fall ist, können Sie hierfür zunächst den Anwendungsproxy implementieren und den ersten Schritt zu einer stabilen Identitätsverwaltung unternehmen.

Die nachstehende Liste ist zwar nicht vollständig, veranschaulicht jedoch einige Möglichkeiten, die Ihnen das Implementieren des Anwendungsproxys in einem Hybridszenario bietet:

* Vereinfachtes externes Veröffentlichen von lokalen Web-Apps ohne eine DMZ
* Unterstützung von einmaligem Anmelden (SSO) für Geräte, Ressourcen und Apps in der Cloud und lokal
* Mehrstufige Authentifizierung für Apps in der Cloud und lokal
* Schnelle Nutzung von Cloudfeatures, mit der Sicherheit der Microsoft Cloud
* Zentralisierung der Benutzerkontoverwaltung
* Zentralisierung der Steuerung von Identität und Sicherheit
* Automatisches Hinzufügen oder Entfernen des Benutzerzugriffs auf Anwendungen auf Grundlage der Gruppenmitgliedschaft

In diesem Artikel wird erläutert, wie Azure AD und der Anwendungsproxy Remotebenutzern einmaliges Anmelden (SSO) ermöglichen. Benutzer stellen ohne VPN, Firewallregeln oder Servern mit zwei Netzwerkumgebungen eine sichere Verbindung mit lokalen Apps her. Dieser Artikel erläutert, wie Ihre lokalen Webanwendungen mithilfe des Anwendungsproxys die Funktionen und Sicherheitsvorteile der Cloud nutzen können. In ihm werden auch die mögliche(n) Architektur und Topologien beschrieben.

## <a name="remote-access-in-the-past"></a>Remotezugriff in der Vergangenheit

Bisher befand sich die gesamte Steuerungsebene für den Schutz interner Ressourcen vor Angreifern und zum Erleichtern des Zugriffs durch Remotebenutzer in der DMZ oder im Umkreisnetzwerk. Die VPN- und Reverseproxylösungen in der DMZ, die von externen Clients für den Zugriff auf Unternehmensressourcen verwendet werden, eignen sich jedoch nicht für die Cloud. Sie weisen in der Regel die folgenden Nachteile auf:

* Hardwarekosten
* Gewährleisten der Sicherheit (Patches, Überwachungsports usw.)
* Authentifizieren von Benutzern im Edge
* Authentifizieren von Benutzern bei Webservern im Umkreisnetzwerk
* Verwalten des VPN-Zugriffs für Remotebenutzer mit der Verteilung und Konfiguration der VPN-Clientsoftware. Zudem Verwalten von in Domänen eingebundenen Servern in der DMZ, die durch externe Angriffe gefährdet sein können.

In der heutigen durch die Cloud dominierten Welt ist Azure AD die beste Lösung, um zu steuern, wer und was in Ihr Netzwerk gelangt. Der Azure AD-Anwendungsproxy lässt sich in moderne Authentifizierungs- und Cloudtechnologien, z.B. SaaS-Anwendungen und Identitätsanbieter, integrieren. Diese Integration ermöglicht Benutzern den Zugriff auf Apps von überall. Der Anwendungsproxy ist nicht nur besser für die moderne digitale Arbeitswelt geeignet, sondern bietet auch höhere Sicherheit als VPN- und Reverseproxylösungen und lässt sich leichter implementieren. Remotebenutzer können auf die gleiche Weise auf Ihre lokalen Anwendungen zugreifen wie auf Microsoft und andere mit Azure AD integrierte SaaS-Apps. Sie brauchen Ihre Anwendungen nicht ändern oder aktualisieren, damit Sie mit dem Anwendungsproxy funktionieren. Für den Anwendungsproxy müssen Sie zudem keine eingehenden Verbindungen über die Firewall öffnen. Mit dem Anwendungsproxy müssen Sie dies nur einmal festlegen und brauchen sich dann nicht mehr darum zu kümmern.

## <a name="the-future-of-remote-access"></a>Die Zukunft des Remotezugriffs

In der heutigen digitalen Arbeitswelt arbeiten die Benutzer an beliebigen Orten mit mehreren Geräten und Apps. Die einzige Konstante ist die Benutzeridentität. Daher ist heutzutage der erste Schritt zu einem sicheren Netzwerk die Verwendung der Funktionen der [Azure AD-Identitätsverwaltung](../../security/fundamentals/identity-management-overview.md) als Sicherheitssteuerungsebene. Ein Modell, in dem die Identität als Steuerungsebene verwendet wird, besteht in der Regel aus den folgenden Komponenten:

* Ein Identitätsanbieter zum Nachverfolgen von Benutzern und Benutzerinformationen.
* Ein Geräteverzeichnis, um eine Liste der Geräte zu verwalten, die Zugriff auf Unternehmensressourcen haben. Dieses Verzeichnis enthält die entsprechenden Geräteinformationen (z.B. Typ des Geräts, Integrität usw.).
* Ein Richtlinienauswertungsdienst, um zu bestimmen, ob Benutzer und Geräte die von den Sicherheitsadministratoren festgelegten Richtlinien erfüllen.
* Die Fähigkeit, Organisationsressourcen Zugriff zu gewähren oder zu verweigern.

Azure AD verfolgt mit dem Anwendungsproxy Benutzer nach, die Zugriff auf lokal und in der Cloud veröffentliche Web-Apps benötigen. Er bietet eine zentrale Verwaltung für diese Apps. Es wird empfohlen, den bedingten Azure AD-Zugriff ebenfalls zu aktivieren, auch wenn dies nicht zwingend erforderlich ist. Indem Sie Bedingungen definieren, wie sich Benutzer authentifizieren und Zugriff erhalten, stellen Sie zudem sicher, dass die richtigen Personen Zugriff auf Anwendungen haben.

**Hinweis:** Es ist wichtig, zu verstehen, dass der Azure AD-Anwendungsproxy ein VPN oder einen Reverseproxy für Roamingbenutzer (oder Remotebenutzer) ersetzen soll, die Zugriff auf interne Ressourcen benötigen. Er ist nicht für interne Benutzer im Unternehmensnetzwerk bestimmt. Interne Benutzer, die den Anwendungsproxy unnötigerweise verwenden, können unerwartete und unerwünschte Leistungsprobleme verursachen.

![Azure Active Directory und alle Ihre Apps](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Ein Überblick über die Funktionsweise des Anwendungsproxys

Der Anwendungsproxy ist ein Azure AD-Dienst, den Sie im Azure-Portal konfigurieren. Sie können mit ihm einen externen öffentlichen HTTP/HTTPS-URL-Endpunkt in der Azure-Cloud veröffentlichen, der eine Verbindung mit einer internen Anwendungsserver-URL in Ihrer Organisation herstellt. Diese lokalen Web-Apps können für die Unterstützung von einmaligem Anmelden in Azure AD integriert werden. Endbenutzer können dann auf die gleiche Weise auf lokale Web-Apps zugreifen wie auf Microsoft 365 und andere SaaS-Apps.

Die Komponenten dieses Features umfassen den Anwendungsproxydienst, der in der Cloud ausgeführt wird, den Anwendungsproxyconnector, der ein einfacher auf einem lokalen Server ausgeführter Agent ist, und den Identitätsanbieter Azure AD. Alle drei Komponenten zusammen ermöglichen dem Benutzer einmaliges Anmelden für den Zugriff auf lokale Webanwendungen.

Externe Benutzer können nach dem Anmelden mit einer vertrauten URL oder über [Meine Apps](../user-help/my-apps-portal-end-user-access.md) auf ihren Desktop- oder iOS/MAC-Geräten auf lokale Webanwendungen zugreifen. Der Anwendungsproxy kann z.B. Remotezugriff und einmaliges Anmelden für Remotedesktop, SharePoint-Websites, Tableau, Qlik, Outlook im Web und branchenspezifische Anwendungen ermöglichen.

![Architektur des Azure AD-Anwendungsproxys](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentifizierung

Es gibt mehrere Möglichkeiten, eine Anwendung für einmaliges Anmelden zu konfigurieren, und die gewählte Methode hängt von der Authentifizierung ab, die von der Anwendung verwendet wird. Der Anwendungsproxy unterstützt die folgenden Typen von Anwendungen:

* Webanwendungen
* Web-APIs, die Sie für umfassende Anwendungen auf unterschiedlichen Geräten verfügbar machen möchten
* Hinter einem Remotedesktopgateway gehostete Anwendungen
* Rich Client-Apps, die in die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](../develop/v2-overview.md) integriert sind

Der Anwendungsproxy eignet sich für Apps, die das folgende Protokoll für die native Authentifizierung verwenden:

* **[Integrierte Windows-Authentifizierung (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Bei IWA verwenden die Anwendungsproxyconnectors die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD), um Benutzer für die Kerberos-Anwendung zu authentifizieren.

Der Anwendungsproxy unterstützt auch die folgenden Authentifizierungsprotokolle mit Drittanbieterintegration oder in bestimmten Konfigurationsszenarien:

* [**Headerbasierte Authentifizierung**](./application-proxy-configure-single-sign-on-with-headers.md). Bei dieser Anmeldungsmethode wird ein Drittanbieter-Authentifizierungsdienst mit dem Namen PingAccess verwendet, wenn die Authentifizierung für die Anwendung mithilfe von Headern erfolgt. In diesem Szenario erfolgt die Authentifizierung durch PingAccess.
* [**Formular- oder kennwortbasierte Authentifizierung**](application-proxy-configure-single-sign-on-password-vaulting.md). Bei dieser Authentifizierungsmethode melden sich Benutzer mit einem Benutzernamen und einem Kennwort bei der Anwendung an, wenn sie zum ersten Mal darauf zugreifen. Nach der ersten Anmeldung werden der Benutzername und das Kennwort von Azure AD für die Anwendung bereitgestellt. In diesem Szenario erfolgt die Authentifizierung durch Azure AD.
* [**SAML-Authentifizierung**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML-basiertes einmaliges Anmelden wird für Anwendungen unterstützt, die das SAML 2.0- oder das WS-Verbundprotokoll verwenden. Mit SAML-SSO nimmt Azure AD die Authentifizierung bei der Anwendung mithilfe des Azure AD-Kontos des Benutzers vor.

Weitere Informationen zu unterstützten Methoden finden Sie unter [Auswählen einer Methode für einmaliges Anmelden](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Sicherheitsvorteile

Die Remotezugriffslösung des Anwendungsproxys und Azure AD bieten mehrere Sicherheitsvorteile für Kunden, darunter die folgenden:

* **Authentifizierter Zugriff**. Der Anwendungsproxy eignet sich optimal zum Veröffentlichen von Anwendungen mit [Vorauthentifizierung](application-proxy-security.md#authenticated-access), um sicherzustellen, dass nur authentifizierte Verbindungen mit Ihrem Netzwerk hergestellt werden. Für mit Vorauthentifizierung veröffentlichte Anwendungen ist ohne gültiges Token kein Datenverkehr durch den Anwendungsproxydienst zur lokalen Umgebung zulässig. Durch die Vorauthentifizierung wird eine erhebliche Anzahl zielgerichteter Angriffe blockiert, da der Zugriff auf die Back-End-Anwendung nur für authentifizierte Identitäten zugelassen wird.
* **Bedingter Zugriff**. Vor dem Herstellen von Verbindungen mit dem Netzwerk können umfassendere Richtlinienkontrollen angewendet werden. Beim bedingten Zugriff können Sie Einschränkungen für den Datenverkehr definieren, den Sie für die Back-End-Anwendung zulassen. Sie erstellen Richtlinien, mit denen Anmeldungen basierend auf dem Standort, der Authentifizierungssicherheit und dem Risikoprofil des Benutzers eingeschränkt werden. Mit de Weiterentwicklung des bedingten Zugriffs werden weitere Kontrollen hinzugefügt, um zusätzliche Sicherheit, z.B. die Integration in Microsoft Cloud App Security (MCAS), zu bieten. Die MCAS-Integration ermöglicht Ihnen das Konfigurieren einer lokalen Anwendung für die [Überwachung in Echtzeit](application-proxy-integrate-with-microsoft-cloud-application-security.md), indem Sie mit bedingtem Zugriff Sitzungen auf Grundlage von Richtlinien für bedingten Zugriff in Echtzeit überwachen und steuern.
* **Beendigung des Datenverkehrs**. Der gesamte Datenverkehr zur Back-End-Anwendung wird im Anwendungsproxydienst in der Cloud beendet, während die Sitzung mit dem Back-End-Server wiederhergestellt wird. Diese Verbindungsstrategie bedeutet, dass Ihre Back-End-Server nicht für direkten HTTP-Datenverkehr zugänglich sind. Sie werden besser vor gezielten DoS-Angriffen (Denial-of-Service) geschützt, da kein Angriff auf die Firewall erfolgt.
* **Gesamter Zugriff in ausgehender Richtung**. Die Anwendungsproxyconnectors verwenden ausgehende Verbindungen mit dem Anwendungsproxydienst in der Cloud nur über die Ports 80 und 443. Wenn keine eingehenden Verbindungen vorhanden sind, besteht keine Notwendigkeit, Firewallports für eingehende Verbindungen oder Komponenten in der DMZ zu öffnen. Alle Verbindungen sind ausgehende Verbindungen über einen sicheren Kanal.
* **Sicherheitsanalyse und KI-basiertes Machine Learning**. Da der Anwendungsproxy Teil von Azure Active Directory ist, kann er [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) nutzen (erfordert eine [Premium P2-Lizenz](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection kombiniert Machine Learning-Sicherheitsinformationen mit Datenfeeds aus der Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) und dem[Microsoft Security Response Center](https://www.microsoft.com/msrc) für die proaktive Identifizierung von gefährdeten Konten. Identity Protection bietet Echtzeitschutz vor Anmeldungen mit hohem Risiko. Dabei werden Faktoren wie Zugriffe von infizierten Geräten, über anonymisierte Netzwerke oder von atypischen oder unwahrscheinlichen Standorten berücksichtigt, um das Risikoprofil einer Sitzung zu erhöhen. Dieses Risikoprofil wird für den Echtzeitschutz verwendet. Viele dieser Berichte und Ereignisse sind über eine API zur Integration in Ihre SIEM-Systeme bereits verfügbar.

* **Remotezugriff als Dienst**. Sie müssen sich nicht mit dem Warten und Patchen von lokalen Servern beschäftigen, um Remotezugriff zu ermöglichen. Der Anwendungsproxy ist ein Internetskalierungsdienst, der sich im Besitz von Microsoft befindet. So erhalten Sie immer die neuesten Sicherheitspatches und -upgrades. Software ohne die richtigen Patches ist immer noch eine häufige Ursache für eine große Zahl von Angriffen. Laut dem US-Heimatschutzministerium lassen sich [85 Prozent der gezielten Angriffe verhindern](https://www.us-cert.gov/ncas/alerts/TA15-119A). Bei diesem Servicemodell können Sie auf den hohen Aufwand verzichten, die Edgeserver zu verwalten und ggf. zu verschlüsseln, um sie zu patchen.

* **Intune-Integration**. Mit Intune wird der Unternehmensdatenverkehr getrennt vom persönlichen Datenverkehr weitergeleitet. Der Anwendungsproxy stellt die Authentifizierung des Datenverkehrs sicher. Der [Anwendungsproxy und Intune Managed Browser](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) können zusammen verwendet werden, um Remotebenutzern den sicheren Zugriff auf interne Websites von iOS- und Android-Geräten zu ermöglichen.

### <a name="roadmap-to-the-cloud"></a>Roadmap zur Cloud

Ein weiterer großer Vorteil der Implementierung des Anwendungsproxys ist die Erweiterung von Azure AD auf die lokale Umgebung. Das Implementieren des Anwendungsproxys ist ein wichtiger Schritt bei der Migration Ihrer Organisation und Apps zur Cloud. Durch die Migration zur Cloud und den Verzicht auf lokale Authentifizierung reduzieren Sie den lokalen Aufwand und verwenden als Steuerungsebene die Identitätsverwaltungsfunktionen von Azure AD. Sie haben Zugriff auf Cloudfunktionen wie einmaliges Anmelden, mehrstufige Authentifizierung und zentrale Verwaltung, und dies mit minimalen Updates vorhandener Anwendungen oder ganz ohne solche Updates. Die erforderlichen Komponenten des Anwendungsproxys zum Einrichten eines Frameworks für Remotezugriff lassen sich einfach installieren. Und durch die Migration zur Cloud haben Sie Zugriff auf die neuesten Features, Updates und Funktionen von Azure AD, z.B. Hochverfügbarkeit und Notfallwiederherstellung.

Weitere Informationen über das Migrieren von Apps zu Azure AD finden Sie im Whitepaper [Migrating Your Applications to Azure Active Directory](https://aka.ms/migrateapps/whitepaper) (Migrieren Ihrer Anwendungen zu Azure Active Directory, in englischer Sprache).

## <a name="architecture"></a>Aufbau

Das folgende Diagramm veranschaulicht, wie die Azure AD-Authentifizierungsdienste und der Anwendungsproxy gemeinsam das einmalige Anmelden bei lokalen Anwendungen für Endbenutzer bereitstellen.

![Ablauf der Authentifizierung über den Azure AD-Anwendungsproxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Nachdem der Benutzer über einen Endpunkt auf die Anwendung zugegriffen hat, wird er an die Azure AD-Anmeldeseite weitergeleitet. Wenn Sie Richtlinien für bedingten Zugriff konfiguriert haben, werden jetzt bestimmte Bedingungen überprüft, um sicherzustellen, dass Sie die Sicherheitsanforderungen Ihrer Organisation erfüllen.
2. Nach der erfolgreichen Anmeldung sendet Azure AD ein Token an das Clientgerät des Benutzers.
3. Der Client sendet das Token an den Anwendungsproxy-Dienst, der den Benutzerprinzipalnamen (UPN) und den Sicherheitsprinzipalnamen (SPN) aus dem Token abruft.
4. Der Anwendungsproxy leitet die Anforderung weiter, die vom [Anwendungsproxyconnector](application-proxy-connectors.md) übernommen wird.
5. Der Connector führt jede weitere Authentifizierung durch, die für den Benutzer erforderlich ist (*optional, je nach Authentifizierungsmethode*), fordert den internen Endpunkt des Anwendungsservers an und sendet die Anforderung an die lokale Anwendung.
6. Die Antwort des Anwendungsservers wird über den Connector an den Anwendungsproxydienst gesendet.
7. Die Antwort wird vom Anwendungsproxydienst an den Benutzer gesendet.

|**Komponente**|**Beschreibung**|
|:-|:-|
|Endpunkt|Der Endpunkt ist eine URL oder ein [Endbenutzerportal](end-user-experiences.md). Benutzer können außerhalb Ihres Netzwerks über eine externe URL auf Anwendungen zugreifen. Benutzer in Ihrem Netzwerk können über eine URL oder ein Endbenutzerportal auf die Anwendung zugreifen. Wenn Benutzer einen dieser Endpunkte erreichen, authentifizieren sie sich in Azure AD und werden dann über den Connector an die lokale Anwendung geleitet.|
|Azure AD|Azure AD führt die Authentifizierung mit dem Mandantenverzeichnis aus, das in der Cloud gespeichert ist.|
|Anwendungsproxy-Dienst|Dieser Anwendungsproxy-Dienst wird in der Cloud als Teil von Azure AD ausgeführt. Er übergibt das Anmeldetoken des Benutzers an den Anwendungsproxy-Connector. Der Anwendungsproxy leitet alle in der Anforderung verfügbaren Header weiter und legt die Header gemäß seinem Protokoll auf die Client-IP-Adresse fest. Enthält die eingehende Anforderung an den Proxy bereits diesen Header, wird die Client-IP-Adresse am Ende der durch Trennzeichen getrennten Liste hinzugefügt, die der Wert des Headers ist.|
|Anwendungsproxyconnector|Der Connector ist ein einfacher Agent, der auf einem Windows-Server innerhalb Ihres Netzwerks ausgeführt wird. Der Connector verwaltet die Kommunikation zwischen dem Anwendungsproxydienst in der Cloud und der lokalen Anwendung. Der Connector verwendet nur ausgehende Verbindungen. Sie müssen also keine eingehenden Ports öffnen oder Ressourcen in die DMZ einfügen. Connectors sind zustandslos und rufen alle Informationen nach Bedarf aus der Cloud ab. Weitere Informationen zu Connectors, z.B. wie der Lastenausgleich und die Authentifizierung funktioniert, finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory wird lokal ausgeführt, um die Authentifizierung für Domänenkonten durchzuführen. Wenn einmaliges Anmelden konfiguriert ist, kommuniziert der Connector mit AD, um jede weitere erforderliche Authentifizierung auszuführen.|
|Lokale Anwendung|Schließlich kann der Benutzer auf eine lokale Anwendung zugreifen.|

Der Azure AD-Anwendungsproxy besteht aus dem cloudbasierten Anwendungsproxydienst und einem lokalen Connector. Der Connector lauscht auf Anforderungen des Anwendungsproxydiensts und wickelt die Verbindungen mit den internen Anwendungen ab. Es ist zu beachten, dass die gesamte Kommunikation per TLS erfolgt und immer vom Connector zum Anwendungsproxydienst verläuft. Das heißt, dass nur ausgehende Kommunikation erfolgt. Der Connector verwendet ein Clientzertifikat, um den Anwendungsproxydienst für alle Aufrufe zu authentifizieren. Die einzige Ausnahme bei der Verbindungssicherheit ist hierbei der anfängliche Setupschritt, bei dem das Clientzertifikat eingerichtet wird. Weitere Informationen finden Sie im Abschnitt [Im Hintergrund](application-proxy-security.md#under-the-hood) des Artikels zum Anwendungsproxy.

### <a name="application-proxy-connectors"></a>Anwendungsproxyconnectors

[Anwendungsproxyconnectors](application-proxy-connectors.md) sind einfache Agents, die lokal bereitgestellt werden und die ausgehende Verbindung mit dem Anwendungsproxydienst vereinfachen. Die Connectors müssen auf einem Windows-Server installiert sein, der auf die Back-End-Anwendung zugreifen kann. Die Benutzer stellen eine Verbindung mit dem Anwendungsproxy-Clouddienst her, der den Datenverkehr wie unten veranschaulicht über die Connectors an die Apps weiterleitet.

![Netzwerkverbindungen des Azure AD-Anwendungsproxys](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Die Einrichtung und Registrierung zwischen einem Connector und dem Anwendungsproxydienst werden wie folgt durchgeführt:

1. Der IT-Administrator öffnet die Ports 80 und 443 für ausgehenden Datenverkehr und ermöglicht den Zugriff auf verschiedene URLs, die für den Connector, den Anwendungsproxydienst und Azure AD erforderlich sind.
2. Der Administrator meldet sich beim Azure-Portal an und führt eine ausführbare Datei aus, um den Connector auf einem lokalen Windows-Server zu installieren.
3. Der Connector beginnt, auf den Anwendungsproxydienst zu lauschen.
4. Der Administrator fügt Azure AD die lokale Anwendung hinzu und konfiguriert Einstellungen, z.B. die URLs, die Benutzer für die Verbindung mit ihren Apps benötigen.

Weitere Informationen finden Sie unter [Planen der Bereitstellung eines Azure AD-Anwendungsproxys](application-proxy-deployment-plan.md).

Es wird empfohlen, aus Gründen der Redundanz und Skalierung immer mehrere Connectors bereitzustellen. Die Connectors wickeln zusammen mit dem Dienst alle Aufgaben für Hochverfügbarkeit ab, und sie können dynamisch hinzugefügt oder entfernt werden. Jedes Mal, wenn eine neue Anforderung eintrifft, wird diese an einen der verfügbaren Connectors geleitet. Wenn ein Connector ausgeführt wird, bleibt er beim Herstellen der Verbindung mit dem Dienst aktiv. Falls ein Connector vorübergehend nicht verfügbar ist, reagiert er nicht auf diesen Datenverkehr. Connectors, die nicht verwendet werden, werden als inaktiv gekennzeichnet und nach 10 Tagen Inaktivität entfernt.

Connectors fragen den Server ab, um zu ermitteln, ob eine neuere Version des Connectors vorhanden ist. Sie können zwar ein manuelles Update durchführen, jedoch werden die Connectors automatisch aktualisiert, solange der Anwendungsproxy-Connectorupdatedienst ausgeführt wird. Bei Mandanten mit mehreren Connectors werden die automatischen Updates nacheinander auf die einzelnen Connectors in jeder Gruppe angewendet, um Ausfallzeiten in Ihrer Umgebung zu vermeiden.

**Hinweis**: Sie können die [Seite mit dem Versionsverlauf](application-proxy-release-version-history.md) des Anwendungsproxys überwachen, um sich durch das Abonnieren des RSS-Feeds benachrichtigen zu lassen, wenn Updates veröffentlicht wurden.

Jeder Anwendungsproxyconnector wird einer [Connectorgruppe](application-proxy-connector-groups.md) zugewiesen. Connectors in der gleichen Connectorgruppe fungieren als einzelne Einheit für Hochverfügbarkeit und Lastenausgleich. Sie können neue Gruppen erstellen, ihnen im Azure-Portal Connectors zuweisen und dann spezielle Connectors für bestimmte Anwendungen zuweisen. Für Hochverfügbarkeit wird empfohlen, dass jede Connectorgruppe mindestens zwei Connectors enthält.

Connectorgruppen sind hilfreich, wenn die folgenden Szenarien unterstützt werden müssen:

* Veröffentlichen geografischer Apps
* Anwendungssegmentierung/-isolation
* Veröffentlichen von Web-Apps, die in der Cloud oder lokal ausgeführt werden

Weitere Informationen zur Optimierung des Netzwerks und zur Auswahl des richtigen Installationsorts der Connectors finden Sie unter [Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Weitere Anwendungsfälle

In den vorangehenden Ausführungen wurde der Anwendungsproxy verwendet, um lokale Apps extern zu veröffentlichen, während einmaliges Anmelden bei allen lokalen und Cloud-Apps ermöglicht wurde. Es gibt jedoch weitere Anwendungsfälle für den Anwendungsproxy, die der Betrachtung wert sind. Dazu gehören:

* **Sicheres Veröffentlichen von REST-APIs**. Wenn Sie über Geschäftslogik oder APIs verfügen, die lokal oder auf virtuellen Computern in der Cloud ausgeführt werden, bietet der Anwendungsproxy einen öffentlichen Endpunkt für den API-Zugriff. Durch den Zugriff auf den API-Endpunkt können Sie Authentifizierung und Autorisierung steuern, ohne dass eingehenden Ports erforderlich sind. Er bietet mit Azure AD Premium-Features, z.B. mehrstufige Authentifizierung und gerätebasierter bedingter Zugriff für Desktops, iOS-, MAC- und Android-Geräte mithilfe von Intune, zusätzliche Sicherheit. Weitere Informationen finden Sie unter [Aktivieren von nativen Clientanwendungen für die Interaktion mit Proxyanwendungen](application-proxy-configure-native-client-application.md) und [Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Remotedesktopdienste** **(RDS)** : RDS-Standardbereitstellungen erfordern offene eingehende Verbindungen. Die [RDS-Bereitstellung mit dem Anwendungsproxy](application-proxy-integrate-with-remote-desktop-services.md) verfügt jedoch über eine permanente ausgehende Verbindung von dem Server, auf dem der Connectordienst ausgeführt wird. So können Sie durch das Veröffentlichen von lokalen Anwendungen über Remotedesktopdienste den Endbenutzern mehr Anwendungen anbieten. Sie können auch mit einem begrenzten Satz der zweistufigen Überprüfung und bedingtem Zugriff auf RDS die Angriffsfläche der Bereitstellung verringern.
* **Veröffentlichen von Anwendungen, die eine Verbindung über WebSockets herstellen**. Die Unterstützung von [Qlik Sense](application-proxy-qlik.md) ist in der öffentlichen Vorschau verfügbar und wird in Zukunft auf weitere Apps erweitert.
* **Aktivieren von nativen Clientanwendungen für die Interaktion mit Proxyanwendungen**. Sie können mit dem Azure Active Directory-Anwendungsproxy Web-Apps veröffentlichen, er kann aber auch zum Veröffentlichen [nativer Clientanwendungen](application-proxy-configure-native-client-application.md) verwendet werden, die mit der Azure AD-Authentifizierungsbibliothek (ADAL) konfiguriert werden. Native Clientanwendungen unterscheiden sich von Web-Apps dadurch, dass sie auf einem Gerät installiert werden, während auf Web-Apps über einen Browser zugegriffen wird.

## <a name="conclusion"></a>Zusammenfassung

Unsere Arbeitsmethoden und die von uns verwendeten Tools sind einem schnellen Wandel unterworfen. Da mehr Mitarbeiter ihre eigenen Geräte zur Arbeit mitbringen und SaaS-Anwendungen (Software-as-a-Service) (SaaS) in hohem Ausmaß verwendet werden, muss auch das Sichern und Verwalten der Daten durch Organisationen weiterentwickelt werden. Unternehmen agieren nicht mehr nur innerhalb der eigenen Wände und durch einen Graben geschützt. Die Datenübertragung erfolgt zwischen mehr Standorten als je zuvor– lokal und in der Cloud. Durch diese Entwicklung wurden die Produktivität und die Fähigkeit zur Zusammenarbeit der Benutzer verbessert, jedoch auch der Schutz vertraulicher Daten schwieriger gemacht.

Ob Sie derzeit Azure AD zum Verwalten von Benutzern in einem Hybridszenario verwenden oder mit der Verwendung der Cloud beginnen möchten – durch das Implementieren des Azure AD-Anwendungsproxys können Sie den lokalen Aufwand verringern, indem Sie Remotezugriff als Dienst bereitstellen.

Organisationen sollten den Anwendungsproxy verwenden, um die folgenden Vorteile zu nutzen:

* Externes Veröffentlichen lokaler Apps ohne den Aufwand, herkömmliche VPN- oder andere lokale Webveröffentlichungslösungen zu verwalten und eine DMZ zu verwenden
* Einmaliges Anmelden bei allen Anwendungen, ob Microsoft 365 oder andere SaaS-Apps, einschließlich lokaler Anwendungen
* Sicherheit auf Cloudniveau, wobei Azure AD mithilfe von Microsoft 365-Telemetrie unberechtigten Zugriff verhindert
* Intune-Integration, um die Authentifizierung des Unternehmensdatenverkehrs sicherzustellen
* Zentralisierung der Benutzerkontoverwaltung
* Automatische Updates, um sicherzustellen, dass Sie über die neuesten Sicherheitspatches verfügen
* Neue Features, sobald sie veröffentlicht werden, aktuelle Unterstützung von SAML-SSO und detaillierte Verwaltung von Anwendungscookies

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Planen, Verwenden und Verwalten des Azure AD-Anwendungsproxys finden Sie unter [Planen der Bereitstellung eines Azure AD-Anwendungsproxys](application-proxy-deployment-plan.md).
* Informationen zum Planen einer Livedemo oder zum Erhalten einer kostenlosen 90-Tage-Testversion für Evaluierungszwecke finden Sie unter [Erste Schritte mit Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).