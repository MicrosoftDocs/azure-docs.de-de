---
title: Synchronisieren von lokalen Partnerkonten als B2B-Benutzer mit der Cloud – Azure Active Directory | Microsoft-Dokumentation
description: Ermöglichen Sie es lokal verwalteten Partnern mithilfe von Azure AD B2B-Zusammenarbeit, mit denselben Anmeldeinformationen auf lokale und cloudbasierte Ressourcen zuzugreifen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ae75311ab61449f37ccea15a0bcb88fed80c3ed
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767343"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Gewähren des Zugriffs auf Cloudressourcen für lokal verwaltete Partnerkonten mit Azure AD B2B-Zusammenarbeit

Vor Azure Active Directory (Azure AD) haben Unternehmen mit lokalen Identitätssystemen Partnerkonten herkömmlicherweise in ihrem lokalen Verzeichnis verwaltet. Wenn Sie in einem solchen Unternehmen damit beginnen, Apps zu Azure AD zu migrieren, möchten Sie sicherstellen, dass Ihre Partner auf die benötigten Ressourcen zugreifen können. Dabei sollte es keine Rolle spielen, ob die Ressourcen lokal oder in der Cloud gespeichert sind. Zudem sollen Ihre Partnerbenutzer in der Lage sein, für lokale und Azure AD-Ressourcen dieselben Anmeldeinformationen zu verwenden. 

Wenn Sie Konten für externe Partner in Ihrem lokalen Verzeichnis erstellen (beispielsweise ein Konto mit dem Anmeldenamen „mkrause“ für eine externe Benutzerin namens Marie Krause in Ihrer Domäne „partners.contoso.com“), können Sie diese Konten jetzt mit der Cloud synchronisieren. Insbesondere können Sie mithilfe von Azure AD Connect die Partnerkonten als Azure AD B2B-Benutzer (also als Benutzer mit „UserType = Guest“) mit der Cloud synchronisieren. Dadurch können Ihre Partner mit denselben Anmeldeinformationen wie für ihre lokalen Konten auf Cloudressourcen zugreifen, ohne mehr Zugriff als nötig zu erhalten. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifizieren von eindeutigen Attributen für UserType

Vor dem Aktivieren der Synchronisierung des UserType-Attributs müssen Sie entscheiden, wie das UserType-Attribut vom lokalen Active Directory abgeleitet werden soll. Das heißt, welche Parameter in Ihrer lokalen Umgebung sind für Ihre externen Mitarbeiter eindeutig? Bestimmen Sie einen Parameter, der diese externen Mitarbeiter von Mitgliedern Ihrer eigenen Organisation unterscheidet.

Hierbei gibt es zwei gängige Vorgehensweisen:

- Sie legen ein nicht verwendetes lokales Active Directory-Attribut (z.B. extensionAttribute1) als Quellattribut fest. 
- Oder Sie leiten den Wert für das UserType-Attribut aus anderen Eigenschaften ab. Angenommen, Sie möchten alle Benutzer als „Guest“ synchronisieren, wenn ihr lokales Active Directory-Attribut UserPrincipalName mit der Domänenangabe *\@partners.contoso.com* endet.
 
Detaillierte Attributanforderungen finden Sie unter [Synchronisierung des Benutzertyps aktivieren](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurieren von Azure AD Connect für die Synchronisierung von Benutzern mit der Cloud

Nachdem Sie das eindeutige Attribut identifiziert haben, können Sie Azure AD Connect so konfigurieren, dass diese Benutzer mit der Cloud als Azure AD B2B-Benutzer (d.h. Benutzer mit UserType = Guest) synchronisiert werden. Aus Sicht der Autorisierung sind diese Benutzer nicht von B2B-Benutzern zu unterscheiden, die durch den Einladungsprozess von Azure AD B2B-Zusammenarbeit erstellt wurden.

Eine Anleitung zur Implementierung finden Sie unter [Synchronisierung des Benutzertyps aktivieren](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Active Directory B2B-Zusammenarbeit für Hybridorganisationen](hybrid-organizations.md)
- [Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD](hybrid-cloud-to-on-premises.md)
- Eine Übersicht zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

