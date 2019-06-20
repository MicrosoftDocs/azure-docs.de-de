---
title: Verwenden benutzerdefinierter Rollen für Azure-Ressourcen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie benutzerdefinierte Rollen für Azure-Ressourcen in Azure AD Privileged Identity Management (PIM) verwenden.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602027"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Verwenden benutzerdefinierter Rollen für Azure-Ressourcen in PIM

Es kann erforderlich sein, in Azure Active Directory Privileged Identity Management (Azure AD PIM) strikte Einstellungen auf einige Mitglieder einer Rolle anzuwenden, während anderen mehr Freiheiten gewährt werden. Stellen Sie sich ein Szenario vor, in dem eine Organisation mehrere Vertragspartner nutzt, um Unterstützung bei der Entwicklung einer Anwendung zu leisten, die in einem Azure-Abonnement ausgeführt wird.

Als Ressourcenadministrator wünschen Sie sich, dass Mitarbeiter ohne Genehmigung für den Zugriff berechtigt sind. Allerdings muss für alle Vertragspartner eine Genehmigung vorhanden sein, wenn sie Zugriff auf die Ressourcen eines Unternehmens anfordern.

Führen Sie die im nächsten Abschnitt angegebenen Schritte aus, um PIM-Zieleinstellungen für Azure-Ressourcenrollen einzurichten.

## <a name="create-the-custom-role"></a>Erstellen der benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle für eine Ressource zu erstellen, führen Sie die Schritte unter [Erstellen benutzerdefinierter Rollen für rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control-custom-roles.md) aus.

Wenn Sie benutzerdefinierte Rollen erstellen, fügen Sie einen beschreibenden Namen hinzu, damit Sie sich leicht merken können, welche integrierte Rolle dupliziert werden soll.

> [!NOTE]
> Stellen Sie sicher, dass die benutzerdefinierte Rolle ein Duplikat der integrierten Rolle ist, die Sie duplizieren möchten, und dass ihr Bereich mit der integrierten Rolle übereinstimmt.

## <a name="apply-pim-settings"></a>Anwenden von PIM-Einstellungen

Nachdem die Rolle in Ihrem Mandanten im Azure-Portal erstellt wurde, navigieren Sie zum Bereich **Privileged Identity Management: Azure-Ressourcen**. Wählen Sie die Ressource aus, für die die Rolle gelten soll.

![Der Bereich „Privileged Identity Management: Azure-Ressourcen“](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurieren Sie PIM-Rolleneinstellungen](pim-resource-roles-configure-role-settings.md), die für diese Mitglieder der Rolle gelten sollen.

Führen Sie abschließend das [Zuweisen von Rollen](pim-resource-roles-assign-roles.md) für einzelne Gruppen mit Mitgliedern aus, für die diese Einstellungen gelten sollen.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-configure-role-settings.md)
- [Benutzerdefinierte Rollen in Azure](../../role-based-access-control/custom-roles.md)
