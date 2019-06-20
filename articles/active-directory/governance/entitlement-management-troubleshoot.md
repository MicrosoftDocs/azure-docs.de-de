---
title: Problembehandlung bei der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, welche Punkte Sie überprüfen sollten, um Probleme mit der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) zu beheben.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473824"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Problembehandlung bei der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel werden einige Punkte beschrieben, die Sie überprüfen sollten, um Probleme mit der Berechtigungsverwaltung von Azure Active Directory (Azure AD) zu beheben.

## <a name="checklist-for-entitlement-management-administration"></a>Prüfliste für die Administration der Berechtigungsverwaltung

* Wenn Sie beim Konfigurieren der Berechtigungsverwaltung eine Meldung vom Typ „Zugriff verweigert“ erhalten und Sie ein globaler Administrator sind, stellen Sie sicher, dass Ihr Verzeichnis über eine [Azure AD Premium P2- oder EMS E5-Lizenz](entitlement-management-overview.md#license-requirements) verfügt.  
* Wenn Sie beim Erstellen oder Anzeigen von Zugriffspaketen eine Meldung vom Typ „Zugriff verweigert“ erhalten und Sie Mitglied der Gruppe „Katalogersteller“ sind, müssen Sie vor dem Erstellen Ihres ersten Zugriffspakets einen Katalog erstellen.

## <a name="checklist-for-adding-a-resource"></a>Prüfliste für das Hinzufügen einer Ressource

* Damit eine Anwendung als Ressource in einem Zugriffspaket dienen kann, muss sie über mindestens eine Ressourcenrolle verfügen, die zugewiesen werden kann. Die Rollen werden von der Anwendung selbst definiert und in Azure AD verwaltet. Beachten Sie, dass das Azure-Portal möglicherweise auch Dienstprinzipale für Dienste anzeigt, die nicht als Anwendungen ausgewählt werden können.  Insbesondere sind **Exchange Online** und **SharePoint Online** Dienste, aber keine Anwendungen, die über Ressourcenrollen im Verzeichnis verfügen. Daher können sie nicht in ein Zugriffspaket einbezogen werden.  Verwenden Sie stattdessen die gruppenbasierte Lizenzierung, um geeignete Lizenzen für Benutzer einzurichten, die Zugriff auf diese Dienste benötigen.

* Damit eine Gruppe als Ressource in einem Zugriffspaket dienen kann, muss sie in Azure AD bearbeitet werden können.  Gruppen, die aus einer lokalen Active Directory-Instanz stammen, können nicht als Ressourcen zugewiesen werden, weil ihre Besitzer- oder Mitgliedsattribute in Azure AD nicht geändert werden können.  

* SharePoint Online-Dokumentbibliotheken und einzelne Dokumente können nicht als Ressourcen hinzugefügt werden.  Erstellen Sie stattdessen eine Azure AD-Sicherheitsgruppe, fügen Sie diese Gruppe und eine Standortrolle zum Zugriffspaket hinzu, und verwenden Sie diese Gruppe in SharePoint Online, um den Zugriff auf die Dokumentbibliothek oder das Dokument zu steuern.

* Wenn bereits Benutzer einer Ressource zugewiesen wurden, die Sie mit einem Zugriffspaket verwalten möchten, stellen Sie sicher, dass die Benutzer dem Zugriffspaket mit einer entsprechenden Richtlinie zugewiesen sind. Möglicherweise möchten Sie beispielsweise eine Gruppe in ein Zugriffspaket einschließen, das bereits Benutzer in der Gruppe enthält. Wenn diese Benutzer in der Gruppe weiterhin Zugriff benötigen, müssen sie über eine entsprechende Richtlinie für die Zugriffspakete verfügen, damit sie ihren Zugriff auf die Gruppe nicht verlieren. Sie können das Zugriffspaket zuweisen, indem Sie entweder die Benutzer bitten, das Zugriffspaket mit der entsprechenden Ressource anzufordern, oder die Benutzer direkt dem Zugriffspaket zuweisen. Weitere Informationen finden Sie unter [Bearbeiten und Verwalten eines vorhandenen Zugriffspakets](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Checkliste für die Bereitstellung des Zugriffs durch externe Benutzer

* Wenn eine B2B-[Zulassungsliste](../b2b/allow-deny-list.md) vorhanden ist, können Benutzer, deren Verzeichnisse nicht zulässig sind, keinen Zugriff anfordern.

* Stellen Sie sicher, dass keine [Richtlinien für den bedingten Zugriff](../conditional-access/require-managed-devices.md) eingerichtet sind, die verhindern würden, dass externe Benutzer Zugriff anfordern oder die Anwendungen in den Zugriffspaketen verwenden können.

## <a name="checklist-for-request-issues"></a>Prüfliste für Anforderungsprobleme

* Wenn ein Benutzer Zugriff auf ein Zugriffspaket anfordern möchte, müssen Sie sicherstellen, dass der Benutzer den **Link zum Portal „Mein Zugriff“** für das Zugriffspaket verwendet. Weitere Informationen finden Sie unter [Kopieren des Links zum Portal „Mein Zugriff“](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Wenn sich ein Benutzer beim Portal „Mein Zugriff“ anmeldet, um ein Zugriffspaket anzufordern, müssen Sie sicherstellen, dass er sich mit seinem Organisationskonto authentifiziert. Bei dem Organisationskonto kann es sich um ein Konto im Ressourcenverzeichnis oder um ein Konto in einem Verzeichnis handeln, das in einer der Richtlinien des Zugriffspakets enthalten ist. Wenn das Konto des Benutzers kein Organisationskonto ist oder das Verzeichnis nicht in der Richtlinie enthalten ist, wird dem Benutzer das Zugriffspaket nicht angezeigt. Weitere Informationen finden Sie unter [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md).

* Wenn die Benutzeranmeldung beim Ressourcenverzeichnis blockiert wird, kann der Benutzer keinen Zugriff im Portal „Mein Zugriff“ anfordern. Bevor der Benutzer den Zugriff anfordern kann, müssen Sie die Anmeldeblockierung aus dem Profil des Benutzers entfernen. Um die Anmeldeblockierung zu entfernen, klicken Sie im Azure-Portal auf **Azure Active Directory**, klicken Sie auf **Benutzer**, klicken Sie auf den Benutzer, und klicken Sie dann auf **Profil**. Bearbeiten Sie den Abschnitt **Einstellungen**, und ändern Sie **Anmeldung blockieren** in **Nein**. Weitere Informationen finden Sie unter [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen mit Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Sie können auch überprüfen, ob der Benutzer aufgrund einer [Identity Protection-Richtlinie](../identity-protection/howto-unblock-user.md) blockiert wurde.

* Wenn ein Benutzer sowohl Anforderer als auch Genehmiger ist, wird ihm im Portal „Mein Zugriff“ auf der Seite **Genehmigungen** die Anforderung für ein Zugriffspaket nicht angezeigt. Dieses Verhalten ist beabsichtigt: Ein Benutzer kann seine eigene Anforderung nicht genehmigen. Stellen Sie sicher, dass für das angeforderte Zugriffspaket zusätzliche genehmigende Personen in der Richtlinie konfiguriert sind. Weitere Informationen finden Sie unter [Bearbeiten einer vorhandenen Richtlinie](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Wenn ein neuer externer Benutzer, der sich vorher noch nicht bei Ihrem Verzeichnis angemeldet hat, ein Zugriffspaket einschließlich einer SharePoint Online-Website erhält, wird das Zugriffspaket erst als vollständig übermittelt angezeigt, nachdem das Benutzerkonto in SharePoint Online bereitgestellt wurde.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Berichten und Protokollen in der Azure AD-Berechtigungsverwaltung](entitlement-management-reports.md)
