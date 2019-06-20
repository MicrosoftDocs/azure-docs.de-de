---
title: Anzeigen von Berichten und Protokollen in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie den Bericht zu Benutzerzuweisungen und Überwachungsprotokolle in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) anzeigen.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64540669"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Anzeigen von Berichten und Protokollen in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Anzeigen von Ressourcen, auf die ein Benutzer Zugriff hat

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Bericht zu Benutzerzuweisungen**.

1. Klicken Sie auf **Benutzer auswählen**, um den Bereich „Benutzer auswählen“ zu öffnen.

1. Suchen Sie in der Liste den Benutzer, zu dem Sie die Ressourcen anzeigen möchten, auf die er Zugriff hat.

1. Klicken Sie auf den Benutzer, und klicken Sie dann auf **Auswählen**.

    Es wird eine Liste der Ressourcen angezeigt, auf die der Benutzer Zugriff hat. Sie enthält das Zugriffspaket, Richtlinien und Datumsangaben.

    ![Bericht zu Benutzerzuweisungen](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Ermitteln des Status einer Benutzeranforderung

Sie können das Azure AD-Überwachungsprotokoll verwenden, um zusätzliche Details darüber abzurufen, wie ein Benutzer Zugriff auf ein Zugriffspaket angefordert und erhalten hat. Insbesondere können Sie die Protokolleinträge in den Kategorien `EntitlementManagement` und `UserManagement` nutzen, um zusätzliche Details zu den Verarbeitungsschritten für jede Anforderung zu erhalten.  

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Überwachungsprotokolle**.

1. Ändern Sie im oberen Bereich die **Kategorie** abhängig vom gewünschten Überwachungsdatensatz in `EntitlementManagement` oder `UserManagement`.  

1. Klicken Sie auf **Anwenden**.

1. Klicken Sie zum Herunterladen der Protokolle auf **Herunterladen**.

Wenn Azure AD eine neue Anforderung empfängt, wird ein Überwachungsdatensatz geschrieben, in dem die **Kategorie** `EntitlementManagement` und die **Aktivität** in der Regel `User requests access package assignment` lautet.  Bei einer im Azure-Portal erstellten direkten Zuweisung heißt das Feld **Aktivität** des Überwachungsdatensatzes `Administrator directly assigns user to access package`, und der Benutzer, der die Zuweisung vornimmt, wird durch das Feld **ActorUserPrincipalName** identifiziert.

Azure AD schreibt zusätzliche Überwachungsdatensätze, während die Anforderung ausgeführt wird, die Folgendes umfassen:

| Category (Kategorie) | Aktivität | Anfragestatus |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Anforderung muss nicht genehmigt werden |
| `UserManagement` | `Create request approval` | Anforderung muss genehmigt werden |
| `UserManagement` | `Add approver to request approval` | Anforderung muss genehmigt werden |
| `EntitlementManagement` | `Approve access package assignment request` | Anforderung genehmigt |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Anforderung wurde genehmigt oder muss nicht genehmigt werden |

Wenn einem Benutzer Zugriff zugewiesen wird, schreibt Azure AD einen Überwachungsdatensatz für die Kategorie `EntitlementManagement` mit der **Aktivität** `Fulfill access package assignment`.  Der Benutzer, der Zugriff erhalten hat, wird durch das Feld **ActorUserPrincipalName** identifiziert.

Wenn kein Zugriff zugewiesen wurde, schreibt Azure AD einen Überwachungsdatensatz für die Kategorie `EntitlementManagement`, wobei die **Aktivität** entweder `Deny access package assignment request` lautet, wenn die Anforderung von einer genehmigenden Person abgelehnt wurde, oder `Access package assignment request timed out (no approver action taken)`, wenn bei der Anforderung ein Timeout aufgetreten ist, bevor sie von einer genehmigenden Person genehmigt werden konnte.

Wenn die Zugriffspaketzuweisung des Benutzers abläuft, vom Benutzer abgebrochen oder von einem Administrator entfernt wird, schreibt Azure AD einen Überwachungsdatensatz für die Kategorie `EntitlementManagement` mit der **Aktivität** `Remove access package assignment`.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der Azure AD-Berechtigungsverwaltung](entitlement-management-troubleshoot.md)
- [Gängige Szenarios](entitlement-management-scenarios.md)
