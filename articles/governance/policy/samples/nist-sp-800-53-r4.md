---
title: Informationen zur Einhaltung der gesetzlichen Bestimmungen gemäß NIST SP 800-53 R4
description: Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß NIST SP 800-53 R4. Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 11/20/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: f4a355bf87f679f3a9ac412fad3a1258ca399654
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987132"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß NIST SP 800-53 R4

Der folgende Artikel enthält Details dazu, wie die integrierte Azure Policy-Initiative zur Einhaltung gesetzlicher Bestimmungen den **Compliancebereichen** und **Steuerungen** in NIST SP 800-53 R4 entspricht.
Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-53 R4](https://nvd.nist.gov/800-53). Grundlegendes zum _Besitzer_ finden Sie unter [Azure Policy-Richtliniendefinition](../concepts/definition-structure.md#type) und [Gemeinsame Verantwortung in der Cloud](../../../security/fundamentals/shared-responsibility.md).

Die folgenden Zuordnungen gelten für die Steuerungen unter **NIST SP 800-53 R4**. Über den rechten Navigationsbereich können Sie direkt zu einem bestimmten **Steuerungsbereich** springen. Viele der Steuerungen werden über die Definition einer [Azure Policy](../overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiativendefinition öffnen Sie **Policy** im Azure-Portal und wählen dann die Seite **Definitionen** aus.
Suchen Sie anschließend nach der integrierten Initiativendefinition zur Einhaltung der gesetzlichen Bestimmungen gemäß **NIST SP 800-53 R4**, und wählen Sie sie aus.

Diese integrierte Initiative wird als Teil des [Blaupausenbeispiels „NIST SP 800-53 R4“](../../blueprints/samples/nist-sp-800-53-r4.md) bereitgestellt.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../overview.md)-Definitionen zugeordnet.
> Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtliniendefinitionen selbst und gewährleistet nicht die vollständige Compliance mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Compliancebereichen, Steuerungen und Azure Policy-Definitionen für diesen Konformitätsstandard können sich im Lauf der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json).

## <a name="access-control"></a>Zugriffssteuerung

### <a name="access-control-policy-and-procedures"></a>Zugriffssteuerungsrichtlinie und -verfahren

**ID:** NIST SP 800-53 R4 AC-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1000: Richtlinien und Verfahren für die Zugriffssteuerung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Von Microsoft verwaltete Steuerung 1001: Richtlinien und Verfahren für die Zugriffssteuerung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Kontoverwaltung

**ID:** NIST SP 800-53 R4 AC-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Veraltete Konten sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Veraltete Konten sollten aus Ihren Abonnements entfernt werden.  Veraltete Konten sind Konten, bei denen die Anmeldung blockiert wurde. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Veraltete Konten mit Besitzerberechtigungen müssen aus Ihrem Abonnement entfernt werden.  Veraltete Konten sind Konten, bei denen die Anmeldung blockiert wurde. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externe Konten mit Besitzerberechtigungen müssen aus Ihrem Abonnement entfernt werden, um einen nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externe Konten mit Leserechten müssen aus Ihrem Abonnement entfernt werden, um nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externe Konten mit Schreibrechten müssen aus Ihrem Abonnement entfernt werden, um nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1002: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Von Microsoft verwaltete Steuerung 1003: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Von Microsoft verwaltete Steuerung 1004: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Von Microsoft verwaltete Steuerung 1005: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Von Microsoft verwaltete Steuerung 1006: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Von Microsoft verwaltete Steuerung 1007: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Von Microsoft verwaltete Steuerung 1008: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Von Microsoft verwaltete Steuerung 1009: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Von Microsoft verwaltete Steuerung 1010: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Von Microsoft verwaltete Steuerung 1011: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Von Microsoft verwaltete Steuerung 1012: Kontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Kontoverwaltung | Automatisierte Systemkontoverwaltung

**ID:** NIST SP 800-53 R4 AC-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1013: Kontoverwaltung \| Automatisierte Systemkontoverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Kontoverwaltung | Entfernen von temporären oder Notfallkonten

**ID:** NIST SP 800-53 R4 AC-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1014: Kontoverwaltung \| Entfernen von temporären oder Notfallkonten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Kontoverwaltung | Inaktive Konten deaktivieren

**ID:** NIST SP 800-53 R4 AC-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1015: Kontoverwaltung \| Deaktivieren inaktiver Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Kontoverwaltung | Automatisierte Überwachung von Aktionen

**ID:** NIST SP 800-53 R4 AC-2 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1016: Kontoverwaltung \| Automatisierte Überwachungsaktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Kontoverwaltung | Abmeldung nach Inaktivität

**ID:** NIST SP 800-53 R4 AC-2 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1017: Kontoverwaltung \| Abmeldung bei Inaktivität](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Kontoverwaltung | Rollenbasierte Schemas

**ID:** NIST SP 800-53 R4 AC-2 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ein Azure Active Directory-Administrator sollte für SQL Server-Instanzen bereitgestellt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Hiermit wird die Bereitstellung eines Azure Active Directory-Administrators für Ihre SQL Server-Instanz überwacht, um die Azure AD-Authentifizierung zu aktivieren. Die Azure AD-Authentifizierung vereinfacht die Berechtigungsverwaltung und ermöglicht eine zentrale Identitätsverwaltung für Datenbankbenutzer und andere Microsoft-Dienste. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Hiermit werden integrierte Rollen wie z.B. „Benutzer“, „Mitwirkender“ und „Leser“ anstelle benutzerdefinierter RBAC-Rollen überwacht, die fehleranfällig sind. Die Verwendung benutzerdefinierter Rollen wird als Ausnahme betrachtet und erfordert eine strenge Überprüfung und Bedrohungsmodellierung. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1018: Kontoverwaltung \| Rollenbasierte Schemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Von Microsoft verwaltete Steuerung 1019: Kontoverwaltung \| Rollenbasierte Schemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Von Microsoft verwaltete Steuerung 1020: Kontoverwaltung \| Rollenbasierte Schemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Hiermit wird überwacht, ob die Clientauthentifizierung in Service Fabric ausschließlich über Azure Active Directory erfolgt. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Kontoverwaltung | Einschränkungen bei der Verwendung von freigegebenen oder Gruppenkonten

**ID:** NIST SP 800-53 R4 AC-2 (9) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1021: Kontoverwaltung \| Einschränkungen bei der Verwendung von freigegebenen oder Gruppenkonten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Kontoverwaltung | Stornierung von Anmeldeinformationen von freigegebenen oder Gruppenkonten

**ID:** NIST SP 800-53 R4 AC-2 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1022: Kontoverwaltung \| Stornierung von Anmeldeinformationen von freigegebenen oder Gruppenkonten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Kontoverwaltung | Nutzungsbedingungen

**ID:** NIST SP 800-53 R4 AC-2 (11) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1023: Kontoverwaltung \| Bedingungen für die Verwendung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Kontoverwaltung | Kontoüberwachung/ungewöhnliche Nutzung

**ID:** NIST SP 800-53 R4 AC-2 (12) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltungsports von virtuellen Computern sollten mit Just-In-Time-Netzwerkzugriffssteuerung geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Hiermit wird der mögliche Just-In-Time-Netzwerkzugriff über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1024: Kontoverwaltung \| Kontoüberwachung/ungewöhnliche Nutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Von Microsoft verwaltete Steuerung 1025: Kontoverwaltung \| Kontoüberwachung/ungewöhnliche Nutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Kontoverwaltung | Deaktivieren von Konten für Hochrisikopersonen

**ID:** NIST SP 800-53 R4 AC-2 (13) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1026: Kontoverwaltung \| Deaktivieren von Konten für Hochrisikopersonen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Zugriffserzwingung

**ID:** NIST SP 800-53 R4 AC-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1027: Erzwingen des Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Erzwingung des Datenflusses

**ID:** NIST SP 800-53 R4 AC-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nicht jeder Ressource den Zugriff auf Ihre Webanwendungen über CORS gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS (Cross-Origin Resource Sharing) darf nicht allen Domänen Zugriff auf Ihre Webanwendung erteilen. Gestatten Sie nur erforderlichen Domänen die Interaktion mit Ihrer Web-App. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1028: Erzwingung des Datenflusses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Informationsflusserzwingung | Sicherheitsrichtlinienfilter

**ID:** NIST SP 800-53 R4 AC-4 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1029: Erzwingung des Datenflusses \| Sicherheitsrichtlinienfilter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Informationsflusserzwingung| Physische/logische Trennung von Informationsflüssen

**ID:** NIST SP 800-53 R4 AC-4 (21) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1030: Erzwingung des Datenflusses \| Physische/logische Trennung von Informationsflows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>Aufgabentrennung

**ID:** NIST SP 800-53 R4 AC-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Es wird empfohlen, bis zu drei Abonnementbesitzer festzulegen, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Windows-Computer überwachen, auf denen angegebene Mitglieder in der Administratorengruppe fehlen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn mindestens eins der im Richtlinienparameter angegebenen Mitglieder nicht in der lokalen Administratorgruppe enthalten ist. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Windows-Computer überwachen, auf denen die angegebenen Mitglieder in der Administratorengruppe enthalten sind](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn die lokale Administratorgruppe mindestens eins der Mitglieder enthält, die im Richtlinienparameter angegeben sind. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Von Microsoft verwaltete Steuerung 1031: Aufgabentrennung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Von Microsoft verwaltete Steuerung 1032: Aufgabentrennung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Von Microsoft verwaltete Steuerung 1033: Aufgabentrennung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Es wird empfohlen, mehrere Abonnementbesitzer festzulegen, um Redundanz beim Administratorzugriff zu gewährleisten. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>Ansatz der geringsten Rechte

**ID:** NIST SP 800-53 R4 AC-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1034: Ansatz der geringsten Rechte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>Ansatz der geringsten Rechte | Autorisieren des Zugriffs auf Sicherheitsfunktionen

**ID:** NIST SP 800-53 R4 AC-6 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1035: Ansatz der geringsten Rechte \| Autorisieren des Zugriffs auf Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Ansatz der geringsten Rechte | Nicht privilegierter Zugriff auf nicht sicherheitsrelevante Funktionen

**ID:** NIST SP 800-53 R4 AC-6 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1036: Ansatz der geringsten Rechte \| Nicht privilegierter Zugriff auf nicht sicherheitsrelevante Funktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>Ansatz der geringsten Rechte | Netzwerkzugriff auf privilegierte Befehle

**ID:** NIST SP 800-53 R4 AC-6 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1037: Ansatz der geringsten Rechte \| Netzwerkzugriff auf privilegierte Befehle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>Ansatz der geringsten Rechte | Privilegierte Konten

**ID:** NIST SP 800-53 R4 AC-6 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1038: Ansatz der geringsten Rechte \| Privilegierte Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>Ansatz der geringsten Rechte | Kontrolle von Benutzerberechtigungen

**ID:** NIST SP 800-53 R4 AC-6 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Es wird empfohlen, bis zu drei Abonnementbesitzer festzulegen, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Windows-Computer überwachen, auf denen angegebene Mitglieder in der Administratorengruppe fehlen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn mindestens eins der im Richtlinienparameter angegebenen Mitglieder nicht in der lokalen Administratorgruppe enthalten ist. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Windows-Computer überwachen, auf denen die angegebenen Mitglieder in der Administratorengruppe enthalten sind](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn die lokale Administratorgruppe mindestens eins der Mitglieder enthält, die im Richtlinienparameter angegeben sind. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Von Microsoft verwaltete Steuerung 1039: Ansatz der geringsten Rechte \| Steuerung von Benutzerberechtigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Von Microsoft verwaltete Steuerung 1040: Ansatz der geringsten Rechte \| Steuerung von Benutzerberechtigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Es wird empfohlen, mehrere Abonnementbesitzer festzulegen, um Redundanz beim Administratorzugriff zu gewährleisten. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>Ansatz der geringsten Rechte | Berechtigungsebenen für die Ausführung von Code

**ID:** NIST SP 800-53 R4 AC-6 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1041: Ansatz der geringsten Rechte \| Berechtigungsebenen für die Ausführung von Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Ansatz der geringsten Rechte | Überwachen der Verwendung privilegierter Funktionen

**ID:** NIST SP 800-53 R4 AC-6 (9) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1042: Ansatz der geringsten Rechte \| Überwachen der Verwendung privilegierter Funktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Ansatz der geringsten Rechte | Unterbinden der Ausführung privilegierter Funktionen durch unberechtigte Benutzer

**ID:** NIST SP 800-53 R4 AC-6 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1043: Ansatz der geringsten Rechte \| Unterbinden der Ausführung privilegierter Funktionen durch unberechtigte Benutzer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Fehlgeschlagene Anmeldeversuche

**ID:** NIST SP 800-53 R4 AC-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1044: fehlerhafte Anmeldeversuche](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Von Microsoft verwaltete Steuerung 1045: fehlerhafte Anmeldeversuche](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Anzahl fehlgeschlagener Anmeldeversuche | Löschen/Zurücksetzen mobiler Geräte

**ID:** NIST SP 800-53 R4 AC-7 (2) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1046: automatische Kontosperre\| Löschen/Zurücksetzen mobiler Geräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Benachrichtigung zur Systemnutzung

**ID:** NIST SP 800-53 R4 AC-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1047: Benachrichtigung zur Systemnutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Von Microsoft verwaltete Steuerung 1048: Benachrichtigung zur Systemnutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Von Microsoft verwaltete Steuerung 1049: Benachrichtigung zur Systemnutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Steuerung gleichzeitiger Sitzungen

**ID:** NIST SP 800-53 R4 AC-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1050: Steuerung gleichzeitiger Sitzungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Sitzungssperre

**ID:** NIST SP 800-53 R4 AC-11 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1051: Sitzungssperre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Von Microsoft verwaltete Steuerung 1052: Sitzungssperre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Sitzungssperre | Unkenntlichmachung der Anzeige

**ID:** NIST SP 800-53 R4 AC-11 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1053: Sitzungssperre \| Unkenntlichmachung der Anzeige](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Beendigung der Sitzung

**ID:** NIST SP 800-53 R4 AC-12 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1054: Beendigung der Sitzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Beendigung der Sitzung| Benutzerseitig eingeleitete Abmeldungen/Meldungsanzeigen

**ID:** NIST SP 800-53 R4 AC-12 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1055: Beendigung der Sitzung \| Benutzerseitig eingeleitete Abmeldungen/Meldungsanzeigen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Von Microsoft verwaltete Steuerung 1056: Beendigung der Sitzung \| Benutzerseitig eingeleitete Abmeldungen/Meldungsanzeigen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Zulässige Aktionen ohne Identifizierung oder Authentifizierung

**ID:** NIST SP 800-53 R4 AC-14 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1057: zulässige Aktionen ohne Identifizierung oder Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Von Microsoft verwaltete Steuerung 1058: zulässige Aktionen ohne Identifizierung oder Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Sicherheitsattribute

**ID:** NIST SP 800-53 R4 AC-16 **Besitzer:** Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Remotezugriff

**ID:** NIST SP 800-53 R4 AC-17 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1059: Remotezugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Von Microsoft verwaltete Steuerung 1060: Remotezugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Remotezugriff | Automatisierte Überwachung/Steuerung

**ID:** NIST SP 800-53 R4 AC-17 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs ohne Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden, aber keine verwaltete Identität aufweisen. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs mit einer benutzerseitig zugewiesenen Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden und mindestens eine benutzerseitig zugewiesene verwaltete Identität aufweisen, aber keine systemseitig zugewiesene verwaltete Identität. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Linux-Computer überwachen, die Remoteverbindungen über Konten ohne Kennwörter zulassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Linux-Computer handelt, die Remoteverbindungen über Konten ohne Kennwörter zulassen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Erweiterung für die Linux-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Linux-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Mit dieser Richtlinie wird die Erweiterung für die Linux-Gastkonfiguration für in Azure gehostete Linux-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Linux-Gastkonfiguration ist eine Voraussetzung für alle Linux-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Linux-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Erweiterung für die Windows-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Windows-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Mit dieser Richtlinie wird die Erweiterung für die Windows-Gastkonfiguration für in Azure gehostete Windows-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Windows-Gastkonfiguration ist eine Voraussetzung für alle Windows-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Windows-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Von Microsoft verwaltete Steuerung 1061: Remotezugriff \| Automatisierte Überwachung/Steuerung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[Remotedebuggen muss für API-Apps deaktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Für das Remotedebuggen müssen eingehende Ports für API-Apps geöffnet werden. Das Remotedebuggen muss deaktiviert werden. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[Remotedebuggen muss für Funktions-Apps deaktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Für das Remotedebuggen müssen die eingehenden Ports für Funktions-Apps geöffnet sein. Das Remotedebuggen muss deaktiviert werden. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Remotedebuggen sollte für Webanwendungen deaktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Für das Remotedebuggen müssen eingehende Ports für eine Webanwendung geöffnet werden. Das Remotedebuggen muss deaktiviert werden. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Netzwerkzugriff auf Speicherkonten einschränken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Der Netzwerkzugriff auf Speicherkonten sollte eingeschränkt werden. Konfigurieren Sie Netzwerkregeln, sodass nur Anwendungen aus zulässigen Netzwerken auf ein Speicherkonto zugreifen können. Um Verbindungen von bestimmten Internetclients oder lokalen Clients zuzulassen, kann Zugriff für Datenverkehr aus bestimmten virtuellen Azure-Netzwerken oder an IP-Adressbereiche im öffentlichen Internet gewährt werden. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Remotezugriff | Schutz der Vertraulichkeit/Integrität mithilfe von Verschlüsselung

**ID:** NIST SP 800-53 R4 AC-17 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1062: Remotezugriff \| Schutz der Vertraulichkeit/Integrität mithilfe von Verschlüsselung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Remotezugriff | Verwaltete Zugriffssteuerungspunkte

**ID:** NIST SP 800-53 R4 AC-17 (3) **Besitzer:** Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1063: Remotezugriff \| Verwaltete Zugriffssteuerungspunkte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Remotezugriff | Privilegierte Befehle/Zugriff

**ID:** NIST SP 800-53 R4 AC-17 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1064: Remotezugriff \| Privilegierte Befehle/privilegierter Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Von Microsoft verwaltete Steuerung 1065: Remotezugriff \| Privilegierte Befehle/privilegierter Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Remotezugriff | Trennen/Deaktivieren des Zugriffs

**ID:** NIST SP 800-53 R4 AC-17 (9) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1066: Remotezugriff \| Trennen/Deaktivieren des Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Drahtloser Zugriff

**ID:** NIST SP 800-53 R4 AC-18 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1067: drahtloser Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Von Microsoft verwaltete Steuerung 1068: drahtloser Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Drahtloser Zugriff | Authentifizierung und Verschlüsselung

**ID:** NIST SP 800-53 R4 AC-18 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1069: drahtloser Zugriff \| Authentifizierung und Verschlüsselung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Drahtloser Zugriff | Deaktivieren von Drahtlosnetzwerken

**ID:** NIST SP 800-53 R4 AC-18 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1070: drahtloser Zugriff \| Deaktivieren von Drahtlosnetzwerken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Drahtloser Zugriff | Beschränken von Konfigurationen durch Benutzer

**ID:** NIST SP 800-53 R4 AC-18 (4) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1071: drahtloser Zugriff \| Beschränken von Konfigurationen durch Benutzer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Drahtloser Zugriff | Antennen/Sendeleistungspegel

**ID:** NIST SP 800-53 R4 AC-18 (5) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1072: drahtloser Zugriff \| Antennen/Sendeleistungspegel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Zugriffssteuerung für mobile Geräte

**ID:** NIST SP 800-53 R4 AC-19 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1073: Zugriffssteuerung für mobile Geräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Von Microsoft verwaltete Steuerung 1074: Zugriffssteuerung für mobile Geräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Zugriffssteuerung für mobile Geräte | Vollständige Geräte-/containerbasierte Verschlüsselung

**ID:** NIST SP 800-53 R4 AC-19 (5) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1075: Zugriffssteuerung für mobile Geräte \| Vollständige geräte-/containerbasierte Verschlüsselung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Verwendung externer Informationssysteme

**ID:** NIST SP 800-53 R4 AC-20 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1076: Verwendung externer Informationssysteme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Von Microsoft verwaltete Steuerung 1077: Verwendung externer Informationssysteme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Verwendung externer Informationssysteme | Beschränkungen der zulässigen Nutzung

**ID:** NIST SP 800-53 R4 AC-20 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1078: Verwendung externer Informationssysteme \| Beschränkungen der zulässigen Nutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Von Microsoft verwaltete Steuerung 1079: Verwendung externer Informationssysteme \| Beschränkungen der zulässigen Nutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>Verwendung externer Informationssysteme | Tragbare Speichergeräte

**ID:** NIST SP 800-53 R4 AC-20 (2) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1080: Verwendung externer Informationssysteme \| Tragbare Speichergeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Gemeinsame Nutzung von Informationen

**ID:** NIST SP 800-53 R4 AC-21 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1081: gemeinsame Nutzung von Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Von Microsoft verwaltete Steuerung 1082: gemeinsame Nutzung von Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Öffentlich zugängliche Inhalte

**ID:** NIST SP 800-53 R4 AC-22 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1083: öffentlich zugängliche Inhalte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Von Microsoft verwaltete Steuerung 1084: öffentlich zugängliche Inhalte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Von Microsoft verwaltete Steuerung 1085: öffentlich zugängliche Inhalte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Von Microsoft verwaltete Steuerung 1086: öffentlich zugängliche Inhalte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |Microsoft implementiert diese Access Control-Steuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Bewusstsein und Training

### <a name="security-awareness-and-training-policy-and-procedures"></a>Sicherheitsbewusstsein und -schulungen – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 AT-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1087: Sicherheitsbewusstsein und -schulungen – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Von Microsoft verwaltete Steuerung 1088: Sicherheitsbewusstsein und -schulungen – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Schulung zum Sicherheitsbewusstsein

**ID:** NIST SP 800-53 R4 AT-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1089: Schulungen zum Sicherheitsbewusstsein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Von Microsoft verwaltete Steuerung 1090: Schulungen zum Sicherheitsbewusstsein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Von Microsoft verwaltete Steuerung 1091: Schulungen zum Sicherheitsbewusstsein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Schulung zum Sicherheitsbewusstsein | Insiderbedrohung

**ID:** NIST SP 800-53 R4 AT-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1092: Schulungen zum Sicherheitsbewusstsein \| Insiderbedrohung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Rollenbasierte Sicherheitsschulung

**ID:** NIST SP 800-53 R4 AT-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1093: Rollenbasierte Sicherheitsschulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Von Microsoft verwaltete Steuerung 1094: Rollenbasierte Sicherheitsschulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Von Microsoft verwaltete Steuerung 1095: Rollenbasierte Sicherheitsschulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Rollenbasierte Sicherheitsschulungen | Praxisnahe Übungen

**ID:** NIST SP 800-53 R4 AT-3 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1096: Rollenbasierte Sicherheitsschulungen \| Praxisnahe Übungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Rollenbasierte Sicherheitsschulungen | Verdächtige Kommunikation und anormales Systemverhalten

**ID:** NIST SP 800-53 R4 AT-3 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1097: Rollenbasierte Sicherheitsschulungen \| Verdächtige Kommunikation und anomales Systemverhalten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Datensätze von Sicherheitsschulungen

**ID:** NIST SP 800-53 R4 AT-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1098: Datensätze von Sicherheitsschulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Von Microsoft verwaltete Steuerung 1099: Datensätze von Sicherheitsschulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |Microsoft implementiert diese Steuerung zu Bewusstsein und Schulungen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Überwachung und Verantwortlichkeit

### <a name="audit-and-accountability-policy-and-procedures"></a>Überwachung und Verantwortlichkeit – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 AU-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1100: Überwachung und Verantwortlichkeit – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Von Microsoft verwaltete Steuerung 1101: Überwachung und Verantwortlichkeit – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Überwachen von Ereignissen

**ID:** NIST SP 800-53 R4 AU-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1102: Überwachen von Ereignissen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Von Microsoft verwaltete Steuerung 1103: Überwachen von Ereignissen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Von Microsoft verwaltete Steuerung 1104: Überwachen von Ereignissen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Von Microsoft verwaltete Steuerung 1105: Überwachen von Ereignissen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Überwachen von Ereignissen | Prüfungen und Updates

**ID:** NIST SP 800-53 R4 AU-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1106: Überwachen von Ereignissen \| Überprüfungen und Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>Inhalt der Überwachungsdatensätze

**ID:** NIST SP 800-53 R4 AU-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1107: Inhalt der Überwachungsdatensätze](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>Inhalt der Überwachungsdatensätze | Zusätzliche Überwachungsinformationen

**ID:** NIST SP 800-53 R4 AU-3 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1108: Inhalt der Überwachungsdatensätze \| Zusätzliche Überwachungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Inhalt der Überwachungsdatensätze | Zentrale Verwaltung von Inhalten geplanter Überwachungsdatensätze

**ID:** NIST SP 800-53 R4 AU-3 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der definierten Liste vorhanden und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) sich nicht in der definierten Liste befindet und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Log Analytics-Arbeitsbereich für VM überwachen – Berichtskonflikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Meldet VMs als nicht konform, wenn sie keine Protokolle an den in der Richtlinien-/Initiativenzuweisung angegebenen Log Analytics-Arbeitsbereich senden. |Überwachung |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1109: Inhalt der Überwachungsdatensätze \| Zentrale Verwaltung von Inhalten geplanter Überwachungsdatensätze](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Speicherkapazität für Überwachungsdatensätze

**ID:** NIST SP 800-53 R4 AU-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1110: Speicherkapazität für Überwachungsdatensätze](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen

**ID:** NIST SP 800-53 R4 AU-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Überwachen der Diagnoseeinstellung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Hiermit wird die Diagnoseeinstellung für ausgewählte Ressourcentypen überwacht. |Auswirkung „AuditIfNotExists“ |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Die Überwachung in SQL Server muss aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Die Überwachung für Ihre SQL Server-Instanz sollte aktiviert werden, um Datenbankaktivitäten in allen Datenbanken auf dem Server, außer in Synapse, nachzuverfolgen und in einem Überwachungsprotokoll zu speichern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1111: Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Von Microsoft verwaltete Steuerung 1112: Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen | Speicherkapazitäten für Überwachungsdatensätze

**ID:** NIST SP 800-53 R4 AU-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1113: Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen \| Speicherkapazitäten für Überwachungsdatensätze](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen | Benachrichtigungen in Echtzeit

**ID:** NIST SP 800-53 R4 AU-5 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1114: Reaktion auf Verarbeitungsfehler bei Überwachungsinformationen \| Benachrichtigungen in Echtzeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung

**ID:** NIST SP 800-53 R4 AU-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1115: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Von Microsoft verwaltete Steuerung 1116: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Prozessintegration

**ID:** NIST SP 800-53 R4 AU-6 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1117: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Prozessintegration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Korrelieren von Überwachungsrepositorys

**ID:** NIST SP 800-53 R4 AU-6 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1118: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Korrelieren von Überwachungsrepositorys](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Zentrale Überprüfung und Analyse

**ID:** NIST SP 800-53 R4 AU-6 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der definierten Liste vorhanden und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) sich nicht in der definierten Liste befindet und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Log Analytics-Arbeitsbereich für VM überwachen – Berichtskonflikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Meldet VMs als nicht konform, wenn sie keine Protokolle an den in der Richtlinien-/Initiativenzuweisung angegebenen Log Analytics-Arbeitsbereich senden. |Überwachung |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1119: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Zentrale Überprüfung und Analyse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Integrations-, Scan- und Überwachungsfunktionen

**ID:** NIST SP 800-53 R4 AU-6 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1120: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Integrations-, Scan- und Überwachungsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Korrelation mit der physischen Überwachung

**ID:** NIST SP 800-53 R4 AU-6 (6) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1121: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Korrelation mit der physischen Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Zulässige Aktionen

**ID:** NIST SP 800-53 R4 AU-6 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1122: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Zulässige Aktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung | Anpassung der Überwachungsebene

**ID:** NIST SP 800-53 R4 AU-6 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1123: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung \| Anpassung der Überwachungsebene](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Überwachungsreduzierung und Berichterstellung

**ID:** NIST SP 800-53 R4 AU-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1124: Überwachungsreduzierung und Berichterstellung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Von Microsoft verwaltete Steuerung 1125: Überwachungsreduzierung und Berichterstellung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Überwachungsreduzierung und Berichterstellung | Automatische Verarbeitung

**ID:** NIST SP 800-53 R4 AU-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1126: Überwachungsreduzierung und Berichterstellung \| Automatische Verarbeitung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Zeitstempel

**ID:** NIST SP 800-53 R4 AU-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1127: Zeitstempel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Von Microsoft verwaltete Steuerung 1128: Zeitstempel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Zeitstempel | Synchronisierung mit einer verbindlichen Zeitquelle

**ID:** NIST SP 800-53 R4 AU-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1129: Zeitstempel \| Synchronisierung mit einer verbindlichen Zeitquelle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Von Microsoft verwaltete Steuerung 1130: Zeitstempel \| Synchronisierung mit einer verbindlichen Zeitquelle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>Schutz von Überwachungsinformationen

**ID:** NIST SP 800-53 R4 AU-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1131: Schutz von Überwachungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Schutz von Überwachungsinformationen | Sicherung von Überwachungsinformationen auf getrennten physischen Systemen/Komponenten

**ID:** NIST SP 800-53 R4 AU-9 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1132: Schutz von Überwachungsinformationen \| Sicherung von Überwachungsinformationen auf getrennten physischen Systemen/Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>Schutz von Überwachungsinformationen | Kryptografischer Schutz

**ID:** NIST SP 800-53 R4 AU-9 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1133: Schutz von Überwachungsinformationen \| Kryptografischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Schutz von Überwachungsinformationen | Zugriff durch eine Teilmenge berechtigter Benutzer

**ID:** NIST SP 800-53 R4 AU-9 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1134: Schutz von Überwachungsinformationen \| Zugriff durch eine Teilmenge berechtigter Benutzer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>Unleugbarkeit

**ID:** NIST SP 800-53 R4 AU-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1135: Unleugbarkeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Aufbewahrung von Überwachungsdatensätzen

**ID:** NIST SP 800-53 R4 AU-11 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1136: Aufbewahrung von Überwachungsdatensätzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Generierung von Überwachungsdatensätzen

**ID:** NIST SP 800-53 R4 AU-12 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der definierten Liste vorhanden und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Überwachen der Diagnoseeinstellung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Hiermit wird die Diagnoseeinstellung für ausgewählte Ressourcentypen überwacht. |Auswirkung „AuditIfNotExists“ |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) sich nicht in der definierten Liste befindet und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Log Analytics-Arbeitsbereich für VM überwachen – Berichtskonflikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Meldet VMs als nicht konform, wenn sie keine Protokolle an den in der Richtlinien-/Initiativenzuweisung angegebenen Log Analytics-Arbeitsbereich senden. |Überwachung |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Die Überwachung in SQL Server muss aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Die Überwachung für Ihre SQL Server-Instanz sollte aktiviert werden, um Datenbankaktivitäten in allen Datenbanken auf dem Server, außer in Synapse, nachzuverfolgen und in einem Überwachungsprotokoll zu speichern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1137: Generierung von Überwachungsdatensätzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Von Microsoft verwaltete Steuerung 1138: Generierung von Überwachungsdatensätzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Von Microsoft verwaltete Steuerung 1139: Generierung von Überwachungsdatensätzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Generierung von Überwachungsdatensätzen | Systemweiter bzw. zeitlich korrelierter Audit-Trail

**ID:** NIST SP 800-53 R4 AU-12 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1140: Generierung von Überwachungsdatensätzen \| Systemweiter bzw. zeitlich korrelierter Überwachungspfad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Generierung von Überwachungsdatensätzen | Änderungen durch autorisierte Personen

**ID:** NIST SP 800-53 R4 AU-12 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1141: Generierung von Überwachungsdatensätzen \| Änderungen durch autorisierte Personen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |Microsoft implementiert diese Steuerung zu Überwachung und Verantwortlichkeit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Sicherheitsbewertung und -autorisierung

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Sicherheitsbewertungs- und -autorisierungsrichtlinie und -verfahren

**ID:** NIST SP 800-53 R4 CA-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1142: Richtlinien und Verfahren zur Sicherheitsbewertung und Autorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Von Microsoft verwaltete Steuerung 1143: Richtlinien und Verfahren zur Sicherheitsbewertung und Autorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Sicherheitsbewertungen

**ID:** NIST SP 800-53 R4 CA-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1144: Sicherheitsbewertungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Von Microsoft verwaltete Steuerung 1145: Sicherheitsbewertungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Von Microsoft verwaltete Steuerung 1146: Sicherheitsbewertungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Von Microsoft verwaltete Steuerung 1147: Sicherheitsbewertungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Sicherheitsbewertungen | Unabhängige Gutachter

**ID:** NIST SP 800-53 R4 CA-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1148: Sicherheitsbewertungen \| Unabhängige Gutachter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Sicherheitsbewertungen | Spezialisierte Bewertungen

**ID:** NIST SP 800-53 R4 CA-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1149: Sicherheitsbewertungen \| Spezialisierte Bewertungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Sicherheitsbewertungen | Externe Organisationen

**ID:** NIST SP 800-53 R4 CA-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1150: Sicherheitsbewertungen \| Externe Organisationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Systemverbindungen

**ID:** NIST SP 800-53 R4 CA-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1151: Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Von Microsoft verwaltete Steuerung 1152: Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Von Microsoft verwaltete Steuerung 1153: Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Systemverbindungen | Nicht geheime, nicht für die nationale Sicherheit relevante Systemverbindungen

**ID:** NIST SP 800-53 R4 CA-3 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1154: Systemverbindungen \| Nicht geheime, nicht für die nationale Sicherheit relevante Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Systemverbindungen | Einschränkungen für Verbindungen mit externen System

**ID:** NIST SP 800-53 R4 CA-3 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1155: Systemverbindungen \| Einschränkungen für Verbindungen mit externen Systemen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Aktions- und Meilensteinplan

**ID:** NIST SP 800-53 R4 CA-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1156: Aktions- und Meilensteinplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Von Microsoft verwaltete Steuerung 1157: Aktions- und Meilensteinplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Sicherheitsautorisierung

**ID:** NIST SP 800-53 R4 CA-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1158: Sicherheitsautorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Von Microsoft verwaltete Steuerung 1159: Sicherheitsautorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Von Microsoft verwaltete Steuerung 1160: Sicherheitsautorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Kontinuierliche Überwachung

**ID:** NIST SP 800-53 R4 CA-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1161: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Von Microsoft verwaltete Steuerung 1162: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Von Microsoft verwaltete Steuerung 1163: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Von Microsoft verwaltete Steuerung 1164: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Von Microsoft verwaltete Steuerung 1165: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Von Microsoft verwaltete Steuerung 1166: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Von Microsoft verwaltete Steuerung 1167: kontinuierliche Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Kontinuierliche Überwachung | Unabhängige Bewertung

**ID:** NIST SP 800-53 R4 CA-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1168: kontinuierliche Überwachung \| Unabhängige Bewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Kontinuierliche Überwachung | Trendanalysen

**ID:** NIST SP 800-53 R4 CA-7 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1169: kontinuierliche Überwachung \| Trendanalyse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Penetrationstests

**ID:** NIST SP 800-53 R4 CA-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1170: Penetrationstests](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Penetrationstests | Unabhängiger Dienstleister oder Team für Penetrationstests

**ID:** NIST SP 800-53 R4 CA-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1171: Penetrationstests \| Unabhängiger Dienstleister oder Team für Penetrationstests](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>Interne Systemverbindungen

**ID:** NIST SP 800-53 R4 CA-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1172: interne Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Von Microsoft verwaltete Steuerung 1173: interne Systemverbindungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |Microsoft implementiert diese Steuerung zur Sicherheitsbewertung und Autorisierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Konfigurationsverwaltung

### <a name="configuration-management-policy-and-procedures"></a>Konfigurationsverwaltung – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 CM-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1174: Konfigurationsverwaltung – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Von Microsoft verwaltete Steuerung 1175: Konfigurationsverwaltung – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Baselinekonfiguration

**ID:** NIST SP 800-53 R4 CM-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1176: Baselinekonfiguration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Baselinekonfiguration | Überprüfungen und Updates

**ID:** NIST SP 800-53 R4 CM-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1177: Baselinekonfiguration \| Überprüfungen und Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Von Microsoft verwaltete Steuerung 1178: Baselinekonfiguration \| Überprüfungen und Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Von Microsoft verwaltete Steuerung 1179: Baselinekonfiguration \| Überprüfungen und Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Baselinekonfiguration | Automatisierungsunterstützung zur Steigerung der Genauigkeit/Währung

**ID:** NIST SP 800-53 R4 CM-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1180: Baselinekonfiguration \| Automatisierungsunterstützung zur Steigerung der Genauigkeit/Währung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Baselinekonfiguration | Beibehaltung von vorherigen Konfigurationen

**ID:** NIST SP 800-53 R4 CM-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1181: Baselinekonfiguration \| Beibehaltung von vorherigen Konfigurationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Baselinekonfiguration | Konfigurieren von Systemen, Komponenten und Geräten für Gebiete mit hohem Risiko

**ID:** NIST SP 800-53 R4 CM-2 (7) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1182: Baselinekonfiguration \| Konfigurieren von Systemen, Komponenten und Geräten für Gebiete mit hohem Risiko](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Von Microsoft verwaltete Steuerung 1183: Baselinekonfiguration \| Konfigurieren von Systemen, Komponenten und Geräten für Gebiete mit hohem Risiko](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Steuerung von Konfigurationsänderungen

**ID:** NIST SP 800-53 R4 CM-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1184: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Von Microsoft verwaltete Steuerung 1185: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Von Microsoft verwaltete Steuerung 1186: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Von Microsoft verwaltete Steuerung 1187: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Von Microsoft verwaltete Steuerung 1188: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Von Microsoft verwaltete Steuerung 1189: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Von Microsoft verwaltete Steuerung 1190: Steuerung von Konfigurationsänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Steuerung von Konfigurationsänderungen | Automatisierte Mechanismen zu Dokumentation, Benachrichtigungen und Änderungsverboten

**ID:** NIST SP 800-53 R4 CM-3 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1191: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentation, Benachrichtigungen und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Von Microsoft verwaltete Steuerung 1192: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentation, Benachrichtigungen und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Von Microsoft verwaltete Steuerung 1193: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentation, Benachrichtigungen und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Von Microsoft verwaltete Steuerung 1194: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentation, Benachrichtigungen und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Von Microsoft verwaltete Steuerung 1195: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentations-, Benachrichtigungs- und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Von Microsoft verwaltete Steuerung 1196: Steuerung von Konfigurationsänderungen \| Automatisierte Mechanismen zu Dokumentations-, Benachrichtigungs- und Änderungsverboten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Steuerung von Konfigurationsänderungen | Testen, Überprüfen und Dokumentieren von Änderungen

**ID:** NIST SP 800-53 R4 CM-3 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1197: Steuerung von Konfigurationsänderungen \| Testen, Überprüfen und Dokumentieren von Änderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Steuerung von Konfigurationsänderungen | Sicherheitsbeauftragter

**ID:** NIST SP 800-53 R4 CM-3 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1198: Steuerung von Konfigurationsänderungen \| Sicherheitsbeauftragter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Steuerung von Konfigurationsänderungen | Verwaltung von kryptographischen Mechanismen

**ID:** NIST SP 800-53 R4 CM-3 (6) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1199: Steuerung von Konfigurationsänderungen \| Verwaltung von kryptografischen Mechanismen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Analyse der Auswirkungen auf die Sicherheit

**ID:** NIST SP 800-53 R4 CM-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1200: Analyse der Auswirkungen auf die Sicherheit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Analyse der Auswirkungen auf die Sicherheit | Trennen von Testumgebungen

**ID:** NIST SP 800-53 R4 CM-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1201: Analyse der Auswirkungen auf die Sicherheit \| Trennen von Testumgebungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Zugriffseinschränkungen für Änderungen

**ID:** NIST SP 800-53 R4 CM-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1202: Zugriffseinschränkungen für Änderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Zugriffseinschränkungen für Änderungen | Automatisierte Zugriffserzwingung und Überwachung

**ID:** NIST SP 800-53 R4 CM-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1203: Zugriffseinschränkungen für Änderungen \| Automatisierte Zugriffserzwingung und Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Zugriffseinschränkungen für Änderungen | Überprüfen von Systemänderungen

**ID:** NIST SP 800-53 R4 CM-5 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1204: Zugriffseinschränkungen für Änderungen \| Überprüfen von Systemänderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Zugriffseinschränkungen für Änderungen | Signierte Komponenten

**ID:** NIST SP 800-53 R4 CM-5 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1205: Zugriffseinschränkungen für Änderungen \| Signierte Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Zugriffseinschränkungen für Änderungen | Beschränken von Berechtigungen in Bezug auf Produktion/Betrieb

**ID:** NIST SP 800-53 R4 CM-5 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1206: Zugriffseinschränkungen für Änderungen \| Beschränken von Berechtigungen in Bezug auf Produktion/Betrieb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Von Microsoft verwaltete Steuerung 1207: Zugriffseinschränkungen für Änderungen \| Beschränken von Berechtigungen in Bezug auf Produktion/Betrieb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Konfigurationseinstellungen

**ID:** NIST SP 800-53 R4 CM-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1208: Konfigurationseinstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Von Microsoft verwaltete Steuerung 1209: Konfigurationseinstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Von Microsoft verwaltete Steuerung 1210: Konfigurationseinstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Von Microsoft verwaltete Steuerung 1211: Konfigurationseinstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Konfigurationseinstellungen | Automatisierte zentrale Verwaltung, Anwendung und Überprüfung

**ID:** NIST SP 800-53 R4 CM-6 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1212: Konfigurationseinstellungen \| Automatisierte zentrale Verwaltung, Anwendung und Überprüfung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Konfigurationseinstellungen | Reaktion auf nicht autorisierte Änderungen

**ID:** NIST SP 800-53 R4 CM-6 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1213: Konfigurationseinstellungen \| Reaktion auf nicht autorisierte Änderungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>Mindestfunktionalität

**ID:** NIST SP 800-53 R4 CM-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1214: Mindestfunktionalität](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Von Microsoft verwaltete Steuerung 1215: Mindestfunktionalität](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>Mindestfunktionalität | Regelmäßige Überprüfung

**ID:** NIST SP 800-53 R4 CM-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1216: Mindestfunktionalität \| Regelmäßige Überprüfung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Von Microsoft verwaltete Steuerung 1217: Mindestfunktionalität \| Regelmäßige Überprüfung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>Mindestfunktionalität | Unterbinden der Programmausführung

**ID:** NIST SP 800-53 R4 CM-7 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Hiermit wird die Anwendungssteuerung aktiviert, um die Liste der als sicher bekannten Anwendungen zu definieren, die auf Ihren Computern ausgeführt werden. Außerdem werden Sie bei Ausführung anderer Anwendungen benachrichtigt. Dies verstärkt den Schutz Ihrer Computer vor Schadsoftware. Um die Konfiguration und Wartung Ihrer Regeln zu vereinfachen, nutzt Security Center Machine Learning, um die auf den einzelnen Computern ausgeführten Anwendungen zu analysieren und eine Liste der als sicher bekannten Anwendungen vorzuschlagen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1218: Mindestfunktionalität \| Unterbinden der Programmausführung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>Mindestfunktionalität | Autorisierte Software und Whitelists

**ID:** NIST SP 800-53 R4 CM-7 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Hiermit wird die Anwendungssteuerung aktiviert, um die Liste der als sicher bekannten Anwendungen zu definieren, die auf Ihren Computern ausgeführt werden. Außerdem werden Sie bei Ausführung anderer Anwendungen benachrichtigt. Dies verstärkt den Schutz Ihrer Computer vor Schadsoftware. Um die Konfiguration und Wartung Ihrer Regeln zu vereinfachen, nutzt Security Center Machine Learning, um die auf den einzelnen Computern ausgeführten Anwendungen zu analysieren und eine Liste der als sicher bekannten Anwendungen vorzuschlagen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1219: Mindestfunktionalität \| Autorisierte Software und Whitelists](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Von Microsoft verwaltete Steuerung 1220: Mindestfunktionalität \| Autorisierte Software und Whitelists](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Von Microsoft verwaltete Steuerung 1221: Mindestfunktionalität \| Autorisierte Software und Whitelists](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Komponentenbestand des Informationssystems

**ID:** NIST SP 800-53 R4 CM-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1222: Komponentenbestand des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Von Microsoft verwaltete Steuerung 1223: Komponentenbestand des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Komponentenbestand des Informationssystems | Updates bei Installationen und Entfernungen

**ID:** NIST SP 800-53 R4 CM-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1224: Komponentenbestand des Informationssystems \| Updates bei Installationen und Entfernungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Komponentenbestand des Informationssystems | Automatisierte Wartung

**ID:** NIST SP 800-53 R4 CM-8 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1225: Komponentenbestand des Informationssystems \| Automatisierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Komponentenbestand des Informationssystems | Automatisierte Erkennung von nicht autorisierten Komponenten

**ID:** NIST SP 800-53 R4 CM-8 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1226: Komponentenbestand des Informationssystems \| Automatisierte Erkennung von nicht autorisierten Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Von Microsoft verwaltete Steuerung 1227: Komponentenbestand des Informationssystems \| Automatisierte Erkennung von nicht autorisierten Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Komponentenbestand des Informationssystems | Informationen zur Verantwortlichkeit

**ID:** NIST SP 800-53 R4 CM-8 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1228: Komponentenbestand des Informationssystems \| Informationen zur Verantwortlichkeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Komponentenbestand des Informationssystems | Vermeidung einer doppelten Kontoverwaltung für Komponenten

**ID:** NIST SP 800-53 R4 CM-8 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1229: Komponentenbestand des Informationssystems \| Vermeidung einer doppelten Kontoverwaltung für Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Konfigurationsverwaltungsplan

**ID:** NIST SP 800-53 R4 CM-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1230: Konfigurationsverwaltungsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Von Microsoft verwaltete Steuerung 1231: Konfigurationsverwaltungsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Von Microsoft verwaltete Steuerung 1232: Konfigurationsverwaltungsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Von Microsoft verwaltete Steuerung 1233: Konfigurationsverwaltungsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Einschränkungen für die Softwarenutzung

**ID:** NIST SP 800-53 R4 CM-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1234: Einschränkungen für die Softwarenutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Von Microsoft verwaltete Steuerung 1235: Einschränkungen für die Softwarenutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Von Microsoft verwaltete Steuerung 1236: Einschränkungen für die Softwarenutzung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Einschränkungen für die Softwarenutzung | Open Source-Software

**ID:** NIST SP 800-53 R4 CM-10 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1237: Einschränkungen für die Softwarenutzung \| Open-Source-Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>Von Benutzern installierte Software

**ID:** NIST SP 800-53 R4 CM-11 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Hiermit wird die Anwendungssteuerung aktiviert, um die Liste der als sicher bekannten Anwendungen zu definieren, die auf Ihren Computern ausgeführt werden. Außerdem werden Sie bei Ausführung anderer Anwendungen benachrichtigt. Dies verstärkt den Schutz Ihrer Computer vor Schadsoftware. Um die Konfiguration und Wartung Ihrer Regeln zu vereinfachen, nutzt Security Center Machine Learning, um die auf den einzelnen Computern ausgeführten Anwendungen zu analysieren und eine Liste der als sicher bekannten Anwendungen vorzuschlagen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1238: von Benutzern installierte Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Von Microsoft verwaltete Steuerung 1239: von Benutzern installierte Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Von Microsoft verwaltete Steuerung 1240: von Benutzern installierte Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Vom Benutzer installierte Software | Benachrichtigungen zu nicht autorisierten Installationen

**ID:** NIST SP 800-53 R4 CM-11 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1241: von Benutzern installierte Software \| Benachrichtigungen zu nicht autorisierten Installationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |Microsoft implementiert diese Steuerung zur Konfigurationsverwaltung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Notfallplanung

### <a name="contingency-planning-policy-and-procedures"></a>Notfallplanungsrichtlinie und -verfahren

**ID:** NIST SP 800-53 R4 CP-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1242: Notfallplanungsrichtlinie und -verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Von Microsoft verwaltete Steuerung 1243: Notfallplanungsrichtlinie und -verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Notfallplan

**ID:** NIST SP 800-53 R4 CP-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1244: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Von Microsoft verwaltete Steuerung 1245: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Von Microsoft verwaltete Steuerung 1246: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Von Microsoft verwaltete Steuerung 1247: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Von Microsoft verwaltete Steuerung 1248: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Von Microsoft verwaltete Steuerung 1249: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Von Microsoft verwaltete Steuerung 1250: Notfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Notfallplan | Koordination mit zugehörigen Plänen

**ID:** NIST SP 800-53 R4 CP-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1251: Notfallplan \| Koordination mit zugehörigen Plänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Notfallplan | Kapazitätsplanung

**ID:** NIST SP 800-53 R4 CP-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1252: Notfallplan \| Kapazitätsplanung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Notfallplan | Wiederaufnehmen wesentlicher Organisationsziele/Geschäftsfunktionen

**ID:** NIST SP 800-53 R4 CP-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1253: Notfallplan \| Wiederaufnehmen wesentlicher Organisationsziele/Geschäftsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Notfallplan | Wiederaufnehmen aller Organisationsziele/Geschäftsfunktionen

**ID:** NIST SP 800-53 R4 CP-2 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1254: Notfallplan \| Wiederaufnehmen aller Organisationsziele/Geschäftsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Notfallplan | Aufrechterhalten wesentlicher Organisationsziele/Geschäftsfunktionen

**ID:** NIST SP 800-53 R4 CP-2 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1255: Notfallplan \| Aufrechterhalten wesentlicher Organisationsziele/Geschäftsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Notfallplan | Identifizieren kritischer Ressourcen

**ID:** NIST SP 800-53 R4 CP-2 (8) **Besitzer:** Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1256: Notfallplan \| Identifizieren kritischer Ressourcen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Schulungen zu Notfallplänen

**ID:** NIST SP 800-53 R4 CP-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1257: Schulungen zu Notfallplänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Von Microsoft verwaltete Steuerung 1258: Schulungen zu Notfallplänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Von Microsoft verwaltete Steuerung 1259: Schulungen zu Notfallplänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Schulungen zu Notfallplänen | Simulierte Ereignisse

**ID:** NIST SP 800-53 R4 CP-3 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1260: Schulungen zu Notfallplänen \| Simulierte Ereignisse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Notfallplantests

**ID:** NIST SP 800-53 R4 CP-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1261: Notfallplantests](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Von Microsoft verwaltete Steuerung 1262: Notfallplantests](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Von Microsoft verwaltete Steuerung 1263: Notfallplantests](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Notfallplantests | Koordination mit zugehörigen Plänen

**ID:** NIST SP 800-53 R4 CP-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1264: Notfallplantests \| Koordination mit zugehörigen Plänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Notfallplantests | Alternativer Verarbeitungsstandort

**ID:** NIST SP 800-53 R4 CP-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1265: Notfallplantests \| Alternativer Verarbeitungsstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Von Microsoft verwaltete Steuerung 1266: Notfallplantests \| Alternativer Verarbeitungsstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Alternativer Speicherort

**ID:** NIST SP 800-53 R4 CP-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1267: alternativer Speicherstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Von Microsoft verwaltete Steuerung 1268: alternativer Speicherstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Alternativer Speicherort | Trennung vom primären Standort

**ID:** NIST SP 800-53 R4 CP-6 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1269: alternativer Speicherstandort \| Trennung vom primären Standort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Alternativer Speicherort | Wiederherstellungszeit/Punktziele

**ID:** NIST SP 800-53 R4 CP-6 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1270: alternativer Speicherstandort \| Wiederherstellungszeit/Punktziele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Alternativer Speicherort | Zugänglichkeit

**ID:** NIST SP 800-53 R4 CP-6 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1271: alternativer Speicherstandort \| Barrierefreiheit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Alternativer Verarbeitungsstandort

**ID:** NIST SP 800-53 R4 CP-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VMs überwachen, für die keine Notfallwiederherstellung konfiguriert ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Hiermit werden VMs überwacht, für die keine Notfallwiederherstellung konfiguriert ist. Weitere Informationen zur Notfallwiederherstellung finden Sie unter [https://aka.ms/asr-doc](https://aka.ms/asr-doc). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1272: alternativer Verarbeitungsstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Von Microsoft verwaltete Steuerung 1273: alternativer Verarbeitungsstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Von Microsoft verwaltete Steuerung 1274: alternativer Verarbeitungsstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Alternativer Verarbeitungsstandort | Trennung vom primären Standort

**ID:** NIST SP 800-53 R4 CP-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1275: alternativer Verarbeitungsstandort \| Trennung vom primären Standort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Alternativer Verarbeitungsstandort | Zugänglichkeit

**ID:** NIST SP 800-53 R4 CP-7 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1276: alternativer Verarbeitungsstandort \| Barrierefreiheit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Alternativer Verarbeitungsstandort | Dienstpriorität

**ID:** NIST SP 800-53 R4 CP-7 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1277: alternativer Verarbeitungsstandort \| Dienstpriorität](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Alternativer Verarbeitungsstandort | Vorbereitung der Verwendung

**ID:** NIST SP 800-53 R4 CP-7 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1278: alternativer Verarbeitungsstandort \| Vorbereitung der Verwendung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Telekommunikationsdienste

**ID:** NIST SP 800-53 R4 CP-8 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1279: Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Telekommunikationsdienste | Priorität von Dienstbereitstellungen

**ID:** NIST SP 800-53 R4 CP-8 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1280: Telekommunikationsdienste \| Priorität von Dienstbereitstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Von Microsoft verwaltete Steuerung 1281: Telekommunikationsdienste \| Priorität von Dienstbereitstellungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Telekommunikationsdienste | Single Points of Failure

**ID:** NIST SP 800-53 R4 CP-8 (2) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1282: Telekommunikationsdienste \| Single Points of Failure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Telekommunikationsdienste | Trennung von primären/alternativen Anbietern

**ID:** NIST SP 800-53 R4 CP-8 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1283: Telekommunikationsdienste \| Trennung von primären/alternativen Anbietern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Telekommunikationsdienste | Anbieternotfallplan

**ID:** NIST SP 800-53 R4 CP-8 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1284: Telekommunikationsdienste \| Anbieternotfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Von Microsoft verwaltete Steuerung 1285: Telekommunikationsdienste \| Anbieternotfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Von Microsoft verwaltete Steuerung 1286: Telekommunikationsdienste \| Anbieternotfallplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Sicherung des Informationssystems

**ID:** NIST SP 800-53 R4 CP-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1287: Sicherung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Von Microsoft verwaltete Steuerung 1288: Sicherung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Von Microsoft verwaltete Steuerung 1289: Sicherung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Von Microsoft verwaltete Steuerung 1290: Sicherung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Sicherung des Informationssystems | Zuverlässigkeits-/Integritätstest

**ID:** NIST SP 800-53 R4 CP-9 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1291: Sicherung des Informationssystems \| Zuverlässigkeits-/Integritätstest](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Sicherung des Informationssystems | Wiederherstellungstests mithilfe von Stichproben

**ID:** NIST SP 800-53 R4 CP-9 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1292: Sicherung des Informationssystems \| Wiederherstellungstests mithilfe von Stichproben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Sicherung des Informationssystems | Separater Speicher für kritische Daten

**ID:** NIST SP 800-53 R4 CP-9 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1293: Sicherung des Informationssystems \| Separater Speicher für kritische Daten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Sicherung des Informationssystems | Übertragung an den alternativen Speicherort

**ID:** NIST SP 800-53 R4 CP-9 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1294: Sicherung des Informationssystems \| Übertragung an den alternativen Speicherstandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Wiederherstellung und Wiederinbetriebnahme des Informationssystems

**ID:** NIST SP 800-53 R4 CP-10 **Besitzer:** Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1295: Wiederherstellung und Wiederinbetriebnahme des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Wiederherstellung und Wiederinbetriebnahme des Informationssystems | Transaktionswiederherstellung

**ID:** NIST SP 800-53 R4 CP-10 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1296: Wiederherstellung und Wiederinbetriebnahme des Informationssystems \| Transaktionswiederherstellung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Wiederherstellung und Wiederinbetriebnahme des Informationssystems | Wiederherstellung innerhalb eines Zeitraums

**ID:** NIST SP 800-53 R4 CP-10 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1297: Wiederherstellung und Wiederinbetriebnahme des Informationssystems \| Wiederherstellung innerhalb eines Zeitraums](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |Microsoft implementiert diese Steuerung zur Notfallplanung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Identifizierung und Authentifizierung

### <a name="identification-and-authentication-policy-and-procedures"></a>Identifikation und Authentifizierung – Richtlinie und Verfahren

**ID:** NIST SP 800-53 R4 IA-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1298: Identifikation, Authentifizierung und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Von Microsoft verwaltete Steuerung 1299: Identifikation, Authentifizierung und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Identifikation und Authentifizierung (Organisationsbenutzer)

**ID:** NIST SP 800-53 R4 IA-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1300: Identifikation und Authentifizierung (Organisationsbenutzer)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf privilegierte Konten

**ID:** NIST SP 800-53 R4 IA-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für Konten mit Schreibberechtigungen für Ihr Abonnement muss MFA aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Schreibrechten aktiviert sein, um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Besitzerberechtigungen aktiviert werden, um eine Sicherheitsverletzung für Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1301: Identifikation und Authentifizierung (Organisationsbenutzer) \| Netzwerkzugriff auf privilegierte Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf nicht privilegierte Konten

**ID:** NIST SP 800-53 R4 IA-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Leserechten aktiviert sein, um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1302: Identifikation und Authentifizierung (Organisationsbenutzer) \| Netzwerkzugriff auf nicht privilegierte Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Lokaler Zugriff auf privilegierte Konten

**ID:** NIST SP 800-53 R4 IA-2 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1303: Identifikation und Authentifizierung (Organisationsbenutzer) \| Lokaler Zugriff auf privilegierte Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Lokaler Zugriff auf nicht privilegierte Konten

**ID:** NIST SP 800-53 R4 IA-2 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1304: Identifikation und Authentifizierung (Organisationsbenutzer) \| Lokaler Zugriff auf nicht privilegierte Konten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identifikation und Authentifizierung (Organisationsbenutzer)| Gruppenauthentifizierung

**ID:** NIST SP 800-53 R4 IA-2 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1305: Identifikation und Authentifizierung (Organisationsbenutzer) \| Gruppenauthentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf privilegierte Konten – Schutz vor Replay-Angriffen

**ID:** NIST SP 800-53 R4 IA-2 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1306: Identifikation und Authentifizierung (Organisationsbenutzer) \| Netzwerkzugriff auf privilegierte Konten – Schutz vor Replay-Angriffen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identifikation und Authentifizierung (Organisationsbenutzer) | Netzwerkzugriff auf nicht privilegierte Konten – Schutz vor Replay-Angriffen

**ID:** NIST SP 800-53 R4 IA-2 (9) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1307: Identifikation und Authentifizierung (Organisationsbenutzer) \| Netzwerkzugriff auf nicht privilegierte Konten – Schutz vor Replay-Angriffen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identifikation und Authentifizierung (Organisationsbenutzer)| Remotezugriff – separates Gerät

**ID:** NIST SP 800-53 R4 IA-2 (11) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1308: Identifikation und Authentifizierung (Organisationsbenutzer) \| Remotezugriff – separates Gerät](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identifikation und Authentifizierung (Organisationsbenutzer)| Akzeptieren von PIV-Anmeldeinformationen

**ID:** NIST SP 800-53 R4 IA-2 (12) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1309: Identifikation und Authentifizierung (Organisationsbenutzer) \| Akzeptieren von PIV-Anmeldeinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Identifikation und Authentifizierung von Geräten

**ID:** NIST SP 800-53 R4 IA-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1310: Identifikation und Authentifizierung von Geräten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Bezeichnerverwaltung

**ID:** NIST SP 800-53 R4 IA-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1311: Bezeichnerverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Von Microsoft verwaltete Steuerung 1312: Bezeichnerverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Von Microsoft verwaltete Steuerung 1313: Bezeichnerverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Von Microsoft verwaltete Steuerung 1314: Bezeichnerverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Von Microsoft verwaltete Steuerung 1315: Bezeichnerverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Bezeichnerverwaltung | Identifizieren des Benutzerstatus

**ID:** NIST SP 800-53 R4 IA-4 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1316: Bezeichnerverwaltung \| Identifizieren des Benutzerstatus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Authentifikatorverwaltung

**ID:** NIST SP 800-53 R4 IA-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs ohne Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden, aber keine verwaltete Identität aufweisen. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs mit einer benutzerseitig zugewiesenen Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden und mindestens eine benutzerseitig zugewiesene verwaltete Identität aufweisen, aber keine systemseitig zugewiesene verwaltete Identität. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Linux-Computer überwachen, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Linux-Computer handelt, bei denen die passwd-Dateiberechtigungen nicht auf 0644 festgelegt sind. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Linux-Computer überwachen, die Konten ohne Kennwörter verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Linux-Computer handelt, die über Konten ohne Kennwörter verfügen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Windows-Computer überwachen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Erweiterung für die Linux-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Linux-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Mit dieser Richtlinie wird die Erweiterung für die Linux-Gastkonfiguration für in Azure gehostete Linux-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Linux-Gastkonfiguration ist eine Voraussetzung für alle Linux-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Linux-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Erweiterung für die Windows-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Windows-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Mit dieser Richtlinie wird die Erweiterung für die Windows-Gastkonfiguration für in Azure gehostete Windows-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Windows-Gastkonfiguration ist eine Voraussetzung für alle Windows-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Windows-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Von Microsoft verwaltete Steuerung 1317: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Von Microsoft verwaltete Steuerung 1318: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Von Microsoft verwaltete Steuerung 1319: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Von Microsoft verwaltete Steuerung 1320: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Von Microsoft verwaltete Steuerung 1321: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Von Microsoft verwaltete Steuerung 1322: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Von Microsoft verwaltete Steuerung 1323: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Von Microsoft verwaltete Steuerung 1324: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Von Microsoft verwaltete Steuerung 1325: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Von Microsoft verwaltete Steuerung 1326: Authentifikatorverwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Authentifikatorverwaltung |Kennwortbasierte Authentifizierung

**ID:** NIST SP 800-53 R4 IA-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs ohne Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden, aber keine verwaltete Identität aufweisen. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs mit einer benutzerseitig zugewiesenen Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Mit dieser Richtlinie wird eine systemseitig zugewiesene verwaltete Identität für in Azure gehostete VMs hinzugefügt, die durch die Gastkonfiguration unterstützt werden und mindestens eine benutzerseitig zugewiesene verwaltete Identität aufweisen, aber keine systemseitig zugewiesene verwaltete Identität. Eine systemseitig zugewiesene verwaltete Identität ist eine Voraussetzung für alle Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Windows-Computer überwachen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[Windows-Computer überwachen, für die kein maximales Kennwortalter von 70 Tagen gilt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, für die kein maximales Kennwortalter von 70 Tagen gilt. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[Windows-Computer überwachen, die kein Mindestkennwortalter von einem Tag verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, die kein Mindestkennwortalter von einem Tag verwenden. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Windows-Computer überwachen, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, auf denen nicht die Einstellung für die Kennwortkomplexität aktiviert ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Windows-Computer überwachen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Windows-Computer überwachen, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn es sich um Windows-Computer handelt, die Kennwörter nicht mit umkehrbarer Verschlüsselung speichern. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Erweiterung für die Linux-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Linux-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Mit dieser Richtlinie wird die Erweiterung für die Linux-Gastkonfiguration für in Azure gehostete Linux-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Linux-Gastkonfiguration ist eine Voraussetzung für alle Linux-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Linux-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Erweiterung für die Windows-Gastkonfiguration bereitstellen, um Gastkonfigurationszuweisungen für Windows-VMs zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Mit dieser Richtlinie wird die Erweiterung für die Windows-Gastkonfiguration für in Azure gehostete Windows-VMs bereitgestellt, die durch die Gastkonfiguration unterstützt werden. Die Erweiterung für die Windows-Gastkonfiguration ist eine Voraussetzung für alle Windows-Gastkonfigurationszuweisungen und muss auf den Computern bereitgestellt werden, bevor eine Richtliniendefinition der Windows-Gastkonfiguration verwendet werden kann. Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Von Microsoft verwaltete Steuerung 1327: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Von Microsoft verwaltete Steuerung 1328: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Von Microsoft verwaltete Steuerung 1329: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Von Microsoft verwaltete Steuerung 1330: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Von Microsoft verwaltete Steuerung 1331: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Von Microsoft verwaltete Steuerung 1332: Authentifikatorverwaltung \| Kennwortbasierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Authentifikatorverwaltung |Kennwortbasierte Authentifizierung

**ID:** NIST SP 800-53 R4 IA-5 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1333: Authentifikatorverwaltung \| PKI-basierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Von Microsoft verwaltete Steuerung 1334: Authentifikatorverwaltung \| PKI-basierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Von Microsoft verwaltete Steuerung 1335: Authentifikatorverwaltung \| PKI-basierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Von Microsoft verwaltete Steuerung 1336: Authentifikatorverwaltung \| PKI-basierte Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Authentifikatorverwaltung | Persönliche Registrierung oder Registrierung über vertrauenswürdige Drittanbieter

**ID:** NIST SP 800-53 R4 IA-5 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1337: Authentifikatorverwaltung \| Persönliche Registrierung oder Registrierung über vertrauenswürdige Drittanbieter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Authentifikatorverwaltung | Automatisierte Unterstützung zum Bestimmen der Kennwortsicherheit

**ID:** NIST SP 800-53 R4 IA-5 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1338: Authentifikatorverwaltung \| Automatisierte Unterstützung zum Bestimmen der Kennwortsicherheit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Authentifikatorverwaltung | Schutz von Authentifikatoren

**ID:** NIST SP 800-53 R4 IA-5 (6) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1339: Authentifikatorverwaltung \| Schutz von Authentifikatoren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Authentifikatorverwaltung | Keine Einbettung unverschlüsselter statischer Authentifikatoren

**ID:** NIST SP 800-53 R4 IA-5 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1340: Authentifikatorverwaltung \| Keine Einbettung unverschlüsselter statischer Authentifikatoren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Authentifikatorverwaltung | Konten auf mehreren Informationssystemen

**ID:** NIST SP 800-53 R4 IA-5 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1341: Authentifikatorverwaltung \| Konten auf mehreren Informationssystemen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Authentifikatorverwaltung | Auf Hardwaretoken basierende Authentifizierung

**ID:** NIST SP 800-53 R4 IA-5 (11) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1342: Authentifikatorverwaltung \| Auf Hardwaretoken basierende Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Authentifikatorverwaltung | Ablauf zwischengespeicherter Authentifikatoren

**ID:** NIST SP 800-53 R4 IA-5 (13) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1343: Authentifikatorverwaltung \| Ablauf zwischengespeicherter Authentifikatoren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Authentifikatorrückmeldung

**ID:** NIST SP 800-53 R4 IA-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1344: Authentifikatorrückmeldung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Kryptografiemodulauthentifizierung

**ID:** NIST SP 800-53 R4 IA-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1345: Kryptografiemodulauthentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Identifikation und Authentifizierung (Nicht-Organisationsbenutzer)

**ID:** NIST SP 800-53 R4 IA-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1346: Identifikation und Authentifizierung (Nicht-Organisationsbenutzer)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identifikation und Authentifizierung (Nicht-Organisationsbenutzer)| Akzeptieren von PIV-Anmeldeinformationen von anderen Behörden

**ID:** NIST SP 800-53 R4 IA-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1347: Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) \| Akzeptieren von PIV-Anmeldeinformationen von anderen Behörden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identifikation und Authentifizierung (Organisationsbenutzer)| Akzeptieren von Anmeldeinformationen von Drittanbietern

**ID:** NIST SP 800-53 R4 IA-8 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1348: Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) \| Akzeptieren von Anmeldeinformationen von Drittanbietern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) | Verwendung von FICAM-genehmigten Produkten

**ID:** NIST SP 800-53 R4 IA-8 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1349: Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) \| Verwendung von FICAM-genehmigten Produkten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) | Verwendung von über FICAM ausgegebenen Profilen

**ID:** NIST SP 800-53 R4 IA-8 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1350: Identifikation und Authentifizierung (Nicht-Organisationsbenutzer) \| Verwendung von über FICAM ausgegebenen Profilen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |Microsoft implementiert diese Steuerung zu Identifikation und Authentifizierung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Reaktion auf Vorfälle

### <a name="incident-response-policy-and-procedures"></a>Reaktion auf Vorfälle – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 IR-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1351: Reaktion auf Vorfälle – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Von Microsoft verwaltete Steuerung 1352: Reaktion auf Vorfälle – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Schulung zur Reaktion auf Vorfälle

**ID:** NIST SP 800-53 R4 IR-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1353: Schulung zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Von Microsoft verwaltete Steuerung 1354: Schulung zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Von Microsoft verwaltete Steuerung 1355: Schulung zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Schulung zur Reaktion auf Vorfälle | Simulierte Ereignisse

**ID:** NIST SP 800-53 R4 IR-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1356: Schulung zur Reaktion auf Vorfälle \| Simulierte Ereignisse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Schulung zur Reaktion auf Vorfälle | Automatisierte Schulungsumgebungen

**ID:** NIST SP 800-53 R4 IR-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1357: Schulung zur Reaktion auf Vorfälle \| Automatisierte Schulungsumgebungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Tests zur Reaktion auf Vorfälle

**ID:** NIST SP 800-53 R4 IR-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1358: Tests zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Tests zur Reaktionsfähigkeit bei Vorfällen | Koordination mit zugehörigen Plänen

**ID:** NIST SP 800-53 R4 IR-3 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1359: Tests zur Reaktion auf Vorfälle \| Koordination mit zugehörigen Plänen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Behandlung von Vorfällen

**ID:** NIST SP 800-53 R4 IR-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1360: Behandlung von Vorfällen ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Von Microsoft verwaltete Steuerung 1361: Behandlung von Vorfällen ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Von Microsoft verwaltete Steuerung 1362: Behandlung von Vorfällen ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Behandlung von Vorfällen | Automatisierte Prozesse zur Behandlung von Vorfällen

**ID:** NIST SP 800-53 R4 IR-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1363: Behandlung von Vorfällen \| Automatisierte Prozesse zur Behandlung von Vorfällen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Behandlung von Vorfällen | Dynamische Neukonfiguration

**ID:** NIST SP 800-53 R4 IR-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1364: Behandlung von Vorfällen \| Dynamische Neukonfiguration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Behandlung von Vorfällen | Fortsetzung von Vorgängen

**ID:** NIST SP 800-53 R4 IR-4 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1365: Behandlung von Vorfällen \| Fortsetzung von Vorgängen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Behandlung von Vorfällen | Korrelation von Informationen

**ID:** NIST SP 800-53 R4 IR-4 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1366: Behandlung von Vorfällen \| Korrelation von Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Behandlung von Vorfällen | Insiderbedrohungen – spezielle Methoden

**ID:** NIST SP 800-53 R4 IR-4 (6) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1367: Behandlung von Vorfällen \| Insiderbedrohungen – spezielle Methoden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Behandlung von Vorfällen | Korrelation mit externen Organisationen

**ID:** NIST SP 800-53 R4 IR-4 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1368: Behandlung von Vorfällen \| Korrelation mit externen Organisationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Überwachen von Vorfällen

**ID:** NIST SP 800-53 R4 IR-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1369: Überwachen von Vorfällen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Überwachung von Vorfällen | Automatisierte Nachverfolgung, Datensammlung und Analyse

**ID:** NIST SP 800-53 R4 IR-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1370: Überwachung von Vorfällen \| Automatisierte Nachverfolgung, Datensammlung und Analyse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Melden von Vorfällen

**ID:** NIST SP 800-53 R4 IR-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1371: Melden von Vorfällen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Von Microsoft verwaltete Steuerung 1372: Melden von Vorfällen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Melden von Vorfällen | Automatisierte Meldung

**ID:** NIST SP 800-53 R4 IR-6 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1373: Melden von Vorfällen \| Automatisierte Meldung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Unterstützung bei der Reaktion auf Vorfälle

**ID:** NIST SP 800-53 R4 IR-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1374: Unterstützung bei der Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Unterstützung bei der Reaktion auf Vorfälle | Automatisierung des Supports zur Sicherstellung der Verfügbarkeit von Informationen und Support

**ID:** NIST SP 800-53 R4 IR-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1375: Unterstützung bei der Reaktion auf Vorfälle \| Automatisierung des Supports zur Sicherstellung der Verfügbarkeit von Informationen und Support](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Unterstützung bei der Reaktion auf Vorfälle | Koordination mit externen Anbietern

**ID:** NIST SP 800-53 R4 IR-7 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1376: Unterstützung bei der Reaktion auf Vorfälle \| Koordination mit externen Anbietern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Von Microsoft verwaltete Steuerung 1377: Unterstützung bei der Reaktion auf Vorfälle \| Koordination mit externen Anbietern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Plan zur Reaktion auf Vorfälle

**ID:** NIST SP 800-53 R4 IR-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1378: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Von Microsoft verwaltete Steuerung 1379: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Von Microsoft verwaltete Steuerung 1380: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Von Microsoft verwaltete Steuerung 1381: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Von Microsoft verwaltete Steuerung 1382: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Von Microsoft verwaltete Steuerung 1383: Plan zur Reaktion auf Vorfälle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Reaktion auf Informationslecks

**ID:** NIST SP 800-53 R4 IR-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1384: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Von Microsoft verwaltete Steuerung 1385: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Von Microsoft verwaltete Steuerung 1386: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Von Microsoft verwaltete Steuerung 1387: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Von Microsoft verwaltete Steuerung 1388: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Von Microsoft verwaltete Steuerung 1389: Reaktion auf Informationslecks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Reaktion auf Informationslecks | Zuständiges Personal

**ID:** NIST SP 800-53 R4 IR-9 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1390: Reaktion auf Informationslecks \| Zuständiges Personal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Reaktion auf Informationslecks | Schulung

**ID:** NIST SP 800-53 R4 IR-9 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1391: Reaktion auf Informationslecks \| Schulung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Reaktion auf Informationslecks | Vorgänge nach einem Leck

**ID:** NIST SP 800-53 R4 IR-9 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1392: Reaktion auf Informationslecks \| Vorgänge nach einem Leck](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Reaktion auf Informationslecks | Offenlegung gegenüber unbefugtem Personal

**ID:** NIST SP 800-53 R4 IR-9 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1393: Reaktion auf Informationslecks \| Offenlegung gegenüber unbefugtem Personal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |Microsoft implementiert diese Steuerung zur Reaktion auf Vorfälle |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Wartung

### <a name="system-maintenance-policy-and-procedures"></a>Systemwartung – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 MA-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1394: Systemwartung – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Von Microsoft verwaltete Steuerung 1395: Systemwartung – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Kontrollierte Wartung

**ID:** NIST SP 800-53 R4 MA-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1396: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Von Microsoft verwaltete Steuerung 1397: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Von Microsoft verwaltete Steuerung 1398: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Von Microsoft verwaltete Steuerung 1399: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Von Microsoft verwaltete Steuerung 1400: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Von Microsoft verwaltete Steuerung 1401: kontrollierte Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrollierte Wartung | Automatisierte Wartungsaktivitäten

**ID:** NIST SP 800-53 R4 MA-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1402: kontrollierte Wartung \| Automatisierte Wartungsaktivitäten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Von Microsoft verwaltete Steuerung 1403: kontrollierte Wartung \| Automatisierte Wartungsaktivitäten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Wartungswerkzeuge

**ID:** NIST SP 800-53 R4 MA-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1404: Wartungswerkzeuge](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Wartungswerkzeuge | Überprüfen der Werkzeuge

**ID:** NIST SP 800-53 R4 MA-3 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1405: Wartungswerkzeuge \| Überprüfen der Werkzeuge](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Wartungswerkzeuge | Überprüfen von Medien

**ID:** NIST SP 800-53 R4 MA-3 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1406: Wartungswerkzeuge \| Überprüfen von Medien](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Wartungswerkzeuge | Vermeidung einer unbefugten Entfernung

**ID:** NIST SP 800-53 R4 MA-3 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1407: Wartungswerkzeuge \| Vermeidung einer unbefugten Entfernung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Von Microsoft verwaltete Steuerung 1408: Wartungswerkzeuge \| Vermeidung einer unbefugten Entfernung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Von Microsoft verwaltete Steuerung 1409: Wartungswerkzeuge \| Vermeidung einer unbefugten Entfernung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Von Microsoft verwaltete Steuerung 1410: Wartungswerkzeuge \| Vermeidung einer unbefugten Entfernung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Nichtlokale Wartung

**ID:** NIST SP 800-53 R4 MA-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1411: nichtlokale Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Von Microsoft verwaltete Steuerung 1412: nichtlokale Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Von Microsoft verwaltete Steuerung 1413: nichtlokale Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Von Microsoft verwaltete Steuerung 1414: nichtlokale Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Von Microsoft verwaltete Steuerung 1415: nichtlokale Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nichtlokale Wartung | Dokumentieren von nichtlokalen Wartungsaktivitäten

**ID:** NIST SP 800-53 R4 MA-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1416: nichtlokale Wartung \| Dokumentieren von nichtlokalen Wartungsaktivitäten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nichtlokale Wartung | Vergleichbares Sicherheits- bzw. Bereinigungssystem

**ID:** NIST SP 800-53 R4 MA-4 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1417: nichtlokale Wartung \| Vergleichbares Sicherheits- bzw. Bereinigungssystem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Von Microsoft verwaltete Steuerung 1418: nichtlokale Wartung \| Vergleichbares Sicherheits- bzw. Bereinigungssystem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nichtlokale Wartung | Kryptografischer Schutz

**ID:** NIST SP 800-53 R4 MA-4 (6) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1419: nichtlokale Wartung \| Kryptografischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Wartungsmitarbeiter

**ID:** NIST SP 800-53 R4 MA-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1420: Wartungsmitarbeiter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Von Microsoft verwaltete Steuerung 1421: Wartungsmitarbeiter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Von Microsoft verwaltete Steuerung 1422: Wartungsmitarbeiter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Wartungsmitarbeiter | Mitarbeiter ohne entsprechende Zugangsberechtigungen

**ID:** NIST SP 800-53 R4 MA-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1423: Wartungsmitarbeiter \| Mitarbeiter ohne entsprechende Zugangsberechtigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Von Microsoft verwaltete Steuerung 1424: Wartungsmitarbeiter \| Mitarbeiter ohne entsprechende Zugangsberechtigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Rechtzeitige Wartung

**ID:** NIST SP 800-53 R4 MA-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1425: rechtzeitige Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |Microsoft implementiert diese Wartungssteuerung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Medienschutz

### <a name="media-protection-policy-and-procedures"></a>Richtlinie und Verfahren zum Medienschutz

**ID:** NIST SP 800-53 R4 MP-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1426: Richtlinien und Verfahren zum Medienschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Von Microsoft verwaltete Steuerung 1427: Richtlinien und Verfahren zum Medienschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Medienzugriff

**ID:** NIST SP 800-53 R4 MP-2 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1428: Medienzugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Medienkennzeichnung

**ID:** NIST SP 800-53 R4 MP-3 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1429: Medienkennzeichnung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Von Microsoft verwaltete Steuerung 1430: Medienkennzeichnung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Medienspeicher

**ID:** NIST SP 800-53 R4 MP-4 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1431: Medienspeicher](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Von Microsoft verwaltete Steuerung 1432: Medienspeicher](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Medientransport

**ID:** NIST SP 800-53 R4 MP-5 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1433: Medientransport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Von Microsoft verwaltete Steuerung 1434: Medientransport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Von Microsoft verwaltete Steuerung 1435: Medientransport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Von Microsoft verwaltete Steuerung 1436: Medientransport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Medientransport | Kryptografischer Schutz

**ID:** NIST SP 800-53 R4 MP-5 (4) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1437: Medientransport \| Kryptografischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Medienbereinigung

**ID:** NIST SP 800-53 R4 MP-6 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1438: Medienbereinigung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Von Microsoft verwaltete Steuerung 1439: Medienbereinigung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Medienbereinigung | Überprüfen / Genehmigen / Überwachen / Dokumentieren / Verifizieren

**ID:** NIST SP 800-53 R4 MP-6 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1440: Medienbereinigung \| Überprüfen/Genehmigen/Überwachen/Dokumentieren/Verifizieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Medienbereinigung | Testen von Arbeitsgeräten

**ID:** NIST SP 800-53 R4 MP-6 (2) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1441: Medienbereinigung \| Testen von Arbeitsgeräten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Medienbereinigung | Nicht zerstörerische Techniken

**ID:** NIST SP 800-53 R4 MP-6 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1442: Medienbereinigung \| Nicht zerstörerische Techniken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Verwenden von Medien

**ID:** NIST SP 800-53 R4 MP-7 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1443: Verwenden von Medien](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Verwenden der Medien | Verhindern der Verwendung ohne Besitzer

**ID:** NIST SP 800-53 R4 MP-7 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1444: Verwenden von Medien \| Verhindern der Verwendung ohne Besitzer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |Microsoft implementiert diese Steuerung zum Medienschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Physischer Schutz und Schutz der Umgebung

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Verfahren für physischen Schutz und Schutz der Umgebung

**ID:** NIST SP 800-53 R4 PE-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1445: Richtlinien und Verfahren für physischen Schutz und Schutz der Umgebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Von Microsoft verwaltete Steuerung 1446: Richtlinien und Verfahren für physischen Schutz und Schutz der Umgebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Autorisierungen für physischen Zugriff

**ID:** NIST SP 800-53 R4 PE-2 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1447: Autorisierungen für physischen Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Von Microsoft verwaltete Steuerung 1448: Autorisierungen für physischen Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Von Microsoft verwaltete Steuerung 1449: Autorisierungen für physischen Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Von Microsoft verwaltete Steuerung 1450: Autorisierungen für physischen Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Steuerung des physischen Zugriffs

**ID:** NIST SP 800-53 R4 PE-3 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1451: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Von Microsoft verwaltete Steuerung 1452: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Von Microsoft verwaltete Steuerung 1453: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Von Microsoft verwaltete Steuerung 1454: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Von Microsoft verwaltete Steuerung 1455: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Von Microsoft verwaltete Steuerung 1456: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Von Microsoft verwaltete Steuerung 1457: Steuerung des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Steuerung des physischen Zugriffs | Zugriff auf das Informationssystem

**ID:** NIST SP 800-53 R4 PE-3 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1458: Steuerung des physischen Zugriffs \| Zugriff auf das Informationssystem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Zugriffssteuerung für das Übertragungsmedium

**ID:** NIST SP 800-53 R4 PE-4 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1459: Zugriffssteuerung für das Übertragungsmedium](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Steuerung des Zugriffs auf Ausgabegeräte

**ID:** NIST SP 800-53 R4 PE-5 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1460: Zugriffssteuerung für Ausgabegeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Überwachen des physischen Zugriffs

**ID:** NIST SP 800-53 R4 PE-6 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1461: Überwachen des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Von Microsoft verwaltete Steuerung 1462: Überwachen des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Von Microsoft verwaltete Steuerung 1463: Überwachen des physischen Zugriffs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Überwachen des physischen Zugriffs | Eindringalarme / Überwachungsgeräte

**ID:** NIST SP 800-53 R4 PE-6 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1464: Überwachen des physischen Zugriffs \| Eindringalarme/Überwachungsgeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Überwachen des physischen Zugriffs | Überwachen des physischen Zugriffs auf Informationssysteme

**ID:** NIST SP 800-53 R4 PE-6 (4) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1465: Überwachen des physischen Zugriffs \| Überwachen des physischen Zugriffs auf Informationssysteme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Datensätze von Besucherzugriffen

**ID:** NIST SP 800-53 R4 PE-8 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1466: Datensätze von Besucherzugriffen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Von Microsoft verwaltete Steuerung 1467: Datensätze von Besucherzugriffen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Datensätze von Besucherzugriffen | Automatische Verwaltung von Datensätzen / Überprüfen

**ID:** NIST SP 800-53 R4 PE-8 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1468: Datensätze von Besucherzugriffen \| Automatische Verwaltung/Überprüfung von Datensätzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Stromversorgungsgeräte und -verkabelung

**ID:** NIST SP 800-53 R4 PE-9 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1469: Stromversorgungsgeräte und -verkabelung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Notabschaltung

**ID:** NIST SP 800-53 R4 PE-10 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1470: Notabschaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Von Microsoft verwaltete Steuerung 1471: Notabschaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Von Microsoft verwaltete Steuerung 1472: Notabschaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Notstromversorgung

**ID:** NIST SP 800-53 R4 PE-11 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1473: Notstromversorgung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Notstromversorgung | Langfristige alternative Stromversorgung – minimale Betriebsfähigkeit

**ID:** NIST SP 800-53 R4 PE-11 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1474: Notstromversorgung \| Langfristige alternative Stromversorgung – minimale Betriebsfähigkeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Notbeleuchtung

**ID:** NIST SP 800-53 R4 PE-12 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1475: Notbeleuchtung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Brandschutz

**ID:** NIST SP 800-53 R4 PE-13 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1476: Brandschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Brandschutz | Erkennungsgeräte / -systeme

**ID:** NIST SP 800-53 R4 PE-13 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1477: Brandschutz \| Erkennungsgeräte/-systeme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Brandschutz | Bekämpfungsgeräte / -systeme

**ID:** NIST SP 800-53 R4 PE-13 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1478: Brandschutz \| Bekämpfungsgeräte/-systeme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Brandschutz | Automatische Brandbekämpfung

**ID:** NIST SP 800-53 R4 PE-13 (3) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1479: Brandschutz \| Automatische Brandbekämpfung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Temperatur- und Luftfeuchtigkeitssteuerung

**ID:** NIST SP 800-53 R4 PE-14 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1480: Temperatur- und Luftfeuchtigkeitssteuerung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Von Microsoft verwaltete Steuerung 1481: Temperatur- und Luftfeuchtigkeitssteuerung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Temperatur- und Luftfeuchtigkeitssteuerung | Überwachung mit Alarmen / Benachrichtigungen

**ID:** NIST SP 800-53 R4 PE-14 (2) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1482: Temperatur- und Luftfeuchtigkeitssteuerung \| Überwachung mit Alarmen/Benachrichtigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Wasserschadenschutz

**ID:** NIST SP 800-53 R4 PE-15 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1483: Wasserschadenschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Wasserschadenschutz | Automatisierungsunterstützung

**ID:** NIST SP 800-53 R4 PE-15 (1) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1484: Wasserschadenschutz \| Automatisierungsunterstützung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Lieferung und Entfernung

**ID:** NIST SP 800-53 R4 PE-16 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1485: Lieferung und Entfernung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Alternativer Arbeitsort

**ID:** NIST SP 800-53 R4 PE-17 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1486: alternativer Arbeitsort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Von Microsoft verwaltete Steuerung 1487: alternativer Arbeitsort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Von Microsoft verwaltete Steuerung 1488: alternativer Arbeitsort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Position der Informationssystemkomponenten

**ID:** NIST SP 800-53 R4 PE-18 **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1489: Position der Informationssystemkomponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |Microsoft implementiert diese Steuerung zum physischen Schutz und Schutz der Umgebung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Planung

### <a name="security-planning-policy-and-procedures"></a>Sicherheitsplanungsrichtlinie und Verfahren

**ID:** NIST SP 800-53 R4 PL-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1490: Sicherheitsplanungsrichtlinien und -verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Von Microsoft verwaltete Steuerung 1491: Sicherheitsplanungsrichtlinien und -verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Systemsicherheitsplan

**ID:** NIST SP 800-53 R4 PL-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1492: Systemsicherheitsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Von Microsoft verwaltete Steuerung 1493: Systemsicherheitsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Von Microsoft verwaltete Steuerung 1494: Systemsicherheitsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Von Microsoft verwaltete Steuerung 1495: Systemsicherheitsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Von Microsoft verwaltete Steuerung 1496: Systemsicherheitsplan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Systemsicherheitsplan | Planen/Koordieren mit anderen Organisationsentitäten

**ID:** NIST SP 800-53 R4 PL-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1497: Systemsicherheitsplan \| Planen/Koordinieren mit anderen Organisationsentitäten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Verhaltensregeln

**ID:** NIST SP 800-53 R4 PL-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1498: Verhaltensregeln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Von Microsoft verwaltete Steuerung 1499: Verhaltensregeln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Von Microsoft verwaltete Steuerung 1500: Verhaltensregeln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Von Microsoft verwaltete Steuerung 1501: Verhaltensregeln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Verhaltensregeln | Einschränkungen für soziale Medien und Networking

**ID:** NIST SP 800-53 R4 PL-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1502: Verhaltensregeln \| Einschränkungen für soziale Medien und Networking](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Informationssicherheitsarchitektur

**ID:** NIST SP 800-53 R4 PL-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1503: Informationssicherheitsarchitektur](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Von Microsoft verwaltete Steuerung 1504: Informationssicherheitsarchitektur](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Von Microsoft verwaltete Steuerung 1505: Informationssicherheitsarchitektur](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |Microsoft implementiert diese Steuerung zur Planung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Personalsicherheit

### <a name="personnel-security-policy-and-procedures"></a>Personalsicherheitsrichtlinie und Verfahren

**ID:** NIST SP 800-53 R4 PS-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1506: Personalsicherheitsrichtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Von Microsoft verwaltete Steuerung 1507: Personalsicherheitsrichtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Risikobezeichnung für Position

**ID:** NIST SP 800-53 R4 PS-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1508: Risikobezeichnung für Position](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Von Microsoft verwaltete Steuerung 1509: Risikobezeichnung für Position](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Von Microsoft verwaltete Steuerung 1510: Risikobezeichnung für Position](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Personalscreening

**ID:** NIST SP 800-53 R4 PS-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1511: Personalscreening](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Von Microsoft verwaltete Steuerung 1512: Personalscreening](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Personalscreening | Informationen mit besonderen Schutzmaßnahmen

**ID:** NIST SP 800-53 R4 PS-3 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1513: Personalscreening \| Informationen mit besonderen Schutzmaßnahmen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Von Microsoft verwaltete Steuerung 1514: Personalscreening \| Informationen mit besonderen Schutzmaßnahmen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Beendigung des Mitarbeiterverhältnisses

**ID:** NIST SP 800-53 R4 PS-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1515: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Von Microsoft verwaltete Steuerung 1516: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Von Microsoft verwaltete Steuerung 1517: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Von Microsoft verwaltete Steuerung 1518: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Von Microsoft verwaltete Steuerung 1519: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Von Microsoft verwaltete Steuerung 1520: Beendigung des Mitarbeiterverhältnisses](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Beendigung des Mitarbeiterverhältnisses | Automatisierte Benachrichtigung

**ID:** NIST SP 800-53 R4 PS-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1521: Beendigung des Mitarbeiterverhältnisses \| Automatisierte Benachrichtigung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Personalübertragung

**ID:** NIST SP 800-53 R4 PS-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1522: Personalübertragung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Von Microsoft verwaltete Steuerung 1523: Personalübertragung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Von Microsoft verwaltete Steuerung 1524: Personalübertragung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Von Microsoft verwaltete Steuerung 1525: Personalübertragung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Zugriffsvereinbarungen

**ID:** NIST SP 800-53 R4 PS-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1526: Zugriffsvereinbarungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Von Microsoft verwaltete Steuerung 1527: Zugriffsvereinbarungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Von Microsoft verwaltete Steuerung 1528: Zugriffsvereinbarungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Personalsicherheit von Dritten

**ID:** NIST SP 800-53 R4 PS-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1529: Personalsicherheit von Dritten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Von Microsoft verwaltete Steuerung 1530: Personalsicherheit von Dritten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Von Microsoft verwaltete Steuerung 1531: Personalsicherheit von Dritten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Von Microsoft verwaltete Steuerung 1532: Personalsicherheit von Dritten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Von Microsoft verwaltete Steuerung 1533: Personalsicherheit von Dritten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Personalsanktionen

**ID:** NIST SP 800-53 R4 PS-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1534: Personalsanktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Von Microsoft verwaltete Steuerung 1535: Personalsanktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |Microsoft implementiert diese Steuerung zur Personalsicherheit |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Risikobewertung

### <a name="risk-assessment-policy-and-procedures"></a>Richtlinien und Verfahren zur Risikobewertung

**ID:** NIST SP 800-53 R4 RA-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1536: Richtlinien und Verfahren zur Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Von Microsoft verwaltete Steuerung 1537: Richtlinien und Verfahren zur Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Sicherheitskategorisierung

**ID:** NIST SP 800-53 R4 RA-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1538: Sicherheitskategorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Von Microsoft verwaltete Steuerung 1539: Sicherheitskategorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Von Microsoft verwaltete Steuerung 1540: Sicherheitskategorisierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Risikobewertung

**ID:** NIST SP 800-53 R4 RA-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1541: Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Von Microsoft verwaltete Steuerung 1542: Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Von Microsoft verwaltete Steuerung 1543: Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Von Microsoft verwaltete Steuerung 1544: Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Von Microsoft verwaltete Steuerung 1545: Risikobewertung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken

**ID:** NIST SP 800-53 R4 RA-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1546: Überprüfung auf Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Von Microsoft verwaltete Steuerung 1547: Überprüfung auf Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Von Microsoft verwaltete Steuerung 1548: Überprüfung auf Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Von Microsoft verwaltete Steuerung 1549: Überprüfung auf Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Von Microsoft verwaltete Steuerung 1550: Überprüfung auf Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Hiermit werden Server, die nicht der konfigurierten Baseline entsprechen, über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Überprüfungsergebnisse der Sicherheitsrisikobewertung und Empfehlungen zum Beheben von Sicherheitsrisiken in der Datenbank überwachen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Überwacht Sicherheitsrisiken, die von der Lösung zur Sicherheitsrisikobewertung erkannt werden, und virtuelle Computer ohne eine Lösung zur Sicherheitsrisikobewertung im Azure Security Center in Form von Empfehlungen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Überprüfung auf Sicherheitsrisiken | Update-Tool-Funktion

**ID:** NIST SP 800-53 R4 RA-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1551: Überprüfung auf Sicherheitsrisiken \| Update von Toolfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Überprüfung auf Sicherheitsrisiken | Aktualisieren nach der Häufigkeit / Vor neuer Überprüfung / Bei Identifikation

**ID:** NIST SP 800-53 R4 RA-5 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1552: Überprüfung auf Sicherheitsrisiken \| Aktualisieren nach Häufigkeit, vor neuer Überprüfung, bei Identifikation](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Überprüfung auf Sicherheitsrisiken | Umfang / Abdeckungstiefe

**ID:** NIST SP 800-53 R4 RA-5 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1553: Überprüfung auf Sicherheitsrisiken \| Umfang/Abdeckungstiefe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Überprüfung auf Sicherheitsrisiken | Erkennbare Informationen

**ID:** NIST SP 800-53 R4 RA-5 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1554: Überprüfung auf Sicherheitsrisiken \| Erkennbare Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Überprüfung auf Sicherheitsrisiken | Privilegierter Zugriff

**ID:** NIST SP 800-53 R4 RA-5 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1555: Überprüfung auf Sicherheitsrisiken \| Privilegierter Zugriff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Überprüfung auf Sicherheitsrisiken | Automatisierte Trendanalysen

**ID:** NIST SP 800-53 R4 RA-5 (6) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1556: Überprüfung auf Sicherheitsrisiken \| Automatisierte Trendanalysen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Überprüfung auf Sicherheitsrisiken | Auswerten der historischen Überwachungsprotokolle

**ID:** NIST SP 800-53 R4 RA-5 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1557: Überprüfung auf Sicherheitsrisiken \| Auswerten der historischen Überwachungsprotokolle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Überprüfung auf Sicherheitsrisiken | Korrelieren von Überprüfungsinformationen

**ID:** NIST SP 800-53 R4 RA-5 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1558: Überprüfung auf Sicherheitsrisiken \| Korrelieren von Überprüfungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |Microsoft implementiert diese Steuerung zur Risikobewertung |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>System- und Diensterwerb

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Richtlinien und Verfahren beim System- und Diensterwerb

**ID:** NIST SP 800-53 R4 SA-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1559: Richtlinien und Verfahren beim System- und Diensterwerb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Von Microsoft verwaltete Steuerung 1560: Richtlinien und Verfahren beim System- und Diensterwerb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Zuordnung von Ressourcen

**ID:** NIST SP 800-53 R4 SA-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1561: Zuordnung von Ressourcen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Von Microsoft verwaltete Steuerung 1562: Zuordnung von Ressourcen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Von Microsoft verwaltete Steuerung 1563: Zuordnung von Ressourcen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Lebenszyklus der Systementwicklung

**ID:** NIST SP 800-53 R4 SA-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1564: Lebenszyklus der Systementwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Von Microsoft verwaltete Steuerung 1565: Lebenszyklus der Systementwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Von Microsoft verwaltete Steuerung 1566: Lebenszyklus der Systementwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Von Microsoft verwaltete Steuerung 1567: Lebenszyklus der Systementwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Erwerbsverfahren

**ID:** NIST SP 800-53 R4 SA-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1568: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Von Microsoft verwaltete Steuerung 1569: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Von Microsoft verwaltete Steuerung 1570: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Von Microsoft verwaltete Steuerung 1571: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Von Microsoft verwaltete Steuerung 1572: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Von Microsoft verwaltete Steuerung 1573: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Von Microsoft verwaltete Steuerung 1574: Erwerbsverfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Erwerbsverfahren | Funktionale Eigenschaften von Sicherheitskontrollen

**ID:** NIST SP 800-53 R4 SA-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1575: Erwerbsverfahren \| Funktionale Eigenschaften von Sicherheitskontrollen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Erwerbsverfahren | Design-/Implementierungsinformationen für Sicherheitskontrollen

**ID:** NIST SP 800-53 R4 SA-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1576: Erwerbsverfahren \| Design-/Implementierungsinformationen für Sicherheitskontrollen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Erwerbsverfahren | Plan zur kontinuierlichen Überwachung

**ID:** NIST SP 800-53 R4 SA-4 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1577: Erwerbsverfahren \| Plan zur kontinuierlichen Überwachung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Erwerbsverfahren | Funktionen/Ports/Protokolle/verwendete Dienste

**ID:** NIST SP 800-53 R4 SA-4 (9) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1578: Erwerbsverfahren \| Funktionen/Ports/Protokolle/verwendete Dienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Erwerbsverfahren | Verwenden genehmigter Piv-Produkte

**ID:** NIST SP 800-53 R4 SA-4 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1579: Erwerbsverfahren \| Verwenden genehmigter PIV-Produkte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Dokumentation des Informationssystems

**ID:** NIST SP 800-53 R4 SA-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1580: Dokumentation des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Von Microsoft verwaltete Steuerung 1581: Dokumentation des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Von Microsoft verwaltete Steuerung 1582: Dokumentation des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Von Microsoft verwaltete Steuerung 1583: Dokumentation des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Von Microsoft verwaltete Steuerung 1584: Dokumentation des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Prinzipien der Sicherheitsentwicklung

**ID:** NIST SP 800-53 R4 SA-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1585: Prinzipien der Sicherheitsentwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Externe Informationssystemdienste

**ID:** NIST SP 800-53 R4 SA-9 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1586: externe Informationssystemdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Von Microsoft verwaltete Steuerung 1587: externe Informationssystemdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Von Microsoft verwaltete Steuerung 1588: externe Informationssystemdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>Externe Informationssystemdienste | Risikobewertungen/Organisationsgenehmigungen

**ID:** NIST SP 800-53 R4 SA-9 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1589: externe Informationssystemdienste \| Risikobewertungen/Organisationsgenehmigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Von Microsoft verwaltete Steuerung 1590: externe Informationssystemdienste \| Risikobewertungen/Organisationsgenehmigungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>Externe Informationssystemdienste | Identifikation von Funktionen/Ports/Protokollen/Diensten

**ID:** NIST SP 800-53 R4 SA-9 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1591: externe Informationssystemdienste \| Identifikation von Funktionen/Ports/Protokollen/Diensten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>Externe Informationssystemdienste | Einheitliche Interessen von Consumern und Anbietern

**ID:** NIST SP 800-53 R4 SA-9 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1592: externe Informationssystemdienste \| Einheitliche Interessen von Consumern und Anbietern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>Externe Informationssystemdienste | Verarbeitung, Speicher und Dienststandort

**ID:** NIST SP 800-53 R4 SA-9 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1593: externe Informationssystemdienste \| Verarbeitungs-, Speicher- und Dienststandort](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Konfigurationsverwaltung durch Entwickler

**ID:** NIST SP 800-53 R4 SA-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1594: Konfigurationsverwaltung durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Von Microsoft verwaltete Steuerung 1595: Konfigurationsverwaltung durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Von Microsoft verwaltete Steuerung 1596: Konfigurationsverwaltung durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Von Microsoft verwaltete Steuerung 1597: Konfigurationsverwaltung durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Von Microsoft verwaltete Steuerung 1598: Konfigurationsverwaltung durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Konfigurationsverwaltung durch Entwickler | Integritätsprüfung für Software/Firmware

**ID:** NIST SP 800-53 R4 SA-10 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1599: Konfigurationsverwaltung durch Entwickler \| Integritätsprüfung für Software/Firmware](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Sicherheitstests und -bewertungen durch Entwickler

**ID:** NIST SP 800-53 R4 SA-11 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1600: Sicherheitstests und -bewertungen durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Von Microsoft verwaltete Steuerung 1601: Sicherheitstests und -bewertungen durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Von Microsoft verwaltete Steuerung 1602: Sicherheitstests und -bewertungen durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Von Microsoft verwaltete Steuerung 1603: Sicherheitstests und -bewertungen durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Von Microsoft verwaltete Steuerung 1604: Sicherheitstests und -bewertungen durch Entwickler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Sicherheitstests und -bewertungen durch Entwickler | Analyse von statischem Code

**ID:** NIST SP 800-53 R4 SA-11 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1605: Sicherheitstests und -bewertungen durch Entwickler \| Analyse von statischem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Sicherheitstests und -bewertungen durch Entwickler | Analyse von Bedrohungen und Sicherheitsrisiken

**ID:** NIST SP 800-53 R4 SA-11 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1606: Sicherheitstests und -bewertungen durch Entwickler \| Analyse von Bedrohungen und Sicherheitsrisiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Sicherheitstests und -bewertungen durch Entwickler | Analyse von dynamischem Code

**ID:** NIST SP 800-53 R4 SA-11 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1607: Sicherheitstests und -bewertungen durch Entwickler \| Analyse von dynamischem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Schutz der Lieferkette

**ID:** NIST SP 800-53 R4 SA-12 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1608: Schutz der Lieferkette](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Verfahren, Standards und Tools der Entwicklung

**ID:** NIST SP 800-53 R4 SA-15 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1609: Verfahren, Standards und Tools für die Entwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Von Microsoft verwaltete Steuerung 1610: Verfahren, Standards und Tools für die Entwicklung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Von Entwicklern angebotene Trainings

**ID:** NIST SP 800-53 R4 SA-16 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1611: von Entwicklern angebotene Schulungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Sicherheitsarchitektur und Design von Entwicklern

**ID:** NIST SP 800-53 R4 SA-17 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1612: Sicherheitsarchitektur und Design von Entwicklern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Von Microsoft verwaltete Steuerung 1613: Sicherheitsarchitektur und Design von Entwicklern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Von Microsoft verwaltete Steuerung 1614: Sicherheitsarchitektur und Design von Entwicklern](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |Microsoft implementiert diese Steuerung zum System- und Diensterwerb |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>System- und Kommunikationsschutz

### <a name="system-and-communications-protection-policy-and-procedures"></a>System- und Kommunikationsschutz – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 SC-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1615: System- und Kommunikationsschutz – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Von Microsoft verwaltete Steuerung 1616: System- und Kommunikationsschutz – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Anwendungspartitionierung

**ID:** NIST SP 800-53 R4 SC-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1617: Anwendungspartitionierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Isolation von Sicherheitsfunktionen

**ID:** NIST SP 800-53 R4 SC-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1618: Isolation von Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Informationen in gemeinsam genutzten Ressourcen

**ID:** NIST SP 800-53 R4 SC-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1619: Informationen in gemeinsam genutzten Ressourcen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Schutz vor Denial-of-Service-Angriffen

**ID:** NIST SP 800-53 R4 SC-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection Standard muss aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |DDoS Protection Standard sollte für alle virtuellen Netzwerke mit einem Subnetz aktiviert werden, das Teil eines Application Gateways mit einer öffentlichen IP-Adresse ist. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1620: Schutz vor Denial-of-Service-Angriffen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Ressourcenverfügbarkeit

**ID:** NIST SP 800-53 R4 SC-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1621: Ressourcenverfügbarkeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Schutz von Grenzen

**ID:** NIST SP 800-53 R4 SC-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auf virtuelle Computer mit Internetzugang müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analysiert die Datenverkehrsmuster von virtuellen Computern mit Internetzugriff und stellt Empfehlungen zu NSG-Regeln (Netzwerksicherheitsgruppe) bereit, um die potenzielle Angriffsfläche zu verringern. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Alle Netzwerkports müssen auf Netzwerksicherheitsgruppen eingeschränkt werden, die Ihrer VM zugeordnet sind.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center hat erkannt, dass die Regeln für eingehenden Datenverkehr einiger Ihrer Netzwerksicherheitsgruppen zu freizügig sind. Regeln für eingehenden Datenverkehr dürfen keinen Zugriff über die Bereiche „Beliebig“ oder „Internet“ zulassen. Dies kann es Angreifern ermöglichen, sich Zugang zu Ihren Ressourcen zu verschaffen. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1622: Schutz von Grenzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Von Microsoft verwaltete Steuerung 1623: Schutz von Grenzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Von Microsoft verwaltete Steuerung 1624: Schutz von Grenzen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[Netzwerkzugriff auf Speicherkonten einschränken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Der Netzwerkzugriff auf Speicherkonten sollte eingeschränkt werden. Konfigurieren Sie Netzwerkregeln, sodass nur Anwendungen aus zulässigen Netzwerken auf ein Speicherkonto zugreifen können. Um Verbindungen von bestimmten Internetclients oder lokalen Clients zuzulassen, kann Zugriff für Datenverkehr aus bestimmten virtuellen Azure-Netzwerken oder an IP-Adressbereiche im öffentlichen Internet gewährt werden. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Schutz von Grenzen | Zugriffspunkte

**ID:** NIST SP 800-53 R4 SC-7 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltungsports von virtuellen Computern sollten mit Just-In-Time-Netzwerkzugriffssteuerung geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Hiermit wird der mögliche Just-In-Time-Netzwerkzugriff über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1625: Schutz von Grenzen \| Zugriffspunkte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Schutz von Grenzen | Externe Telekommunikationsdienste

**ID:** NIST SP 800-53 R4 SC-7 (4) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltungsports von virtuellen Computern sollten mit Just-In-Time-Netzwerkzugriffssteuerung geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Hiermit wird der mögliche Just-In-Time-Netzwerkzugriff über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1626: Schutz von Grenzen \| Externe Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Von Microsoft verwaltete Steuerung 1627: Schutz von Grenzen \| Externe Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Von Microsoft verwaltete Steuerung 1628: Schutz von Grenzen \| Externe Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Von Microsoft verwaltete Steuerung 1629: Schutz von Grenzen \| Externe Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Von Microsoft verwaltete Steuerung 1630: Schutz von Grenzen \| Externe Telekommunikationsdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Schutz von Grenzen | Richtlinie zur standardmäßigen Verweigerung von Programmen und ausnahmebasierten Zulassung

**ID:** NIST SP 800-53 R4 SC-7 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1631: Schutz von Grenzen \| Richtlinie zur standardmäßigen Verweigerung von Programmen mit ausnahmebasierter Zulassung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Schutz von Grenzen | Unterbinden von getrenntem Tunneln für Remotegeräte

**ID:** NIST SP 800-53 R4 SC-7 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1632: Schutz von Grenzen \| Unterbinden von getrenntem Tunneln für Remotegeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Schutz von Grenzen | Weiterleiten von Datenverkehr an authentifizierte Proxyserver

**ID:** NIST SP 800-53 R4 SC-7 (8) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1633: Schutz von Grenzen \| Weiterleiten von Datenverkehr an authentifizierte Proxyserver](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Schutz von Grenzen | Vermeidung von nicht autorisierter Exfiltration

**ID:** NIST SP 800-53 R4 SC-7 (10) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1634: Schutz von Grenzen \| Vermeidung von nicht autorisierter Exfiltration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Schutz von Grenzen | Hostbasierter Schutz

**ID:** NIST SP 800-53 R4 SC-7 (12) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1635: Schutz von Grenzen \| Hostbasierter Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Schutz von Grenzen | Isolation von Sicherheitstools, -mechanismen und unterstützenden Komponenten

**ID:** NIST SP 800-53 R4 SC-7 (13) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1636: Schutz von Grenzen \| Isolation von Sicherheitstools, -mechanismen und unterstützenden Komponenten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Schutz von Grenzen | Sicherer Ausfall

**ID:** NIST SP 800-53 R4 SC-7 (18) **Besitzer:** Microsoft

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1637: Schutz von Grenzen \| Ausfallschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Schutz von Grenzen | Dynamische Isolation und Trennung

**ID:** NIST SP 800-53 R4 SC-7 (20) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1638: Schutz von Grenzen \| Dynamische Isolation und Trennung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Schutz von Grenzen | Isolation von Komponenten des Informationssystems

**ID:** NIST SP 800-53 R4 SC-7 (21) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1639: Schutz von Grenzen \| Isolation von Komponenten des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>Vertraulichkeit und Integrität von übertragenen Informationen

**ID:** NIST SP 800-53 R4 SC-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1640: Vertraulichkeit und Integrität von übertragenen Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Vertraulichkeit und Integrität von übertragenen Informationen | Kryptografischer oder alternativer physischer Schutz

**ID:** NIST SP 800-53 R4 SC-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auf API-Apps sollte nur über HTTPS zugegriffen werden können](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Durch die Verwendung von HTTPS wird die Server-/Dienstauthentifizierung sichergestellt, und Daten werden bei der Übertragung vor Abhörangriffen auf der Vermittlungsschicht geschützt. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Windows-Webserver überwachen, die keine sicheren Kommunikationsprotokolle verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Erfordert, dass die erforderlichen Komponenten im Bereich der Richtlinienzuweisung bereitgestellt werden. Einzelheiten dazu finden Sie unter [https://aka.ms/gcpol](https://aka.ms/gcpol). Computer werden als nicht konform eingestuft, wenn der Registrierungsschlüssel „HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols“ Protokolle enthält, die weniger sicher sind als die Auswahl im Richtlinienparameter. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |
|[Zugriff auf Funktions-App nur über HTTPS gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Durch die Verwendung von HTTPS wird die Server-/Dienstauthentifizierung sichergestellt, und Daten werden bei der Übertragung vor Abhörangriffen auf der Vermittlungsschicht geschützt. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1641: Vertraulichkeit und Integrität von übertragenen Informationen \| Kryptografischer oder alternativer physischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Für Ihren Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Aktivieren der Überprüfung nur für Verbindungen über SSL mit Azure Cache for Redis Durch die Verwendung sicherer Verbindungen wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, wie z.B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Sichere Übertragung in Speicherkonten sollte aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Hiermit wird die Anforderung sicherer Übertragungen in Ihren Speicherkonto überwacht. Sichere Übertragung ist eine Option, die erzwingt, dass Ihr Storage-Konto nur Anforderungen von sicheren Verbindungen (HTTPS) akzeptiert. Durch die Verwendung von HTTPS wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, z. B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Zugriff auf Webanwendung nur über HTTPS gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Durch die Verwendung von HTTPS wird die Server-/Dienstauthentifizierung sichergestellt, und Daten werden bei der Übertragung vor Abhörangriffen auf der Vermittlungsschicht geschützt. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="network-disconnect"></a>Netzwerktrennung

**ID:** NIST SP 800-53 R4 SC-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1642: Netzwerktrennung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Einrichtung und Verwaltung von Kryptografieschlüsseln

**ID:** NIST SP 800-53 R4 SC-12 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1643: Einrichtung und Verwaltung von Kryptografieschlüsseln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Einrichtung und Verwaltung von Kryptografieschlüsseln | Verfügbarkeit

**ID:** NIST SP 800-53 R4 SC-12 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1644: Einrichtung und Verwaltung von Kryptografieschlüsseln \| Verfügbarkeit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Einrichtung und Verwaltung von Kryptografieschlüsseln | Symmetrische Schlüssel

**ID:** NIST SP 800-53 R4 SC-12 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1645: Einrichtung und Verwaltung von Kryptografieschlüsseln \| Symmetrische Schlüssel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Einrichtung und Verwaltung von Kryptografieschlüsseln | Asymmetrische Schlüssel

**ID:** NIST SP 800-53 R4 SC-12 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1646: Einrichtung und Verwaltung von Kryptografieschlüsseln \| Asymmetrische Schlüssel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Kryptografischer Schutz

**ID:** NIST SP 800-53 R4 SC-13 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1647: kryptografischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>Gemeinsame Computergeräte

**ID:** NIST SP 800-53 R4 SC-15 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1648: gemeinsame Computergeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Von Microsoft verwaltete Steuerung 1649: gemeinsame Computergeräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Public Key-Infrastrukturzertifikate

**ID:** NIST SP 800-53 R4 SC-17 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1650: Public Key-Infrastrukturzertifikate](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Mobiler Code

**ID:** NIST SP 800-53 R4 SC-18 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1651: mobiler Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Von Microsoft verwaltete Steuerung 1652: mobiler Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Von Microsoft verwaltete Steuerung 1653: mobiler Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Voice over IP

**ID:** NIST SP 800-53 R4 SC-19 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1654: Voice over IP](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Von Microsoft verwaltete Steuerung 1655: Voice over IP](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Sicherer Namens- und Adressauflösungsdienst (autoritative Quelle)

**ID:** NIST SP 800-53 R4 SC-20 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1656: sicherer Namens- und Adressauflösungsdienst (autoritative Quelle)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Von Microsoft verwaltete Steuerung 1657: sicherer Namens- und Adressauflösungsdienst (autoritative Quelle)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Sicherer Namens- und Adressauflösungsdienst (rekursiver oder Cachekonfliktlöser)

**ID:** NIST SP 800-53 R4 SC-21 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1658: sicherer Namens- und Adressauflösungsdienst (rekursiver oder Cachekonfliktlöser)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektur und Bereitstellung für den Namens- und Adressauflösungsdienst

**ID:** NIST SP 800-53 R4 SC-22 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1659: Architektur und Bereitstellung für den Namens- und Adressauflösungsdienst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Echtheit von Sitzungen

**ID:** NIST SP 800-53 R4 SC-23 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1660: Echtheit von Sitzungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Echtheit von Sitzungen | Invalidierung von Sitzungs-IDs bei der Abmeldung

**ID:** NIST SP 800-53 R4 SC-23 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1661: Echtheit von Sitzungen \| Invalidierung von Sitzungs-IDs bei der Abmeldung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Ausfall in bekanntem Zustand

**ID:** NIST SP 800-53 R4 SC-24 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1662: Ausfall in bekanntem Zustand](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Schutz von ruhenden Informationen

**ID:** NIST SP 800-53 R4 SC-28 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1663: Schutz von ruhenden Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Schutz von ruhenden Informationen | Kryptografischer Schutz

**ID:** NIST SP 800-53 R4 SC-28 (1) **Besitzer:** Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Hiermit werden VMs ohne aktivierte Datenträgerverschlüsselung über Azure Security Center als Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1664: Schutz von ruhenden Informationen \| Kryptografischer Schutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[Transparent Data Encryption für SQL-Datenbanken aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>Prozessisolation

**ID:** NIST SP 800-53 R4 SC-39 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1665: Prozessisolation](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |Microsoft implementiert diese Steuerung zum System- und Kommunikationsschutz |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>System- und Informationsintegrität

### <a name="system-and-information-integrity-policy-and-procedures"></a>Integrität von System und Informationen – Richtlinien und Verfahren

**ID:** NIST SP 800-53 R4 SI-1 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1666: Integrität von Systemen und Informationen – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Von Microsoft verwaltete Steuerung 1667: Integrität von Systemen und Informationen – Richtlinien und Verfahren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Fehlerbehebung

**ID:** NIST SP 800-53 R4 SI-2 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1668: Fehlerbehebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Von Microsoft verwaltete Steuerung 1669: Fehlerbehebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Von Microsoft verwaltete Steuerung 1670: Fehlerbehebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Von Microsoft verwaltete Steuerung 1671: Fehlerbehebung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[Systemupdates für VM-Skalierungsgruppen sollten installiert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Hiermit überprüfen Sie, ob Systemsicherheitsupdates und kritische Updates fehlen, durch deren Installation sichergestellt werden soll, dass Ihre Windows- und Linux-VM-Skalierungsgruppen sicher sind. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[Systemupdates sollten auf Ihren Computern installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Hiermit werden Server, die nicht der konfigurierten Baseline entsprechen, über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Überprüfungsergebnisse der Sicherheitsrisikobewertung und Empfehlungen zum Beheben von Sicherheitsrisiken in der Datenbank überwachen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Überwacht Sicherheitsrisiken, die von der Lösung zur Sicherheitsrisikobewertung erkannt werden, und virtuelle Computer ohne eine Lösung zur Sicherheitsrisikobewertung im Azure Security Center in Form von Empfehlungen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Fehlerbehebung | Zentrale Verwaltung

**ID:** NIST SP 800-53 R4 SI-2 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1672: Fehlerbehebung \| Zentrale Verwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Fehlerbehebung | Automatisierter Fehlerbehebungsstatus

**ID:** NIST SP 800-53 R4 SI-2 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1673: Fehlerbehebung \| Automatisierter Fehlerbehebungsstatus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Fehlerbehebung | Zeitraum bis zur Behebung von Fehlern / Benchmarks für Korrekturmaßnahmen

**ID:** NIST SP 800-53 R4 SI-2 (3) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1674: Fehlerbehebung \| Zeitraum bis zur Behebung von Fehlern/Benchmarks für Korrekturmaßnahmen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Von Microsoft verwaltete Steuerung 1675: Fehlerbehebung \| Zeitraum bis zur Behebung von Fehlern/Benchmarks für Korrekturmaßnahmen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Schutz vor schädlichem Code

**ID:** NIST SP 800-53 R4 SI-3 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Hiermit überprüfen Sie die Existenz und die Integrität einer Endpoint Protection-Lösung in Ihren VM-Skalierungsgruppen, um sie vor Bedrohungen und Sicherheitsrisiken zu schützen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1676: Schutz vor schädlichem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Von Microsoft verwaltete Steuerung 1677: Schutz vor schädlichem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Von Microsoft verwaltete Steuerung 1678: Schutz vor schädlichem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Von Microsoft verwaltete Steuerung 1679: Schutz vor schädlichem Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Hiermit werden Server ohne installierten Endpoint Protection-Agent über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Schutz vor schädlichem Code | Zentrale Verwaltung

**ID:** NIST SP 800-53 R4 SI-3 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Hiermit überprüfen Sie die Existenz und die Integrität einer Endpoint Protection-Lösung in Ihren VM-Skalierungsgruppen, um sie vor Bedrohungen und Sicherheitsrisiken zu schützen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1680: Schutz vor schädlichem Code \| Zentrale Verwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Hiermit werden Server ohne installierten Endpoint Protection-Agent über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Schutz vor schädlichem Code | Automatische Updates

**ID:** NIST SP 800-53 R4 SI-3 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1681: Schutz vor schädlichem Code \| Automatische Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Schutz vor schädlichem Code | Basierend auf der Erkennung nicht vorhandener Signaturen

**ID:** NIST SP 800-53 R4 SI-3 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1682: Schutz vor schädlichem Code \| Erkennung basierend auf nicht vorhandenen Signaturen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Überwachung des Informationssystems

**ID:** NIST SP 800-53 R4 SI-4 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der definierten Liste vorhanden und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) sich nicht in der definierten Liste befindet und der Agent nicht installiert ist. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, während Unterstützung hinzugefügt wird. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Log Analytics-Arbeitsbereich für VM überwachen – Berichtskonflikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Meldet VMs als nicht konform, wenn sie keine Protokolle an den in der Richtlinien-/Initiativenzuweisung angegebenen Log Analytics-Arbeitsbereich senden. |Überwachung |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Von Microsoft verwaltete Steuerung 1683: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Von Microsoft verwaltete Steuerung 1684: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Von Microsoft verwaltete Steuerung 1685: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Von Microsoft verwaltete Steuerung 1686: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Von Microsoft verwaltete Steuerung 1687: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Von Microsoft verwaltete Steuerung 1688: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Von Microsoft verwaltete Steuerung 1689: Überwachung des Informationssystems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Überwachung des Informationssystems | Systemweites Angriffserkennungssystem

**ID:** NIST SP 800-53 R4 SI-4 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1690: Überwachung des Informationssystems \| Systemweites Intrusion-Detection-System](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Überwachung des Informationssystems | Automatisierte Tools für die Echtzeitanalyse

**ID:** NIST SP 800-53 R4 SI-4 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1691: Überwachung des Informationssystems \| Automatisierte Tools für die Echtzeitanalyse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Überwachung des Informationssystems | Ein- und ausgehender Kommunikationsdatenverkehr

**ID:** NIST SP 800-53 R4 SI-4 (4) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1692: Überwachung des Informationssystems \| Ein- und ausgehender Kommunikationsdatenverkehr](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Überwachung des Informationssystems | Vom System generierte Warnungen

**ID:** NIST SP 800-53 R4 SI-4 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1693: Überwachung des Informationssystems \| Vom System generierte Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Überwachung des Informationssystems | Analysieren von Anomalien im Kommunikationsdatenverkehr

**ID:** NIST SP 800-53 R4 SI-4 (11) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1694: Überwachung des Informationssystems \| Analysieren von Anomalien im Kommunikationsdatenverkehr](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Überwachung des Informationssystems | Angriffserkennung in Drahtlosnetzwerken

**ID:** NIST SP 800-53 R4 SI-4 (14) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1695: Überwachung des Informationssystems \| Angriffserkennung in Drahtlosnetzwerken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Überwachung des Informationssystems | Korrelieren der Überwachungsinformationen

**ID:** NIST SP 800-53 R4 SI-4 (16) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1696: Überwachung des Informationssystems \| Korrelieren der Überwachungsinformationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Überwachung des Informationssystems | Analyse des Datenverkehrs / verdeckte Ausschleusung

**ID:** NIST SP 800-53 R4 SI-4 (18) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1697: Überwachung des Informationssystems \| Analyse des Datenverkehrs/verdeckte Exfiltration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Überwachung des Informationssystems | Personen mit erhöhtem Risiko

**ID:** NIST SP 800-53 R4 SI-4 (19) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1698: Überwachung des Informationssystems \| Personen mit erhöhtem Risiko](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Überwachung des Informationssystems | Privilegierte Benutzer

**ID:** NIST SP 800-53 R4 SI-4 (20) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1699: Überwachung des Informationssystems \| Privilegierte Benutzer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Überwachung des Informationssystems | Nicht autorisierte Netzwerkdienste

**ID:** NIST SP 800-53 R4 SI-4 (22) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1700: Überwachung des Informationssystems \| Nicht autorisierte Netzwerkdienste](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Überwachung des Informationssystems | Hostbasierte Geräte

**ID:** NIST SP 800-53 R4 SI-4 (23) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1701: Überwachung des Informationssystems \| Hostbasierte Geräte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Überwachung des Informationssystems | Anzeichen einer Kompromittierung

**ID:** NIST SP 800-53 R4 SI-4 (24) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1702: Überwachung des Informationssystems \| Anzeichen einer Kompromittierung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Sicherheitswarnungen, Empfehlungen und Direktiven

**ID:** NIST SP 800-53 R4 SI-5 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1703: Sicherheitswarnungen, Empfehlungen und Direktiven](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Von Microsoft verwaltete Steuerung 1704: Sicherheitswarnungen, Empfehlungen und Direktiven](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Von Microsoft verwaltete Steuerung 1705: Sicherheitswarnungen, Empfehlungen und Direktiven](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Von Microsoft verwaltete Steuerung 1706: Sicherheitswarnungen, Empfehlungen und Direktiven](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Sicherheitswarnungen, Empfehlungen und Direktiven | Automatisierte Warnungen und Empfehlungen

**ID:** NIST SP 800-53 R4 SI-5 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1707: Sicherheitswarnungen, Empfehlungen und Direktiven \| Automatisierte Warnungen und Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Überprüfung von Sicherheitsfunktionen

**ID:** NIST SP 800-53 R4 SI-6 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1708: Überprüfung von Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Von Microsoft verwaltete Steuerung 1709: Überprüfung von Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Von Microsoft verwaltete Steuerung 1710: Überprüfung von Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Von Microsoft verwaltete Steuerung 1711: Überprüfung von Sicherheitsfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Integrität von Software, Firmware und Informationen

**ID:** NIST SP 800-53 R4 SI-7 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1712: Integrität von Software, Firmware und Informationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integrität von Software, Firmware und Informationen | Integritätsüberprüfungen

**ID:** NIST SP 800-53 R4 SI-7 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1713: Integrität von Software, Firmware und Informationen \| Integritätsüberprüfungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integrität von Software, Firmware und Informationen | Automatisierte Benachrichtigungen bei Integritätsverletzungen

**ID:** NIST SP 800-53 R4 SI-7 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1714: Integrität von Software, Firmware und Informationen \| Automatisierte Benachrichtigungen bei Integritätsverletzungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integrität von Software, Firmware und Informationen | Automatisierte Reaktion bei Integritätsverletzungen

**ID:** NIST SP 800-53 R4 SI-7 (5) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1715: Integrität von Software, Firmware und Informationen \| Automatisierte Reaktion bei Integritätsverletzungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integrität von Software, Firmware und Informationen | Integration von Erkennung und Reaktion

**ID:** NIST SP 800-53 R4 SI-7 (7) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1716: Integrität von Software, Firmware und Informationen \| Integration von Erkennung und Reaktion](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integrität von Software, Firmware und Informationen | Binärcode oder ausführbarer Maschinencode

**ID:** NIST SP 800-53 R4 SI-7 (14) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1717: Integrität von Software, Firmware und Informationen \| Binärcode oder ausführbarer Maschinencode](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Von Microsoft verwaltete Steuerung 1718: Integrität von Software, Firmware und Informationen \| Binärcode oder ausführbarer Maschinencode](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>Spamschutz

**ID:** NIST SP 800-53 R4 SI-8 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1719: Spamschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Von Microsoft verwaltete Steuerung 1720: Spamschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>Spamschutz | Zentrale Verwaltung

**ID:** NIST SP 800-53 R4 SI-8 (1) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1721: Spamschutz \| Zentrale Verwaltung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>Spamschutz | Automatische Updates

**ID:** NIST SP 800-53 R4 SI-8 (2) **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1722: Spamschutz \| Automatische Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Überprüfen von Informationseingaben

**ID:** NIST SP 800-53 R4 SI-10 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1723: Überprüfen von Informationseingaben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Fehlerbehandlung

**ID:** NIST SP 800-53 R4 SI-11 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1724: Fehlerbehandlung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Von Microsoft verwaltete Steuerung 1725: Fehlerbehandlung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Informationsverarbeitung und -aufbewahrung

**ID:** NIST SP 800-53 R4 SI-12 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1726: Informationsverarbeitung und -aufbewahrung](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Arbeitsspeicherschutz

**ID:** NIST SP 800-53 R4 SI-16 **Besitzer:** Shared

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Von Microsoft verwaltete Steuerung 1727: Arbeitsspeicherschutz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |Microsoft implementiert diese Steuerung für die Integrität von Systemen und Informationen |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel über Azure Policy:

- Übersicht über die [Einhaltung gesetzlicher Bestimmungen](../concepts/regulatory-compliance.md).
- Weitere Informationen finden Sie unter [Struktur der Initiativendefinition](../concepts/initiative-definition-structure.md).
- Sehen Sie sich weitere Beispiele unter [Azure Policy-Beispiele](./index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
