---
title: Azure AD Multi-Factor Authentication – Versionen und Verbrauchstarife
description: Lernen Sie den Client für Azure AD Multi-Factor Authentication sowie die verschiedenen verfügbaren Methoden und Versionen kennen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743954"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Features und Lizenzen für Azure AD Multi-Factor Authentication

Zum Schutz von Benutzerkonten in Ihrer Organisation sollten Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verwenden. Dieses Feature ist insbesondere für Konten wichtig, die privilegierten Zugriff auf Ressourcen haben. Grundfunktionen für die mehrstufige Authentifizierung stehen für Administratoren von Microsoft 365 und Azure Active Directory (Azure AD) ohne Zusatzkosten zur Verfügung. Wenn Sie für Ihre Administratoren ein Upgrade der Funktionen durchführen oder die mehrstufige Authentifizierung auf die übrigen Benutzer ausweiten möchten, können Sie Azure AD Multi-Factor Authentication auf mehreren Wegen erwerben.

> [!IMPORTANT]
> In diesem Artikel werden die verschiedenen Möglichkeiten für die Lizenzierung und Verwendung von Azure AD Multi-Factor Authentication erläutert. Ausführliche Informationen zur Preisgestaltung und Abrechnung finden Sie unter [Azure AD Multi-Factor Authentication – Preise](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Verfügbare Versionen von Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication kann entsprechend den Anforderungen Ihrer Organisation auf unterschiedliche Weise verwendet und lizenziert werden. Abhängig von der Lizenz für Azure AD, EMS oder Microsoft 365, die Sie zurzeit besitzen, sind Sie möglicherweise bereits berechtigt, Azure AD Multi-Factor Authentication zu verwenden. In der folgenden Tabelle werden die verschiedenen Möglichkeiten zum Erwerb von Azure AD Multi-Factor Authentication erläutert sowie jeweils einige Funktionen und Anwendungsfälle beschrieben.

| Benutzer von | Funktionen und Anwendungsfälle |
| --- | --- |
| Microsoft 365 Business Premium und EMS oder Microsoft 365 E3 und E5 | EMS E3, Microsoft 365 E3 und Microsoft 365 Business Premium enthalten Azure AD Premium P1. EMS E5 oder Microsoft 365 E5 umfasst Azure AD Premium P2. Sie können die in den folgenden Abschnitten beschriebenen Funktionen für den bedingten Zugriff auch für die Bereitstellung der mehrstufigen Authentifizierung für Benutzer verwenden. |
| Azure AD Premium P1 | Mit dem [bedingten Zugriff von Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) können Sie Benutzer bei bestimmten Szenarien oder Ereignissen zur mehrstufigen Authentifizierung auffordern und auf diese Weise Ihre geschäftlichen Anforderungen erfüllen. |
| Azure AD Premium P2 | Bietet die höchste Sicherheitsstufe und eine verbesserte Benutzerumgebung. Erweitert die Funktionen von Azure AD Premium P1 um den [risikobasierten bedingten Zugriff](../conditional-access/howto-conditional-access-policy-risk.md), der sich an Benutzermuster anpasst und Eingabeaufforderungen für die mehrstufige Authentifizierung minimiert. |
| Alle Microsoft 365-Pläne | Azure AD Multi-Factor Authentication kann mithilfe von [Sicherheitsstandardwerten](../fundamentals/concept-fundamentals-security-defaults.md) [auf Benutzerbasis aktiviert](howto-mfa-userstates.md) oder für alle Benutzer aktiviert bzw. deaktiviert werden. Die Verwaltung von Azure AD Multi-Factor Authentication erfolgt über das Microsoft 365-Portal. Führen Sie zugunsten einer optimierten Benutzerumgebung ein Upgrade auf Azure AD Premium P1 oder P2 durch, und verwenden Sie den bedingten Zugriff. Weitere Informationen finden Sie unter [Schützen von Microsoft 365-Ressourcen mit der mehrstufigen Authentifizierung](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Free | Sie können [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) verwenden, um die mehrstufige Authentifizierung für alle Benutzer zu aktivieren. Sie haben zwar keine differenzierte Kontrolle über aktivierte Benutzer oder Szenarien, verfügen aber über einen zusätzlichen Sicherheitsschritt.<br /> Auch wenn die Sicherheitsstandards nicht zum Aktivieren der mehrstufigen Authentifizierung für alle Benutzer verwendet werden, können Benutzer mit der Rolle *globaler Azure AD-Administrator* für die Verwendung der mehrstufigen Authentifizierung konfiguriert werden. Diese Funktion der kostenlosen Version stellt sicher, dass die kritischen Administratorkonten durch die mehrstufige Authentifizierung geschützt sind. |

## <a name="feature-comparison-of-versions"></a>Funktionsvergleich der Versionen

In der folgenden Tabelle werden die Funktionen aufgeführt, die in den verschiedenen Versionen von Azure AD Multi-Factor Authentication zur Verfügung stehen. Planen Sie Ihre Anforderungen an eine sichere Benutzerauthentifizierung, und legen Sie dann fest, welcher Ansatz diese Anforderungen erfüllt. Beispiel: Obwohl Azure AD Free Azure AD Multi-Factor Authentication über Sicherheitsstandards bereitstellt, kann für Authentifizierungsaufforderungen nur die mobile Authentifikator-App und kein Telefonanruf bzw. keine SMS verwendet werden. Dieser Ansatz kann eine Einschränkung darstellen, wenn Sie nicht gewährleisten können, dass die Authentifikator-App auf dem persönlichen Gerät des Benutzers installiert ist.

| Funktion | Azure AD Free – Sicherheitsstandards | Azure AD Free – Globale Azure AD-Administratoren | Microsoft 365-Apps | Azure AD Premium P1 oder P2 |
| --- |:---:|:---:|:---:|:---:|
| Schutz von Azure AD-Administratorkonten eines Mandanten mit MFA | ● | ● (gilt nur für *globale Azure AD-Administratorkonten*) | ● | ● |
| Mobile App als zweiter Faktor | ● | ● | ● | ● |
| Telefonanruf als zweiter Faktor | | ● | ● | ● |
| SMS als zweiter Faktor | | ● | ● | ● |
| Administrative Kontrolle über Überprüfungsmethoden | | ● | ● | ● |
| Betrugswarnung | | | | ● |
| MFA-Berichte | | | | ● |
| Benutzerdefinierte Begrüßungen für Telefonanrufe | | | | ● |
| Benutzerdefinierte Anrufer-ID für Telefonanrufe | | | | ● |
| Vertrauenswürdige IP-Adressen | | | | ● |
| Speichern der MFA für vertrauenswürdige Geräte | | ● | ● | ● |
| MFA für lokale Anwendungen | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Erwerben und Aktivieren von Azure AD Multi-Factor Authentication

Registrieren Sie sich für einen berechtigten Azure AD-Tarif (oder erwerben Sie einen solchen Tarif), um Azure AD Multi-Factor Authentication verwenden zu können. Azure AD ist in vier Editionen erhältlich: Free, Microsoft 365-Apps, Premium P1 und Premium P2.

Die kostenlose Version (Free) ist im Azure-Abonnement enthalten. Im [folgenden Abschnitt](#azure-ad-free-tier) finden Sie Informationen zur Verwendung der Sicherheitsstandards bzw. zum Schutz von Konten mit der Rolle *globaler Azure AD-Administrator*.

Die Azure AD Premium-Editionen sind über Ihren Microsoft-Vertreter, das [Open Volume License-Programm](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) und das [Cloud Solution Providers-Programm](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409) erhältlich. Azure- und Microsoft 365-Abonnenten können Azure Active Directory Premium P1 und P2 auch online erwerben. Für einen Kauf müssen Sie sich [anmelden](https://portal.office.com/Commerce/Catalog.aspx).

Nachdem Sie den erforderlichen Azure AD-Tarif erworben haben, müssen Sie [Azure AD Multi-Factor Authentication planen und bereitstellen](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Tarif „Azure AD Free“

Alle Benutzer in einem Mandanten der Edition „Azure AD Free“ können Azure AD Multi-Factor Authentication über die Sicherheitsstandards verwenden. Bei Verwendung der Sicherheitsstandards der Edition „Azure AD Free“ kann nur die mobile Authentifikator-App für Azure AD Multi-Factor Authentication verwendet werden.

* [Weitere Informationen zu Azure AD-Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md)
* [Aktivieren von Sicherheitsstandards für Benutzer von Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Wenn Sie Azure AD Multi-Factor Authentication nicht für alle Benutzer aktivieren möchten, können Sie stattdessen Benutzerkonten nur mit der Rolle *globaler Azure AD-Administrator* schützen. Mit dieser Methode werden zusätzliche Authentifizierungsaufforderungen für kritische Administratorkonten bereitgestellt. Sie können Azure AD Multi-Factor Authentication je nach verwendetem Kontotyp mit einer der folgenden Methoden aktivieren:

* Wenn Sie ein Microsoft-Konto verwenden, können Sie sich [für die mehrstufige Authentifizierung registrieren](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Falls Sie kein Microsoft-Konto verwenden, [aktivieren Sie die mehrstufige Authentifizierung für einen Benutzer oder eine Gruppe in Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Kosten finden Sie unter [Azure AD Multi-Factor Authentication – Preise](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Was ist bedingter Zugriff?](../conditional-access/overview.md)

