---
title: Details der Einhaltung gesetzlicher Bestimmungen für CIS Microsoft Azure Foundations Benchmark
description: Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß CIS Microsoft Azure Foundations Benchmark. Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 11/20/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 2ed1a40ba7589d25864cd5bdd90526635f189d6d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991314"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß CIS Microsoft Azure Foundations Benchmark

Der folgende Artikel enthält Details dazu, wie die integrierte Azure Policy-Initiative zur Einhaltung gesetzlicher Bestimmungen den **Compliancebereichen** und **Steuerungen** in CIS Microsoft Azure Foundations Benchmark entspricht.
Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/). Grundlegendes zum _Besitzer_ finden Sie unter [Azure Policy-Richtliniendefinition](../concepts/definition-structure.md#type) und [Gemeinsame Verantwortung in der Cloud](../../../security/fundamentals/shared-responsibility.md).

Die folgenden Zuordnungen gelten für die Steuerelemente für **CIS Microsoft Azure Foundations Benchmark**. Über den rechten Navigationsbereich können Sie direkt zu einem bestimmten **Steuerungsbereich** springen. Viele der Steuerungen werden über die Definition einer [Azure Policy](../overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiativendefinition öffnen Sie **Policy** im Azure-Portal und wählen dann die Seite **Definitionen** aus.
Suchen Sie anschließend die integrierte Initiativendefinition zur Einhaltung der gesetzlichen Bestimmungen gemäß **CIS Microsoft Azure Foundations Benchmark 1.1.0**, und wählen Sie sie aus.

Diese integrierte Initiative wird als Teil des [Blaupausenbeispiels für CIS Microsoft Azure Foundations Benchmark](../../blueprints/samples/cis-azure-1-1-0.md) bereitgestellt.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../overview.md)-Definitionen zugeordnet.
> Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtliniendefinitionen selbst und gewährleistet nicht die vollständige Compliance mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Compliancebereichen, Steuerungen und Azure Policy-Definitionen für diesen Konformitätsstandard können sich im Lauf der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0_audit.json).

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Sicherstellen, dass die mehrstufige Authentifizierung für alle privilegierten Benutzer aktiviert ist

**ID:** CIS Azure 1.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für Konten mit Schreibberechtigungen für Ihr Abonnement muss MFA aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Schreibrechten aktiviert sein, um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Besitzerberechtigungen aktiviert werden, um eine Sicherheitsverletzung für Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Sicherstellen, dass die mehrstufige Authentifizierung für alle nicht privilegierten Benutzer aktiviert ist

**ID:** CIS Azure 1.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Leserechten aktiviert sein, um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Sicherstellen, dass keine Gastbenutzer vorhanden sind

**ID:** CIS Azure 1.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externe Konten mit Besitzerberechtigungen müssen aus Ihrem Abonnement entfernt werden, um einen nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externe Konten mit Leserechten müssen aus Ihrem Abonnement entfernt werden, um nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externe Konten mit Schreibrechten müssen aus Ihrem Abonnement entfernt werden, um nicht überwachten Zugriff zu verhindern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Sicherstellen, dass keine benutzerdefinierten Abonnementbesitzerrollen erstellt werden

**ID:** CIS Azure 1.23 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Mit dieser Richtlinie wird sichergestellt, dass keine benutzerdefinierten Abonnementbesitzerrollen vorhanden sind. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Sicherstellen, dass „Automatische Bereitstellung des Überwachungs-Agents“ auf „Ein“ festgelegt ist

**ID:** CIS Azure 2.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für Ihr Abonnement muss die automatische Bereitstellung des Log Analytics-Überwachungs-Agents aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Hiermit wird die automatische Bereitstellung des Log Analytics-Überwachungs-Agents aktiviert, um Sicherheitsdaten zu erfassen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Systemupdates überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemupdates sollten auf Ihren Computern installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Betriebssystem-Sicherheitsrisiken überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Hiermit werden Server, die nicht der konfigurierten Baseline entsprechen, über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Endpoint Protection überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Hiermit werden Server ohne installierten Endpoint Protection-Agent über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Datenträgerverschlüsselung überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.6 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Hiermit werden VMs ohne aktivierte Datenträgerverschlüsselung über Azure Security Center als Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Netzwerksicherheitsgruppen überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.7 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auf virtuelle Computer mit Internetzugang müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analysiert die Datenverkehrsmuster von virtuellen Computern mit Internetzugriff und stellt Empfehlungen zu NSG-Regeln (Netzwerksicherheitsgruppe) bereit, um die potenzielle Angriffsfläche zu verringern. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Next Generation Firewall-Überwachung aktivieren“ nicht deaktiviert ist

**ID:** CIS Azure 2.9 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuelle Computer mit Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Schützen Sie Ihre virtuellen Computer vor potenziellen Bedrohungen, indem Sie den Zugriff darauf mithilfe von Netzwerksicherheitsgruppen (NSGs) einschränken. Weitere Informationen zum Steuern des Datenverkehrs mit NSGs finden Sie unter [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc). |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Subnetze sollten einer Netzwerksicherheitsgruppe zugeordnet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Schützen Sie Ihr Subnetz vor potenziellen Bedrohungen, indem Sie den Zugriff auf das Subnetz mit einer Netzwerksicherheitsgruppe (NSG) einschränken. NSGs enthalten eine Liste der ACL-Regeln (Access Control List), die den Netzwerkdatenverkehr an Ihr Subnetz zulassen oder verweigern. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Sicherheitsrisikobewertung überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.10 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Überwacht Sicherheitsrisiken, die von der Lösung zur Sicherheitsrisikobewertung erkannt werden, und virtuelle Computer ohne eine Lösung zur Sicherheitsrisikobewertung im Azure Security Center in Form von Empfehlungen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „JIT-Netzwerkzugriff überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.12 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verwaltungsports von virtuellen Computern sollten mit Just-In-Time-Netzwerkzugriffssteuerung geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Hiermit wird der mögliche Just-In-Time-Netzwerkzugriff über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „Monitor Adaptive Application Whitelisting“ (Whitelist für adaptive Anwendungssteuerung überwachen) nicht deaktiviert ist

**ID:** CIS Azure 2.13 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Hiermit wird die Anwendungssteuerung aktiviert, um die Liste der als sicher bekannten Anwendungen zu definieren, die auf Ihren Computern ausgeführt werden. Außerdem werden Sie bei Ausführung anderer Anwendungen benachrichtigt. Dies verstärkt den Schutz Ihrer Computer vor Schadsoftware. Um die Konfiguration und Wartung Ihrer Regeln zu vereinfachen, nutzt Security Center Machine Learning, um die auf den einzelnen Computern ausgeführten Anwendungen zu analysieren und eine Liste der als sicher bekannten Anwendungen vorzuschlagen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „SQL-Überwachung überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.14 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Überwachung in SQL Server muss aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Die Überwachung für Ihre SQL Server-Instanz sollte aktiviert werden, um Datenbankaktivitäten in allen Datenbanken auf dem Server, außer in Synapse, nachzuverfolgen und in einem Überwachungsprotokoll zu speichern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Sicherstellen, dass die ASC-Standardrichtlinieneinstellung „SQL-Verschlüsselung überwachen“ nicht deaktiviert ist

**ID:** CIS Azure 2.15 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption für SQL-Datenbanken aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Sicherstellen, dass „E-Mail-Adressen für Sicherheitskontakt“ festgelegt ist

**ID:** CIS Azure 2.16 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für Ihr Abonnement muss eine E-Mail-Adresse als Sicherheitskontakt angegeben sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Hiermit wird eine E-Mail-Adresse angegeben, an die Benachrichtigungen gesendet werden, wenn Azure Security Center gefährdete Ressourcen erkennt. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>Sicherstellen, dass für den Sicherheitskontakt die „Telefonnummer“ festgelegt ist

**ID:** CIS Azure 2.17 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für Ihr Abonnement muss eine Telefonnummer für den Sicherheitskontakt angegeben werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |Hiermit wird eine Telefonnummer für den Erhalt von Benachrichtigungen angegeben, wenn Azure Security Center gefährdete Ressourcen erkennt. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Sicherstellen, dass „E-Mail-Benachrichtigung für Warnungen mit hohem Schweregrad senden“ auf „Ein“ festgelegt ist

**ID:** CIS Azure 2.18 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[E-Mail-Benachrichtigung zu Warnungen mit hohem Schweregrad muss aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Hiermit werden E-Mail-Sicherheitswarnungen an den Sicherheitskontakt aktiviert, damit dieser E-Mails zu Sicherheitswarnungen von Microsoft erhält. So wird sichergestellt, dass die richtigen Personen über potenzielle Sicherheitsprobleme informiert werden und die Risiken entschärfen können. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Sicherstellen, dass „E-Mails auch an Abonnementbesitzer senden“ auf „Ein“ festgelegt ist

**ID:** CIS Azure 2.19 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[E-Mail-Benachrichtigung des Abonnementbesitzers bei Warnungen mit hohem Schweregrad muss aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Hiermit werden E-Mail-Sicherheitswarnungen an Abonnementbesitzer aktiviert, damit diese E-Mails zu Sicherheitswarnungen von Microsoft erhalten. So wird sichergestellt, dass Abonnementbesitzer über potenzielle Sicherheitsprobleme informiert werden und das Risiko zeitnah entschärfen können. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Speicherkonten

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Sicherstellen, dass „Sichere Übertragung erforderlich“ aktiviert ist

**ID:** CIS Azure 3.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sichere Übertragung in Speicherkonten sollte aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Hiermit wird die Anforderung sicherer Übertragungen in Ihren Speicherkonto überwacht. Sichere Übertragung ist eine Option, die erzwingt, dass Ihr Storage-Konto nur Anforderungen von sicheren Verbindungen (HTTPS) akzeptiert. Durch die Verwendung von HTTPS wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, z. B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Sicherstellen, dass die Standard-Netzwerkzugriffsregel für Speicherkonten auf „Verweigern“ festgelegt ist

**ID:** CIS Azure 3.7 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Netzwerkzugriff auf Speicherkonten einschränken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Der Netzwerkzugriff auf Speicherkonten sollte eingeschränkt werden. Konfigurieren Sie Netzwerkregeln, sodass nur Anwendungen aus zulässigen Netzwerken auf ein Speicherkonto zugreifen können. Um Verbindungen von bestimmten Internetclients oder lokalen Clients zuzulassen, kann Zugriff für Datenverkehr aus bestimmten virtuellen Azure-Netzwerken oder an IP-Adressbereiche im öffentlichen Internet gewährt werden. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Sicherstellen, dass „Vertrauenswürdige Microsoft-Dienste“ für den Speicherkontozugriff aktiviert ist

**ID:** CIS Azure 3.8 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Speicherkonten sollten Zugriff von vertrauenswürdigen Microsoft-Diensten zulassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Einige Microsoft-Dienste, die mit Speicherkonten interagieren, agieren von Netzwerken aus, denen nicht mithilfe von Netzwerkregeln Zugriff gewährt werden kann. Lassen Sie für vertrauenswürdige Microsoft-Dienste die Umgehung der Netzwerkregeln zu, damit solche Dienste ordnungsgemäß funktionieren. Diese Dienste verwenden dann eine strenge Authentifizierung, um auf das Speicherkonto zuzugreifen. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Datenbankdienste

### <a name="ensure-that-auditing-is-set-to-on"></a>Sicherstellen, dass „Überwachung“ auf „Ein“ festgelegt ist

**ID:** CIS Azure 4.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Überwachung in SQL Server muss aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Die Überwachung für Ihre SQL Server-Instanz sollte aktiviert werden, um Datenbankaktivitäten in allen Datenbanken auf dem Server, außer in Synapse, nachzuverfolgen und in einem Überwachungsprotokoll zu speichern. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Sicherstellen, dass „AuditActionGroups“ in der Überwachungsrichtlinie für eine SQL Server-Instanz ordnungsgemäß festgelegt ist

**ID:** CIS Azure 4.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für SQL-Überwachungseinstellungen müssen Aktionsgruppen zum Erfassen kritischer Aktivitäten konfiguriert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Die AuditActionsAndGroups-Eigenschaft muss mindestens SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP enthalten, um eine umfassende Überwachungsprotokollierung sicherzustellen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Sicherstellen, dass die Aufbewahrungsdauer für die Überwachung auf über 90 Tage festgelegt ist

**ID:** CIS Azure 4.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server-Instanzen müssen mit einer Überwachungsaufbewahrungsdauer von mehr als 90 Tagen konfiguriert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Hiermit werden SQL Server-Instanzen überwacht, die mit einer Überwachungsaufbewahrungsdauer von weniger als 90 Tagen konfiguriert sind. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Sicherstellen, dass „Advanced Data Security“ für eine SQL Server-Instanz auf „Ein“ festgelegt ist

**ID:** CIS Azure 4.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security sollte für SQL Managed Instance aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Hiermit wird jede SQL Managed Instance-Instanz ohne Advanced Data Security überwacht. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Für Ihre SQL Server-Instanzen muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Überwachen von SQL-Servern ohne Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Sicherstellen, dass der Azure Active Directory-Administrator konfiguriert ist

**ID:** CIS Azure 4.8 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ein Azure Active Directory-Administrator sollte für SQL Server-Instanzen bereitgestellt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Hiermit wird die Bereitstellung eines Azure Active Directory-Administrators für Ihre SQL Server-Instanz überwacht, um die Azure AD-Authentifizierung zu aktivieren. Die Azure AD-Authentifizierung vereinfacht die Berechtigungsverwaltung und ermöglicht eine zentrale Identitätsverwaltung für Datenbankbenutzer und andere Microsoft-Dienste. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Sicherstellen, dass „Datenverschlüsselung“ für eine SQL-Datenbank auf „Ein“ festgelegt ist

**ID:** CIS Azure 4.9 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption für SQL-Datenbanken aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Sicherstellen, dass die TDE-Schutzvorrichtung der SQL Server-Instanz mit BYOK (also unter Verwendung eines eigenen Schlüssels) verschlüsselt ist

**ID:** CIS Azure 4.10 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[TDE-Schutz der verwalteten SQL-Instanz muss mit einem eigenen Schlüssel verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |TDE (Transparent Data Encryption) mit Unterstützung eines eigenen Schlüssels bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL Server-TDE-Schutz muss mit eigenem Schlüssel verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |TDE (Transparent Data Encryption) mit Unterstützung eines eigenen Schlüssels bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Sicherstellen, dass „SSL-Verbindung erzwingen“ für den MySQL-Datenbankserver auf „AKTIVIERT“ festgelegt ist

**ID:** CIS Azure 4.11 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Erzwingen einer SSL-Verbindung muss für MySQL-Datenbankserver aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure-Datenbank für MySQL unterstützt die Verbindung Ihres Servers mit Azure-Datenbank für MySQL mit Clientanwendungen, die Secure Sockets Layer (SSL) verwenden. Durch das Erzwingen von SSL-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln. Diese Konfiguration erzwingt, dass SSL für den Zugriff auf Ihren Datenbankserver immer aktiviert ist. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Sicherstellen, dass der Serverparameter „log_checkpoints“ für den PostgreSQL-Datenbankserver auf „EIN“ festgelegt ist

**ID:** CIS Azure 4.12 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Protokollprüfpunkte für PostgreSQL-Datenbankserver müssen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Mit dieser Richtlinie können PostgreSQL-Datenbanken in Ihrer Umgebung überwacht werden, ohne dass die log_checkpoints-Einstellung aktiviert ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Sicherstellen, dass „SSL-Verbindung erzwingen“ für den PostgreSQL-Datenbankserver auf „AKTIVIERT“ festgelegt ist

**ID:** CIS Azure 4.13 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Erzwingen einer SSL-Verbindung muss für PostgreSQL-Datenbankserver aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL unterstützt das Herstellen einer Verbindung zwischen Ihrem Azure Database for PostgreSQL-Server und Clientanwendungen unter Verwendung von Secure Sockets Layer (SSL). Durch das Erzwingen von SSL-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln. Diese Konfiguration erzwingt, dass SSL für den Zugriff auf Ihren Datenbankserver immer aktiviert ist. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Sicherstellen, dass der Serverparameter „log_connections“ für den PostgreSQL-Datenbankserver auf „EIN“ festgelegt ist

**ID:** CIS Azure 4.14 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Protokollverbindungen für PostgreSQL-Datenbankserver müssen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Mit dieser Richtlinie können PostgreSQL-Datenbanken in Ihrer Umgebung überwacht werden, ohne dass die log_connections-Einstellung aktiviert ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Sicherstellen, dass der Serverparameter „log_disconnections“ für den PostgreSQL-Datenbankserver auf „EIN“ festgelegt ist

**ID:** CIS Azure 4.15 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verbindungstrennungen für PostgreSQL-Datenbankserver müssen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Mit dieser Richtlinie können PostgreSQL-Datenbanken in Ihrer Umgebung überwacht werden, ohne dass die log_disconnections-Einstellung aktiviert ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Sicherstellen, dass der Serverparameter „connection_throttling“ für den PostgreSQL-Datenbankserver auf „EIN“ festgelegt ist

**ID:** CIS Azure 4.17 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Verbindungsdrosselung muss für PostgreSQL-Datenbankserver aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Mit dieser Richtlinie können Sie überwachen, ob für alle PostgreSQL-Datenbanken in Ihrer Umgebung die Verbindungsdrosselung aktiviert ist. Diese Einstellung aktiviert die temporäre Verbindungsdrosselung pro IP-Adresse bei zu vielen Anmeldefehlern aufgrund ungültiger Kennwörter. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

### <a name="ensure-that-a-log-profile-exists"></a>Sicherstellen, dass ein Protokollprofil vorhanden ist

**ID:** CIS Azure 5.1.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure-Abonnements benötigen ein Protokollprofil für das Aktivitätsprotokoll](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Diese Richtlinie stellt sicher, dass ein Protokollprofil für den Export von Aktivitätsprotokollen aktiviert ist. Sie überwacht, ob für den Export der Protokolle entweder in ein Speicherkonto oder in einen Event Hub ein Protokollprofil erstellt wurde. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Sicherstellen, dass die Speicherdauer für Aktivitätsprotokolle auf mindestens 365 Tage festgelegt ist

**ID:** CIS Azure 5.1.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Das Aktivitätsprotokoll muss mindestens ein Jahr lang aufbewahrt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Diese Richtlinie überwacht das Aktivitätsprotokoll, wenn die Aufbewahrung nicht auf 365 Tage oder unbegrenzt (Festlegung der Aufbewahrungstage auf 0) festgelegt ist. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Sicherstellen, dass mit dem Überwachungsprofil alle Aktivitäten erfasst werden

**ID:** CIS Azure 5.1.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Das Azure Monitor-Protokollprofil muss Protokolle für die Kategorien „write“, „delete“ und „action“ erfassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Diese Richtlinie stellt sicher, dass ein Protokollprofil Protokolle für die Kategorien „write“, „delete“ und „action“ sammelt. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Sicherstellen, dass mit dem Protokollprofil die Aktivitätsprotokolle für alle Regionen erfasst werden (einschließlich global)

**ID:** CIS Azure 5.1.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor muss Aktivitätsprotokolle aus allen Regionen erfassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Diese Richtlinie überwacht das Azure Monitor-Protokollprofil darauf, dass keine Aktivitäten aus allen unterstützten Azure-Regionen, einschließlich „Global“, exportiert werden. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Sicherstellen, dass das Speicherkonto, das den Container mit Aktivitätsprotokollen enthält, per BYOK (Bring Your Own Key) verschlüsselt ist

**ID:** CIS Azure 5.1.6 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Das Speicherkonto, das den Container mit Aktivitätsprotokollen enthält, muss mit BYOK verschlüsselt sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Diese Richtlinie überwacht, ob das Speicherkonto, das den Container mit Aktivitätsprotokollen enthält, mit BYOK verschlüsselt ist. Die Richtlinie funktioniert nur, wenn sich das Speicherkonto in demselben Abonnement wie die Aktivitätsprotokolle befindet. Weitere Informationen zur Azure Storage-Verschlüsselung im Ruhezustand finden Sie unter [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Sicherstellen, dass die Protokollierung für Azure Key Vault aktiviert ist

**ID:** CIS Azure 5.1.7 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnoseprotokolle in Key Vault sollten aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Hiermit wird die Aktivierung von Diagnoseprotokollen überwacht. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn es zu einem Sicherheitsincident kommt oder Ihr Netzwerk gefährdet ist. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Richtlinienzuweisung erstellen“ vorhanden ist

**ID:** CIS Azure 5.2.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte Richtlinienvorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Diese Richtlinie überwacht bestimmte Richtlinienvorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Netzwerksicherheitsgruppe erstellen oder aktualisieren“ vorhanden ist

**ID:** CIS Azure 5.2.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Diese Richtlinie überwacht bestimmte administrative Vorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Netzwerksicherheitsgruppe löschen“ vorhanden ist

**ID:** CIS Azure 5.2.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Diese Richtlinie überwacht bestimmte administrative Vorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für die Regel „Netzwerksicherheitsgruppe erstellen oder aktualisieren“ vorhanden ist

**ID:** CIS Azure 5.2.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Diese Richtlinie überwacht bestimmte administrative Vorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für die Regel „Netzwerksicherheitsgruppe löschen“ vorhanden ist

**ID:** CIS Azure 5.2.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Diese Richtlinie überwacht bestimmte administrative Vorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Sicherheitslösung erstellen oder aktualisieren“ vorhanden ist

**ID:** CIS Azure 5.2.6 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte Sicherheitsvorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Diese Richtlinie überwacht bestimmte Sicherheitsvorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Sicherheitslösung löschen“ vorhanden ist

**ID:** CIS Azure 5.2.7 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte Sicherheitsvorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Diese Richtlinie überwacht bestimmte Sicherheitsvorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „SQL Server-Firewallregel erstellen oder aktualisieren bzw. löschen“ vorhanden ist

**ID:** CIS Azure 5.2.8 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Diese Richtlinie überwacht bestimmte administrative Vorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Sicherstellen, dass eine Aktivitätsprotokollwarnung für „Sicherheitsrichtlinie aktualisieren“ vorhanden ist

**ID:** CIS Azure 5.2.9 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für bestimmte Sicherheitsvorgänge muss eine Aktivitätsprotokollwarnung vorliegen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Diese Richtlinie überwacht bestimmte Sicherheitsvorgänge ohne Konfiguration von Aktivitätsprotokollwarnungen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Netzwerk

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Sicherstellen, dass der RDP-Zugriff aus dem Internet eingeschränkt ist

**ID:** CIS Azure 6.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[RDP-Zugriff über das Internet blockieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Diese Richtlinie überwacht jede Netzwerksicherheitsregel, die RDP-Zugriff über das Internet zulässt. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Sicherstellen, dass der SSH-Zugriff aus dem Internet eingeschränkt ist

**ID:** CIS Azure 6.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SSH-Zugriff über das Internet blockieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Diese Richtlinie überwacht jede Netzwerksicherheitsregel, die SSH-Zugriff über das Internet zulässt. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Sicherstellen, dass Network Watcher aktiviert ist

**ID:** CIS Azure 6.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher muss aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher ist ein regionaler Dienst, mit dem Sie Bedingungen auf der Ebene von Netzwerkszenarien in Azure überwachen und diagnostizieren können. Die Überwachung auf Szenarioebene erlaubt die umfassende Diagnose von Problemen auf Netzwerkebene. Die Tools zur Netzwerkdiagnose und -visualisierung von Network Watcher helfen Ihnen dabei, Ihr Netzwerk in Azure zu verstehen, Diagnosen durchzuführen und Einblicke zu gewinnen. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Sicherstellen, dass der Betriebssystem-Datenträger verschlüsselt ist

**ID:** CIS Azure 7.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Hiermit werden VMs ohne aktivierte Datenträgerverschlüsselung über Azure Security Center als Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Sicherstellen, dass reguläre Datenträger verschlüsselt sind

**ID:** CIS Azure 7.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Hiermit werden VMs ohne aktivierte Datenträgerverschlüsselung über Azure Security Center als Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Sicherstellen, dass „nicht angefügte Datenträger“ verschlüsselt sind

**ID:** CIS Azure 7.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nicht angefügte Datenträger müssen verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Diese Richtlinie überwacht nicht angefügte Datenträger ohne aktivierte Verschlüsselung. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Sicherstellen, dass nur genehmigte Erweiterungen installiert werden

**ID:** CIS Azure 7.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Es dürfen nur genehmigte VM-Erweiterungen installiert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Diese Richtlinie regelt die nicht genehmigten VM-Erweiterungen. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Sicherstellen, dass die neuesten Betriebssystempatches für alle virtuellen Computer angewendet werden

**ID:** CIS Azure 7.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemupdates sollten auf Ihren Computern installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Sicherstellen, dass Endpoint Protection für alle virtuellen Computer installiert ist

**ID:** CIS Azure 7.6 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Hiermit werden Server ohne installierten Endpoint Protection-Agent über Azure Security Center in Form von Empfehlungen überwacht. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Weitere Überlegungen zur Sicherheit

### <a name="ensure-the-key-vault-is-recoverable"></a>Sicherstellen, dass der Schlüsseltresor wiederhergestellt werden kann

**ID:** CIS Azure 8.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Für den Schlüsseltresor muss der Löschschutz aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Das böswillige Löschen eines Schlüsseltresors kann zu dauerhaftem Datenverlust führen. Ein böswilliger Insider in Ihrer Organisation kann möglicherweise die Berechtigung zum Löschen und endgültigen Löschen von Schlüsseltresoren erhalten. Der Löschschutz schützt Sie vor Insiderangriffen, indem ein verbindlicher Aufbewahrungszeitraum für vorläufig gelöschte Schlüsseltresore durchgesetzt wird. Niemand innerhalb Ihrer Organisation oder von Microsoft kann Ihre Schlüsseltresore während des Aufbewahrungszeitraums für vorläufiges Löschen löschen. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Aktivieren der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Kubernetes Service

**ID:** CIS Azure 8.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rollenbasierte Zugriffssteuerung (RBAC) sollte für Kubernetes Service verwendet werden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Um eine granulare Filterung anhand der durch die Benutzer ausführbaren Aktionen zu ermöglichen, verwenden Sie die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC), um Berechtigungen in Kubernetes Service-Clustern zu verwalten und relevante Autorisierungsrichtlinien zu konfigurieren. |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Sicherstellen, dass die App Service-Authentifizierung für Azure App Service festgelegt ist

**ID:** CIS Azure 9.1 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Authentifizierung sollte für Ihre API-App aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Die Azure App Service-Authentifizierung ist ein Feature, mit dem Sie verhindern können, dass anonyme HTTP-Anforderungen die API-App erreichen, oder mit dem Sie Anforderungen mit Token vor dem Erreichen der API-App authentifizieren können. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Die Authentifizierung sollte für Ihre Funktions-App aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Die Azure App Service-Authentifizierung ist ein Feature, mit dem Sie verhindern können, dass anonyme HTTP-Anforderungen die Funktions-App erreichen, oder mit dem Sie Anforderungen mit Token vor dem Erreichen der Funktions-App authentifizieren können. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Die Authentifizierung sollte in Ihrer Web-App aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Die Azure App Service-Authentifizierung ist ein Feature, mit dem Sie verhindern können, dass anonyme HTTP-Anforderungen die Web-App erreichen, oder mit dem Sie Anforderungen mit Token vor dem Erreichen der Web-App authentifizieren können. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Sicherstellen, dass die Web-App den gesamten HTTP-Datenverkehr in Azure App Service an HTTPS umleitet

**ID:** CIS Azure 9.2 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zugriff auf Webanwendung nur über HTTPS gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Durch die Verwendung von HTTPS wird die Server-/Dienstauthentifizierung sichergestellt, und Daten werden bei der Übertragung vor Abhörangriffen auf der Vermittlungsschicht geschützt. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Sicherstellen, dass die Web-App die neueste Version der TLS-Verschlüsselung verwendet

**ID:** CIS Azure 9.3 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API-App muss neueste TLS-Version verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade auf neueste TLS-Version durchführen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Funktions-App muss neueste TLS-Version verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade auf neueste TLS-Version durchführen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web-App muss neueste TLS-Version verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade auf neueste TLS-Version durchführen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Sicherstellen, dass „Clientzertifikate (eingehende Clientzertifikate)“ für die Web-App auf „Ein“ festgelegt ist

**ID:** CIS Azure 9.4 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherstellen, dass die API-App „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Mithilfe von Clientzertifikaten kann die App ein Zertifikat für eingehende Anforderungen anfordern. Nur Clients mit einem gültigen Zertifikat können auf die App zugreifen. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Sicherstellen, dass für die Funktions-App „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Mithilfe von Clientzertifikaten kann die App ein Zertifikat für eingehende Anforderungen anfordern. Nur Clients mit einem gültigen Zertifikat können auf die App zugreifen. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Sicherstellen, dass für die Web-App „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Mithilfe von Clientzertifikaten kann die App ein Zertifikat für eingehende Anforderungen anfordern. Nur Clients mit einem gültigen Zertifikat können auf die App zugreifen. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Sicherstellen, dass „Registrierung bei Azure Active Directory“ für App Service aktiviert ist

**ID:** CIS Azure 9.5 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API-App muss verwaltete Identität verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Verwaltete Identität zur Erhöhung der Authentifizierungssicherheit verwenden |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Funktions-App muss verwaltete Identität verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Verwaltete Identität zur Erhöhung der Authentifizierungssicherheit verwenden |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web-App muss verwaltete Identität verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Verwaltete Identität zur Erhöhung der Authentifizierungssicherheit verwenden |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Sicherstellen, dass die neueste „PHP-Version“ angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird

**ID:** CIS Azure 9.7 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherstellen, dass die neueste PHP-Version angegeben ist, wenn sie als Teil der API-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Für die PHP-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle PHP-Version für API-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Sicherstellen, dass die neueste PHP-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Für die PHP-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle PHP-Version für Web-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Sicherstellen, dass die neueste „Python-Version“ angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird

**ID:** CIS Azure 9.8 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherstellen, dass die neueste Python-Version angegeben ist, wenn sie als Teil der API-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Für die Python-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Python-Version für API-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Sicherstellen, dass die neueste Python-Version angegeben ist, wenn sie als Teil der Funktions-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Für die Python-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Python-Version für Funktions-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Sicherstellen, dass die neueste Python-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Für die Python-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Python-Version für Web-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Sicherstellen, dass die neueste „Java-Version“ angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird

**ID:** CIS Azure 9.9 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherstellen, dass die neueste Java-Version angegeben ist, wenn sie als Teil der API-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Für die Java-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Python-Version für API-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Sicherstellen, dass die neueste Java-Version angegeben ist, wenn sie als Teil der Funktions-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Für die Java-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Java-Version für Funktions-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Sicherstellen, dass die neueste Java-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Für die Java-Software werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Es wird empfohlen, die aktuelle Java-Version für Web-Apps zu verwenden, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Sicherstellen, dass die neueste „HTTP-Version“ angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird

**ID:** CIS Azure 9.10 **Besitz**: Kunde

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sicherstellen, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der API-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Für HTTP werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Verwenden Sie die aktuelle HTTP-Version für Web-Apps, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Sicherstellen, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der Funktions-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Für HTTP werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Verwenden Sie die aktuelle HTTP-Version für Web-Apps, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Sicherstellen, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Für HTTP werden in regelmäßigen Abständen neue Versionen herausgegeben – entweder zum Beheben von Sicherheitsmängeln oder zum Bereitstellen neuer Funktionen. Verwenden Sie die aktuelle HTTP-Version für Web-Apps, um von Sicherheitsfixes (falls vorhanden) und/oder neuen Funktionen der neuesten Version zu profitieren. Diese Richtlinie gilt zurzeit nur für Linux-Web-Apps. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel über Azure Policy:

- Übersicht über die [Einhaltung gesetzlicher Bestimmungen](../concepts/regulatory-compliance.md).
- Weitere Informationen finden Sie unter [Struktur der Initiativendefinition](../concepts/initiative-definition-structure.md).
- Sehen Sie sich weitere Beispiele unter [Azure Policy-Beispiele](./index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
