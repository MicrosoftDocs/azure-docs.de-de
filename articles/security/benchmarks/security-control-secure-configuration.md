---
title: 'Azure-Sicherheitskontrolle: sichere Konfiguration'
description: 'Azure-Sicherheitskontrolle: Sichere Konfiguration'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 349456b0a4de65520dd633efe0573d24ea1c89c4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409024"
---
# <a name="security-control-secure-configuration"></a>Sicherheitskontrolle: Sichere Konfiguration

Erstellen, implementieren und verwalten Sie die Sicherheitskonfiguration von Azure-Ressourcen aktiv (nachverfolgen, melden und korrigieren), um zu verhindern, dass Angreifer anfällige Dienste und Einstellungen ausnutzen.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.1 | 5,1 | Kunde |

Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Konfiguration Ihrer Azure-Ressourcen zu erstellen. Sie können auch integrierte Azure Policy-Definitionen verwenden.

Außerdem hat Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen/übertreffen.

Sie können auch Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.2 | 5,1 | Kunde |

Verwenden Sie die Azure Security Center-Empfehlungen, um die Sicherheitskonfigurationen für alle Computeressourcen zu verwalten.  Darüber hinaus können Sie auch benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems festzulegen.

- [Überwachen von Empfehlungen in Azure Security Center](../../security-center/security-center-recommendations.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../../security-center/recommendations-reference.md)

- [Übersicht über Azure Automation State Configuration](../../automation/automation-dsc-overview.md)

- [Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure](../../virtual-machines/windows/upload-generalized-managed.md)

- [Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.3 | 5,2 | Kunde |

Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen.  Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die Sicherheitskonfiguration der von Ihrer Organisation benötigten Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.4 | 5,2 | Shared |

Befolgen Sie die Azure Security Center-Empfehlungen zum Durchführen von Sicherheitsrisikobewertungen für Ihre Azure-Computeressourcen.  Darüber hinaus können Sie auch Azure Resource Manager-Vorlagen, benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems zu verwalten.   Die Vorlagen für virtuelle Computer von Microsoft in Kombination mit Azure Automation Desired State Configuration können als Hilfe beim Erfüllen und Verwalten der Sicherheitsanforderungen dienen. 

Beachten Sie hierbei auch, dass von Microsoft im Azure Marketplace veröffentlichte VM-Images von Microsoft verwaltet und gepflegt werden. 

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Erstellen eines virtuellen Azure-Computers mithilfe einer Azure Resource Manager-Vorlage](../../virtual-machines/windows/ps-template.md)

- [Übersicht über Azure Automation State Configuration](../../automation/automation-dsc-overview.md)

- [Erstellen eines virtuellen Windows-Computers im Azure-Portal](../../virtual-machines/windows/quick-create-portal.md)

- [Herunterladen einer Vorlage für einen virtuellen Computer](../../virtual-machines/windows/download-template.md)

- [Beispielskript zum Hochladen einer generalisierten virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](../../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7,5 | 5.3 | Kunde |

Verwenden Sie Azure DevOps, um Ihren Code sicher zu speichern und zu verwalten, z. B. benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen und Desired State Configuration-Skripts. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierte Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.6 | 5.3 | Kunde |

Wenn benutzerdefinierte Images verwendet werden, sollten Sie durch die Nutzung der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) sicherstellen, dass nur autorisierte Benutzer auf die Images zugreifen können. Mit einem Katalog mit freigegebenen Images können Sie Ihre Images für unterschiedliche Benutzer, Dienstprinzipale oder AD-Gruppen in Ihrer Organisation freigeben.  Speichern Sie Containerimages in Azure Container Registry, und verwenden Sie Azure RBAC, um sicherzustellen, dass nur autorisierte Benutzer auf die Images zugreifen können.  

- [Grundlegendes zu Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Grundlegendes zu Azure RBAC für Container Registry](../../container-registry/container-registry-roles.md)

- [Konfigurieren von Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Übersicht über den Katalog mit freigegebenen Images](../../virtual-machines/windows/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7,7 | 5.4 | Kunde |

Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure-Ressourcen zu erstellen. Sie können auch auf integrierte Richtliniendefinitionen zurückgreifen, die sich auf Ihre spezifischen Ressourcen beziehen.  Zusätzlich können Sie Azure Automation verwenden, um Konfigurationsänderungen bereitzustellen.

- [Konfigurieren und Verwalten von Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Aliase](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7,8 | 5.4 | Kunde |

Azure Automation DSC ist ein Konfigurationsverwaltungsdienst für DSC-Knoten (Desired State Configuration) in jedem beliebigen Cloud- oder lokalen Rechenzentrum. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist. 

- [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.9 | 5.5 | Kunde |

Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen.  Nutzen Sie Azure Policy auch für Warnungen und Überprüfungen in Bezug auf Azure-Ressourcenkonfigurationen.

- [Umsetzen von Empfehlungen in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.10 | 5.5 | Kunde |

Verwenden Sie Azure Security Center, um Baselineüberprüfungen für Betriebssystem- und Docker-Einstellungen für Container durchzuführen.

- [Grundlegendes zu Azure Security Center-Containerempfehlungen](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.11 | Version 13.1 | Kunde |

Verwenden Sie die verwaltete Dienstidentität in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen und zu schützen.

- [Integrieren mit verwalteten Azure-Identitäten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Erstellen einer Key Vault-Instanz](../../key-vault/secrets/quick-create-portal.md)

- [Authentifizieren bei Key Vault](../../key-vault/general/authentication.md)

- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.12 | 4,1 | Kunde |

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Konfigurieren von verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 7.13 | 18.1, 18.7 | Kunde |

Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der nächsten Sicherheitskontrolle:  [Schutz vor Schadsoftware](security-control-malware-defense.md)