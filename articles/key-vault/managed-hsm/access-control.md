---
title: Zugriffssteuerung für verwaltetes Azure-HSM
description: Verwalten Sie Zugriffsberechtigungen für verwaltetes Azure-HSM und für Schlüssel. In diesem Artikel wird das Authentifizierungs- und Autorisierungsmodell für verwaltetes HSM und das Schützen Ihrer HSMs behandelt.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 816941fe0ec3a81c41da56acedcedf2de7febe74
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445233"
---
# <a name="managed-hsm-access-control"></a>Zugriffssteuerung für verwaltetes HSM

> [!NOTE]
> Der Key Vault-Ressourcenanbieter unterstützt zwei Ressourcentypen: **Tresore** und **verwaltete HSMs**. Die in diesem Artikel beschriebene Zugriffssteuerung gilt nur für **verwaltete HSMs**. Weitere Informationen zur Zugriffssteuerung für verwaltete HSM finden Sie unter [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../general/rbac-guide.md).

Verwaltetes HSM von Azure Key Vault ist ein Clouddienst zum Schutz von Verschlüsselungsschlüsseln. Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, muss der Zugriff auf Ihre verwalteten HSMs geschützt werden, sodass nur autorisierte Anwendungen und Benutzer darauf zugreifen können. Dieser Artikel enthält eine Übersicht über das Zugriffssteuerungsmodell für verwaltete HSMs. Er erläutert die Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre verwalteten HSMs schützen.

## <a name="access-control-model"></a>Zugriffssteuerungsmodell

Der Zugriff auf ein verwaltetes HSM wird über zwei Schnittstellen gesteuert: die **Verwaltungsebene** und die **Datenebene**. Die Verwaltungsebene dient zum Verwalten des HSMs selbst. Zu den Vorgängen auf dieser Ebene gehören das Erstellen und Löschen von verwalteten HSMs und das Abrufen von verwalteten HSM-Eigenschaften. Auf der Datenebene arbeiten Sie mit den Daten, die in einem verwalteten HSM gespeichert sind, d. h. mit HSM-gestützten Verschlüsselungsschlüsseln. Sie können Schlüssel hinzufügen, löschen, ändern und mithilfe von Schlüsseln kryptografische Vorgänge durchführen, Rollenzuweisungen zur Zugriffssteuerung für die Schlüssel verwalten, eine vollständige HSM-Sicherung erstellen, eine vollständige Sicherung wiederherstellen und die Sicherheitsdomäne über die Datenebenenschnittstelle verwalten.

Um auf ein verwaltetes HSM in einer der beiden Ebenen zugreifen zu können, müssen alle Aufrufer über eine ordnungsgemäße Authentifizierung und Autorisierung verfügen. Die Authentifizierung stellt die Identität des Anrufers fest. Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. Ein Aufrufer kann einer der [Sicherheitsprinzipale](../../role-based-access-control/overview.md#security-principal) sein, die in Azure Active Directory definiert sind: Benutzer, Gruppe, Dienstprinzipal oder verwaltete Identität.

Für die Authentifizierung verwenden beide Ebenen Azure Active Directory. Für die Autorisierung verwenden sie wie folgt verschiedene Systeme:
- Die Verwaltungsebene verwendet die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), ein Autorisierungssystem, das auf Azure Resource Manager basiert. 
- Die Datenebene verwendet ein RBAC auf Ebene des verwalteten HSMs (Lokale RBAC für verwaltetes HSM), ein Autorisierungssystem, das auf der Ebene verwalteter HSMs implementiert und erzwungen wird.

Wenn ein verwaltetes HSM erstellt wird, stellt der Anforderer auch eine Liste von Datenebenenadministratoren bereit (es werden alle [Sicherheitsprinzipale](../../role-based-access-control/overview.md#security-principal) unterstützt). Nur diese Administratoren können auf die Datenebene des verwalteten HSMs zuzugreifen, um wichtige Vorgänge durchzuführen und die Rollenzuweisungen der Datenebene zu verwalten (Lokale RBAC für verwaltetes HSM).

Das Berechtigungsmodell für beide Ebenen verwendet dieselbe Syntax, aber sie werden auf verschiedenen Ebenen erzwungen, und die Rollenzuweisungen verwenden unterschiedliche Bereiche. Auf Verwaltungsebene wird Azure RBAC von Azure Resource Manager erzwungen, während auf der Datenebene vom verwalteten HSM selbst die lokale RBAC des verwalteten HSM durchgesetzt wird.

> [!IMPORTANT]
> Die Gewährung des Zugriffs auf die Verwaltungsebene eines verwalteten HSMs für einen Sicherheitsprinzipalen ermöglicht ihnen keinen Zugriff auf die Datenebene für den Zugriff auf Schlüssel oder Rollenzuweisungen der Datenebene (Lokale RBAC für verwaltetes HSM). Diese Isolation ist so konzipiert, dass eine unbeabsichtigte Erweiterung von Berechtigungen, die den Zugriff auf in verwaltetem HSM gespeicherte Schlüssel beeinträchtigen, verhindert wird.

So kann z. B. ein Abonnementadministrator (da er die Berechtigung „Mitwirkender“ für alle Ressourcen im Abonnement besitzt) ein verwaltetes HSM in seinem Abonnement löschen. Wenn er jedoch keinen Datenebenenzugriff hat, der speziell über die lokale RBAC des verwalteten HSM gewährt wird, kann er keinen Zugriff auf Schlüssel erhalten oder die Rollenzuweisung im verwalteten HSM verwalten, um sich selbst oder anderen Zugriff auf die Datenebene zu gewähren.

## <a name="azure-active-directory-authentication"></a>Authentifizierung über Azure Active Directory

Wenn Sie in einem Azure-Abonnement ein verwaltetes HSM erstellen, wird es automatisch mit dem Azure Active Directory-Mandanten des Abonnements verknüpft. Alle Aufrufer in beiden Ebenen müssen bei diesem Mandanten registriert sein und sich authentifizieren, um auf das verwaltete HSM zugreifen zu können.

Die Anwendung authentifiziert sich mit Azure Active Directory, bevor sie eine der beiden Ebenen aufruft. Die Anwendung kann eine beliebige [unterstützte Authentifizierungsmethode](../../active-directory/develop/authentication-vs-authorization.md) verwenden, die auf dem Anwendungstyp basiert. Die Anwendung erwirbt ein Token für eine Ressource in der Ebene, um Zugriff zu erhalten. Die Ressource ist ein Endpunkt in der Verwaltungs- oder Datenebene, basierend auf der Azure-Umgebung. Anschließend sendet die Anwendung unter Verwendung des Tokens eine REST-API-Anforderung an den verwalteten HSM-Endpunkt. Weitere Informationen finden Sie in der [Gesamtdarstellung des Authentifizierungsablaufs](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Die Verwendung eines einzigen Authentifizierungsmechanismus für beide Ebenen hat mehrere Vorteile:

- Organisationen können den Zugriff auf alle verwalteten HSMs in ihrer Organisation zentral steuern.
- Wenn ein Benutzer aus der Organisation ausscheidet, verliert er umgehend den Zugriff auf alle verwalteten HSMs in der Organisation.
- Organisationen können die Authentifizierung über die Optionen in Azure Active Directory anpassen und so beispielsweise die mehrstufige Authentifizierung aktivieren, um die Sicherheit zu verbessern.

## <a name="resource-endpoints"></a>Ressourcenendpunkte

Sicherheitsprinzipale greifen über Endpunkte auf die Ebenen zu. Die Zugriffssteuerung für die beiden Ebenen arbeiten voneinander unabhängig. Um einer Anwendung Zugriff auf die Verwendung von Schlüsseln in einem verwalteten HSM zu gewähren, gestatten Sie den Zugriff auf die Datenebene mithilfe der lokalen RBAC für verwaltetes HSM. Um einem Benutzer Zugriff auf verwaltete HSM-Ressourcen zu gewähren, um die verwalteten HSMs zu erstellen, zu lesen, zu löschen, zu verschieben und andere Eigenschaften und Tags zu bearbeiten, verwenden Sie Azure RBAC.

Die folgende Tabelle zeigt die Endpunkte für die Verwaltungs- und Datenebene.

| Zugriffs&nbsp;ebene | Zugriffsendpunkte | Operationen (Operations) | Zugriffssteuerungsmechanismus |
| --- | --- | --- | --- |
| Verwaltungsebene | **Global:**<br> management.azure.com:443<br> | Erstellen, Lesen, Aktualisieren, Löschen und Verschieben von verwalteten HSMs<br>Festlegen von verwalteten HSM-Tags | Azure RBAC |
| Datenebene | **Global:**<br> &lt;hsm-name&gt;.vault.azure.net:443<br> | **Schlüssel**: decrypt, encrypt,<br> unwrap, wrap, verify, sign, get, list, update, create, import, delete, backup, restore, purge<br/><br/> **Rollenverwaltung auf Datenebene (Lokale RBAC für verwaltetes HSM):** _Rollendefinitionen auflisten, Rollen zuweisen, Rollenzuweisungen löschen, benutzerdefinierte Rollen definieren<br/><br/>_ *Sicherung/Wiederherstellung: **Sicherung, Wiederherstellung, Status von Sicherungs-/Wiederherstellungsvorgängen überprüfen <br/><br/>** Sicherheitsdomäne:** Sicherheitsdomäne herunter- und hochladen | Lokale RBAC für verwaltetes HSM |
|||||
## <a name="management-plane-and-azure-rbac"></a>Verwaltungsebene und Azure RBAC

In der Verwaltungsebene autorisieren Sie mit Azure RBAC die Vorgänge, die ein Anrufer ausführen darf. Im Azure RBAC-Modell verfügt jedes Azure-Abonnement über eine Instanz von Azure Active Directory. Über dieses Verzeichnis gewähren Sie Benutzern, Gruppen und Anwendungen Zugriff. Der Zugriff wird gewährt, um Ressourcen im Azure-Abonnement zu verwalten, die das Bereitstellungsmodell von Azure Resource Manager verwenden. Um den Zugriff zu gewähren, können Sie das [Azure-Portal](https://portal.azure.com/), die [Azure CLI](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs) oder die [Azure Resource Manager-REST-APIs](/rest/api/authorization/roleassignments) verwenden.

Mit Azure Active Directory erstellen Sie einen Schlüsseltresor in einer Ressourcengruppe und steuern den Zugriff. So können Sie Benutzern oder einer Gruppe die Verwaltung von Schlüsseltresoren in einer Ressourcengruppe ermöglichen. Sie gewähren den Zugriff auf eine bestimmte Bereichsebene, indem Sie entsprechende Azure-Rollen zuordnen. Um einem Benutzer Zugriff für die Verwaltung von Schlüsseltresoren zu gewähren, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle `key vault Contributor` zu. Die folgenden Bereichsebenen können einer Azure-Rolle zugeordnet werden:

- **Verwaltungsgruppe**:  Eine auf Abonnementebene zugewiesene Azure-Rolle gilt für alle Abonnements in dieser Verwaltungsgruppe.
- **Abonnement**: Eine auf Abonnementebene zugewiesene Azure-Rolle gilt für alle Ressourcengruppen und Ressourcen innerhalb des Abonnements.
- **Ressourcengruppe**: Eine auf Ressourcengruppenebene zugewiesene Azure-Rolle gilt für alle Ressourcen in der Ressourcengruppe.
- **Bestimmte Ressourcen**: Eine für eine bestimmte Ressource zugewiesene Azure-Rolle gilt für diese Ressource. In diesem Fall ist die Ressource ein bestimmter Schlüsseltresor.

Es gibt verschiedene vordefinierte Rollen. Wenn eine vordefinierte Rolle nicht Ihren Anforderungen entspricht, können Sie Ihre eigene Rolle definieren. Weitere Informationen finden Sie unter [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Datenebene und lokale RBAC für verwaltetes HSM

Sie gewähren einem Sicherheitsprinzipal Zugriff zur Ausführung bestimmter Schlüsselvorgänge, indem Sie ihm eine Rolle zuweisen. Für jede Rollenzuweisung müssen Sie eine Rolle und einen Bereich angeben, für den diese Zuweisung gilt. Für lokale RBAC für verwaltetes HSM stehen zwei Bereiche zur Verfügung.

- **„/“ oder „/keys“** : HSM-Ebenenbereich Sicherheitsprinzipale, denen eine Rolle in diesem Bereich zugewiesen wurde, können die in der Rolle definierten Vorgänge für alle Objekte (Schlüssel) im verwalteten HSM ausführen.
- **„/keys/&lt;Schlüsselname&gt;“** : Schlüsselebenenbereich Sicherheitsprinzipale, denen eine Rolle in diesem Bereich zugewiesen wurde, können die in dieser Rolle definierten Vorgänge nur für alle Versionen des angegebenen Schlüssels ausführen.

## <a name="next-steps"></a>Nächste Schritte

- Ein Tutorial mit ersten Schritten für Administratoren finden Sie unter [Was ist verwaltetes HSM von Azure Key Vault? (Vorschauversion)](overview.md).
- Ein Tutorial zur Rollenverwaltung finden Sie unter [Lokale RBAC für verwaltetes HSM](role-management.md).
- Weitere Informationen zur Verwendungsprotokollierung für verwaltete HSMs finden Sie unter [Protokollierung für verwaltete HSMs](logging.md).