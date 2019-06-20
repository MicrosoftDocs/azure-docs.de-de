---
title: Zugreifen auf Blob- oder Warteschlangendaten über das Azure-Portal – Azure Storage
description: Wenn Sie über das Azure-Portal auf Blob- oder Warteschlangendaten zugreifen, richtet das Portal tatsächlich Anforderungen an Azure Storage. Diese Anforderungen an Azure Storage können entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto authentifiziert und autorisiert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154004"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Zugreifen auf Blob- oder Warteschlangendaten über das Azure-Portal

Wenn Sie über das [Azure-Portal](https://portal.azure.com) auf Blob- oder Warteschlangendaten zugreifen, richtet das Portal tatsächlich Anforderungen an Azure Storage. Diese Anforderungen an Azure Storage können entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto authentifiziert und autorisiert werden. Das Portal zeigt an, welche Authentifizierungsmethode Sie verwenden, und ermöglicht Ihnen, zwischen den beiden zu wechseln, sofern Sie die entsprechenden Berechtigungen haben.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Für den Zugriff auf Blob- oder Warteschlangendaten benötigte Berechtigungen

Je nachdem, wie Sie den Zugriff auf Blob- oder Warteschlangendaten im Azure-Portal authentifizieren möchten, benötigen Sie spezielle Berechtigungen. Meist werden diese Berechtigungen über die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) bereitgestellt. Weitere Informationen dazu finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Kontozugriffsschlüssel

Für den Zugriff auf Blob- und Warteschlangendaten mit dem Kontozugriffsschlüssel muss Ihnen eine RBAC-Rolle zugewiesen sein, die die RBAC-Aktion **Microsoft.Storage/storageAccounts/listkeys/action** einschließt. Bei dieser RBAC-Rolle kann es sich um eine integrierte oder benutzerdefinierte Rolle handeln. Es folgen integrierte Rollen mit Unterstützung für **Microsoft.Storage/storageAccounts/listkeys/action**:

- Die Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner)
- Die Azure Resource Manager-Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor)
- Die Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wenn Sie versuchen, im Azure-Portal auf Blob- oder Warteschlangendaten zuzugreifen, prüft das Portal zunächst, ob Ihnen eine Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, verwendet das Portal den Kontoschlüssel für den Zugriff auf Blob- und Warteschlangendaten. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, versucht das Portal, über Ihr Azure AD-Konto auf Daten zuzugreifen.

> [!NOTE]
> Die zu „Administrator für klassisches Abonnement“ gehörigen Rollen „Dienstadministrator“ und „Co-Administrator“ schließen die Entsprechung der Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) ein. Die Rolle **Besitzer** schließt alle Aktionen einschließlich **Microsoft.Storage/storageAccounts/listkeys/action** ein, sodass ein Benutzer mit einer dieser Administratorrollen auch mit dem Kontoschlüssel auf Blob- und Warteschlangendaten zugreifen kann. Weitere Informationen finden Sie unter [Administrator für klassisches Abonnement](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Azure AD-Konto

Für den Zugriff auf Blob- oder Warteschlangendaten im Azure-Portal über Ihr Azure AD-Konto müssen Sie die beiden folgenden Voraussetzungen erfüllen:

- Ihnen wurde die Azure Resource Manager-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) mindestens bis zur Ebene des Speicherkontos oder höher zugewiesen. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel.
- Ihnen wurde entweder eine integrierte oder benutzerdefinierte Rolle zugewiesen, die den Zugriff auf Blob- oder Warteschlangendaten ermöglicht.

Die Zuweisung der Rolle **Leser** oder einer anderen Azure Resource Manager-Rollen ist notwendig, damit der Benutzer die Ressourcen der Speicherkontenverwaltung im Azure-Portal anzeigen und nutzen kann. Die RBAC-Rollen, die Zugriff auf Blob- oder Warteschlangendaten gewähren, lassen keinen Zugriff auf Ressourcen zur Verwaltung von Speicherkonten zu. Um im Portal auf Blob- oder Warteschlangendaten zugreifen zu können, benötigt der Benutzer Berechtigungen zum Navigieren durch die Ressourcen des Speicherkontos. Weitere Informationen zu dieser Anforderung finden Sie unter [Zuweisen der Rolle „Leser“ für den Zugriff auf das Portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Es folgen die integrierten Rollen, die den Zugriff auf Ihre Blob- oder Warteschlangendaten unterstützen:

- [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Dient zur Steuerung des POSIX-Zugriffs für Azure Data Lake Storage Gen2.
- [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Weist Lese-, Schreib- und Löschberechtigungen für Blobs zu.
- [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Weist ausschließlich Leseberechtigungen für Blobs zu.
- [Mitwirkender an Storage-Warteschlangendaten](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Weist Lese-, Schreib- und Löschberechtigungen für Warteschlangen zu.
- [Storage-Warteschlangendatenleser](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Weist ausschließlich Leseberechtigungen für Warteschlangen zu.
    
Benutzerdefinierte Rollen können verschiedene Kombinationen der von den integrierten Rollen gebotenen Berechtigungen unterstützen. Weitere Informationen zum Erstellen benutzerdefinierter RBAC-Rollen finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](../../role-based-access-control/custom-roles.md) und [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Das Auflisten von Warteschlangen wird von der Rolle „Administrator für klassisches Abonnement“ nicht unterstützt. Um Warteschlangen auflisten zu können, muss einem Benutzer die Azure Resource Manager-Rolle **Leser**, die Rolle **Storage-Warteschlangendatenleser** oder die Rolle **Mitwirkender an Storage-Warteschlangendaten** zugewiesen sein.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigieren zu Blobs oder Warteschlangen im Portal

Um Blob- oder Warteschlangendaten im Portal anzuzeigen, navigieren Sie zur **Übersicht** Ihres Speicherkontos und klicken auf die Links zu **Blobs** oder **Warteschlangen**. Alternativ können Sie im Menü zu den Abschnitten **Blob-Dienst** und **Warteschlangendienst** navigieren. 

![Navigieren zu Blob- oder Warteschlangendaten im Azure-Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Bestimmen der aktuellen Authentifizierungsmethode

Wenn Sie zu einem Container oder einer Warteschlange navigieren, zeigt das Azure-Portal an, ob Sie zur Authentifizierung derzeit den Kontozugriffsschlüssel oder Ihr Azure AD-Konto verwenden.

Die Beispiele in diesem Abschnitt zeigen den Zugriff auf einen Container und dessen Blobs, aber das Portal zeigt die gleiche Meldung an, wenn Sie auf eine Warteschlange und deren Nachrichten zugreifen oder Warteschlangen auflisten.

### <a name="account-access-key"></a>Kontozugriffsschlüssel

Wenn Sie sich mit dem Kontozugriffsschlüssel authentifizieren, wird im Portal **Zugriffsschlüssel** als Authentifizierungsmethode angegeben:

![Derzeit erfolgt der Zugriff auf Containerdaten mit dem Kontoschlüssel](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Um zur Verwendung des Azure AD-Kontos zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie über die Ihnen zugewiesenen RBAC-Rollen die entsprechenden Berechtigungen haben, können Sie fortfahren. Wenn Ihnen jedoch die benötigten Berechtigungen fehlen, erhalten Sie eine Fehlermeldung wie die folgende:

![Angezeigter Fehler, wenn das Azure AD-Konto den Zugriff nicht unterstützt](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Beachten Sie, dass in der Liste keine Blobs enthalten sind, wenn Ihrem Azure AD-Konto die Berechtigung zu ihrer Anzeige fehlt. Klicken Sie auf den Link **Zum Zugriffsschlüssel wechseln**, um erneut den Zugriffsschlüssel für die Authentifizierung zu nutzen.

### <a name="azure-ad-account"></a>Azure AD-Konto

Wenn Sie sich mit Ihrem Azure AD-Konto authentifizieren, wird im Portal **Azure AD-Benutzerkonto** als Authentifizierungsmethode angegeben:

![Derzeit erfolgt der Zugriff auf Containerdaten mit dem Azure AD-Konto](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Um zur Verwendung des Zugriffsschlüssels zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie Zugriff auf den Kontoschlüssel haben, können Sie fortfahren. Wenn Sie keinen Zugriff auf den Kontoschlüssel haben, erhalten Sie eine Fehlermeldung wie die folgende:

![Angezeigter Fehler, wenn Sie keinen Zugriff auf den Kontoschlüssel haben](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Beachten Sie, dass in der Liste keine Blobs enthalten sind, wenn Sie keinen Zugriff auf die Kontoschlüssel haben. Klicken Sie auf den Link **Zum Azure AD-Benutzerkonto wechseln**, um erneut das Azure AD-Konto für die Authentifizierung zu nutzen.

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](storage-auth-aad.md)
- [Gewähren von Zugriff auf Azure-Container und -Warteschlangen im Azure-Portal mithilfe von RBAC](storage-auth-aad-rbac-portal.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über die Azure CLI](storage-auth-aad-rbac-cli.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über PowerShell](storage-auth-aad-rbac-powershell.md)
