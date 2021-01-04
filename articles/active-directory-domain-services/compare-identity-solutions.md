---
title: Vergleichen von Active Directory-basierten Diensten in Azure | Microsoft-Dokumentation
description: In dieser Übersicht vergleichen Sie die verschiedenen Identitätsangebote für Active Directory Domain Services, Azure Active Directory und Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: justinha
ms.openlocfilehash: 479cc036ed3231d970d46eef9d89daa39a0b0876
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620187"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Vergleichen von selbstverwalteten Active Directory Domain Services, Azure Active Directory und verwalteten Azure Active Directory Domain Services

Es gibt drei gängige Möglichkeiten zur Verwendung von Active Directory-basierten Diensten in Azure, um für Anwendungen, Dienste oder Geräte den Zugriff auf eine zentrale Identität bereitzustellen. Mit dieser Auswahl von Identitätslösungen können Sie flexibel das am besten geeignete Verzeichnis für die Anforderungen Ihrer Organisation nutzen. Wenn Sie beispielsweise in erster Linie reine Cloudbenutzer verwalten, die mobile Geräte nutzen, ist es unter Umständen nicht sinnvoll, Ihre eigene AD DS-Identitätslösung (Active Directory Domain Services) zu erstellen und zu betreiben. Stattdessen können Sie einfach Azure Active Directory verwenden.

Die drei Active Directory-basierten Identitätslösungen haben zwar einen gemeinsamen Namen und die gleiche Technologie, aber sie sind so konzipiert, dass Dienste für die unterschiedlichen Anforderungen von Kunden bereitgestellt werden. Die allgemeinen Identitätslösungen und Featuresätze sind:

* **Active Directory Domain Services (AD DS)** : Für Unternehmen geeigneter LDAP-Server (Lightweight Directory Access Protocol) mit wichtigen Features, z. B. Identität und Authentifizierung, Computerobjektverwaltung, Gruppenrichtlinie und Vertrauensstellungen.
    * AD DS ist eine zentrale Komponente in vielen Organisationen mit einer lokalen IT-Umgebung und verfügt über wichtige Features für die Bereiche Benutzerkontoauthentifizierung und Computerverwaltung.
    * Weitere Informationen finden Sie unter [Übersicht über Active Directory Domain Services][overview-adds].
* **Azure Active Directory (Azure AD)** : Cloudbasierte Verwaltung von Identitäten und mobilen Geräten, die Benutzerkonto- und Authentifizierungsdienste für Ressourcen (z. B. Microsoft 365), das Azure-Portal oder SaaS-Anwendungen bereitstellt.
    * Azure AD kann mit einer lokalen AD DS-Umgebung synchronisiert werden, um eine zentrale Identität für Benutzer bereitzustellen, die in der Cloud nativ funktioniert.
    * Weitere Informationen zu Azure AD finden Sie in [Was ist Azure Active Directory?][whatis-azuread].
* **Azure Active Directory Domain Services (Azure AD DS)** : Stellt verwaltete Domänendienste mit vollständig kompatiblen herkömmlichen AD DS-Features bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung.
    * Azure AD DS ist in Azure AD integriert, und Azure AD kann wiederum mit einer lokalen AD DS-Umgebung synchronisiert werden. Dank dieser Option können Anwendungsfälle mit zentraler Identität auf herkömmliche Webanwendungen erweitert werden, die in Azure im Rahmen einer Lift & Shift-Strategie ausgeführt werden.
    * Weitere Informationen zur Synchronisierung (mit Azure AD und lokal) finden Sie unter [Synchronisieren von Objekten und Anmeldeinformationen in einer verwalteten Domäne][synchronization].

In diesem Übersichtsartikel wird verglichen und gegenübergestellt, wie diese Identitätslösungen zusammenarbeiten können oder einzeln eingesetzt werden – je nach den Anforderungen Ihrer Organisation.

> [!div class="nextstepaction"]
> [Im ersten Schritt erstellen Sie im Azure-Portal eine verwaltete Azure AD DS-Domäne][tutorial-create].

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS und selbstverwaltete AD DS

Wenn Sie über Anwendungen und Dienste verfügen, die Zugriff auf herkömmliche Authentifizierungsmechanismen benötigen, z. B. Kerberos oder NTLM, gibt es zwei Möglichkeiten für die Bereitstellung von Active Directory Domain Services in der Cloud:

* Eine *verwaltete Domäne*, die Sie mit Azure Active Directory Domain Services (Azure AD DS) erstellen. Microsoft erstellt und verwaltet die erforderlichen Ressourcen.
* Eine *selbstverwaltete* Domäne, die Sie unter Verwendung von herkömmlichen Ressourcen wie etwa virtuellen Computern (virtual machines, VMs), Windows Server-Gastbetriebssystem und Active Directory Domain Services (AD DS) erstellen und konfigurieren. Anschließend können Sie diese Ressourcen weiter verwalten.

Mit Azure AD DS werden die wichtigen Dienstkomponenten von Microsoft für Sie als Umgebung für *verwaltete* Domänen bereitgestellt und gepflegt. Sie führen die Bereitstellung, die Verwaltung, das Patchen und das Schützen der AD DS-Infrastruktur für Komponenten wie VMs, Windows Server-Betriebssystem oder Domänencontroller (DCs) nicht durch.

Mit Azure AD DS wird verglichen mit herkömmlichen selbstverwalteten AD DS-Umgebungen eine geringere Menge von Features bereitgestellt, sodass die Komplexität des Entwurfs und der Verwaltung teilweise reduziert wird. Es müssen beispielsweise keine AD-Gesamtstrukturen, -Domänen, -Websites und -Replikationslinks entworfen und gepflegt werden. Das [Erstellen von Gesamtstrukturvertrauensstellungen zwischen Azure AD DS und lokalen Umgebungen][create-forest-trust] ist nach wie vor möglich.

Für Anwendungen und Dienste, die in der Cloud ausgeführt werden und Zugriff auf herkömmliche Authentifizierungsmechanismen benötigen, z. B. Kerberos oder NTLM, wird von Azure AD DS eine verwaltete Domänenumgebung mit minimalem Verwaltungsaufwand bereitgestellt. Weitere Informationen finden Sie unter [Verwaltungskonzepte für Benutzerkonten, Kennwörter und die Verwaltung in Azure AD DS][administration-concepts].

Wenn Sie eine selbstverwaltete AD DS-Umgebung bereitstellen und ausführen, müssen Sie alle zugeordneten Infrastruktur- und Verzeichniskomponenten pflegen. Für eine selbstverwaltete AD DS-Umgebung fällt zusätzlicher Verwaltungsaufwand an, aber Sie können dann weitere Aufgaben durchführen, z. B. eine Erweiterung des Schemas oder die Erstellung von Gesamtstrukturvertrauensstellungen.

Häufig genutzte Bereitstellungsmodelle für eine selbstverwaltete AD DS-Umgebung, über die eine Identität für Anwendungen und Dienste in der Cloud bereitgestellt wird, sind beispielsweise:

* **Eigenständige AD DS-Bereitstellung nur in der Cloud:** Azure-VMs werden als Domänencontroller konfiguriert, und es wird eine separate AD DS-Umgebung erstellt, die auf die Cloud beschränkt ist. Diese AD DS-Umgebung wird nicht in eine lokale AD DS-Umgebung integriert. Es wird ein anderer Satz mit Anmeldeinformationen verwendet, um die Anmeldung und Verwaltung für VMs in der Cloud durchzuführen.
* **Ressourcengesamtstruktur-Bereitstellung:** Azure-VMs werden als Domänencontroller konfiguriert, und es wird eine AD DS-Domäne im Rahmen einer vorhandenen Gesamtstruktur erstellt. Anschließend wird eine Vertrauensstellung mit einer lokalen AD DS-Umgebung konfiguriert. Andere Azure-VMs können den Domänenbeitritt für diese Ressourcengesamtstruktur in der Cloud durchführen. Die Benutzerauthentifizierung erfolgt über eine VPN-/ExpressRoute-Verbindung mit der lokalen AD DS-Umgebung.
* **Erweitern der lokalen Domäne auf Azure**: Mit einem virtuellen Azure-Netzwerk wird eine Verbindung mit einem lokalen Netzwerk über eine VPN-/ExpressRoute-Verbindung hergestellt. Azure-VMs stellen die Verbindung mit diesem virtuellen Azure-Netzwerk her, damit der Domänenbeitritt für die lokale AD DS-Umgebung durchgeführt werden kann.
    * Eine Alternative besteht darin, Azure-VMs zu erstellen und als Replikatdomänencontroller über die lokale AD DS-Domäne höherzustufen. Diese Domänencontroller werden über eine VPN-/ExpressRoute-Verbindung mit der lokalen AD DS-Umgebung repliziert. Die lokale AD DS-Domäne wird praktisch auf Azure erweitert.

In der folgenden Tabelle sind einige Features aufgeführt, die Sie für Ihre Organisation ggf. benötigen, sowie die Unterschiede zwischen einer verwalteten Azure AD DS-Domäne und einer selbstverwalteten AD DS-Domäne:

| **Feature** | **Azure AD DS** | **Selbstverwaltete AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Verwalteter Dienst**                               | **&#x2713;** | **&#x2715;** |
| **Sichere Bereitstellungen**                            | **&#x2713;** | Der Administrator schützt die Bereitstellung. |
| **DNS-Server**                                    | **&#x2713;** (verwalteter Dienst) |**&#x2713;** |
| **Domänen- oder Unternehmensadministratorrechte** | **&#x2715;** | **&#x2713;** |
| **Domänenbeitritt**                                   | **&#x2713;** | **&#x2713;** |
| **Domänenauthentifizierung mithilfe von NTLM und Kerberos** | **&#x2713;** | **&#x2713;** |
| **Eingeschränkte Kerberos-Delegierung**               | Ressourcenbasiert | Ressourcen- und kontobasiert|
| **Benutzerdefinierte OE-Struktur**                           | **&#x2713;** | **&#x2713;** |
| **Gruppenrichtlinie**                                  | **&#x2713;** | **&#x2713;** |
| **Schemaerweiterungen**                             | **&#x2715;** | **&#x2713;** |
| **AD-Domänen-/Gesamtstrukturvertrauensstellungen**                     | **&#x2713;** (nur unidirektionale ausgehende Gesamtstrukturvertrauensstellung) | **&#x2713;** |
| **Sicheres LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP-Lesevorgänge**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-Schreibvorgänge**                                    | **&#x2713;** (in der verwalteten Domäne) | **&#x2713;** |
| **Geografisch verteilte Bereitstellungen**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS und Azure AD

Azure AD ermöglicht Ihnen das Verwalten der Identität von Geräten, die von der Organisation verwendet werden, und das Steuern des Zugriffs auf Unternehmensressourcen über diese Geräte. Benutzer können auch ihr persönliches Gerät (BYO-Modell (Bring Your Own)) bei Azure AD registrieren, wodurch das Gerät eine Identität erhält. Daraufhin wird das Gerät von Azure AD authentifiziert, wenn ein Benutzer sich bei Azure AD anmeldet und das Gerät für den Zugriff auf geschützte Ressourcen verwendet. Darüber hinaus kann das Gerät mithilfe von Software zur mobilen Geräteverwaltung (Mobile Device Management, MDM), z. B. Microsoft Intune, verwaltet werden. Mit dieser Verwaltungsfunktion können Sie den Zugriff auf vertrauliche Ressourcen von verwalteten und richtlinienkompatiblen Geräten einschränken.

Herkömmliche Computer und Laptops können ebenfalls Azure AD beitreten. Dieser Mechanismus bietet die gleichen Vorteile wie das Registrieren eines persönlichen Geräts bei Azure AD, z. B. die Zulassung einer Anmeldung von Benutzern am Gerät mit ihren Anmeldeinformationen des Unternehmens.

In Azure AD eingebundene Geräte bieten Ihnen folgende Vorteile:

* Einmaliges Anmelden (Single Sign-On, SSO) bei Anwendungen, die per Azure AD geschützt sind.
* Mit der Unternehmensrichtlinie kompatibles Roaming von Benutzereinstellungen auf allen Geräten.
* Zugriff auf den Microsoft Store für Unternehmen mit Unternehmensanmeldeinformationen.
* Windows Hello for Business.
* Eingeschränkter Zugriff auf Apps und Ressourcen über Geräte, die mit der Unternehmensrichtlinien kompatibel sind.

Für Geräte kann der Beitritt zu Azure AD mit oder ohne Hybridbereitstellung, die über eine lokale AD DS-Umgebung verfügt, durchgeführt werden. In der folgenden Tabelle sind gängige Gerätebesitzmodelle und deren typische Einbindung in eine Domäne aufgeführt:

| **Gerätetyp**                                        | **Geräteplattformen**             | **Mechanismus**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Persönliche Geräte                                          | Windows 10, iOS, Android, macOS | Bei Azure AD registriert    |
| Im Besitz der Organisation befindliches Gerät, das nicht in die lokale AD DS-Instanz eingebunden ist | Windows 10                       | In Azure AD eingebunden        |
| Im Besitz der Organisation befindliches Gerät, das in eine lokale AD DS-Instanz eingebunden ist  | Windows 10                       | Hybrid in Azure AD eingebunden |

Auf einem in Azure AD eingebundenen oder registrierten Gerät wird die Benutzerauthentifizierung mit modernen Protokollen auf OAuth-/OpenID Connect-Basis durchgeführt. Diese Protokolle sind für den Einsatz im Internet konzipiert und eignen sich daher hervorragend für mobile Szenarien, in denen Benutzer von überall auf Unternehmensressourcen zugreifen.

Mit in Azure AD DS eingebundenen Geräten können Anwendungen die Kerberos- und NTLM-Protokolle für die Authentifizierung verwenden und so Legacyanwendungen unterstützen, die migriert werden, um im Rahmen einer Lift & Shift-Strategie auf Azure-VMs ausgeführt zu werden. In der folgenden Tabelle sind die Unterschiede aufgeführt, die in Bezug auf die Darstellung der Geräte und deren Authentifizierung gegenüber dem Verzeichnis gelten:

| **Aspekt**                      | **Eingebunden in Azure AD**                                 | **Eingebunden in Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Gerät gesteuert von            | Azure AD                                            | Verwaltete Azure AD DS-Domäne                                                |
| Darstellung im Verzeichnis | Geräteobjekte im Azure AD-Verzeichnis            | Computerobjekte in der verwalteten Azure AD DS-Domäne                        |
| Authentifizierung                  | OAuth-/OpenID Connect-basierte Protokolle              | Kerberos- und NTLM-Protokolle                                               |
| Verwaltung                      | Software zur mobilen Geräteverwaltung (Mobile Device Management, MDM) wie z.B. Intune | Gruppenrichtlinie                                                              |
| Netzwerk                      | Im Internet einsetzbar                             | Muss über eine Verbindung oder eine Peeringverknüpfung mit dem virtuellen Netzwerk verfügen, in dem die virtuelle Domäne bereitgestellt wird |
| Ideal für...                    | Mobile oder Desktop-Endbenutzergeräte                  | In Azure bereitgestellte Server-VMs                                              |


Wenn lokale Instanzen von AD DS und Azure AD per ADFS für die Verbundauthentifizierung konfiguriert wurden, ist in Azure DS kein (aktueller/gültiger) Kennworthash vorhanden. Azure AD-Benutzerkonten, die vor der Implementierung der Verbundauthentifizierung erstellt wurden, verfügen unter Umständen über einen alten Kennworthash. Dieser entspricht jedoch wahrscheinlich keinem Hash ihres lokalen Kennworts. Die Anmeldeinformationen des Benutzers können daher nicht von Azure AD DS validiert werden.

## <a name="next-steps"></a>Nächste Schritte

Als Einstieg in Azure AD DS [erstellen Sie eine verwaltete Azure AD DS-Domäne im Azure-Portal][tutorial-create].

Sie können auch mehr über [Verwaltungskonzepte für Benutzerkonten, Kennwörter und Verwaltung in Azure AD DS][administration-concepts] und die [Synchronisierung von Objekten und Anmeldeinformationen in einer verwalteten Domäne][synchronization] erfahren.

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
