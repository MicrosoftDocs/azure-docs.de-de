---
title: Überwachen von Identität und Zugriff in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Identitäts- und Zugriffsfunktion in Azure Security Center die Zugriffsaktivitäten der Benutzer sowie identitätsbezogene Probleme überwachen können.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: b36f52fd97a9f7ff294f14ec394ff9a9f607b5b0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187152"
---
# <a name="monitor-identity-and-access"></a>Überwachen der Identität und des Zugriffs

Der Fokus bei der Entwicklung des Sicherheitsbereichs wurde von der Netzwerkorientierung auf die Identitätsorientierung verlagert. Aufgrund dieser Entwicklung geht es bei der Sicherheit weniger um die Verteidigung Ihres Netzwerks, sondern mehr um die Verwaltung der Sicherheit Ihrer Apps, Daten und Benutzer.

Durch die Überwachung von Aktivitäten und Konfigurationseinstellungen zu Identitäten können Sie proaktive Maßnahmen ergreifen, bevor es zu einem Vorfall kommt, oder auf einen Angriffsversuch reagieren.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Welche Vorkehrungen zum Schutz von Identitäten und Zugriff bietet Security Center? 

Azure Security Center verfügt über zwei dedizierte Sicherheitskontrollen, mit denen Sie die Identitäts- und Sicherheitsanforderungen Ihrer Organisation erfüllen: 

 - **Verwalten des Zugriffs und der Berechtigungen**: Es wird empfohlen, dem [Modell eines Zugriffs mit den geringsten Rechten](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) zu folgen und sicherzustellen, dass Ihre Benutzer nur über den für die Ausführung ihrer Aufgaben benötigten Zugriff verfügen. Diese Steuerung enthält auch Empfehlungen zum Implementieren der [rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md), um den Zugriff auf Ihre Ressourcen zu kontrollieren.
 
 - **Aktivieren der MFA**: Wenn [Multi-Factor Authentication (MFA)](https://www.microsoft.com/security/business/identity/mfa) aktiviert ist, sind Ihre Konten sicherer, und Benutzer können sich weiterhin bei nahezu allen Anwendungen mit der einmaligen Anmeldung (Single Sign-On, SSO) authentifizieren.

### <a name="example-recommendations-for-identity-and-access"></a>Beispiele für Empfehlungen für Identität und Zugriff

Beispiele für Empfehlungen, die möglicherweise im Security Center auf der Seite **Empfehlungen** unter den beiden Steuerungen angezeigt werden:

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden
- Veraltete Konten müssen aus Ihrem Abonnement entfernt werden. (Veraltete Konten sind Konten, die nicht mehr benötigt werden und bei denen die Anmeldung bei Azure Active Directory blockiert wurde.)

> [!TIP]
> Weitere Informationen zu diesen und anderen Empfehlungen, die in diesen Steuerungen angezeigt werden können, finden Sie unter [Empfehlungen zu Identität und Zugriff](recommendations-reference.md#recs-identity).

### <a name="limitations"></a>Einschränkungen

Es gibt einige Einschränkungen beim Identitäts- und Zugriffsschutz im Security Center:

- Empfehlungen zu Identitäten werden für Abonnements mit mehr als 600 Konten nicht angeboten. In solchen Fällen werden diese Empfehlungen unter „Nicht verfügbare Bewertungen“ aufgeführt.
- Für die Administrator-Agents des CSP-Partners (Cloud Solution Provider) sind keine Empfehlungen zu Identitäten verfügbar.
- Mit Empfehlungen zu Identitäten werden keine Konten identifiziert, die mit einem PIM-System (Privileged Identity Management) verwaltet werden. Wenn Sie ein PIM-Tool verwenden, werden unter der Steuerung **Zugriff und Berechtigungen verwalten** möglicherweise falsche Ergebnisse angezeigt.

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Mehrstufige Authentifizierung (MFA) und Azure Active Directory 

Zum Aktivieren von MFA sind [Berechtigungen für Azure Active Directory (AD)-Mandanten](../active-directory/roles/permissions-reference.md) erforderlich.

- Wenn Sie eine Premium-Edition von AD besitzen, können Sie MFA über den [bedingten Zugriff](../active-directory/conditional-access/concept-conditional-access-policy-common.md) aktivieren.
- Wenn Sie die AD Free-Edition verwenden, aktivieren Sie **Sicherheitsstandards**, wie in der [Azure Active Directory-Dokumentation](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) beschrieben.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifizieren von Konten ohne aktivierte mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)

Verwenden Sie die folgende Azure Resource Graph-Abfrage, um anzuzeigen, für welche Konten MFA nicht aktiviert ist. Die Abfrage gibt alle fehlerhaften Ressourcen (Konten) zur Empfehlung „MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein“ zurück. 

1. Öffnen Sie den **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Geben Sie die folgende Abfrage ein, und wählen Sie **Abfrage ausführen** aus.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. Mit der `additionalData`-Eigenschaft lässt sich die Liste der Kontoobjekt-IDs für Konten anzeigen, für die MFA nicht erzwungen wird. 

    > [!NOTE]
    > Die Konten werden mit ihrer Objekt-ID und nicht mit ihrem Kontonamen angezeigt, um den Datenschutz für die Kontoinhaber sicherzustellen.

> [!TIP]
> Sie können auch die REST-API-Methode [Assessments – Get](/rest/api/securitycenter/assessments/get) im Security Center verwenden.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie im folgenden Artikel:

- [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)