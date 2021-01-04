---
title: Entwerfen von Hybrididentitäten – Strategie für die Einführung in Azure | Microsoft-Dokumentation
description: Mit der bedingten Zugriffssteuerung überprüft Azure AD die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836188"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definieren einer Strategie zur Hybrididentitätsübernahme
In dieser Aufgabe definieren Sie die Strategie zur Hybrididentitätsübernahme für Ihre Hybrididentitätslösung, um die geschäftlichen Anforderungen zu erfüllen, die hier erörtert wurden:

* [Ermitteln von Geschäftsanforderungen](plan-hybrid-identity-design-considerations-business-needs.md)
* [Ermitteln der Anforderungen an die Verzeichnissynchronisierung](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Ermitteln der Anforderungen für die Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definieren der Strategie für Geschäftsanforderungen
Die erste Aufgabe ist, die Geschäftsanforderungen der Organisation zu ermitteln.  Dies kann sehr umfangreich sein, und Sie müssen darauf achten, sich auf das Wesentliche zu konzentrieren.  Halten Sie den Entwurf zu Anfang einfach, planen Sie jedoch stets mit ein, dass er in Zukunft einfach geändert werden kann.  Azure Active Directory ist sowohl für einfache als auch äußerst komplexe Entwürfe die Microsoft Identity Platform, die Microsoft 365, Microsoft Online Services und cloudfähige Anwendungen unterstützt.

## <a name="define-an-integration-strategy"></a>Definieren einer Integrationsstrategie
Die drei wichtigsten Integrationsszenarien von Microsoft sind Cloudidentitäten, synchronisierte Identitäten und verbundene Identitäten.  Sie sollten eine dieser Integrationsstrategien anwenden.  Die Entscheidung für die zu verwendende Strategie kann unter anderem davon abhängen, welche Art von Benutzeroberfläche Sie bereitstellen möchten, ob bereits eine Infrastruktur vorhanden ist und welche Lösung die kostengünstigste ist.  

![Integrationsszenarien](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

In der obigen Abbildung sind folgende Szenarien definiert:

* **Cloudidentitäten**: Hierbei handelt es sich um Identitäten, die ausschließlich in der Cloud vorhanden sind.  Im Fall von Azure AD würden sie sich speziell in Ihrem Azure AD-Verzeichnis befinden.
* **Synchronisiert**: Hierbei handelt es sich um Identitäten, die lokal vorhanden sind und in der Cloud.  Mithilfe von Azure AD Connect werden diese Benutzer entweder erstellt oder mit vorhandenen Azure AD-Konten verknüpft.  Das Kennworthash des Benutzers in der lokalen Umgebung wird mit der Cloud synchronisiert.  Beim Synchronisieren ist allerdings Folgendes zu beachten: Wenn ein Benutzer in der lokalen Umgebung deaktiviert wird, kann es bis zu drei Stunden dauern, bis der Kontostatus in Azure AD angezeigt wird.  Dies liegt am Zeitintervall für die Synchronisierung.
* **Im Verbund**: Diese Identitäten sind sowohl lokal als auch in der Cloud vorhanden.  Mithilfe von Azure AD Connect werden diese Benutzer entweder erstellt oder mit vorhandenen Azure AD-Konten verknüpft.  

> [!NOTE]
> Weitere Informationen zu den Optionen für die Synchronisierung finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Die folgende Tabelle hilft Ihnen bei der Bestimmung der Vor- und Nachteile der folgenden Strategien:

| Strategie | Vorteile | Nachteile |
| --- | --- | --- |
| **Cloudidentitäten** |Für kleine Organisationen einfacher zu verwalten. <br> Keine lokale Installation erforderlich. Keine zusätzliche Hardware erforderlich.<br>Problemlos deaktivierbar, wenn der Benutzer das Unternehmen verlässt |Benutzer müssen sich für den Zugriff auf Workloads in der Cloud anmelden <br> Kennwörter können, müssen aber nicht für Cloud- und lokale Identitäten identisch sein |
| **Synchronisiert** |Lokales Kennwort dient zur Authentifizierung sowohl bei lokalen als auch bei Cloudverzeichnissen <br>Einfacher zu verwalten für kleine, mittlere und große Organisationen <br>Benutzer können für einige Ressourcen einmaliges Anmelden (Single Sign-On, SSO) verwenden. <br> Von Microsoft bevorzugte Synchronisierungsmethode <br> Einfacher zu verwalten |Einige Kunden sind aufgrund spezifischer Unternehmensrichtlinien möglicherweise zurückhaltend bezüglich der Synchronisierung ihrer Verzeichnisse mit der Cloud. |
| **Im Verbund** |Benutzer können einmaliges Anmelden (Single Sign-On, SSO) nutzen <br>Wenn ein Benutzer aufhört oder das Unternehmen verlässt, kann das Konto sofort deaktiviert und der Zugriff widerrufen werden.<br> Unterstützt erweiterte Szenarien, die mit Synchronisierung nicht möglich sind |Mehr Schritte für Einrichtung und Konfiguration <br> Höherer Wartungsaufwand <br> Erfordert ggf. zusätzliche Hardware für die STS-Infrastruktur <br> Möglicherweise muss zusätzliche Hardware auf dem Verbundserver installiert werden. Bei Verwendung von AD FS ist zusätzliche Software erforderlich <br> Erfordert umfangreiches Einrichtung für SSO <br> Kritischer Point of Failure: Wenn der Verbundserver ausfällt, können sich die Benutzer nicht authentifizieren. |

### <a name="client-experience"></a>Clienterfahrung
Von der Strategie, die Sie verwenden, hängt die Benutzeranmeldung ab.  Die folgenden Tabellen zeigen Ihnen, wie die jeweilige Benutzeranmeldung aussieht.  Nicht alle Anbieter von Verbundidentität unterstützen SSO in allen Szenarien.

**Einer Domäne angehörende und private Netzwerkanwendungen**:

| Application | Synchronisierte Identität | Verbundidentität |
| --- | --- | --- |
| Webbrowser |Formularbasierte Authentifizierung |Einmaliges Anmelden, manchmal muss die Organisations-ID angegeben werden |
| Outlook |Aufforderung zur Eingabe von Anmeldeinformationen |Aufforderung zur Eingabe von Anmeldeinformationen |
| Skype for Business (Lync) |Aufforderung zur Eingabe von Anmeldeinformationen |Einmaliges Anmelden für Lync, Aufforderung zur Eingabe von Anmeldeinformationen für Exchange |
| OneDrive for Business |Aufforderung zur Eingabe von Anmeldeinformationen |Einmaliges Anmelden |
| Office Pro Plus-Abonnement |Aufforderung zur Eingabe von Anmeldeinformationen |Einmaliges Anmelden |

**Externe oder nicht vertrauenswürdige Quellen**:

| Application | Synchronisierte Identität | Verbundidentität |
| --- | --- | --- |
| Webbrowser |Formularbasierte Authentifizierung |Formularbasierte Authentifizierung |
| Outlook, Skype for Business (Lync), OneDrive for Business, Office-Abonnement |Aufforderung zur Eingabe von Anmeldeinformationen |Aufforderung zur Eingabe von Anmeldeinformationen |
| Exchange ActiveSync |Aufforderung zur Eingabe von Anmeldeinformationen |Einmaliges Anmelden für Lync, Aufforderung zur Eingabe von Anmeldeinformationen für Exchange |
| Mobile Apps |Aufforderung zur Eingabe von Anmeldeinformationen |Aufforderung zur Eingabe von Anmeldeinformationen |

Wenn Sie in Aufgabe 1 ermittelt haben, dass Sie einen Drittanbieter-IdP haben oder verwenden werden, um einen Verbund mit Azure AD bereitzustellen, müssen Sie die folgenden unterstützten Funktionen kennen:

* Jeder SAML 2.0-Anbieter, der mit dem SP-Lite-Profil kompatibel ist, kann die Authentifizierung bei Azure AD und zugehörigen Anwendungen unterstützen
* Die passive Authentifizierung wird unterstützt, was die Authentifizierung bei OWA, SPO usw. erleichtert
* Exchange Online-Clients können über das SAML 2.0 Enhanced Client Profile (ECP) unterstützt werden.

Sie müssen auch wissen, welche Funktionen nicht zur Verfügung stehen:

* Ohne Unterstützung von WS-Trust/WS-Verbund fallen alle anderen aktiven Clients weg
  * Das bedeutet, kein Lync-Client, OneDrive-Client, Office-Abonnement, Office Mobile vor Office 2016
* Der Übergang von Office zur passiven Authentifizierung erlaubt zwar die Unterstützung reiner SAML 2.0-IdPs, jedoch immer noch auf clientspezifischer Basis

> [!NOTE]
> Die aktuelle Liste finden Sie im Artikel [Azure-AD-Verbund – Kompatibilitätsliste](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definieren der Strategie für die Synchronisierung
In dieser Aufgabe definieren Sie, welche Tools verwendet werden, um die lokalen Daten der Organisation mit der Cloud zu synchronisieren, und welche Topologie Sie verwenden sollten.  Da die meisten Organisationen Active Directory verwenden, werden Informationen zur Verwendung von Azure AD Connect zur Behandlung der obigen Fragen detailliert dargestellt.  Für Umgebungen, die nicht über Active Directory verfügen, stehen Informationen zur Verwendung von FIM 2010 R2 oder MIM 2016 zur Verfügung, um die Planung dieser Strategie zu unterstützen.  Zukünftige Versionen von Azure AD Connect werden jedoch LDAP-Verzeichnisse unterstützen – je nachdem, wie Ihr Zeitplan aussieht, können diese Informationen möglicherweise hilfreich sein.

### <a name="synchronization-tools"></a>Synchronisierungstools
Im Laufe der Jahre wurden mehrere Synchronisierungstools für verschiedene Szenarien verwendet.  Derzeit ist Azure AD Connect das Tool der Wahl für alle unterstützten Szenarien.  AAD Sync und DirSync werden auch noch eingesetzt und könnten sogar jetzt in Ihrer Umgebung vorhanden sein. 

> [!NOTE]
> Aktuelle Informationen zu den unterstützten Funktionen der einzelnen Tools finden Sie im Artikel [Vergleich von Tools für die Verzeichnisintegration](plan-hybrid-identity-design-considerations-tools-comparison.md) .  
> 
> 

### <a name="supported-topologies"></a>Unterstützte Topologien
Bei der Definition einer Strategie für die Synchronisierung muss die verwendete Topologie bestimmt werden. Je nach den Informationen, die in Schritt 2 ermittelt wurden, können Sie bestimmen, welche Topologie die richtige ist. Die einzelne Gesamtstruktur, einzelne Azure AD-Topologie wird am häufigsten verwendet und besteht aus einer einzelnen Active Directory-Gesamtstruktur und einer einzelnen Instanz von Azure AD.  Sie wird in der Mehrzahl der Szenarien verwendet und ist die erwartete Topologie beim Einsatz der Expressinstallation von Azure AD Connect, wie in der folgenden Abbildung dargestellt.

![Unterstützte Topologien](./media/plan-hybrid-identity-design-considerations/single-forest.png) Szenario mit einzelner Gesamtstruktur – wird häufig bei großen und auch kleinen Organisationen verwendet, um mehrere Gesamtstrukturen zu haben, wie in Abbildung 5 dargestellt.

> [!NOTE]
> Weitere Informationen zu den verschiedenen lokalen und Azure AD-Topologien mit Azure AD Connect-Synchronisierung finden Sie im Artikel [Topologien für Azure AD Connect](plan-connect-topologies.md).
> 
> 

![Topologie mit mehreren Gesamtstrukturen](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Szenario mit mehreren Gesamtstrukturen

In diesem Fall empfiehlt sich ggf. die Verwendung der einzelnen Azure AD-Topologie mit mehreren Gesamtstrukturen, sofern Folgendes zutrifft:

* Benutzer haben nur 1 Identität in allen Gesamtstrukturen – weiter unten im Abschnitt zu sich eindeutig identifizierenden Benutzern wird dies ausführlich beschrieben.
* Der Benutzer authentifiziert sich bei der Gesamtstruktur, in der seine Identität gespeichert ist.
* UPN und Quellanker (unveränderliche ID) stammen aus dieser Gesamtstruktur.
* Auf alle Gesamtstrukturen kann Azure AD Connect zugreifen – dies bedeutet, dass kein Domänenbeitritt erforderlich ist und ggf. zur Erleichterung die Platzierung in einer DMZ möglich ist.
* Benutzer haben nur ein einziges Postfach.
* Die Gesamtstruktur, die das Postfach eines Benutzers hostet, hat die beste Datenqualität für Attribute, die in der globalen Adressliste von Exchange (Global Address List, GAL) sichtbar sind.
* Wenn kein Postfach für den Benutzer vorhanden ist, kann jede Gesamtstruktur verwendet werden, um diese Werte beizutragen.
* Wenn Sie ein verknüpftes Postfach haben, dann wird auch ein anderes Konto in einer anderen Gesamtstruktur für die Anmeldung verwendet.

> [!NOTE]
> Objekte, die sowohl lokal als auch in der Cloud vorhanden sind, werden über einen eindeutigen Bezeichner „verbunden“. Dieser eindeutige Bezeichner wird im Kontext der Verzeichnissynchronisierung als „SourceAnchor“ bezeichnet. Im Kontext des einmaligen Anmeldens wird er als „ImmutableId“ bezeichnet. [Entwurfskonzepte für Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) finden Sie weitere Überlegungen zur Verwendung von „SourceAnchor“.
> 
> 

Wenn die obigen nicht zutreffen und Sie mehrere aktive Konten oder Postfächer haben, wählt Azure AD Connect eines von ihnen aus und ignoriert die anderen.  Wenn Sie verknüpfte Postfächer, jedoch kein anderes Konto haben, werden diese Konten nicht in Azure AD exportiert, und dieser Benutzer wird nicht Mitglied einer Gruppe.  Dies ist ein beabsichtigter Unterschied zur früheren Situation mit DirSync und soll diese Szenarien mit mehreren Gesamtstrukturen besser unterstützen. Ein Szenario mit mehreren Gesamtstrukturen ist in der folgenden Abbildung dargestellt.

![Mehrere Azure AD-Mandanten](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Szenario mit mehreren Gesamtstrukturen und mehreren Azure AD-Verzeichnissen**

In Azure AD sollte nur ein einzelnes Verzeichnis für eine Organisation vorhanden sein, doch eine 1:1-Beziehung zwischen einem Azure AD Connect-Synchronisierungsserver und einem Azure AD-Verzeichnis wird unterstützt.  Für jede Instanz von Azure AD benötigen Sie eine Installation von Azure AD Connect.  Darüber hinaus ist Azure AD vom Konzept her isoliert, und Benutzer in einer Instanz von Azure AD können Benutzer in anderen Instanzen nicht sehen.

Eine lokale Instanz von Active Directory wie in der folgenden Abbildung dargestellt mit mehreren Azure AD-Verzeichnissen zu verbinden, ist möglich und wird unterstützt:

![Einzelne Gesamtstruktur mit Filterung](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Szenario einer einzelnen Gesamtstruktur mit Filterung**

Hierfür müssen folgende Voraussetzungen erfüllt sein:

* Die Azure AD Connect-Synchronisierungsserver müssen für das Filtern konfiguriert werden, sodass sie über jeweils exklusive Gruppen von Objekten verfügen.  Hierzu wird z. B. der Gültigkeitsbereich jedes Servers auf eine bestimmte Domäne oder OE festgelegt.
* Eine DNS-Domäne kann nur in einem einzelnen Azure AD-Verzeichnis registriert werden, sodass die UPNs der Benutzer im lokalen AD separate Namespaces verwenden müssen.
* Benutzer in einer Instanz von Azure AD können nur Benutzer aus ihrer Instanz sehen.  Sie können keine Benutzer in anderen Instanzen sehen.
* Nur eines der Azure AD-Verzeichnisse kann den Exchange-Hybrid mit dem lokalen AD aktivieren.
* Gegenseitige Ausschließlichkeit gilt auch für das Zurückschreiben.  Folglich werden einige Zurückschreibfeatures mit dieser Topologie nicht unterstützt, da sie eine lokale Einzelkonfiguration voraussetzen.  Dies schließt Folgendes ein:
  * Gruppenzurückschreibung mit Standardkonfiguration
  * Gerätezurückschreibung

Folgendes wird nicht unterstützt und sollte nicht als Implementierung ausgewählt werden:

* Die Verbindung mehrerer Azure AD Connect-Synchronisierungsserver mit dem gleichen Azure AD-Verzeichnis wird auch dann nicht unterstützt, wenn sie dazu konfiguriert sind, jeweils exklusive Gruppen von Objekten zu synchronisieren.
* Die Synchronisierung des gleichen Benutzers mit mehreren Azure AD-Verzeichnissen wird nicht unterstützt. 
* Es wird auch nicht unterstützt, die Konfiguration so zu ändern, dass Benutzer in einem Azure AD in einem anderen Azure AD-Verzeichnis als Kontakte angezeigt werden. 
* Es wird auch nicht unterstützt, die Azure AD Connect-Synchronisierung so zu ändern, dass Verbindungen zu mehreren Azure AD-Verzeichnissen möglich sind.
* Azure AD-Verzeichnisse sind als isoliert konzipiert. Es wird nicht unterstützt, die Konfiguration der Azure AD Connect-Synchronisierung so zu ändern, dass in einem Versuch, eine gemeinsame und einheitliche GAL zwischen den Verzeichnissen zu erstellen, Daten aus einem anderen Azure AD-Verzeichnis gelesen werden. Es wird auch nicht unterstützt, mithilfe der Azure AD Connect-Synchronisierung Benutzer als Kontakte nach einem anderen lokalen AD zu exportieren.

> [!NOTE]
> Für den Fall, dass Ihre Organisation die Verbindung von Computern Ihres Netzwerks mit dem Internet einschränkt, werden in diesem Artikel die Endpunkte aufgeführt (FQDNs, IPv4- und IPv6-Adressbereiche), die Sie in Ihre Ausgangszulassungslisten und Zone vertrauenswürdiger Sites von Clientcomputern im Internet Explorer einbeziehen sollten, um sicherzustellen, dass Ihre Computer Microsoft 365 erfolgreich verwenden können. Weitere Informationen finden Sie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definieren der Multi-Factor Authentication-Strategie
In dieser Aufgabe definieren Sie die Strategie der zu verwendenden mehrstufigen Authentifizierung.  Azure AD Multi-Factor Authentication ist in zwei verschiedenen Versionen verfügbar.  Eine ist cloudbasiert und die andere lokal basiert mit Einsatz des Azure MFA-Servers.  Basierend auf der Auswertung, die Sie oben ausgeführt haben, können Sie die richtige Lösung für Ihre Strategie ermitteln.  Bestimmen Sie mithilfe der folgenden Tabelle, welche Entwurfsoption für die Sicherheitsanforderungen Ihres Unternehmens am besten geeignet ist:

Mehrstufige Entwurfsoptionen:

| Zu sichernde Ressourcen | MFA in der Cloud | Lokale MFA |
| --- | --- | --- |
| Microsoft-Apps |ja |ja |
| SaaS-Apps im Appkatalog |ja |ja |
| Über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen |ja |ja |
| Nicht über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen |nein |ja |
| Remotezugriff wie VPN, RDG |nein |ja |

Selbst wenn Sie sich für eine Lösung für Ihre Strategie entschieden haben, müssen Sie noch mit der obigen Auswertung feststellen, wo sich die Benutzer befinden.  Dies kann zu einer Änderung der Lösung führen.  Verwenden Sie für diese Feststellung die folgende Tabelle:

| Benutzerstandort | Bevorzugte Entwurfsoption |
| --- | --- |
| Azure Active Directory |Multi-Factor Authentication in der Cloud |
| Azure AD und lokales AD über einen Verbund mit AD FS |Beide |
| Azure AD und lokales AD mit Azure AD Connect ohne Kennwortsynchronisierung |Beide |
| Azure AD und lokales AD mit Azure AD Connect mit Kennwortsynchronisierung |Beide |
| Lokales AD |Multi-Factor Authentication-Server |

> [!NOTE]
> Sie sollten außerdem sicherstellen, dass die ausgewählte Entwurfsoption für die mehrstufige Authentifizierung die Features unterstützt, die für Ihren Entwurf erforderlich sind.  Weitere Informationen finden Sie unter [Auswählen der richtigen mehrstufigen Sicherheitslösung](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Authentication-Anbieter
Multi-Factor Authentication ist standardmäßig für globale Administratoren verfügbar, die über einen Azure Active Directory-Mandanten verfügen. Wenn Sie jedoch Multi-Factor Authentication für alle Benutzer verfügbar machen möchten und/oder wenn Sie möchten, dass die globalen Administratoren Features wie das Verwaltungsportal, benutzerdefinierte Grußformeln und Berichte nutzen können, müssen Sie einen Multi-Factor Authentication-Anbieter erwerben und konfigurieren.

> [!NOTE]
> Sie sollten außerdem sicherstellen, dass die ausgewählte Entwurfsoption für die mehrstufige Authentifizierung die Features unterstützt, die für Ihren Entwurf erforderlich sind. 
> 
> 

## <a name="next-steps"></a>Nächste Schritte
[Bestimmen der Datenschutzanforderungen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](plan-hybrid-identity-design-considerations-overview.md)

