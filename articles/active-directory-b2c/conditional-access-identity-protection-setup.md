---
title: Einrichten von Identity Protection und bedingtem Zugriff in Azure AD B2C
description: Hier erfahren Sie, wie Sie Identity Protection und bedingten Zugriff für Ihren Azure AD B2C-Mandanten konfigurieren, um Risikoanmeldungen und andere Risikoereignisse anzuzeigen und Richtlinien auf der Grundlage von Risikoerkennungen zu erstellen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2472183673e5f06f5664a306a69d14c2eaf5f82d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949767"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Einrichten von Identity Protection und bedingtem Zugriff in Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection bietet eine kontinuierliche Risikoerkennung für Ihren Azure AD B2C-Mandanten. Wenn Sie für Ihren Azure AD B2C-Mandanten den Tarif „Premium P2“ verwenden, können Sie im Azure-Portal detaillierte Identity Protection-Risikoereignisse anzeigen. Sie können auch auf diesen Risikoerkennungen basierende Richtlinien für [bedingten Zugriff](../active-directory/conditional-access/overview.md) verwenden, um Aktionen zu bestimmen und Organisationsrichtlinien zu erzwingen.

## <a name="prerequisites"></a>Voraussetzungen

- Ihr Azure AD B2C-Mandant muss [mit einem Azure AD-Abonnement verknüpft sein](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- Für die Verwendung von bedingtem Zugriff auf der Grundlage von Anmeldungen und Benutzerrisiko ist Azure AD B2C Premium P2 erforderlich. [Stellen Sie Ihren Azure AD B2C-Tarif auf „Premium P2“ um](./billing.md) (falls erforderlich). 
- Für die Verwaltung von Identity Protection und bedingtem Zugriff in Ihrem B2C-Mandanten benötigen Sie ein Konto, dem die Rolle „Globaler Administrator“ oder „Sicherheitsadministrator“ zugewiesen ist.
- Um diese Funktionen in Ihrem Mandanten verwenden zu können, müssen Sie zuerst zum Azure AD B2C-Tarif „Premium P2“ wechseln.

## <a name="set-up-identity-protection"></a>Einrichten von Identity Protection

Identity Protection ist standardmäßig aktiviert. Um Identity Protection-Risikoereignisse in Ihrem Azure AD B2C-Mandanten anzeigen zu können, müssen Sie Ihren Azure AD B2C-Mandanten einfach nur mit einem Azure AD-Abonnement verknüpfen und den Azure AD B2C-Tarif „Premium P2“ auswählen. Detaillierte Risikoereignisberichte können im Azure-Portal angezeigt werden.

### <a name="supported-identity-protection-risk-detections"></a>Unterstützte Identity Protection-Risikoerkennungen

Für Azure AD B2C werden derzeit folgende Risikoerkennungen unterstützt:  

|Risikoerkennungstyp  |BESCHREIBUNG  |
|---------|---------|
| Ungewöhnlicher Ortswechsel     | Anmeldung von einem ungewöhnlichen Standort, basierend auf den letzten Anmeldevorgängen des Benutzers        |
|Anonyme IP-Adresse     | Anmeldung von einer anonymen IP-Adresse (z.B. Tor-Browser, anonymisierte VPNs)        |
|Mit Schadsoftware verknüpfte IP-Adresse     | Anmeldung von einer mit Schadsoftware verknüpften IP-Adresse         |
|Ungewöhnliche Anmeldeeigenschaften     | Anmeldung mit Eigenschaften, die für den angegebenen Benutzer in letzter Zeit nicht verwendet wurden        |
|Benutzergefährdung durch Administrator bestätigt    | Ein Administrator hat angegeben, dass ein Benutzer kompromittiert wurde.             |
|Kennwortspray     | Anmeldung über einen Kennwortspray-Angriff      |
|Azure AD Threat Intelligence     | Auf der Grundlage der internen und externen Quellen für Bedrohungsdaten von Microsoft wurde ein bekanntes Angriffsmuster identifiziert        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Anzeigen von Risikoereignissen für Ihren Azure AD B2C-Mandanten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.

1. Wählen Sie unter **Sicherheit** die Option **Riskante Benutzer (Vorschau)** aus.

   ![Riskante Benutzer](media/conditional-access-identity-protection-setup/risky-users.png)

1. Wählen Sie unter **Sicherheit** die Option **Risikoerkennung (Vorschau)** aus.

   ![Risikoerkennungen](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Hinzufügen einer Richtlinie für bedingten Zugriff 

Wenn Sie eine Richtlinie für bedingten Zugriff hinzufügen möchten, die auf Identity Protection-Risikoerkennungen basiert, vergewissern Sie sich, dass Sicherheitsstandards für Ihren Azure AD B2C-Mandanten deaktiviert sind, und erstellen Sie anschließend Richtlinien für bedingten Zugriff.

### <a name="to-disable-security-defaults"></a>Deaktivieren von Sicherheitsstandards

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

3. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie es aus.

4. Wählen Sie **Eigenschaften** und anschließend **Sicherheitsstandards verwalten** aus.

   ![Deaktivieren der Sicherheitsstandards](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. Wählen Sie unter „Sicherheitsstandards aktivieren“ die Option „Nein“ aus. 

   ![Festlegen der Einstellung „Sicherheitsstandards aktivieren“ auf „Nein“](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Erstellen einer Richtlinie für bedingten Zugriff

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.

1. Wählen Sie unter **Sicherheit** die Option **Conditional Access (Preview)** (Bedingter Zugriff (Vorschau)) aus. Daraufhin wird die Seite **Richtlinien für bedingten Zugriff** angezeigt. 

1. Wählen Sie **Neue Richtlinie** aus, und folgen Sie der Dokumentation für bedingten Azure AD-Zugriff, um eine neue Richtlinie zu erstellen. Es folgt ein Beispiel:

   - [Risikobasierter bedingter Zugriff beim Anmelden: Aktivieren mit einer Richtlinie für bedingten Zugriff](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > Wählen Sie beim Auswählen der Benutzer, auf die Sie die Richtlinie anwenden möchten, nicht nur **Alle Benutzer** aus. Andernfalls wird unter Umständen auch Ihre eigene Anmeldung blockiert.

## <a name="test-the-conditional-access-policy"></a>Testen der Richtlinie für bedingten Zugriff

1. Erstellen Sie wie oben beschrieben eine Richtlinie für bedingten Zugriff, und verwenden Sie dabei die folgenden Einstellungen:
   
   - Wählen Sie für **Benutzer und Gruppen** den Testbenutzer aus. Wählen Sie nicht **Alle Benutzer** aus, da Sie sich ansonsten selbst nicht mehr anmelden können.
   - Wählen Sie für **Cloud-Apps oder -aktionen** die Option **Apps auswählen** und anschließend Ihre Anwendung der vertrauenden Seite aus.
   - Wählen Sie unter den Bedingungen die Option **Anmelderisiko** und die Risikostufen **Hoch**, **Mittel** und **Niedrig** aus.
   - Wählen Sie für **Gewähren** die Option **Zugriff blockieren** aus.

      ![Auswählen von „Zugriff blockieren“](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Wählen Sie **Erstellen** aus, um Ihre Testrichtlinie für bedingten Zugriff zu aktivieren.

1. Verwenden Sie den [Tor-Browser](https://www.torproject.org/download/), um eine Risikoanmeldung zu simulieren. 

1. Im decodierten Token „jwt.ms“ für den Anmeldeversuch sollte zu sehen sein, dass die Anmeldung blockiert wurde:

   ![Testen einer blockierten Anmeldung](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Überprüfen der Ergebnisse des bedingten Zugriffs im Überwachungsbericht

So überprüfen Sie das Ergebnis eines Ereignisses für bedingten Zugriff:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

3. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.

4. Wählen Sie unter **Aktivitäten** die Option **Überwachungsprotokolle** aus.

5. Filtern Sie das Überwachungsprotokoll. Legen Sie hierzu **Kategorie** auf **B2C** und **Aktivitätsressourcentyp** auf **IdentityProtection** fest. Wählen Sie dann **Anwenden** aus.

6. Überprüfen Sie die Überwachungsaktivität für maximal die letzten sieben Tage. Folgende Aktivitätstypen sind enthalten:

   - **Evaluate conditional access policies** (Richtlinien für bedingten Zugriff auswerten): Dieser Überwachungsprotokolleintrag gibt an, dass während einer Authentifizierung eine Auswertung für bedingten Zugriff durchgeführt wurde.
   - **Remediate user** (Benutzer bereinigen): Dieser Eintrag gibt an, dass die Gewährung oder die Anforderungen einer Richtlinie für bedingten Zugriff durch den Endbenutzer erfüllt wurde bzw. wurden und dass diese Aktivität an die Risikoengine gemeldet wurde, um das Risiko des Benutzers zu verringern.

7. Wählen Sie in der Liste einen Eintrag vom Typ **Evaluate conditional access policy** (Richtlinien für bedingten Zugriff auswerten) aus, um die Seite **Aktivitätsdetails: Überwachungsprotokoll** zu öffnen. Dort werden neben den Überwachungsprotokollbezeichnern die folgenden Informationen im Abschnitt **Zusätzliche Informationen** angezeigt:

   - ConditionalAccessResult: Die für die Auswertung der bedingten Richtlinie erforderliche Gewährung.
   - AppliedPolicies: Eine Liste mit allen Richtlinien für bedingten Zugriff, bei denen die Bedingungen erfüllt wurden und die Richtlinien aktiviert sind.
   - ReportingPolicies: Eine Liste mit den Richtlinien für bedingten Zugriff, die auf den reinen Berichtsmodus festgelegt und bei denen die Bedingungen erfüllt wurden.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md)