---
title: Wählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung | Microsoft-Dokumentation
description: Dieser Leitfaden dient CEOs, CIOs, CISOs, Chief Identity Architects, Enterprise Architects sowie Sicherheits- und IT-Entscheidungsträgern als Hilfe, die für die Auswahl einer Authentifizierungsmethode für ihre Azure AD-Hybrididentitätslösung in mittleren bis großen Unternehmen verantwortlich sind.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 773d4dd28da3165261d75e4f800750c1f54377d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702290"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung 

Dieser Artikel ist der Beginn einer Reihe von Artikeln, die Organisationen bei der Implementierung einer kompletten Azure AD-Hybrididentitätslösung (Azure Active Directory) unterstützen. Diese Lösung wurde [als Hybrid Identity Digital Transformation Framework](https://aka.ms/aadframework) (digitales Transformationsframework für Hybrididentitäten) entworfen. Hiermit werden die Geschäftsergebnisse und Ziele abgedeckt, auf die sich Organisationen konzentrieren sollten, um sicherzustellen, dass sie eine stabile und sichere Hybrididentitätslösung implementiert haben. 

Das erste Geschäftsergebnis des Frameworks beschreibt die Anforderungen an Organisationen, den Authentifizierungsprozess zu sichern, wenn Benutzer auf Cloud-Apps zugreifen. Das erste Unternehmensziel beim Geschäftsergebnis mit sicherer Authentifizierung ist, Benutzern die Möglichkeit zu geben, sich mit ihren lokalen Benutzernamen und Kennwörtern an Cloud-Apps anzumelden. Durch diesen Anmelde- und Authentifizierungsprozess stehen in der Cloud alle Türen offen.

Die Wahl der richtigen Authentifizierungsmethode ist die erste Hürde für Organisationen, die ihre Apps in die Cloud verschieben möchten. Nehmen Sie diese Entscheidung aus den folgenden Gründen nicht auf die leichte Schulter:

1. Es ist die erste Entscheidung für eine Organisation, die die Umstellung auf die Cloud vollziehen möchte. 

2. Die Authentifizierungsmethode ist eine wichtige Komponente der Präsenz einer Organisation in der Cloud. Hierüber wird der Zugriff auf alle Clouddaten und -ressourcen gesteuert.

3. Sie ist die Grundlage aller anderen erweiterten Features in Bezug auf die Sicherheit und Benutzererfahrung in Azure AD.

4. Es ist schwierig, die Authentifizierungsmethode zu ändern, nachdem sie implementiert wurde.

Die Identität ist die neue Steuerebene der IT-Sicherheit. Die Authentifizierung ist für eine Organisation daher der Zugriffsschutz für die neue Cloudwelt. Organisationen benötigen eine Steuerebene für die Identität, mit der ihre Sicherheit gestärkt und die Cloud-Apps vor Eindringlingen geschützt werden.

### <a name="out-of-scope"></a>Nicht betreffende Organisationen
Organisationen, die über keine lokalen Verzeichnisse verfügen, stehen nicht im Mittelpunkt dieses Artikels. Normalerweise werden Identitäten von diesen Unternehmen nur in der Cloud erstellt, wofür keine Hybrididentitätslösung erforderlich ist. Ausschließlich in der Cloud existierende Identitäten sind nicht mit entsprechenden lokalen Identitäten verknüpft.

## <a name="authentication-methods"></a>Authentifizierungsmethoden
Wenn die Azure AD-Hybrididentitätslösung Ihre neue Steuerungsebene bildet, ist die Authentifizierung die Grundlage für den Cloudzugriff. Die Wahl der richtigen Authentifizierungsmethode ist daher eine wichtige erste Entscheidung bei der Einrichtung einer Azure AD-Hybrididentitätslösung. Implementieren Sie die Authentifizierungsmethode, die mit Azure AD Connect konfiguriert wird. Hierüber werden auch Benutzer in der Cloud bereitgestellt.

Bei der Auswahl einer Authentifizierungsmethode müssen Sie die Zeit, die vorhandene Infrastruktur, die Komplexität und die Implementierungskosten für die gewählte Lösung berücksichtigen. Diese Faktoren sind für jede Organisation unterschiedlich und können sich im Laufe der Zeit ändern. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD unterstützt für Hybrididentitätslösungen die folgenden Authentifizierungsmethoden.

### <a name="cloud-authentication"></a>Cloudauthentifizierung
Wenn Sie diese Authentifizierungsmethode wählen, übernimmt Azure AD die Anmeldung für Benutzer. In Verbindung mit dem nahtlosen einmaligen Anmelden (SSO) können sich Benutzer bei Cloud-Apps anmelden, ohne ihre Anmeldeinformationen erneut eingeben zu müssen. Bei der Cloudauthentifizierung können Sie zwischen zwei Optionen wählen: 

**Azure AD-Kennworthashsynchronisierung**: Dies ist der einfachste Weg, die Authentifizierung für lokale Verzeichnisobjekte in Azure AD zu ermöglichen. Benutzer können den gleichen Benutzernamen und das gleiche Kennwort wie in der lokalen Umgebung verwenden, ohne eine zusätzliche Infrastruktur bereitstellen zu müssen. Für einige Premium-Features von Azure AD, z.B. Identity Protection und [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync), ist unabhängig davon, welche Authentifizierungsmethode Sie wählen, eine Kennworthashsynchronisierung erforderlich.

> [!NOTE] 
> Kennwörter werden nie im Klartext gespeichert oder mit einem umkehrbaren Algorithmus in Azure AD verschlüsselt. Weitere Informationen zum eigentlichen Prozess der Kennworthashsynchronisierung finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization). 

**Azure AD-Passthrough-Authentifizierung**: Es wird eine einfache Kennwortüberprüfung für Azure AD-Authentifizierungsdienste bereitgestellt, indem ein Software-Agent verwendet wird, der auf einem oder mehreren lokalen Servern ausgeführt wird. Die Server überprüfen die Benutzer direkt über Ihre lokale Active Directory-Instanz, um sicherzustellen, dass die Kennwortüberprüfung nicht in der Cloud erfolgt. 

Unternehmen mit einer Sicherheitsanforderung zur sofortigen Durchsetzung von lokalen Benutzerkontozuständen, Kennwortrichtlinien und Anmeldezeiten würden ggf. diese Authentifizierungsmethode verwenden. Weitere Informationen zum eigentlichen Passthrough-Authentifizierungsprozess finden Sie unter [Benutzeranmeldung mit der Azure AD-Passthrough-Authentifizierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Verbundauthentifizierung
Wenn Sie diese Authentifizierungsmethode wählen, übergibt Azure AD den Authentifizierungsprozess zur Validierung des Benutzerkennworts an ein separates vertrauenswürdiges Authentifizierungssystem, z.B. an eine lokale Instanz von Active Directory-Verbunddienste (AD FS).

Das Authentifizierungssystem kann zusätzliche erweiterte Authentifizierungsanforderungen bereitstellen. Beispiele hierfür sind eine Smartcard-basierte Authentifizierung oder eine mehrstufige Authentifizierung durch Drittanbieter. Weitere Informationen finden Sie im [Windows Server 2016 und 2012 R2 AD FS-Bereitstellungshandbuch](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Der folgende Abschnitt hilft Ihnen, anhand einer Entscheidungsstruktur die für Sie richtige Authentifizierungsmethode zu ermitteln. So können Sie entscheiden, ob Sie eine Cloud- oder Verbundauthentifizierung für Ihre Azure AD-Hybrididentitätslösung einsetzen möchten.

## <a name="decision-tree"></a>Entscheidungsstruktur

![Entscheidungsstruktur zur Azure AD-Authentifizierung](media/azure-ad/azure-ad-authn-image1.png)

Informationen zu Entscheidungsfragen:

1. Azure AD kann die Anmeldung für Benutzer verarbeiten, ohne auf lokale Komponenten zur Überprüfung von Kennwörtern angewiesen zu sein.
2. Azure AD kann die Benutzeranmeldung an einen vertrauenswürdigen Authentifizierungsanbieter übergeben, z.B. Microsoft AD FS.
3. Azure AD benötigt einige lokale Komponenten, wenn Sie Active Directory-Sicherheitsrichtlinien auf Benutzerebene anwenden müssen, z.B. für abgelaufene Konten, deaktivierte Konten, abgelaufene Kennwörter, gesperrte Konten und Anmeldezeiten für Benutzeranmeldungen.
4. Anmeldefeatures, die nicht nativ durch Azure AD unterstützt werden:
   * Melden Sie sich mit Smartcards oder Zertifikaten an.
   * Melden Sie sich mit einem lokalen MFA-Server an.
   * Melden Sie sich mit einer Authentifizierungslösung eines Drittanbieters an.
   * Lokale Authentifizierungslösung für mehrere Standorte.
5. Azure AD Identity Protection benötigt für die Bereitstellung des Berichts *Benutzer mit kompromittierten Anmeldeinformationen* die Kennworthashsynchronisierung, unabhängig davon, welche Anmeldemethode Sie auswählen. Organisationen können ein Failover zur Kennworthashsynchronisierung ausführen, wenn bei der primären Anmeldemethode ein Fehler auftritt und diese vor dem Fehlerereignis konfiguriert wurde.

>[!NOTE]
> Azure AD Identity Protection erfordert Lizenzen von [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="detailed-considerations"></a>Ausführliche Überlegungen

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudauthentifizierung Kennworthashsynchronisierung

* **Aufwand**: Die Kennworthashsynchronisierung ist mit dem geringsten Aufwand in Bezug auf Bereitstellung, Wartung und Infrastruktur verbunden.  Diese Art von Aufwand fällt normalerweise für Organisationen an, in denen sich Benutzer nur an Office 365, SaaS-Apps und anderen Azure AD-basierten Ressourcen anmelden müssen. Im aktivierten Zustand ist die Kennworthashsynchronisierung Teil des Azure AD Connect-Synchronisierungsprozesses, der alle zwei Minuten ausgeführt wird.

* **Benutzererfahrung**: Stellen Sie das nahtlose einmalige Anmelden mit Kennworthashsynchronisierung bereit, um die Anmeldeerfahrung für Benutzer zu verbessern. Beim nahtlosen einmaligen Anmelden werden für Benutzer bei der Anmeldung keine unnötigen Aufforderungen angezeigt.

* **Erweiterte Szenarien**: Wenn Organisationen sich dafür entscheiden, können sie Erkenntnisse aus Identitäten gewinnen, indem Azure AD Identity Protection-Berichte mit Azure AD Premium P2 verwendet werden. Ein Beispiel hierfür ist der Bericht zu kompromittierten Anmeldeinformationen. Windows Hello for Business verfügt über [spezifische Anforderungen, wenn Sie die Kennworthashsynchronisierung verwenden](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync) erfordern Kennworthashsynchronisierung, um Endbenutzern ihre Unternehmensanmeldeinformationen in der verwalteten Domäne bereitzustellen.

    Organisationen, die eine mehrstufige Authentifizierung mit Kennworthashsynchronisierung benötigen, müssen die Multi-Factor Authentication von Azure AD verwenden. Es ist für diese Organisationen nicht möglich, Verfahren zur mehrstufigen Authentifizierung zu nutzen, die von Drittanbietern oder lokal angeboten werden.

* **Geschäftskontinuität**: Die Verwendung der Kennworthashsynchronisierung mit Cloudauthentifizierung ist als Clouddienst, der auf alle Microsoft-Datencenter skaliert wird, hoch verfügbar. Stellen Sie einen zweiten Azure AD Connect-Server im Stagingmodus für eine Standbykonfiguration bereit, um sicherzustellen, dass die Kennworthashsynchronisierung nicht längere Zeit ausfällt.

* **Überlegungen**: Derzeit werden von der Kennworthashsynchronisierung nicht sofort Änderungen des Zustands lokaler Konten erzwungen. In dieser Situation hat ein Benutzer Zugriff auf Cloud-Apps, bis der Status des Benutzerkontos mit Azure AD synchronisiert ist. Es kann für Organisationen ratsam sein, diese Einschränkung zu umgehen, indem ein neuer Synchronisierungszyklus ausgeführt wird, nachdem Administratoren Massenupdates für Zustände lokaler Benutzerkonten vorgenommen haben. Ein Beispiel hierfür ist die Deaktivierung von Konten.

> [!NOTE]
> Das Kennwort ist abgelaufen, und die Status von Kontosperren sind derzeit nicht über Azure AD Connect mit Azure AD synchronisiert. 

Informationen zu den Bereitstellungsschritten finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization).

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudauthentifizierung Passthrough-Authentifizierung  

* **Aufwand**: Für die Passthrough-Authentifizierung benötigen Sie mindestens einen einfachen Agent (Empfehlung: drei Agents), die auf vorhandenen Servern installiert sind. Diese Agents müssen Zugriff auf Ihre lokale Instanz von Active Directory Domain Services haben, einschließlich Ihrer lokalen AD-Domänencontroller. Sie benötigen Berechtigungen für den Zugriff auf das Internet und den Zugriff auf Ihre Domänencontroller. Aus diesem Grund wird das Bereitstellen der Agents in einem Umkreisnetzwerk nicht unterstützt. 

    Für die Passthrough-Authentifizierung ist der uneingeschränkte Netzwerkzugriff auf Domänencontroller erforderlich. Der gesamte Netzwerkverkehr ist verschlüsselt und auf Authentifizierungsanfragen beschränkt. Weitere Informationen zu diesem Prozess finden Sie unter [Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-security-deep-dive).

* **Benutzererfahrung**: Stellen Sie das nahtlose einmalige Anmelden mit Passthrough-Authentifizierung bereit, um die Anmeldeerfahrung für Benutzer zu verbessern. Beim nahtlosen einmaligen Anmelden werden nach der Anmeldung eines Benutzers keine unnötigen Aufforderungen angezeigt.

* **Erweiterte Szenarien**: Bei der Passthrough-Authentifizierung wird die lokale Kontorichtlinie zum Zeitpunkt der Anmeldung erzwungen. Beispielsweise wird der Zugriff verweigert, wenn ein Konto eines lokalen Benutzers den Status „Deaktiviert“, „Gesperrt“ oder [Kennwort abgelaufen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) hat oder wenn der Zugriffsversuch außerhalb der für den Benutzer zulässigen Anmeldestunden liegt. 

    Organisationen, die eine mehrstufige Authentifizierung mit Passthrough-Authentifizierung benötigen, müssen die Multi-Factor Authentication (MFA) von Azure verwenden. Es ist für diese Organisationen nicht möglich, ein Verfahren zur mehrstufigen Authentifizierung zu nutzen, das von Drittanbietern oder lokal angeboten wird. Für die erweiterten Features muss die Kennworthashsynchronisierung unabhängig davon bereitgestellt werden, ob Sie die Passthrough-Authentifizierung wählen. Ein Beispiel hierfür ist der Bericht zu kompromittierten Anmeldeinformationen von Identity Protection.

* **Geschäftskontinuität**: Wir empfehlen Ihnen, zwei zusätzliche Agents für die Passthrough-Authentifizierung bereitzustellen. Diese Bereitstellung gilt zusätzlich zum ersten Agent auf dem Azure AD Connect-Server. Mit dieser zusätzlichen Bereitstellung wird für Authentifizierungsanforderungen die Hochverfügbarkeit sichergestellt. Wenn Sie drei Agents bereitgestellt haben, kann ein Agent immer noch ausfallen, wenn ein anderer Agent wegen Wartungsarbeiten ausfällt. 

    Die Bereitstellung der Kennworthashsynchronisierung zusätzlich zur Passthrough-Authentifizierung hat noch einen weiteren Vorteil. Sie dient als sekundäre Authentifizierungsmethode, wenn die primäre Authentifizierungsmethode nicht mehr verfügbar ist.

* **Überlegungen**: Sie können die Kennworthashsynchronisierung als sekundäre Authentifizierungsmethode für die Passthrough-Authentifizierung verwenden, wenn die Agents die Anmeldeinformationen eines Benutzers aufgrund eines signifikanten lokalen Ausfalls nicht überprüfen können. Das Failover zur Kennworthashsynchronisierung erfolgt nicht automatisch, und Sie müssen Azure AD Connect verwenden, um die Anmeldemethode manuell zu wechseln. 

    Mehr zu weiteren Überlegungen zur Passthrough-Authentifizierung, z.B. der Unterstützung alternativer IDs, erfahren Sie in den [häufig gestellten Fragen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq).

Weitere Informationen zu den Bereitstellungsschritten finden Sie unter [Benutzeranmeldung mit der Azure Active Directory-Passthrough-Authentifizierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Verbundauthentifizierung

* **Aufwand**: Ein Verbundauthentifizierungssystem greift auf ein externes vertrauenswürdiges System zurück, um Benutzer zu authentifizieren. Einige Unternehmen möchten ihre bestehenden Investitionen in Verbundsysteme in Verbindung mit ihrer Azure AD-Hybrididentitätslösung wiederverwenden. Die Wartung und Verwaltung des Verbundsystems wird nicht über Azure AD gesteuert. Die Organisation, die das Verbundsystem verwendet, muss selbst sicherstellen, dass es die erforderliche Sicherheit bietet und die Authentifizierungslast bewältigen kann. 

* **Benutzererfahrung**: Die Benutzererfahrung der Verbundauthentifizierung hängt von der Implementierung der Funktionen, der Topologie und der Konfiguration der Verbundfarm ab. Einige Organisationen benötigen diese Flexibilität, um den Zugriff auf die Verbundfarm an ihre Sicherheitsanforderungen anzupassen und zu konfigurieren. Beispielsweise ist es möglich, intern verbundene Benutzer und Geräte so zu konfigurieren, dass sie automatisch angemeldet werden, ohne dass die Anmeldeinformationen abgefragt werden. Diese Konfiguration funktioniert, weil die Anmeldung an den Geräten bereits erfolgt ist. Falls erforderlich, kann der Anmeldeprozess für Benutzer durch einige erweiterte Sicherheitsfeatures schwieriger gestaltet werden.

* **Erweiterte Szenarien**: Eine Lösung für die Verbundauthentifizierung ist normalerweise erforderlich, wenn für Kunden eine Authentifizierungsanforderung besteht, die von Azure AD nicht nativ unterstützt wird. Sehen Sie sich die ausführlichen Informationen hierzu an, damit Sie [die richtige Anmeldeoption auswählen können](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Beachten Sie die folgenden häufigen Anforderungen:

  * Authentifizierung mit Smartcards oder Zertifikaten
  * Lokale MFA-Server oder mehrstufige Authentifizierung von Drittanbietern
  * Authentifizierung mit Drittanbieter-Authentifizierungslösungen. Siehe die [Azure AD-Verbund – Kompatibilitätsliste](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-compatibility).
  * Anmeldungen, für die ein sAMAccountName erforderlich ist, z.B. „DOMÄNE\Benutzername“, anstatt eines Benutzerprinzipalnamens (UPN), z.B. user@domain.com

* **Geschäftskontinuität**: Für Verbundsysteme ist normalerweise ein Serverarray mit Lastenausgleich erforderlich, das als Farm bezeichnet wird. Diese Farm wird in einer Topologie mit einem internen Netzwerk und einem Umkreisnetzwerk konfiguriert, um die Hochverfügbarkeit für Authentifizierungsanforderungen sicherzustellen.

    Stellen Sie die Kennworthashsynchronisierung zusammen mit der Verbundauthentifizierung als sekundäre Authentifizierungsmethode bereit, wenn die primäre Authentifizierungsmethode nicht mehr verfügbar ist. Ein Beispiel hierfür ist der Fall, in dem die lokalen Server nicht mehr verfügbar sind. Einige Großunternehmen benötigen eine Verbundlösung, um mehrere per Geo-DNS konfigurierte Internetzugangspunkte für Authentifizierungsanforderungen mit geringer Latenz zu unterstützen.

* **Überlegungen**: Für Verbundsysteme müssen in der Regel größere Investitionen in die lokale Infrastruktur getätigt werden. Die meisten Organisationen wählen diese Option, wenn sie bereits in ein lokales Verbundsystem investiert haben. Sie wird auch gewählt, wenn es eine wichtige geschäftliche Anforderung ist, einen Anbieter für Einzelidentitäten zu verwenden. Die Bereitstellung und Fehlerbehebung bei einer Verbundlösung ist im Vergleich zu Cloudauthentifizierungslösungen komplexer.

Für eine nicht routingfähige Domäne, die in Azure AD nicht verifiziert werden kann, benötigen Sie eine zusätzliche Konfiguration, unter der Sie die Benutzer-ID implementieren können. Diese Anforderung wird als Unterstützung für alternative Anmelde-IDs bezeichnet. Weitere Informationen zu Einschränkungen und Anforderungen finden Sie unter [Konfigurieren von alternativen Anmelde-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). Wenn Sie sich entschließen, einen externen Anbieter für mehrstufige Authentifizierung mit Verbund zu nutzen, sollten Sie sich vergewissern, dass der Anbieter WS-Trust unterstützt, damit Geräte in Azure AD eingebunden werden können.

Informationen zu den Bereitstellungsschritten finden Sie unter [Bereitstellen von Verbundservern](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers).

> [!NOTE] 
> Bei der Bereitstellung Ihrer Azure AD-Hybrididentitätslösung müssen Sie eine der von Azure AD Connect unterstützten Topologien implementieren. Erfahren Sie mehr über unterstützte und nicht unterstützte Konfigurationen unter [Topologien für Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies).

## <a name="architecture-diagrams"></a>Architekturdiagramme

Im folgenden Diagramm sind die allgemeinen Architekturkomponenten dargestellt, die für die einzelnen Authentifizierungsmethoden erforderlich sind und die Sie mit Ihrer Azure AD-Hybrididentitätslösung verwenden können. Sie erhalten einen Überblick über die Unterschiede zwischen den Lösungen.

* Einfachheit einer Lösung für die Kennworthashsynchronisierung:

    ![Azure AD-Hybrididentität mit Kennworthashsynchronisierung](media/azure-ad/azure-ad-authn-image2.png)

* Agent-Anforderungen der Passthrough-Authentifizierung mit zwei Agents für Redundanz:

    ![Azure AD-Hybrididentität mit Passthrough-Authentifizierung](media/azure-ad/azure-ad-authn-image3.png)

* Komponenten, die für den Verbund im Umkreisnetzwerk und internen Netzwerk Ihrer Organisation erforderlich sind:

    ![Azure AD-Hybrididentität mit Verbundauthentifizierung](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Vergleichen von Methoden

|Aspekt|Kennworthashsynchronisierung + nahtloses einmaliges Anmelden|Passthrough-Authentifizierung + nahtloses einmaliges Anmelden|Verbund mit AD FS|
|:-----|:-----|:-----|:-----|
|Wo findet Authentifizierung statt?|In der Cloud|In der Cloud nach einem sicheren Kennwortüberprüfungsaustausch mit dem lokalen Authentifizierungs-Agent|Lokal|
|Welche lokalen Serveranforderungen gibt es über das Bereitstellungssystem hinaus: Azure AD Connect?|Keine|Ein Server für jeden zusätzlichen Authentifizierungs-Agent|Mindestens zwei AD FS-Server<br><br>Mindestens zwei WAP-Server im Umkreis-/DMZ-Netzwerk|
|Welche lokalen Anforderungen hinsichtlich Internet und Netzwerk gibt es über das Bereitstellungssystem hinaus?|Keine|[Ausgehender Internetzugriff](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) von den Servern, auf denen Authentifizierung-Agents ausgeführt werden|[Eingehender Internetzugriff](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) auf WAP-Server im Umkreisnetzwerk<br><br>Eingehender Netzwerkzugriff auf AD FS-Server von WAP-Servern im Umkreisnetzwerk<br><br>Netzwerklastenausgleich|
|Ist ein SSL-Zertifikat erforderlich?|Nein |Nein |Ja|
|Gibt es eine Systemüberwachungslösung?|Nicht erforderlich|Agent-Status, bereitgestellt von [Azure Active Directory Admin Center](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Erhalten Benutzer einmaliges Anmelden für Cloudressourcen über Geräte, die in die Domäne eingebunden sind und zum Unternehmensnetzwerk gehören?|Ja, mit [nahtlosem einmaligen Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ja, mit [nahtlosem einmaligen Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ja|
|Welche Anmeldetypen werden unterstützt?|Benutzerprinzipalname + Kennwort<br><br>Integrierte Windows-Authentifizierung mit [nahtlosem einmaligen Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternative Anmelde-ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)|Benutzerprinzipalname + Kennwort<br><br>Integrierte Windows-Authentifizierung mit [nahtlosem einmaligen Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternative Anmelde-ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq)|Benutzerprinzipalname + Kennwort<br><br>sAMAccountName + Kennwort<br><br>Integrierte Windows-Authentifizierung<br><br>[Zertifikat- und Smartcard-Authentifizierung](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternative Anmelde-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Wird Windows Hello for Business unterstützt?|[Modell der schlüsselbasierten Vertrauensstellung](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modell der schlüsselbasierten Vertrauensstellung](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Erfordert eine Windows Server 2016-Domänenfunktionsebene*|[Modell der schlüsselbasierten Vertrauensstellung](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modell der Zertifikatvertrauensstellung](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Welche Optionen gibt es für die mehrstufige Authentifizierung?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Benutzerdefinierte Steuerelemente mit bedingtem Zugriff*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Benutzerdefinierte Steuerelemente mit bedingtem Zugriff*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA über Drittanbieter](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Benutzerdefinierte Steuerelemente mit bedingtem Zugriff*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|
|Welche Benutzerkontenstatus werden unterstützt?|Deaktivierte Konten<br>(bis zu 30 Minuten Verzögerung)|Deaktivierte Konten<br><br>Konto gesperrt<br><br>Konto abgelaufen<br><br>Kennwort abgelaufen<br><br>Anmeldestunden|Deaktivierte Konten<br><br>Konto gesperrt<br><br>Konto abgelaufen<br><br>Kennwort abgelaufen<br><br>Anmeldestunden|
|Welche Optionen für bedingten Zugriff gibt es?|[Bedingter Zugriff in Azure AD mit Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Bedingter Zugriff in Azure AD mit Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Bedingter Zugriff in Azure AD mit Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[AD FS-Anspruchsregeln](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Wird Blockieren älterer Protokolle unterstützt?|[Ja](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Ja](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Ja](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Können das Logo, das Bild und die Beschreibung auf den Anmeldeseiten angepasst werden?|[Ja, mit Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ja, mit Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ja](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Welche erweiterten Szenarien werden unterstützt?|[Intelligente Kennwortsperrung](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Berichte über kompromittierte Anmeldeinformationen mit Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Intelligente Kennwortsperrung](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Authentifizierungssystem mit geringer Wartezeit für mehrere Standorte<br><br>[AD FS-Extranetsperre](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integration in Identitätssysteme von Drittanbietern](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

> [!NOTE] 
> Der bedingte Zugriff in Azure AD über benutzerdefinierte Steuerelemente unterstützt zurzeit keine Geräteregistrierung.

## <a name="recommendations"></a>Empfehlungen
Mit Ihrem Identitätssystem wird sichergestellt, dass Ihre Benutzer Zugriff auf Cloud-Apps und die branchenspezifischen Apps haben, die Sie migrieren und in der Cloud zur Verfügung stellen. Die Authentifizierung steuert den Zugriff auf Apps, um für autorisierte Benutzer das produktive Arbeiten zu ermöglichen und unautorisierte Akteure von den vertraulichen Daten Ihrer Organisation fernzuhalten.

Verwenden oder aktivieren Sie die Kennworthashsynchronisierung aus folgenden Gründen, unabhängig von der gewählten Authentifizierungsmethode:

1. **Hochverfügbarkeit und Notfallwiederherstellung**: Die Passthrough-Authentifizierung und der Verbund richten sich nach der lokalen Infrastruktur. Für die Passthrough-Authentifizierung umfasst der lokale Aufwand die erforderliche Serverhardware und das Netzwerk für die Passthrough-Authentifizierungs-Agents. Für den Verbund ist der lokale Aufwand noch höher. In Ihrem Umkreisnetzwerk müssen Server vorhanden sein, die als Proxy für Authentifizierungsanforderungen und die internen Verbundserver dienen. 

    Stellen Sie redundante Server bereit, um Single Points of Failure zu vermeiden. Authentifizierungsanforderungen werden dann, auch wenn Komponenten ausfallen, immer verarbeitet. Sowohl die Passthrough-Authentifizierung als auch der Verbund sind ebenfalls darauf angewiesen, dass Domänencontroller auf Authentifizierungsanforderungen antworten, und auch diese Controller können ausfallen. Für viele dieser Komponenten ist eine Wartung erforderlich, damit sie fehlerfrei bleiben. Die Wahrscheinlichkeit von Ausfällen ist höher, wenn die Wartung nicht richtig geplant und implementiert wird. Vermeiden Sie Ausfälle mithilfe der Kennworthashsynchronisierung. Der Dienst für die Microsoft Azure AD-Cloudauthentifizierung kann weltweit skaliert werden und ist immer verfügbar.

2. **Sicherung bei lokalem Ausfall**:  Die Folgen eines lokalen Ausfalls aufgrund eines Cyberangriffs oder einer Katastrophe können beträchtlich sein. Sie reichen von einem Markenschaden bis hin zu einer handlungsunfähigen Organisation, die den Angriff nicht bewältigen kann. In letzter Zeit sind viele Organisationen Opfer von Angriffen mit Schadsoftware geworden, z.B. gezielter Ransomware, die zu einem Ausfall der lokalen Server geführt hat. Microsoft hat seine Kunden bei der Bewältigung dieser Art von Angriffen unterstützt und zwei Kategorien von Organisationen erkannt:

   * Organisationen, die die Kennworthashsynchronisierung bereits aktiviert hatten, haben ihre Authentifizierungsmethode geändert und die Kennworthashsynchronisierung verwendet. Der Onlinezustand konnte innerhalb weniger Stunden wiederhergestellt werden. Durch den Zugriff auf E-Mails über Office 365 konnten Probleme gelöst werden, und der Zugriff auf andere cloudbasierte Workloads wurde ermöglicht.

   * Organisationen, die die Kennworthashsynchronisierung zuvor nicht aktiviert hatten, mussten für die Kommunikation und Problembehebung auf nicht vertrauenswürdige externe E-Mail-Systeme zurückgreifen. In diesen Fällen dauerte es Wochen, bis die Identitätsinfrastruktur vor Ort wiederhergestellt war, bevor sich die Benutzer wieder bei Cloud-basierten Anwendungen anmelden können.

3. **Identitätsschutz**: Eine der besten Möglichkeiten, Benutzer in der Cloud zu schützen, ist Azure AD Identity Protection mit Azure AD Premium P2. Microsoft überprüft das Internet ständig auf Benutzer- und Kennwortlisten, die unautorisierte Akteure verkaufen möchten und im Darknet anbieten. Azure AD kann diese Informationen verwenden, um zu überprüfen, ob einer der Benutzernamen mit Kennwort in Ihrer Organisation kompromittiert ist. Daher ist es wichtig, die Kennworthashsynchronisierung unabhängig von der verwendeten Authentifizierungsmethode (Verbund- oder Passthrough-Authentifizierung) zu aktivieren. Kompromittierte Anmeldeinformationen werden in Berichtform angezeigt. Verwenden Sie diese Informationen, um Benutzer zu blockieren oder zum Ändern ihrer Kennwörter zu zwingen, wenn diese versuchen, sich mit kompromittierten Kennwörtern anzumelden.

Schließlich soll noch erwähnt werden, dass Microsoft laut [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) über die umfangreichsten Funktionen für die Identitäts- und Zugriffsverwaltung verfügt. Microsoft verarbeitet jeden Monat [450 Milliarden Authentifizierungsanforderungen](https://www.microsoft.com/en-us/security/intelligence-report), um Zugriff auf Tausende von SaaS-Anwendungen wie Office 365 für nahezu jedes Gerät bereitzustellen. 

## <a name="conclusion"></a>Zusammenfassung

Dieser Artikel beschreibt verschiedene Authentifizierungsoptionen, die Organisationen konfigurieren und bereitstellen können, um den Zugriff auf Cloud-Apps zu unterstützen. Um verschiedene geschäftliche, sicherheitsbezogene und technische Anforderungen zu erfüllen, können Organisationen zwischen Kennworthashsynchronisierung, Passthrough-Authentifizierung und der Verbundmethode wählen. 

Sehen Sie sich die einzelnen Authentifizierungsmethoden an. Werden Ihre Geschäftsanforderungen durch den Aufwand für die Bereitstellung der Lösung und mit der Benutzeroberfläche für den Anmeldevorgang erfüllt? Prüfen Sie auch, ob Ihre Organisation die Funktionen für erweiterte Szenarien und Geschäftskontinuität der einzelnen Authentifizierungsmethoden benötigt. Evaluieren Sie außerdem die Aspekte der einzelnen Authentifizierungsmethoden. Werden Sie durch bestimmte Aspekte daran gehindert, die Methode Ihrer Wahl zu implementieren?

## <a name="next-steps"></a>Nächste Schritte

In der heutigen Welt sind Bedrohungen 24 Stunden am Tag präsent und kommen von überall her. Implementieren Sie die passende Authentifizierungsmethode, um Ihre Sicherheitsrisiken zu minimieren und Ihre Identitäten zu schützen.

[Beginnen Sie](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) mit Azure AD, und setzen Sie die richtige Authentifizierungslösung für Ihre Organisation ein.

Wenn Sie eine Migration von der Verbund- zur Cloudauthentifizierung erwägen, helfen Ihnen die Informationen unter [Ändern der Benutzeranmeldungsmethode](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method) weiter. Verwenden Sie als Hilfe beim Planen und Implementieren der Migration [diese Pläne für die Projektbereitstellung](https://aka.ms/deploymentplans).
