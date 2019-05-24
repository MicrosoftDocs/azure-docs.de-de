---
title: Arbeiten mit Sicherheitsrichtlinien | Microsoft-Dokumentation
description: In diesem Artikel ist beschrieben, wie Sie mit Sicherheitsrichtlinien i Azure Security Center arbeiten.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 7f738350743b0b80bf39a322043c514081721def
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967832"
---
# <a name="working-with-security-policies"></a>Arbeiten mit Sicherheitsrichtlinien

In diesem Artikel wird beschrieben, wie Sicherheitsrichtlinien konfiguriert werden und wie Sie sie in Security Center anzeigen. Azure Security Center weist seine [integrierten Sicherheitsrichtlinien](security-center-policy-definitions.md) automatisch für jedes Abonnement zu, für das das Onboarding durchgeführt wird. Sie können sie in [Azure Policy](../governance/policy/overview.md) konfigurieren und haben hierbei auch die Möglichkeit, Richtlinien übergreifend für Verwaltungsgruppen und mehrere Abonnements festzulegen.

Anweisungen dazu, wie Richtlinien über PowerShell festgelegt werden, finden Sie unter [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mithilfe des Azure PowerShell-Moduls](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> Die Integration von Security Center in Azure Policy hat begonnen. Bestandskunden werden automatisch zur neuen integrierten Initiative in Azure Policy anstelle der vorherigen Sicherheitsrichtlinien in Security Center migriert. Diese Änderung wirkt sich nicht auf Ihre Ressourcen oder Umgebung aus, mit der Ausnahme, dass die neue Initiative in Azure Policy vorhanden ist.

## <a name="what-are-security-policies"></a>Was sind Sicherheitsrichtlinien?
Eine Sicherheitsrichtlinie definiert die gewünschte Konfiguration Ihrer Workloads und trägt zur Erfüllung unternehmensbezogener oder gesetzlicher Sicherheitsanforderungen bei. In Azure Policy können Sie Richtlinien für Ihre Azure-Abonnements definieren und auf die Art der Workload oder auf die Vertraulichkeit der Daten abstimmen. So kann etwa für Anwendungen mit regulierten Daten (beispielsweise personenbezogene Informationen) eine höhere Sicherheitsstufe erforderlich sein als für andere Workloads. Um eine Richtlinie mehreren Abonnements oder Verwaltungsgruppen zuzuweisen, legen Sie diese in [Azure Policy](../governance/policy/overview.md) fest.

Ihre Sicherheitsrichtlinien sind die Grundlage der Sicherheitsempfehlungen, die Sie in Azure Security Center erhalten. Sie können ihre Einhaltung überwachen, damit Sie potenzielle Sicherheitsrisiken identifizieren und Bedrohungen eindämmen können. Weitere Informationen zur Ermittlung der Option, die für Sie geeignet ist, finden Sie in der Liste mit den [integrierten Sicherheitsrichtlinien](security-center-policy-definitions.md).

Wenn Sie Security Center aktivieren, wird die in Security Center integrierte Sicherheitsrichtlinie in Azure Policy als integrierte Initiative unter der Kategorie Security Center dargestellt. Die integrierte Initiative wird automatisch allen in Security Center registrierten Abonnements (Tarife „Free“ oder „Standard“) zugewiesen. Die integrierte Initiative enthält nur Überwachungsrichtlinien.


### <a name="management-groups"></a>Verwaltungsgruppen
Wenn Ihre Organisation über viele Abonnements verfügt, benötigen Sie möglicherweise eine Möglichkeit zur effizienten Verwaltung von Zugriff, Richtlinien und Konformität für diese Abonnements. Azure-Verwaltungsgruppen stellen einen abonnementübergreifenden Bereich dar. Sie organisieren Abonnements in Containern, die als „Verwaltungsgruppen“ bezeichnet werden, und wenden Ihre Governancerichtlinien auf die Verwaltungsgruppen an. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Richtlinien. Jedes Verzeichnis erhält eine einzelne Verwaltungsgruppe auf oberster Ebene, die als Stammverwaltungsgruppe (Root) bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Stammverwaltungsgruppe ermöglicht das Anwenden von globalen Richtlinien und RBAC-Zuweisungen auf Verzeichnisebene. Befolgen Sie die Anleitung unter [Erzielen der mandantenweiten Sichtbarkeit für Azure Security Center](security-center-management-groups.md), um Verwaltungsgruppen für die Verwendung mit Azure Security Center einzurichten.

> [!NOTE]
> Es ist wichtig, dass Sie die Hierarchie von Verwaltungsgruppen und Abonnements verstehen. Weitere Informationen zu Verwaltungsgruppen, zur Stammveraltung und zum Zugriff auf Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/index.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Funktionsweise von Sicherheitsrichtlinien
Security Center erstellt für jedes Ihrer Azure-Abonnements automatisch eine Standardsicherheitsrichtlinie. Sie können die Richtlinien in Azure Policy bearbeiten, um Folgendes zu erreichen:
- Erstellen von neuen Richtliniendefinitionen
- Übergreifendes Zuweisen von Richtlinien für Verwaltungsgruppen und Abonnements, die für eine gesamte Organisation oder eine Geschäftseinheit innerhalb der Organisation stehen können
- Überwachen der Richtlinienkonformität

Weitere Informationen zu Azure Policy finden Sie unter [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md).

Eine Azure-Richtlinie umfasst die folgenden Komponenten:

- Eine **Richtlinie** ist eine Regel.
- Eine **Initiative** ist eine Sammlung mit Richtlinien.
- Eine **Zuweisung** ist die Anwendung einer Initiative oder Richtlinie auf einen bestimmten Bereich (Verwaltungsgruppe, Abonnement oder Ressourcengruppe).

## <a name="view-security-policies"></a>Anzeigen von Sicherheitsrichtlinien

Zeigen Sie Ihre Sicherheitsrichtlinien in Security Center wie folgt an:

1. Wählen Sie im **Security Center**-Dashboard die Option **Sicherheitsrichtlinie**.

    ![Bereich „Richtlinienverwaltung“](./media/security-center-policies/security-center-policy-mgt.png)

   Auf dem Bildschirm **Richtlinienverwaltung** können Sie die Anzahl von Verwaltungsgruppen, Abonnements und Arbeitsbereichen sowie Ihre Verwaltungsgruppenstruktur anzeigen.

   > [!NOTE]
   > - Im Security Center-Dashboard wird unter **Abonnementabdeckung** ggf. eine höhere Anzahl von Abonnements als unter **Richtlinienverwaltung** angezeigt. Unter der „Abonnementabdeckung“ wird die Anzahl von Abonnements vom Typ „Standard“, „Free“ und „Nicht abgedeckt“ angezeigt. Für Abonnements vom Typ „Nicht abgedeckt“ ist Security Center nicht aktiviert, und sie werden unter **Richtlinienverwaltung** nicht angezeigt.
   >

   In den Spalten der Tabelle wird Folgendes angezeigt:

   - **Zuweisung der Richtlinieninitiative**: [Integrierte Richtlinien](security-center-policy-definitions.md) und Initiativen von Security Center, die einem Abonnement oder einer Verwaltungsgruppe zugewiesen sind.
   - **Abdeckung**: Identifiziert den Tarif („Free“ oder „Standard“), unter dem die Verwaltungsgruppe, das Abonnement oder der Arbeitsbereich ausgeführt werden.  Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md).
   - **Einstellungen**: Abonnements verfügen über den Link **Einstellungen bearbeiten**. Indem Sie **Einstellungen bearbeiten** wählen, können Sie Ihre [Security Center-Einstellungen](security-center-policies-overview.md) für jedes Abonnement bzw. jede Verwaltungsgruppe aktualisieren.
   - **Sicherheitsbewertung**: Die [Sicherheitsbewertung](security-center-secure-score.md) informiert Sie über den Sicherheitszustand Ihrer Workload und hilft Ihnen, Verbesserungsempfehlungen Priorität einzuräumen.

2. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, zu dem bzw. der Sie die Richtlinien anzeigen möchten.

   - Auf dem Bildschirm **Sicherheitsrichtlinie** wird die Aktion der Richtlinien widergespiegelt, die dem von Ihnen gewählten Abonnement oder der Verwaltungsgruppe zugewiesen sind.
   - Verwenden Sie oben die angegebenen Links, um die einzelnen **Richtlinienzuweisungen** zu öffnen, die für das Abonnement bzw. die Verwaltungsgruppe gelten. Sie können die Links nutzen, um auf die Zuweisung zuzugreifen und die Richtlinie zu bearbeiten oder zu deaktivieren. Wenn Sie beispielsweise feststellen, dass eine bestimmte Zuweisung den Endpunktschutz verhindert, können Sie über den Link auf die Richtlinie zugreifen und sie bearbeiten oder deaktivieren.
   - In der Liste mit den Richtlinien können Sie die aktive Anwendung der Richtlinie auf Ihr Abonnement oder die Verwaltungsgruppe anzeigen. Dies bedeutet Folgendes: Die Einstellungen der einzelnen Richtlinien, die für den Bereich gelten, werden berücksichtigt, und für Sie wird das kumulierte Ergebnis dazu angegeben, welche Aktion von der Richtlinie durchgeführt wird. Wenn die Richtlinie beispielsweise in einer Zuweisung deaktiviert wird, aber in einer anderen auf „AuditIfNotExist“ festgelegt ist, wird aufgrund der Kumulation „AuditIfNotExist“ angewendet. Die aktivere Auswirkung hat immer Vorrang.
   - Für die Richtlinien können sich die folgenden Auswirkungen ergeben: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Weitere Informationen zur Anwendung von Auswirkungen finden Sie unter [Grundlegendes zu Richtlinienauswirkungen](../governance/policy/concepts/effects.md).

   ![Bildschirm mit Richtlinien](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Beim Anzeigen von zugewiesenen Richtlinien können Sie mehrere Zuweisungen sehen und die jeweilige Konfiguration der Zuweisungen auch einzeln prüfen.

## <a name="edit-security-policies"></a>Bearbeiten von Sicherheitsrichtlinien
Sie können die Standardsicherheitsrichtlinie für jedes Ihrer Azure-Abonnements und jede Verwaltungsgruppe in [Azure Policy](../governance/policy/tutorials/create-and-manage.md) bearbeiten. Eine Sicherheitsrichtlinie kann nur von einem Besitzer, Mitwirkenden oder Systemadministrator des Abonnements oder der enthaltenden Verwaltungsgruppe geändert werden.

Eine Anleitung zur Bearbeitung einer Sicherheitsrichtlinie in Azure Policy finden Sie unter [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md).

Sie können Sicherheitsrichtlinien über das Azure Policy-Portal, über die REST-API oder über Windows PowerShell bearbeiten. Das folgende Beispiel enthält Anweisungen zum Bearbeiten über die REST-API.


## <a name="disable-security-policies"></a>Deaktivieren von Sicherheitsrichtlinien
Wenn die Standardsicherheitsrichtlinie eine Empfehlung generiert, die für Ihre Umgebung nicht relevant ist, können Sie dies beenden, indem Sie die Richtliniendefinition deaktivieren, die die Empfehlung sendet.
Weitere Informationen zu Empfehlungen finden Sie unter [Verwalten von Sicherheitsempfehlungen](security-center-recommendations.md).

1. Klicken Sie im Security Center im Abschnitt **Richtlinie und Konformität** auf **Sicherheitsrichtlinie**.

   ![Richtlinienverwaltung](./media/tutorial-security-policy/policy-management.png)

2. Klicken Sie auf das Abonnement, für das Sie die Empfehlung deaktivieren möchten.

   > [!Note]
   > Denken Sie daran, dass eine Verwaltungsgruppe ihre Richtlinien auf ihre Abonnements anwendet. Aus diesem Grund erhalten Sie weiterhin Richtlinienempfehlungen, wenn Sie eine Abonnementrichtlinie deaktivieren und das Abonnement zu einer Verwaltungsgruppe gehört, die immer noch dieselbe Richtlinie verwendet. Die Richtlinie wird immer noch von der Verwaltungsebene aus angewandt, und die Empfehlungen werden immer noch generiert.

1. Klicken Sie auf die zugewiesene Richtlinie.

   ![Richtlinie deaktivieren](./media/tutorial-security-policy/security-policy.png)

1. Suchen Sie im Abschnitt **PARAMETER** die Richtlinie, die die zu deaktivierende Empfehlung aufruft, und wählen Sie in der Dropdownliste die Option **Deaktiviert** aus.

   ![Richtlinie deaktivieren](./media/tutorial-security-policy/disable-policy.png)
1. Klicken Sie auf **Speichern**.
   > [!Note]
   > Es kann bis zu 12 Stunden dauern, bis die Änderungen zum Deaktivieren der Richtlinie wirksam werden.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurieren einer Sicherheitsrichtlinie über die REST-API

Als Teil der nativen Integration in Azure Policy ermöglicht Ihnen Azure Security Center die Nutzung der REST-API von Azure Policy, um Richtlinienzuweisungen zu erstellen. Die folgenden Anweisungen führen Sie durch das Erstellen von Richtlinienzuweisungen sowie durch das Anpassen vorhandener Zuweisungen. 

Wichtige Konzepte in Azure Policy: 

- Eine **Richtliniendefinition** ist eine Regel. 

- Eine **Initiative** ist eine Sammlung von Richtliniendefinitionen (Regeln). 

- Eine **Zuweisung** ist eine Anwendung einer Initiative oder Richtlinie für einen bestimmten Bereich (Verwaltungsgruppe, Abonnement usw.). 

Security Center hat eine integrierte Initiative, die alle seiner Sicherheitsrichtlinien enthält. Um die Richtlinien von Security Center für Ihre Azure-Ressourcen zu bewerten, sollten Sie eine Zuweisung zur Verwaltungsgruppe oder zum Abonnement erstellen, die oder das Sie bewerten möchten.  

In der integrierten Initiative sind alle Richtlinien von Security Center standardmäßig aktiviert. Sie können bestimmte Richtlinien in der integrierten Initiative deaktivieren, z. B. können Sie alle Security Center-Richtlinien außer **Webanwendungsfirewall** anwenden, indem Sie den Wert des Effect-Parameters der Richtlinie in **Disabled** ändern. 

### <a name="api-examples"></a>API-Beispiele

Ersetzen Sie diese Variablen in den folgenden Beispielen:

- Geben Sie für **{scope}**  den Namen der Verwaltungsgruppe oder des Abonnements ein, auf die oder das Sie die Richtlinie anwenden.
- Geben Sie für **{policyAssignmentName}**  den [Namen der relevanten Richtlinienzuweisung ](#policy-names) ein.
- Geben Sie für **{name}**  Ihren Namen oder den Namen des Administrators ein, der die Richtlinienänderung genehmigt hat.

In diesem Beispiel wird gezeigt, wie Sie die integrierte Security Center-Initiative einem Abonnement oder einer Verwaltungsgruppe zuweisen:
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

In diesem Beispiel wird gezeigt, wie Sie die integrierte Security Center-Initiative einem Abonnement zuweisen, wobei die folgenden Richtlinien deaktiviert sind: 

- Systemupdates („systemUpdatesMonitoringEffect“) 

- Sicherheitskonfigurationen („systemConfigurationsMonitoringEffect“) 

- Endpunktschutz (Endpoint Protection) („endpointProtectionMonitoringEffect“) 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 

In diesem Beispiel wird gezeigt, wie Sie eine Zuweisung entfernt wird:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### Referenz der Richtliniennamen <a name="policy-names"></a>

|Richtlinienname in Security Center|Richtlinienname, der in Azure Policy angezeigt wird |Name des Effect-Parameters der Richtlinie|
|----|----|----|
|SQL-Verschlüsselung |Nicht verschlüsselte SQL-Datenbank in Azure Security Center überwachen |sqlEncryptionMonitoringEffect| 
|SQL-Überwachung |Nicht überwachte SQL-Datenbank in Azure Security Center überwachen |sqlAuditingMonitoringEffect|
|Systemupdates |Fehlende Systemupdates in Azure Security Center überwachen |systemUpdatesMonitoringEffect|
|Speicherverschlüsselung |Fehlende Blobverschlüsselung für Speicherkonten überwachen |storageEncryptionMonitoringEffect|
|JIT-Netzwerkzugriff |Möglichen Just-In-Time-Netzwerkzugriff in Azure Security Center überwachen |jitNetworkAccessMonitoringEffect |
|Adaptive Anwendungssteuerungen |Mögliche App-Whitelist in Azure Security Center überwachen |adaptiveApplicationControlsMonitoringEffect|
|Netzwerksicherheitsgruppen |Zu wenig einschränkenden Netzwerkzugriff in Azure Security Center überwachen |networkSecurityGroupsMonitoringEffect| 
|Sicherheitskonfigurationen |Betriebssystem-Sicherheitsrisiken in Azure Security Center überwachen |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen |endpointProtectionMonitoringEffect |
|Datenträgerverschlüsselung |Nicht verschlüsselte VM-Datenträger in Azure Security Center überwachen |diskEncryptionMonitoringEffect|
|Sicherheitsrisikobewertung |VM-Sicherheitsrisiken in Azure Security Center überwachen |vulnerabilityAssessmentMonitoringEffect|
|Web Application Firewall |Nicht geschützte Webanwendung in Azure Security Center überwachen |webApplicationFirewallMonitoringEffect |
|Firewall der nächsten Generation |Nicht geschützte Netzwerkendpunkte in Azure Security Center überwachen| |


### <a name="who-can-edit-security-policies"></a>Wer kann Sicherheitsrichtlinien bearbeiten?
Security Center verwendet die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Dabei werden integrierte Rollen bereitgestellt, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können. In Security Center werden den Benutzern nur Informationen zu den Ressourcen angezeigt, auf die sie Zugriff haben. Das bedeutet, dass Benutzern die Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ für das Abonnement oder die Ressourcengruppe einer Ressource zugewiesen wird. Neben diesen Rollen gibt es zwei spezifische Security Center-Rollen:

- Sicherheitsleseberechtigter: Kann Informationen in Security Center (wie etwa Empfehlungen, Warnungen, Richtlinien und die Integrität) anzeigen, aber keine Änderungen vornehmen.
- Sicherheitsadministrator: Verfügt über dieselben Anzeigeberechtigungen wie ein Sicherheitsleseberechtigter, ist zusätzlich aber auch zum Aktualisieren der Sicherheitsrichtlinie und zum Verwerfen von Empfehlungen und Warnungen berechtigt.



## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sicherheitsrichtlinien in Azure Policy bearbeitet werden. Weitere Informationen zu Security Center finden Sie in den folgenden Artikeln:

* [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md): Hier erfahren Sie, wie Sie die Entwurfsaspekte zu Azure Security Center planen und verstehen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Erzielen der mandantenweiten Sichtbarkeit für Azure Security Center](security-center-management-groups.md): Erfahren Sie, wie Sie Verwaltungsgruppen für Azure Security Center einrichten.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.

Weitere Informationen zu Azure Policy finden Sie unter [Was ist Azure Policy?](../governance/policy/overview.md).
