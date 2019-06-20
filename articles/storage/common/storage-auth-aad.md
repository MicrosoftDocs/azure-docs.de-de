---
title: Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory | Microsoft-Dokumentation
description: Authentifizieren Sie den Zugriff auf Azure-Blobs und -Warteschlangen mit Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147267"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory

Azure Storage unterstützt Authentifizierung und Autorisierung mit Azure Active Directory (AD) für Blob- und Warteschlangendienste. Mit Azure AD können Sie mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) den Zugriff auf Benutzer-, Gruppen- oder Anwendungsdienstprinzipale gewährleisten. 

Die Authentifizierung von Benutzern oder Anwendungen mithilfe von Azure AD-Anmeldeinformationen bietet mehr Sicherheit und Benutzerfreundlichkeit als andere Autorisierungsmethoden. Während Sie weiterhin die Autorisierung mit gemeinsam verwendetem Schlüssel mit Ihren Anwendungen verwenden können, macht Azure AD das Speichern Ihrer Kontozugriffsschlüssel mit Ihrem Code überflüssig. Sie können auch weiterhin Shared Access Signatures für zum Gewähren eines differenzierten Zugriffs auf Ressourcen in Ihrem Speicherkonto verwenden. Azure AD bietet jedoch ähnliche Funktionen, bei denen Sie weder SAS-Token verwalten noch sich um das Widerrufen einer gefährdeten SAS kümmern müssen. Microsoft empfiehlt, nach Möglichkeit die Azure AD-Authentifizierung für Ihre Azure Storage-Anwendungen zu nutzen.

Die Authentifizierung und Autorisierung mit Azure AD-Anmeldeinformationen ist für alle universellen Speicherkonten sowie alle Blob Storage-Konten in allen öffentlichen Regionen und nationalen Clouds verfügbar. Die Azure AD-Autorisierung wird nur für Speicherkonten unterstützt, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Übersicht über Azure AD für Blobs und Warteschlangen

Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf eine Blob- oder Warteschlangenressource zuzugreifen, muss die Anforderung autorisiert werden, sofern es sich nicht um ein Blob für den anonymen Zugriff handelt. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess. Zunächst wird die Identität des Sicherheitsprinzipals authentifiziert, und ein OAuth 2.0-Token wird zurückgegeben. Anschließend wird das Token als Teil einer Anforderung an den Blob- oder Warteschlangendienst übergeben und vom Dienst verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

Für den Authentifizierungsschritt ist es erforderlich, dass eine Anwendung zur Laufzeit ein OAuth 2.0-Zugriffstoken anfordert. Wenn eine Anwendung in einer Azure-Entität, z. B. einem virtuellen Azure-Computer, einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf Blobs oder Warteschlangen über eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erfolgen. Informationen zur Autorisierung von Anforderungen über eine verwaltete Identität an den Azure Blob- oder Warteschlangendienst finden Sie unter [Authentifizieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen](storage-auth-aad-msi.md).

Für den Autorisierungsschritt ist es erforderlich, dem Sicherheitsprinzipal mindestens eine RBAC-Rolle zuzuweisen. Azure Storage umfasst RBAC-Rollen mit gängigen Gruppen von Berechtigungen für Blob- und Warteschlangendaten. Die möglichen Berechtigungen eines Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Weitere Informationen zum Zuweisen von RBAC-Rollen in Azure Storage finden Sie unter [Verwalten der Zugriffsrechte für Azure Storage-Daten mit RBAC](storage-auth-aad-rbac.md).

Für native Anwendungen und Webanwendungen, die Anforderungen an den Azure Blob- oder Warteschlangendienst senden, kann die Authentifizierung auch mit Azure AD erfolgen. Informationen zum Anfordern eines Zugriffstokens und seiner Verwendung zum Autorisieren der Anforderungen für Blob- oder Warteschlangendaten finden Sie unter [Authentifizieren mit Azure AD über eine Azure Storage-Anwendung](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Zuweisen von RBAC-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf abgesicherte Ressourcen über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe integrierter RBAC-Rollen mit üblichen Berechtigungssätzen für den Zugriff auf Blob- und Warteschlangendaten. Außerdem können Sie benutzerdefinierte Rollen für den Zugriff auf Blob- und Warteschlangendaten definieren.

Wenn einem Azure AD-Sicherheitsprinzipal eine RBAC-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Integrierte RBAC-Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Informationen zum Zuweisen einer integrierten RBAC-Rolle zu einem Sicherheitsprinzipal finden Sie in den folgenden Artikeln:

- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über das Azure-Portal](storage-auth-aad-rbac-portal.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über die Azure CLI](storage-auth-aad-rbac-cli.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über PowerShell](storage-auth-aad-rbac-powershell.md)

Weitere Informationen dazu, wie integrierte Rollen für Azure Storage definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Weitere Informationen zum Erstellen benutzerdefinierter RBAC-Rollen finden Sie unter [Erstellen benutzerdefinierter Rollen für die rollenbasierte Zugriffssteuerung in Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Zugriffsberechtigungen für Datenvorgänge

Einzelheiten zu den Berechtigungen, die für spezifische Vorgänge des Blob- oder Warteschlangendiensts erforderlich sind, finden Sie unter [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Ressourcenumfang

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Zugreifen auf Daten über ein Azure AD-Konto

Der Zugriff auf Blob- oder Warteschlangendaten über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle kann über das Azure AD-Konto eines Benutzers oder über die Kontozugriffsschlüssel (Authentifizierung über gemeinsam verwendete Schlüssel) autorisiert werden.

### <a name="data-access-from-the-azure-portal"></a>Datenzugriff über das Azure-Portal

Im Azure-Portal können Sie über Ihr Azure AD-Konto oder die Kontozugriffsschlüssel auf Blob- und Warteschlangendaten in einem Azure Storage-Konto zugreifen. Welches Autorisierungsschema im Azure-Portal verwendet wird, hängt von den RBAC-Rollen ab, die Ihnen zugewiesen sind.

Wenn Sie versuchen, auf Blob- oder Warteschlangendaten zuzugreifen, wird im Azure-Portal zunächst überprüft, ob Ihnen eine RBAC-Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal der Kontoschlüssel für den Zugriff auf Blob- und Warteschlangendaten per Authentifizierung über gemeinsam verwendete Schlüssel verwendet. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal versucht, über Ihr Azure AD-Konto auf die Daten zuzugreifen.

Für den Zugriff auf Blob- oder Warteschlangendaten über das Azure-Portal mithilfe Ihres Azure AD-Kontos benötigen Sie Berechtigungen für den Zugriff auf Blob- und Warteschlangendaten sowie das Navigieren durch die Ressourcen des Speicherkontos im Azure-Portal. Die integrierten Rollen, die von Azure Storage bereitgestellt werden, gewähren Zugriff auf Blob- und Warteschlangenressourcen, aber nicht auf die Speicherkontoressourcen. Aus diesem Grund erfordert der Zugriff auf das Portal außerdem die Zuweisung einer Azure Resource Manager-Rolle (z. B. [Leser](../../role-based-access-control/built-in-roles.md#reader)) mindestens auf Ebene des Speicherkontos. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel. Weitere Informationen zum Zuweisen von Berechtigungen zu Benutzern für den Datenzugriff im Azure-Portal über ein Azure AD-Konto finden Sie unter [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über das Azure-Portal](storage-auth-aad-rbac-portal.md).

Wenn Sie zu einem Container oder einer Warteschlange navigieren, wird im Azure-Portal angegeben, welches Autorisierungsschema verwendet wird. Weitere Informationen zum Datenzugriff im Portal finden Sie unter [Zugreifen auf Blob- oder Warteschlangendaten über das Azure-Portal](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Datenzugriff über PowerShell oder die Azure-Befehlszeilenschnittstelle

In der Azure-Befehlszeilenschnittstelle und PowerShell ist die Anmeldung mit Azure AD-Anmeldeinformationen möglich. Nach der Anmeldung wird Ihre Sitzung unter diesen Anmeldeinformationen ausgeführt. Weitere Informationen finden Sie unter [Ausführen von Azure CLI- oder PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blob- oder Warteschlangendaten](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Azure AD-Authentifizierung per SMB für Azure Files

Azure Files unterstützt die Authentifizierung mit Azure AD über SMB nur für in die Domäne eingebundene virtuelle Computer (Vorschau). Informationen zur Verwendung von Azure AD über SMB für Azure Files finden Sie unter [Übersicht über die Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen](storage-auth-aad-msi.md)
- [Authentifizieren mit Azure Active Directory aus einer Anwendung für den Zugriff auf Blobs und Warteschlangen](storage-auth-aad-app.md)
- [Azure Storage support for Azure Active Directory based access control generally available](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/) (Azure Storage-Unterstützung für Azure Active Directory-basierte Zugriffssteuerung allgemein verfügbar)
