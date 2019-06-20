---
title: 'Schnellstart: Hinzufügen einer Benennungsrichtlinie für Office 365-Gruppen: Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie in Azure Active Directory neue Benutzer hinzugefügt oder bestehende gelöscht werden.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17ef24d753041934f68f3daee950aaa0bec46ba
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734757"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Schnellstart: Benennungsrichtlinie für Gruppen in Azure Active Directory

In dieser Schnellstartanleitung richten Sie eine Benennungsrichtlinie in Ihrem Azure Active Directory-Mandanten (Azure AD) für von Benutzern erstellte Office 365-Gruppen ein, um das Sortieren und Durchsuchen der Gruppen in Ihrem Mandanten zu erleichtern. Sie können die Benennungsrichtlinie beispielsweise für Folgendes verwenden:

* Kommunizieren der Funktion einer Gruppe, die Mitgliedschaft, die geografische Region oder den Ersteller der Gruppe
* Kategorisieren von Gruppen im Adressbuch
* Blockieren bestimmter Wörter in Gruppennamen und Aliasen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurieren der Benennungsrichtlinie für Gruppen für einen Mandanten im Azure-Portal

1. Melden Sie sich mit einem Benutzeradministratorkonto beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie **Gruppen** aus, und wählen Sie dann **Benennungsrichtlinie** aus, um die Seite „Benennungsrichtlinie“ zu öffnen.

    ![Öffnen der Seite „Benennungsrichtlinie“ im Admin Center](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Anzeigen oder Bearbeiten von Präfix-/Suffixbenennungsrichtlinien

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Benennungsrichtlinie für Gruppen** aus.
1. Sie können die aktuellen Präfix- oder Suffixbenennungsrichtlinien einzeln anzeigen oder bearbeiten, indem Sie die Attribute oder Zeichenfolgen auswählen, die Sie als Teil der Benennungsrichtlinie erzwingen möchten.
1. Um ein Präfix oder Suffix aus der Liste zu entfernen, wählen Sie das Präfix oder Suffix aus, und wählen Sie dann **Löschen** aus. Es können mehrere Elemente gleichzeitig gelöscht werden.
1. Wählen Sie **Speichern** aus, damit die an der Richtlinie vorgenommenen Änderungen wirksam werden.

### <a name="view-or-edit-the-custom-blocked-words"></a>Anzeigen oder Bearbeiten der benutzerdefinierten blockierten Wörter

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Blockierte Wörter** aus.

    ![Bearbeiten und Hochladen der Liste der blockierten Wörter für die Benennungsrichtlinie](./media/groups-naming-policy/blockedwords.png)

1. Wählen Sie **Herunterladen** aus, um die aktuelle Liste der benutzerdefinierten blockierten Wörter anzuzeigen oder zu bearbeiten.
1. Laden Sie die neue Liste der benutzerdefinierten blockierten Wörter durch Auswählen des Dateisymbols hoch.
1. Wählen Sie **Speichern** aus, damit die an der Richtlinie vorgenommenen Änderungen wirksam werden.

Das ist alles. Sie haben Ihre Benennungsrichtlinie festgelegt und benutzerdefinierte blockierte Wörter hinzugefügt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Entfernen der Benennungsrichtlinie im Azure-Portal

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Richtlinie löschen** aus.
1. Nach dem Bestätigen des Löschvorgangs wird die Benennungsrichtlinie einschließlich aller Präfix-/Suffixbenennungsrichtlinien und benutzerdefinierten blockierten Wörter entfernt.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Benennungsrichtlinie für Ihre Azure AD-Organisation im Azure-Portal festlegen.

Im nächsten Artikel finden Sie weitere Informationen zu den PowerShell-Cmdlets für eine Benennungsrichtlinie, technischen Einschränkungen, zum Hinzufügen einer Liste der benutzerdefinierten blockierten Wörter und zu den Benutzeroberflächen für Endbenutzer in Office 365-Apps.
> [!div class="nextstepaction"]
> [Durchsetzen einer Benennungsrichtlinie und Verwenden von PowerShell](groups-naming-policy.md)