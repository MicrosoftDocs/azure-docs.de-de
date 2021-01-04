---
title: Sicherheitsaspekte für Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Die Sicherheitsaspekte bei Verwendung des Azure AD-Anwendungsproxys werden beschrieben.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794c4e1a0859fc8a36b0abf4fcc9d5243c8bd308
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649567"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Sicherheitsaspekte beim Remotezugriff auf Apps mit dem Azure AD-Anwendungsproxy

In diesem Artikel werden die Komponenten beschrieben, die Ihre Benutzer und Anwendungen schützen, wenn Sie den Azure Active Directory-Anwendungsproxy verwenden.

Im folgenden Diagramm ist dargestellt, wie mit Azure AD der sichere Remotezugriff auf Ihre lokalen Anwendungen ermöglicht wird.

 ![Diagramm des sicheren Remotezugriffs über den Azure AD-Anwendungsproxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Sicherheitsvorteile

Der Azure AD-Anwendungsproxy bietet die folgenden Sicherheitsvorteile:

### <a name="authenticated-access"></a>Authentifizierter Zugriff 

Wenn Sie sich für die Verwendung der Azure Active Directory-Präauthentifizierung entscheiden, kann nur mit authentifizierten Verbindungen auf Ihr Netzwerk zugegriffen werden.

Der Azure AD-Anwendungsproxy basiert auf dem Azure AD-Sicherheitstokendienst (Security Token Service, STS) für alle Authentifizierungen.  Bei der Vorauthentifizierung liegt es in der Natur der Sache, dass eine erhebliche Anzahl von anonymen Angriffen blockiert wird, da nur für authentifizierte Identitäten der Zugriff auf die Back-End-Anwendung zugelassen wird.

Wenn Sie Passthrough als Methode für die Präauthentifizierung wählen, kommen Sie nicht in den Genuss dieses Vorteils. 

### <a name="conditional-access"></a>Bedingter Zugriff

Wenden Sie umfassendere Richtlinienkontrollen an, bevor Verbindungen mit Ihrem Netzwerk hergestellt werden.

Beim [bedingten Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md) können Sie Einschränkungen definieren, um zu steuern, wie Benutzer auf Ihre Anwendungen zugreifen können. Sie können Richtlinien erstellen, mit denen Anmeldungen basierend auf dem Standort, der Authentifizierungssicherheit und dem Risikoprofil des Benutzers eingeschränkt werden.

Außerdem können Sie den bedingten Zugriff verwenden, um Multi-Factor Authentication-Richtlinien zu konfigurieren und Ihren Benutzerauthentifizierungen so eine weitere Sicherheitsebene hinzuzufügen. Darüber hinaus können Ihre Anwendungen auch über bedingten Azure AD-Zugriff an Microsoft Cloud App Security weitergeleitet werden, um von Echtzeitüberwachung und Steuerungsmöglichkeiten durch [Zugriffsrichtlinien](/cloud-app-security/access-policy-aad) und [Sitzungsrichtlinien](/cloud-app-security/session-policy-aad) zu profitieren.

### <a name="traffic-termination"></a>Beendigung des Datenverkehrs

Der gesamte Datenverkehrsvorgang wird in der Cloud beendet.

Da es sich beim Azure AD-Anwendungsproxy um einen Reverseproxy handelt, wird der gesamte Datenverkehr zu Back-End-Anwendungen beim Dienst beendet. Die Sitzung kann nur mit dem Back-End-Server wiederhergestellt werden. Das bedeutet, dass Ihre Back-End-Server keinem direkten HTTP-Datenverkehr ausgesetzt werden. Diese Konfiguration bedeutet, dass Sie besser vor zielgerichteten Angriffen geschützt sind.

### <a name="all-access-is-outbound"></a>Gesamter Zugriff in ausgehender Richtung 

Sie müssen keine eingehenden Verbindungen mit dem Unternehmensnetzwerk öffnen.

Für Anwendungsproxyconnectors werden nur ausgehende Verbindungen mit dem Azure AD-Anwendungsproxydienst verwendet. Dies bedeutet, dass Firewallports nicht für eingehende Verbindungen geöffnet werden müssen. Herkömmliche Proxys erforderten ein Umkreisnetzwerk (auch bekannt als *DMZ*, *demilitarisierte Zone* und *überwachtes Subnetz*) und erlaubten den Zugriff auf nicht authentifizierte Verbindungen am Rand des Netzwerks. Dieses Szenario erfordert Investitionen in Web Application Firewall-Produkte, um Datenverkehr zu analysieren und die Umgebung zu schützen. Bei Verwenden des Anwendungsproxys benötigen Sie kein Umkreisnetzwerk, da alle Verbindungen ausgehend sind und über einen sicheren Kanal erfolgen.

Informationen zu Connectors finden Sie unter [Understand Azure AD Application Proxy connectors (Grundlegendes zu Azure AD-Anwendungsproxyconnectors)](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analysen und Machine Learning auf Cloudebene 

Setzen Sie auf Sicherheit und Schutz auf dem neuesten Stand.

Als Teil von Azure Active Directory kann der Anwendungsproxy [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) nutzen. Dabei profitiert er von Daten aus dem Microsoft Security Response Center und der Digital Crimes Unit. Zusammen identifizieren wir proaktiv kompromittierte Konten und ermöglichen den Schutz vor Anmeldungen mit hohem Risikofaktor. Wir berücksichtigen zahlreiche Faktoren, um zu bestimmen, welche Anmeldeversuche mit hohem Risiko behaftet sind. Zu diesen Faktoren zählen das Markieren infizierter Geräte, das Anonymisieren von Netzwerken sowie atypische oder unwahrscheinliche Standorte.

Viele dieser Berichte und Ereignisse sind bereits über eine API für die Integration in Ihre Sicherheitsinformations- und Ereignisverwaltungssysteme (Security Information and Event Management, SIEM) verfügbar.

### <a name="remote-access-as-a-service"></a>Remotezugriff als Dienst

Sie müssen sich nicht mit dem Warten und Patchen von lokalen Servern beschäftigen.

Software ohne die richtigen Patches ist immer noch eine häufige Ursache für eine große Zahl von Angriffen. Der Azure AD-Anwendungsproxy ist ein Internetskalierungsdienst, der sich im Besitz von Microsoft befindet. So erhalten Sie immer die neuesten Sicherheitspatches und -upgrades.

Zur Erhöhung der Sicherheit von Anwendungen, die vom Azure AD-Anwendungsproxy veröffentlicht werden, blockieren wir die Indizierung und Archivierung Ihrer Anwendungen durch Webcrawlerroboter. Jedes Mal, wenn ein Webcrawlerroboter versucht, die robots-Einstellungen für eine veröffentlichte App abzurufen, antwortet der Anwendungsproxy mit einer Datei „robots.txt“, die `User-agent: * Disallow: /` enthält.

#### <a name="azure-ddos-protection-service"></a>Azure DDoS Protection-Dienst

Über den Anwendungsproxy veröffentlichte Anwendungen sind vor verteilten Denial-of-Service-Angriffen (Distributed Denial of Service, DDoS) geschützt. Dieser Schutz wird von Microsoft verwaltet und in allen unseren Rechenzentren automatisch aktiviert. Der Azure DDoS Protection-Dienst bietet eine ununterbrochene Datenverkehrsüberwachung sowie Echtzeit-Risikominderung von häufig vorkommenden Angriffen auf Netzwerkebene. 

## <a name="under-the-hood"></a>Hinter den Kulissen

Der Azure AD-Anwendungsproxy besteht aus zwei Teilen:

* Cloudbasierter Dienst: Dieser Dienst wird in Azure ausgeführt und befindet sich dort, wo die externen Client-/Benutzerverbindungen hergestellt werden.
* [Lokaler Connector:](application-proxy-connectors.md) Als lokale Komponente lauscht der Connector auf Anforderungen des Azure AD-Anwendungsproxydiensts und wickelt die Verbindungen mit den internen Anwendungen ab. 

Ein Flow zwischen dem Connector und dem Anwendungsproxydienst wird in folgenden Fällen eingerichtet:

* Bei der Ersteinrichtung des Connectors.
* Der Connector ruft Konfigurationsinformationen aus dem Anwendungsproxydienst ab.
* Ein Benutzer greift auf eine veröffentlichte Anwendung zu.

>[!NOTE]
>Die gesamte Kommunikation erfolgt über TLS und verläuft immer vom Connector zum Anwendungsproxydienst. Der Dienst gilt nur für ausgehenden Datenverkehr.

Der Connector verwendet ein Clientzertifikat, um den Anwendungsproxydienst für nahezu alle Aufrufe zu authentifizieren. Die einzige Ausnahme zu dieser Vorgehensweise ist hierbei der anfängliche Setupschritt, bei dem das Clientzertifikat eingerichtet wird.

### <a name="installing-the-connector"></a>Installieren des Connectors

Bei der Ersteinrichtung des Connectors treten die folgenden Flow-Ereignisse ein:

1. Die Connectorregistrierung beim Dienst erfolgt im Rahmen der Connectorinstallation. Benutzer werden aufgefordert, ihre Azure AD-Administratoranmeldeinformationen einzugeben.  Das aus dieser Authentifizierung abgerufene Token wird dann an den Azure AD-Anwendungsproxydienst übermittelt.
2. Der Anwendungsproxydienst wertet das Token aus. Er überprüft, ob der Benutzer ein Unternehmensadministrator im Mandanten ist.  Wenn der Benutzer kein Administrator ist, wird der Prozess beendet.
3. Der Connector generiert eine Clientzertifikatanforderung und übergibt sie mit dem Token an den Anwendungsproxydienst. Der Dienst überprüft wiederum das Token und signiert die Clientzertifikatanforderung.
4. Der Connector verwendet dieses Clientzertifikat für die zukünftige Kommunikation mit dem Anwendungsproxydienst.
5. Der Connector führt einen ersten Abruf der Systemkonfigurationsdaten vom Dienst durch, indem das dazugehörige Clientzertifikat verwendet wird, und ist jetzt zum Verarbeiten von Anforderungen bereit.

### <a name="updating-the-configuration-settings"></a>Aktualisieren der Konfigurationseinstellungen

Wenn der Anwendungsproxydienst die Konfigurationseinstellungen aktualisiert, treten die folgenden Flow-Ereignisse ein:

1. Der Connector stellt die Verbindung mit dem Konfigurationsendpunkt im Anwendungsproxydienst mit dem dazugehörigen Clientzertifikat her.
2. Nachdem das Clientzertifikat überprüft wurde, gibt der Anwendungsproxydienst Konfigurationsdaten an den Connector zurück. Dies kann beispielsweise die Connectorgruppe sein, in der der Connector enthalten sein soll.
3. Der Connector generiert eine neue Zertifikatanforderung, wenn das aktuelle Zertifikat mehr als 180 Tage alt ist, sodass das Clientzertifikat praktisch alle 180 Tage aktualisiert wird.

### <a name="accessing-published-applications"></a>Zugreifen auf veröffentlichte Anwendungen

Wenn Benutzer auf eine veröffentlichte Anwendung zugreifen, treten zwischen dem Anwendungsproxydienst und dem Anwendungsproxyconnector die folgenden Ereignisse ein:

1. Der Dienst authentifiziert den Benutzer für die Anwendung
2. Der Dienst fügt eine Anforderung in die Connectorwarteschlange ein.
3. Ein Connector verarbeitet die Anforderung aus der Warteschlange
4. Der Connector wartet auf eine Antwort
5. Der Dienst streamt Daten an den Benutzer

Lesen Sie weiter, um mehr Informationen dazu zu erhalten, was in den einzelnen Schritten passiert.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Der Dienst authentifiziert den Benutzer für die Anwendung

Wenn Sie für die App die Verwendung von Passthrough als Präauthentifizierungsmethode konfiguriert haben, werden die Schritte in diesem Abschnitt übersprungen.

Falls Sie für die App die Präauthentifizierung mit Azure AD konfiguriert haben, werden Benutzer für die Authentifizierung an den Azure AD STS umgeleitet, und die folgenden Schritte werden ausgeführt:

1. Der Anwendungsproxy überprüft etwaige Richtlinienanforderungen in Bezug auf den bedingten Zugriff für die jeweilige Anwendung. Mit diesem Schritt wird sichergestellt, dass der Benutzer der Anwendung zugewiesen wurde. Wenn die zweistufige Überprüfung erforderlich ist, fordert die Authentifizierungssequenz den Benutzer zu einer zweistufigen Authentifizierung auf.

2. Nachdem alle Überprüfungen bestanden wurden, stellt Azure AD STS ein signiertes Token für die Anwendung aus und leitet den Benutzer zurück zum Anwendungsproxydienst.

3. Der Anwendungsproxy stellt sicher, dass das Token für die richtige Anwendung ausgestellt wurde. Dieser Schritt wird zusammen mit anderen Überprüfungen durchgeführt, z.B. mit der Sicherstellung, dass das Token von Azure AD signiert wurde und noch nicht abgelaufen ist.

4. Mit dem Anwendungsproxy wird ein verschlüsseltes Cookie für die Authentifizierung festgelegt, um anzugeben, dass die Authentifizierung für die Anwendung erfolgt ist. Das Cookie enthält einen Ablaufzeitstempel, der auf dem Token von Azure AD und anderen Daten basiert, z.B. dem Benutzernamen, auf dem die Authentifizierung basiert. Das Cookie wird mit einem privaten Schlüssel verschlüsselt, der nur dem Anwendungsproxydienst bekannt ist.

5. Der Anwendungsproxy leitet den Benutzer zurück zur ursprünglich angeforderten URL.

Wenn für einen Teil der Schritte für die Vorauthentifizierung Fehler auftreten, wird die Anforderung des Benutzers verworfen, und dem Benutzer wird eine Meldung mit einem Hinweis zur Problemursache angezeigt.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Der Dienst fügt eine Anforderung in die Connectorwarteschlange ein.

Für Connectors wird eine ausgehende Verbindung zum Anwendungsproxydienst offengehalten. Bei Empfang einer Anforderung reiht der Dienst diese in eine Warteschlange für eine der offenen Verbindungen ein, damit sie vom Connector verarbeitet werden kann.

Die Anforderung enthält Elemente aus der Anwendung, z.B. Anforderungsheader und Daten aus dem verschlüsselten Cookie, den Benutzer, der die Anforderung gesendet hat, und die Anforderungs-ID. Zusammen mit der Anforderung werden zwar Daten aus dem verschlüsselten Cookie gesendet, aber das Authentifizierungscookie selbst wird nicht gesendet.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Der Connector verarbeitet die Anforderung aus der Warteschlange. 

Auf Grundlage der Anforderung führt der Anwendungsproxy eine der folgenden Aktionen durch:

* Wenn die Anforderung ein einfacher Vorgang ist, z.B. ohne Daten im Text wie bei einer *GET*-RESTful-Anforderung, stellt der Connector eine Verbindung mit der internen Zielressource her und wartet dann auf eine Antwort.

* Wenn in der Anforderung im Textbereich Daten zugeordnet sind, z.B. bei einem *POST*-RESTful-Vorgang, stellt der Connector mit dem Clientzertifikat eine ausgehende Verbindung mit der Anwendungsproxyinstanz her. Die Verbindung wird zum Anfordern der Daten und zum Öffnen einer Verbindung mit der internen Ressource hergestellt. Nach Erhalt der Anforderung vom Connector beginnt der Anwendungsproxydienst damit, Inhalte vom Benutzer zu akzeptieren und Daten an den Connector weiterzuleiten. Der Connector leitet die Daten wiederum an die interne Ressource weiter.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Der Connector wartet auf eine Antwort.

Nachdem die Anforderung/Übertragung des gesamten Inhalts an das Back-End abgeschlossen ist, wartet der Connector auf eine Antwort.

Nach dem Erhalt einer Antwort stellt der Connector eine ausgehende Verbindung mit dem Anwendungsproxydienst her, um die Headerdetails zurückzugeben und mit dem Streamen der Rückgabedaten zu beginnen.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Der Dienst streamt Daten an den Benutzer. 

Hier kann eine Verarbeitung der Anwendung erfolgen. Wenn Sie den Anwendungsproxy so konfiguriert haben, dass in Ihrer Anwendung Header oder URLs übersetzt werden, wird diese Verarbeitung während dieses Schritts je nach Bedarf durchgeführt.


## <a name="next-steps"></a>Nächste Schritte

[Aspekte der Netzwerktopologie bei Verwendung des Azure AD-Anwendungsproxys](application-proxy-network-topology.md)

[Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)