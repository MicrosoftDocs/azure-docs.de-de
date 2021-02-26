---
title: Synchronisieren von lokalen Partnerkonten als B2B-Benutzer mit der Cloud – Azure AD
description: Ermöglichen Sie es lokal verwalteten Partnern mithilfe von Azure AD B2B-Zusammenarbeit, mit denselben Anmeldeinformationen auf lokale und cloudbasierte Ressourcen zuzugreifen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575922"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten mit Azure AD B2B-Zusammenarbeit

Vor Azure Active Directory (Azure AD) haben Unternehmen mit lokalen Identitätssystemen Partnerkonten herkömmlicherweise in ihrem lokalen Verzeichnis verwaltet. Wenn Sie in einem solchen Unternehmen damit beginnen, Apps zu Azure AD zu migrieren, möchten Sie sicherstellen, dass Ihre Partner auf die benötigten Ressourcen zugreifen können. Dabei sollte es keine Rolle spielen, ob die Ressourcen lokal oder in der Cloud gespeichert sind. Zudem sollen Ihre Partnerbenutzer in der Lage sein, für lokale und Azure AD-Ressourcen dieselben Anmeldeinformationen zu verwenden. 

Wenn Sie Konten für externe Partner in Ihrem lokalen Verzeichnis erstellen (beispielsweise ein Konto mit dem Anmeldenamen „mkrause“ für eine externe Benutzerin namens Marie Krause in Ihrer Domäne „partners.contoso.com“), können Sie diese Konten jetzt mit der Cloud synchronisieren. Insbesondere können Sie mithilfe von Azure AD Connect die Partnerkonten mit der Cloud synchronisieren, wobei ein Benutzerkonto mit „UserType = Guest“ erstellt wird. Dadurch können Ihre Partner mit denselben Anmeldeinformationen wie für ihre lokalen Konten auf Cloudressourcen zugreifen, ohne mehr Zugriff als nötig zu erhalten.

> [!NOTE]
> Weitere Informationen finden Sie auch unter [Einladen von internen Benutzern zur B2B-Zusammenarbeit](invite-internal-users.md). Mit diesem Feature können Sie interne Gastbenutzer zur Verwendung von B2B Collaboration einladen, unabhängig davon, ob ihre Konten aus Ihrem lokalen Verzeichnis mit der Cloud synchronisiert wurden. Wenn Benutzer die Einladung zur Verwendung von B2B Collaboration annehmen, können sie ihre eigenen Identitäten und Anmeldeinformationen verwenden, um sich bei den Ressourcen anzumelden, auf die sie zugreifen sollen. So müssen keine Kennwörter oder Kontolebenszyklen verwaltet werden.

## <a name="identify-unique-attributes-for-usertype"></a>Identifizieren von eindeutigen Attributen für UserType

Vor dem Aktivieren der Synchronisierung des UserType-Attributs müssen Sie entscheiden, wie das UserType-Attribut vom lokalen Active Directory abgeleitet werden soll. Das heißt, welche Parameter in Ihrer lokalen Umgebung sind für Ihre externen Mitarbeiter eindeutig? Bestimmen Sie einen Parameter, der diese externen Mitarbeiter von Mitgliedern Ihrer eigenen Organisation unterscheidet.

Hierbei gibt es zwei gängige Vorgehensweisen:

- Sie legen ein nicht verwendetes lokales Active Directory-Attribut (z.B. extensionAttribute1) als Quellattribut fest. 
- Oder Sie leiten den Wert für das UserType-Attribut aus anderen Eigenschaften ab. Angenommen, Sie möchten alle Benutzer als „Guest“ synchronisieren, wenn ihr lokales Active Directory-Attribut UserPrincipalName mit der Domänenangabe *\@partners.contoso.com* endet.
 
Detaillierte Attributanforderungen finden Sie unter [Synchronisierung des Benutzertyps aktivieren](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurieren von Azure AD Connect für die Synchronisierung von Benutzern mit der Cloud

Nachdem Sie das eindeutige Attribut identifiziert haben, können Sie Azure AD Connect so konfigurieren, dass diese Benutzer mit der Cloud synchronisiert werden, wobei ein Benutzerkonto mit „UserType = Guest“ erstellt wird. Aus Sicht der Autorisierung sind diese Benutzer nicht von B2B-Benutzern zu unterscheiden, die durch den Einladungsprozess von Azure AD B2B-Zusammenarbeit erstellt wurden.

Eine Anleitung zur Implementierung finden Sie unter [Synchronisierung des Benutzertyps aktivieren](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Active Directory B2B-Zusammenarbeit für Hybridorganisationen](hybrid-organizations.md)
- [Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD](hybrid-cloud-to-on-premises.md)
- Eine Übersicht zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

