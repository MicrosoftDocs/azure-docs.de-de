---
title: 'Schützen des Zugriffs auf ein verwaltetes HSM: Verwaltetes HSM von Azure Key Vault'
description: Hier erfahren Sie, wie Sie den Zugriff auf ein verwaltetes HSM mithilfe von Azure RBAC und lokaler RBAC für verwaltete HSMs schützen.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 99918d039052c9913400b85ac3caa4a1a5481155
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445318"
---
# <a name="secure-access-to-your-managed-hsms"></a>Schützen des Zugriffs auf Ihre verwalteten HSMs

Verwaltetes HSM von Azure Key Vault ist ein Clouddienst zum Schutz von Verschlüsselungsschlüsseln. Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, muss der Zugriff auf Ihre verwalteten HSMs geschützt werden, sodass nur autorisierte Anwendungen und Benutzer darauf zugreifen können. Dieser Artikel enthält eine Übersicht über das Zugriffssteuerungsmodell für verwaltete HSMs. Er erläutert die Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre verwalteten HSMs schützen.

In diesem Tutorial erfahren Sie anhand eines einfachen Beispiels, wie Sie Aufgabentrennung und Zugriffssteuerung mithilfe von Azure RBAC und lokaler RBAC für verwaltete HSMs erreichen. Weitere Informationen zum Zugriffssteuerungsmodell für verwaltete HSMs finden Sie unter [Zugriffssteuerung für verwaltetes HSM](access-control.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
* Azure-Befehlszeilenschnittstelle ab Version 2.12.0. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).
* Ein verwaltetes HSM in Ihrem Abonnement. Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md). Dort erfahren Sie, wie Sie ein verwaltetes HSM bereitstellen und aktivieren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

Weitere Informationen zu den Anmeldeoptionen für die Befehlszeilenschnittstelle finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="example"></a>Beispiel

In diesem Beispiel wird eine Anwendung entwickelt, die einen RSA-Schlüssel mit 2.048 Bit für Signierungsvorgänge verwendet. Die Anwendung wird auf einem virtuellen Azure Computer (virtual machine, VM) mit [verwalteter Identität](../../active-directory/managed-identities-azure-resources/overview.md) ausgeführt. Der zum Signieren verwendete RSA-Schlüssel wird im verwalteten HSM gespeichert.

Folgende Rollen kümmern sich um die Verwaltung, Bereitstellung und Überwachung der Anwendung:

- **Sicherheitsteam:** IT-Mitarbeiter aus dem Büro des CSO (Chief Security Officer, leitender Sicherheitsbeauftragter) oder Mitwirkende in vergleichbarer Position. Das Sicherheitsteam ist für die sichere Aufbewahrung von Schlüsseln verantwortlich: RSA-oder EC-Schlüssel zum Signieren und RSA- oder AES-Schlüssel für die Datenverschlüsselung.
- **Entwickler und Operatoren**: Die Mitarbeiter, die die Anwendung entwickeln und in Azure bereitstellen. Die Mitglieder dieses Teams sind nicht Teil des Sicherheitspersonals. Sie sollten keinen Zugriff auf vertrauliche Daten wie RSA-Schlüssel haben. Nur die von ihnen bereitgestellte Anwendung sollte auf diese vertraulichen Daten zugreifen können.
- **Prüfer:** Diese Rolle ist für Mitwirkende gedacht, die nicht Mitglieder der Entwicklung oder des allgemeinen IT-Personals sind. Sie prüft die Verwendung und Verwaltung von Zertifikaten, Schlüsseln sowie Geheimnissen und achtet auf die Einhaltung der Datensicherheitsstandards.

Es gibt noch eine weitere Rolle, die außerhalb des Anwendungsbereichs unserer Anwendung liegt: den Administrator für Abonnements (oder Ressourcengruppen). Der Abonnementadministrator richtet die anfänglichen Zugriffsberechtigungen für das Sicherheitsteam ein. Sie gewähren dem Sicherheitsteam Zugriff, indem sie eine Ressourcengruppe verwenden, die über die von der Anwendung benötigten Ressourcen verfügt.

Wir müssen die folgenden Vorgänge für unsere Rollen autorisieren:

**Sicherheitsteam**
- Erstellen des verwalteten HSM
- Herunterladen der Sicherheitsdomäne des verwalteten HSM (für die Notfallwiederherstellung)
- Aktivieren Sie die Protokollierung.
- Generieren oder Importieren von Schlüsseln
- Erstellen der Sicherungen des verwalteten HSM für die Notfallwiederherstellung
- Festlegen der lokalen RBAC für das verwaltete HSM, um Benutzern und Anwendungen Zugriff für bestimmte Vorgänge zu gewähren
- Regelmäßiges Austauschen der Schlüssel

**Entwickler und Operatoren**
- Beziehen des Verweises (Schlüssel-URI) vom Sicherheitsteam für den zum Signieren verwendeten RSA-Schlüssel
- Entwickeln und Bereitstellen der Anwendung, die programmgesteuert auf die Schlüssel zugreift

**Prüfer**
- Überprüfen des Ablaufdatums der Schlüssel, um sicherzustellen, dass die Schlüssel auf dem neuesten Stand sind
- Überwachen von Rollenzuweisungen, um sicherzustellen, dass nur autorisierte Benutzer/Anwendungen auf Schlüssel zugreifen können
- Überprüfen der Protokolle für das verwaltete HSM, um die ordnungsgemäße Verwendung von Schlüsseln gemäß Datensicherheitsstandards sicherzustellen

Die folgende Tabelle enthält eine Zusammenfassung der Rollenzuweisungen für Teams und Ressourcen für den Zugriff auf das verwaltete HSM:

| Role | Verwaltungsebenenrolle | Datenebenenrolle |
| --- | --- | --- |
| Sicherheitsteam | Mitwirkender für verwaltete HSMs | Administrator für verwaltete HSMs |
| Entwickler und Bediener | Keine | Keine |
| Prüfer | Keine | Kryptografieprüfer für verwaltete HSMs |
| Verwaltete Identität des virtuellen Computers, der von der Anwendung verwendet wird| Keine | Kryptografiebenutzer für verwaltete HSMs |
| Verwaltete Identität des Speicherkontos, das von der Anwendung verwendet wird| Keine| Dienstverschlüsselung für verwaltete HSMs |

Die drei Teamrollen benötigen neben Berechtigungen für das verwaltete HSM auch Zugriff auf andere Ressourcen. Damit Entwickler oder Operatoren VMs (oder das Web-Apps-Feature von Azure App Service) bereitstellen können, müssen diese als `Contributor` auf diese Ressourcentypen zugreifen können. Prüfer benötigen Lesezugriff auf das Speicherkonto mit den gespeicherten Protokollen für das verwaltete HSM.

Für die Zuweisung von Verwaltungsebenenrollen (Azure RBAC) können Sie das Azure-Portal oder eine andere Verwaltungsschnittstelle wie etwa die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwenden. Für die Zuweisung von Datenebenenrollen für das verwaltete HSM muss die Azure-Befehlszeilenschnittstelle verwendet werden.

Bei den Ausschnitten für die Azure-Befehlszeilenschnittstelle in diesem Abschnitt wird Folgendes angenommen:

- Der Azure Active Directory-Administrator hat Sicherheitsgruppen erstellt, die die drei Rollen darstellen: Contoso-Sicherheitsteam, Contoso-App-DevOps und Contoso-App-Prüfer. Der Administrator hat Benutzer den jeweiligen Gruppen hinzugefügt.
- Alle Ressourcen befinden sich in der Ressourcengruppe **ContosoAppRG**.
- Die Protokolle des verwalteten HSM sind im Speicherkonto **contosologstorage** gespeichert.
- Das verwaltete HSM **ContosoMHSM** und das Speicherkonto **contosologstorage** befinden sich am gleichen Azure-Standort.

Der Abonnementadministrator weist dem Sicherheitsteam die Rolle `Managed HSM Contributor` (Mitwirkender für verwaltete HSMs) zu. Mit dieser Rolle kann das Sicherheitsteam vorhandene verwaltete HSMs verwalten und neue verwaltete HSMs erstellen. Wenn bereits verwaltete HSMs vorhanden sind, muss ihnen die Rolle „Administrator für verwaltete HSMs“ zugewiesen werden, um deren Verwaltung zu ermöglichen.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Das Sicherheitsteam richtet die Protokollierung ein und weist den Prüfern und der VM-Anwendung Rollen zu.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

In diesem Tutorial werden hauptsächlich Aktionen gezeigt, die für die Zugriffssteuerung relevant sind. Weitere Aktionen und Vorgänge im Zusammenhang mit der Bereitstellung von Anwendungen auf Ihrem virtuellen Computer, der Aktivierung der Verschlüsselung mit kundenseitig verwaltetem Schlüssel für ein Speicherkonto und der Erstellung verwalteter HSMs werden hier nicht gezeigt, da bei diesem Beispiel die Zugriffssteuerung und Rollenverwaltung im Mittelpunkt steht.

Bei diesem Beispiel handelt es sich um ein einfaches Szenario. Echte Szenarien können komplexer sein. Sie können die Berechtigungen für Ihren Schlüsseltresor entsprechend Ihren Anforderungen anpassen. Wir gingen davon aus, dass das Sicherheitsteam die Schlüssel- und Geheimnisverweise (URIs und Fingerabdrücke) bereitstellt, die von den DevOps-Mitarbeitern in ihren Anwendungen verwendet werden. Entwickler und Betreiber benötigen keinen Zugriff auf die Datenebene. Wir konzentrierten uns auf das Sichern Ihres Schlüsseltresors. Ähnliche Überlegungen sollten Sie für den Schutz Ihrer [virtuellen Computer](https://azure.microsoft.com/services/virtual-machines/security/), [Speicherkonten](../../storage/blobs/security-recommendations.md) und anderen Azure-Ressourcen anstellen.

## <a name="resources"></a>Ressourcen

- [Azure RBAC-Dokumentation](../../role-based-access-control/overview.md)
- [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).
- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Nächste Schritte

Ein Tutorial mit ersten Schritten für Administratoren finden Sie unter [Was ist verwaltetes HSM von Azure Key Vault? (Vorschauversion)](overview.md).

Weitere Informationen zur Verwendungsprotokollierung für verwaltete HSMs finden Sie unter [Protokollierung für verwaltete HSMs](logging.md).
