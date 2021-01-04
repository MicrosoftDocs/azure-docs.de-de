---
title: Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Active Directory
titleSuffix: Azure Storage
description: Autorisieren Sie den Zugriff auf Azure-Blobs und -Warteschlangen mit Azure Active Directory (Azure AD). Weisen Sie Azure-Rollen für Zugriffsrechte zu. Greifen Sie mit einem Azure AD-Konto auf Daten zu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635397"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory

Azure Storage unterstützt mithilfe von Azure Active Directory (Azure AD) das Autorisieren von Anforderungen an Blob und Queue Storage. Mit Azure AD können Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handeln kann. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann anschließend zum Autorisieren einer Anforderung mit Blob oder Queue Storage verwendet werden.

Die Autorisierung von Anforderungen für Azure Storage mit Azure AD bietet über die Autorisierung mit einem gemeinsam verwalteten Schlüssel überlegene Sicherheit und Benutzerfreundlichkeit. Microsoft empfiehlt die Verwendung der Azure AD-Autorisierung mit Ihren Blob- und Queue-Anwendungen (wenn möglich), um die potenziellen Sicherheitsrisiken zu minimieren, die ein gemeinsam verwendeter Schlüssel birgt.

Die Autorisierung mit Azure AD ist für alle universellen Speicherkonten sowie alle Blob Storage-Konten in allen öffentlichen Regionen und nationalen Clouds verfügbar. Die Azure AD-Autorisierung wird nur für Speicherkonten unterstützt, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden.

Blob Storage unterstützt zudem das Erstellen von Shared Access Signatures (SAS), die mit Azure AD-Anmeldeinformationen unterzeichnet werden. Weitere Informationen finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md).

Azure Files unterstützt die Autorisierung mit AD (Vorschauversion) oder Azure AD DS (allgemein verfügbar) über SMB nur für in die Domäne eingebundene VMs. Informationen zur Verwendung von AD (Vorschauversion) oder Azure AD DS (allgemein verfügbar) über SMB für Azure Files finden Sie unter [Übersicht über die Unterstützung der identitätsbasierten Authentifizierung mit Azure Files für den SMB-Zugriff](../files/storage-files-active-directory-overview.md).

Die Autorisierung mit Azure AD wird für Azure Table Storage nicht unterstützt. Verwenden Sie einen gemeinsam verwalteten Schlüssel zum Autorisieren von Anforderungen an Table Storage.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Übersicht über Azure AD für Blobs und Warteschlangen

Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf eine Blob- oder Warteschlangenressource zuzugreifen, muss die Anforderung autorisiert werden, sofern es sich nicht um ein Blob für den anonymen Zugriff handelt. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess. Zunächst wird die Identität des Sicherheitsprinzipals authentifiziert, und ein OAuth 2.0-Token wird zurückgegeben. Anschließend wird das Token als Teil einer Anforderung an den Blob- oder Warteschlangendienst übergeben und vom Dienst verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

Für den Authentifizierungsschritt ist es erforderlich, dass eine Anwendung zur Laufzeit ein OAuth 2.0-Zugriffstoken anfordert. Wenn eine Anwendung in einer Azure-Entität, z. B. einem virtuellen Azure-Computer, einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf Blobs oder Warteschlangen über eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erfolgen. Informationen zur Autorisierung von Anforderungen über eine verwaltete Identität an den Azure Blob- oder Warteschlangendienst finden Sie unter [Authorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources (Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen)](storage-auth-aad-msi.md).

Der Autorisierungsschritt erfordert, dass dem Sicherheitsprinzipal mindestens eine Azure-Rolle zugewiesen wird. Azure Storage umfasst Azure-Rollen mit gängigen Gruppen von Berechtigungen für Blob- und Warteschlangendaten. Die möglichen Berechtigungen eines Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Weitere Informationen zum Zuweisen von Azure-Rollen für Azure Storage finden Sie unter [Verwalten von Zugriffsrechten für Speicherdaten mithilfe von Azure RBAC](./storage-auth-aad-rbac-portal.md).

Für native Anwendungen und Webanwendungen, die Anforderungen an den Azure Blob- oder Warteschlangendienst senden, kann die Autorisierung auch mit Azure AD erfolgen. Informationen zum Anfordern eines Zugriffstokens und seiner Verwendung zum Autorisieren der Anforderungen für Blob- oder Warteschlangendaten finden Sie unter [Authorize access to Azure Storage with Azure AD from an Azure Storage application (Autorisieren des Zugriffs auf Azure Storage mit Azure AD über eine Azure Storage-Anwendung)](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe in Azure integrierter Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Blob- oder Warteschlangendaten. Außerdem können Sie benutzerdefinierte Rollen für den Zugriff auf Blob- und Warteschlangendaten definieren.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>In Azure integrierte Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Informationen zum Zuweisen einer integrierten Azure-Rolle zu einem Sicherheitsprinzipal finden Sie in den folgenden Artikeln:

- [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](storage-auth-aad-rbac-portal.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](storage-auth-aad-rbac-cli.md)
- [Verwenden des Azure PowerShell-Moduls zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](storage-auth-aad-rbac-powershell.md)

Weitere Informationen dazu, wie integrierte Rollen für Azure Storage definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Zugriffsberechtigungen für Datenvorgänge

Einzelheiten zu den Berechtigungen, die für spezifische Vorgänge des Blob- oder Warteschlangendiensts erforderlich sind, finden Sie unter [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Ressourcenumfang

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Zugreifen auf Daten über ein Azure AD-Konto

Der Zugriff auf Blob- oder Warteschlangendaten über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle kann über das Azure AD-Konto eines Benutzers oder über die Kontozugriffsschlüssel (Autorisierung mit gemeinsam verwendetem Schlüssel) autorisiert werden.

### <a name="data-access-from-the-azure-portal"></a>Datenzugriff über das Azure-Portal

Im Azure-Portal können Sie über Ihr Azure AD-Konto oder die Kontozugriffsschlüssel auf Blob- und Warteschlangendaten in einem Azure Storage-Konto zugreifen. Welches Autorisierungsschema im Azure-Portal verwendet wird, hängt von den Ihnen zugewiesenen Azure-Rollen ab.

Wenn Sie versuchen, auf Blob- oder Warteschlangendaten zuzugreifen, wird im Azure-Portal zunächst überprüft, ob Ihnen eine Azure-Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal der Kontoschlüssel für den Zugriff auf Blob- und Warteschlangendaten per Authentifizierung über gemeinsam verwendete Schlüssel verwendet. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal versucht, über Ihr Azure AD-Konto auf die Daten zuzugreifen.

Für den Zugriff auf Blob- oder Warteschlangendaten über das Azure-Portal mithilfe Ihres Azure AD-Kontos benötigen Sie Berechtigungen für den Zugriff auf Blob- und Warteschlangendaten sowie das Navigieren durch die Ressourcen des Speicherkontos im Azure-Portal. Die integrierten Rollen, die von Azure Storage bereitgestellt werden, gewähren Zugriff auf Blob- und Warteschlangenressourcen, aber nicht auf die Speicherkontoressourcen. Aus diesem Grund erfordert der Zugriff auf das Portal außerdem die Zuweisung einer Azure Resource Manager-Rolle (z. B. [Leser](../../role-based-access-control/built-in-roles.md#reader)) mindestens auf Ebene des Speicherkontos. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel. Weitere Informationen zum Zuweisen von Berechtigungen zu Benutzern für den Datenzugriff im Azure-Portal über ein Azure AD-Konto finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](storage-auth-aad-rbac-portal.md).

Wenn Sie zu einem Container oder einer Warteschlange navigieren, wird im Azure-Portal angegeben, welches Autorisierungsschema verwendet wird. Weitere Informationen zum Datenzugriff im Portal finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md) sowie unter [Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Datenzugriff über PowerShell oder die Azure-Befehlszeilenschnittstelle

In der Azure-Befehlszeilenschnittstelle und PowerShell ist die Anmeldung mit Azure AD-Anmeldeinformationen möglich. Nach der Anmeldung wird Ihre Sitzung unter diesen Anmeldeinformationen ausgeführt. Weitere Informationen finden Sie unter [Ausführen von Azure CLI- oder PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blob- oder Warteschlangendaten](../blobs/authorize-data-operations-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

- [Authorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources (Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen)](storage-auth-aad-msi.md)
- [Authentifizieren mit Azure Active Directory aus einer Anwendung für den Zugriff auf Blobs und Warteschlangen](storage-auth-aad-app.md)
- [Azure Storage support for Azure Active Directory based access control generally available](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/) (Azure Storage-Unterstützung für Azure Active Directory-basierte Zugriffssteuerung allgemein verfügbar)