---
title: 'Schnellstart: Benennungsrichtlinie für Gruppen – Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie in Azure Active Directory neue Benutzer hinzugefügt oder bestehende gelöscht werden.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6013d14d7639bf4f7fe318c9a2da96e4d8acbcb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546283"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Schnellstart: Benennungsrichtlinie für Gruppen in Azure Active Directory

In dieser Schnellstartanleitung richten Sie eine Benennungsrichtlinie in Ihrer Azure AD-Organisation (Azure Active Directory) für von Benutzern erstellte Microsoft 365-Gruppen ein, um das Sortieren und Durchsuchen der Gruppen in Ihrer Organisation zu erleichtern. Sie können die Benennungsrichtlinie beispielsweise für Folgendes verwenden:

* Kommunizieren der Funktion einer Gruppe, die Mitgliedschaft, die geografische Region oder den Ersteller der Gruppe
* Kategorisieren von Gruppen im Adressbuch
* Blockieren bestimmter Wörter in Gruppennamen und Aliasen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Konfigurieren der Benennungsrichtlinie für Gruppen im Azure-Portal

1. Melden Sie sich mit einem Benutzeradministratorkonto beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie **Gruppen** aus, und wählen Sie dann **Benennungsrichtlinie** aus, um die Seite „Benennungsrichtlinie“ zu öffnen.

    ![Öffnen der Seite „Benennungsrichtlinie“ im Admin Center](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Anzeigen oder Bearbeiten von Präfix-/Suffixbenennungsrichtlinien

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Benennungsrichtlinie für Gruppen** aus.
1. Sie können die aktuellen Präfix- oder Suffixbenennungsrichtlinien einzeln anzeigen oder bearbeiten, indem Sie die Attribute oder Zeichenfolgen auswählen, die Sie als Teil der Benennungsrichtlinie erzwingen möchten.
1. Um ein Präfix oder Suffix aus der Liste zu entfernen, wählen Sie das Präfix oder Suffix aus, und wählen Sie dann **Löschen** aus. Es können mehrere Elemente gleichzeitig gelöscht werden.
1. Wählen Sie **Speichern** aus, damit die an der Richtlinie vorgenommenen Änderungen wirksam werden.

### <a name="view-or-edit-the-custom-blocked-words"></a>Anzeigen oder Bearbeiten der benutzerdefinierten blockierten Wörter

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Blockierte Wörter** aus.

    ![Bearbeiten und Hochladen der Liste der blockierten Wörter für die Benennungsrichtlinie](./media/groups-quickstart-naming-policy/blockedwords.png)

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

Im nächsten Artikel finden Sie weitere Informationen zu den PowerShell-Cmdlets für eine Benennungsrichtlinie, technische Einschränkungen, zum Hinzufügen einer benutzerdefinierten Liste der blockierten Wörter und zu den Benutzeroberflächen für Endbenutzer in Microsoft 365-Apps.
> [!div class="nextstepaction"]
> [Durchsetzen einer Benennungsrichtlinie und Verwenden von PowerShell](groups-naming-policy.md)