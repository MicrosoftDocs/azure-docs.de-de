---
title: Verwenden des Azure Storage-Ressourcenanbieters für den Zugriff auf Verwaltungsressourcen
description: Der Azure Storage-Ressourcenanbieter ist ein Dienst, der Zugriff auf Verwaltungsressourcen für Azure Storage bietet. Mit dem Azure Storage-Ressourcenanbieter können Sie Ressourcen wie Speicherkonten, private Endpunkte und Kontozugriffsschlüssel erstellen, aktualisieren, verwalten und löschen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789674"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Verwenden des Azure Storage-Ressourcenanbieters für den Zugriff auf Verwaltungsressourcen

Azure Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Der Azure Storage-Ressourcenanbieter ist ein Dienst, der auf Azure Resource Manager basiert und Zugriff auf Verwaltungsressourcen für Azure Storage bietet. Mit dem Azure Storage-Ressourcenanbieter können Sie Ressourcen wie Speicherkonten, private Endpunkte und Kontozugriffsschlüssel erstellen, aktualisieren, verwalten und löschen. Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Mit dem Azure Storage-Ressourcenanbieter können Sie Aktionen wie das Erstellen oder Löschen eines Speicherkontos oder das Abrufen einer Liste von Speicherkonten in einem Abonnement ausführen. Verwenden Sie Azure Active Directory (Azure AD) zum Autorisieren von Anforderungen an den Azure Storage-Ressourcenanbieter. In diesem Artikel wird beschrieben, wie Berechtigungen für Verwaltungsressourcen zugewiesen werden. Außerdem wird auf Beispiele verwiesen, die veranschaulichen, wie Anforderungen an den Azure Storage-Ressourcenanbieter gestellt werden.

## <a name="management-resources-versus-data-resources"></a>Verwaltungsressourcen im Vergleich zu Datenressourcen

Microsoft stellt zwei REST-APIs für das Arbeiten mit Azure Storage-Ressourcen bereit. Diese APIs bilden die Grundlage für alle Aktionen, die Sie für Azure Storage ausführen können. Die Azure Storage-REST-API ermöglicht Ihnen das Arbeiten mit Daten in Ihrem Speicherkonto, einschließlich Blob-, Warteschlangen-, Datei- und Tabellendaten. Die REST-API des Azure Storage-Ressourcenanbieters ermöglicht Ihnen das Arbeiten mit dem Speicherkonto und zugehörigen Ressourcen.

Für eine Anforderung, die Blobdaten liest oder schreibt, sind andere Berechtigungen erforderlich als für eine Anforderung, die einen Verwaltungsvorgang ausführt. Azure RBAC ermöglicht eine differenzierte Steuerung der Berechtigungen für beide Arten von Ressourcen. Wenn Sie einem Sicherheitsprinzipal eine Azure-Rolle zuweisen, stellen Sie sicher, dass Sie genau wissen, welche Berechtigungen diesem Prinzipal erteilt werden. Eine ausführliche Referenz mit Beschreibungen der Aktionen, die den einzelnen integrierten Azure-Rollen zugeordnet sind, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Azure Storage unterstützt die Verwendung von Azure AD zum Autorisieren von Anforderungen an Blob Storage und Queue Storage. Informationen zu Azure-Rollen für Datenvorgänge in Blobs und Warteschlangen finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Zuweisen von Verwaltungsberechtigungen mit der rollenbasierten Zugriffskontrolle von Azure (Azure RBAC)

Jedes Azure-Abonnement verfügt über ein zugeordnetes Azure Active Directory, mit dem Benutzer, Gruppen und Anwendungen verwaltet werden. Ein Benutzer, eine Gruppe oder eine Anwendung wird im Kontext der [Microsoft Identity Platform](../../active-directory/develop/index.yml) auch als Sicherheitsprinzipal bezeichnet. Sie können einem Sicherheitsprinzipal, der in Active Directory definiert ist, Zugriff auf Ressourcen in einem Abonnement gewähren, indem Sie die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) verwenden.

Wenn Sie einem Sicherheitsprinzipal eine Azure-Rolle zuweisen, geben Sie auch den Bereich an, in dem die von der Rolle gewährten Berechtigungen gültig sind. Bei Verwaltungsvorgängen können Sie eine Rolle auf Ebene des Abonnements, der Ressourcengruppe oder des Speicherkontos zuweisen. Sie können einem Sicherheitsprinzipal eine Azure-Rolle über das [Azure-Portal](https://portal.azure.com/), die [Azure CLI-Tools](/cli/azure/install-classic-cli), [PowerShell](/powershell/azure/) oder die [REST-API des Azure Storage-Ressourcenanbieters](/rest/api/storagerp) zuweisen.

Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md) und unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Administratorrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Integrierte Rollen für Verwaltungsvorgänge

Azure bietet integrierte Rollen, mit denen Berechtigungen zum Aufrufen von Verwaltungsvorgängen erteilt werden. Azure Storage bietet außerdem integrierte Rollen speziell für die Verwendung mit dem Azure Storage-Ressourcenanbieter.

Integrierte Rollen, mit denen Berechtigungen zum Aufrufen von Speicherverwaltungsvorgängen erteilt werden, umfassen die in der folgenden Tabelle beschriebenen Rollen:

|    Azure-Rolle    |    BESCHREIBUNG    |    Beinhaltet den Zugriff auf Kontoschlüssel?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Besitzer** | Kann alle Speicherressourcen und den Zugriff auf Ressourcen verwalten.  | Ja. Bietet Berechtigungen zum Anzeigen und erneuten Generieren der Speicherkontoschlüssel. |
| **Mitwirkender**  | Kann alle Speicherressourcen, aber nicht den Zugang zu Ressourcen verwalten. | Ja. Bietet Berechtigungen zum Anzeigen und erneuten Generieren der Speicherkontoschlüssel. |
| **Leser** | Kann Informationen über das Speicherkonto, aber nicht die Kontoschlüssel anzeigen. | Nein. |
| **Mitwirkender von Speicherkonto** | Kann das Speicherkonto verwalten, Informationen zu Ressourcengruppen und Ressourcen des Abonnements abrufen sowie Bereitstellungen von Abonnementressourcengruppen erstellen und verwalten. | Ja. Bietet Berechtigungen zum Anzeigen und erneuten Generieren der Speicherkontoschlüssel. |
| **Benutzerzugriffsadministrator** | Kann den Zugriff auf das Speicherkonto verwalten.   | Ja. Gestattet einem Sicherheitsprinzipal das Zuweisen von Berechtigungen für sich selbst und andere. |
| **Mitwirkender von virtuellen Computern** | Kann virtuelle Computer verwalten, jedoch nicht das Speicherkonto, mit dem sie verbunden sind.   | Ja. Bietet Berechtigungen zum Anzeigen und erneuten Generieren der Speicherkontoschlüssel. |

In der dritten Tabellenspalte ist angegeben, ob die integrierte Rolle die Aktion **Microsoft.Storage/storageAccounts/listkeys/action** unterstützt. Mit dieser Aktion werden Berechtigungen zum Lesen und erneuten Generieren der Speicherkontoschlüssel erteilt. Berechtigungen für den Zugriff auf Azure Storage-Verwaltungsressourcen umfassen keine Berechtigungen für den Datenzugriff. Wenn ein Benutzer jedoch Zugriff auf die Kontoschlüssel hat, kann er mithilfe der Kontoschlüssel über die Autorisierung mit gemeinsam verwendetem Schlüssel auf Azure Storage-Daten zugreifen.

### <a name="custom-roles-for-management-operations"></a>Benutzerdefinierte Rollen für Verwaltungsvorgänge

Azure unterstützt auch das Festlegen benutzerdefinierter Azure-Rollen für den Zugriff auf Verwaltungsressourcen. Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Codebeispiele

Codebeispiele, in denen das Autorisieren und Aufrufen von Verwaltungsvorgängen aus den Azure Storage-Verwaltungsbibliotheken gezeigt wird, sind in den folgenden Beispielen enthalten:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung

Das Resource Manager-Bereitstellungsmodell und das klassische Bereitstellungsmodell sind zwei unterschiedliche Methoden zum Bereitstellen und Verwalten Ihrer Azure-Lösungen. Microsoft empfiehlt beim Erstellen eines neuen Speicherkontos die Verwendung des Azure Resource Manager-Bereitstellungsmodells. Microsoft empfiehlt außerdem, nach Möglichkeit vorhandene klassische Speicherkonten mit dem Resource Manager-Modell neu zu erstellen. Zwar können Sie ein Speicherkonto auch mit dem klassischen Bereitstellungsmodell erstellen, doch ist das klassische Modell weniger flexibel und wird schließlich eingestellt werden.

Weitere Informationen zu Azure-Bereitstellungsmodellen finden Sie unter [Resource Manager-Bereitstellung und klassische Bereitstellung](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)
- [Skalierbarkeitsziele für den Azure Storage-Ressourcenanbieter](scalability-targets-resource-provider.md)