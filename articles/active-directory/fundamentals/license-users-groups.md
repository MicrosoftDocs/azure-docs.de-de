---
title: Zuweisen oder Entfernen von Lizenzen – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Anweisungen zum Zuweisen oder Entfernen von Azure Active Directory-Lizenzen für Ihre Benutzer oder Gruppen.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e7a3f80067adb3093bd27e34a45b3afd72b4993
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60247638"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Zuweisen oder Entfernen von Lizenzen im Azure Active Directory-Portal
Viele Azure Active Directory (Azure AD)-Dienste erfordern, dass Sie ein Azure AD-Produkt aktivieren und jeden Ihrer Benutzer oder Gruppen (und zugehörige Mitglieder) für dieses Produkt lizenzieren. Nur Benutzer mit aktiven Lizenzen können auf die lizenzierten Azure AD-Dienste zugreifen und diese nutzen.

## <a name="available-product-editions"></a>Verfügbare Produkteditionen
Für das Azure AD-Produkt sind verschiedene Editionen verfügbar.

- Azure AD Free

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

Spezifische Informationen zu jeder Produktedition und den zugehörigen Lizenzdetails finden Sie unter [Welche Lizenz benötige ich?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Anzeigen Ihrer Produktedition und den Lizenzdetails
Sie können Ihre verfügbaren Produkte, einschließlich der einzelnen Lizenzen, einsehen und nach allen anstehenden Ablaufdaten und der Anzahl der verfügbaren Zuweisungen suchen.

### <a name="to-find-your-product-and-license-details"></a>So finden Sie Ihr Produkt und die Lizenzdetails
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** und dann **Lizenzen** aus.

    Die Seite **Lizenzen** wird angezeigt.

    ![Seite „Lizenzen“ mit der Anzahl der erworbenen Produkte und der zugewiesenen Lizenzen](media/license-users-groups/license-details-blade.png)
    
3. Wählen Sie den Link **Gekaufte Produkte**, um die Seite **Produkte** anzuzeigen und die Details **Zugewiesen**, **Verfügbar** und **Bald ablaufend** für jede Produktedition anzuzeigen.

    ![Seite „Produkte“ mit Informationen zu Produkteditionen und zugewiesenen Lizenzen](media/license-users-groups/license-products-blade-with-products.png)

4. Wählen Sie den Namen einer Produktedition, um die lizenzierten Benutzer und Gruppen anzuzeigen.

## <a name="assign-licenses-to-users-or-groups"></a>Zuweisen von Lizenzen zu Benutzern oder Gruppen
Stellen Sie sicher, dass jeder, der einen lizenzierten Azure AD-Dienst nutzen muss, über die entsprechende Lizenz verfügt. Es liegt an Ihnen, ob Sie die Lizenzrechte einzelnen Benutzern oder einer ganzen Gruppe hinzufügen möchten.

>[!Note]
>Die gruppenbasierte Lizenzierung ist eine öffentliche Previewfunktion von Azure AD und mit jedem kostenpflichtigen Azure AD-Lizenzplan verfügbar. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Detaillierte Informationen zum Hinzufügen neuer Benutzer finden Sie unter [Hinzufügen oder Löschen von Benutzern in Azure Active Directory](add-users-azure-active-directory.md). Detaillierte Informationen zum Erstellen von Gruppen und Hinzufügen von Mitgliedern finden Sie unter [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Zuweisen einer Lizenz zu einem bestimmten Benutzer
1. Wählen Sie auf der Seite **Produkte** den Namen der Edition aus, die Sie dem Benutzer zuweisen möchten. Zum Beispiel _Azure Active Directory Premium Plan 2_.

    ![Seite „Produkte“mit hervorgehobener Produktedition](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Wählen Sie auf der Seite **Azure Active Directory Premium Plan 2** die Option **Zuweisen**.

    ![Seite „Produkte“ mit hervorgehobener Option „Zuweisen“](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Wählen Sie auf der Seite **Zuweisen** **Benutzer und Gruppen**, und suchen und wählen Sie dann den Benutzer, dem Sie die Lizenz zuweisen. Beispielsweise _Mary Parker_.

    ![Seite „Lizenz hinzufügen“ mit den hervorgehobenen Optionen „Suchen“ und „Auswählen“](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Wählen Sie **Zuordnungsoptionen**, stellen Sie sicher, dass die entsprechenden Lizenzoptionen aktiviert sind, und wählen Sie dann **OK**.

    ![Seite „Lizenzoption“ mit allen in der Edition verfügbaren Optionen](media/license-users-groups/license-option-blade-assignments.png)

    Die Seite **Lizenz zuweisen** wird aktualisiert, um anzuzeigen, dass ein Benutzer ausgewählt ist und dass die Zuweisungen konfiguriert sind.

    >[!NOTE]
    >Nicht alle Microsoft-Dienste sind an allen Standorten verfügbar. Bevor einem Benutzer eine Lizenz zugewiesen werden kann, müssen Sie den **Nutzungsspeicherort** angeben. Sie können diesen Wert im Bereich **Azure Active Directory &gt; Benutzer &gt; Profil &gt; Einstellungen** in Azure AD festlegen.

5. Wählen Sie **Zuweisen** aus.

    Der Benutzer wird der Liste der lizenzierten Benutzer hinzugefügt und hat Zugriff auf die enthaltenen Azure AD-Dienste.

### <a name="to-assign-a-license-to-an-entire-group"></a>Zuweisen einer Lizenz zur gesamten Gruppe
1. Wählen Sie auf der Seite **Produkte** den Namen der Edition aus, die Sie dem Benutzer zuweisen möchten. Zum Beispiel _Azure Active Directory Premium Plan 2_.

    ![Blatt „Produkte“mit hervorgehobener Produktedition](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Wählen Sie auf der Seite **Azure Active Directory Premium Plan 2** die Option **Zuweisen**.

    ![Seite „Produkte“ mit hervorgehobener Option „Zuweisen“](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Wählen Sie auf der Seite **Zuweisen** **Benutzer und Gruppen**, und suchen und wählen Sie dann die Gruppe, der Sie die Lizenz zuweisen. Beispielsweise _MDM-Richtlinie – Westen_.

    ![Seite „Lizenz hinzufügen“ mit den hervorgehobenen Optionen „Suchen“ und „Auswählen“](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Wählen Sie **Zuordnungsoptionen**, stellen Sie sicher, dass die entsprechenden Lizenzoptionen aktiviert sind, und wählen Sie dann **OK**.

    ![Seite „Lizenzoption“ mit allen in der Edition verfügbaren Optionen](media/license-users-groups/license-option-blade-group-assignments.png)

    Die Seite **Lizenz zuweisen** wird aktualisiert, um anzuzeigen, dass ein Benutzer ausgewählt ist und dass die Zuweisungen konfiguriert sind.

    >[!NOTE]
    >Nicht alle Microsoft-Dienste sind an allen Standorten verfügbar. Bevor einer Gruppe eine Lizenz zugewiesen werden kann, müssen Sie den **Nutzungsspeicherort** für alle Mitglieder angeben. Sie können diesen Wert im Bereich **Azure Active Directory &gt; Benutzer &gt; Profil &gt; Einstellungen** in Azure AD festlegen. Jeder Benutzer, dessen Verwendungsstandort nicht angegeben ist, erbt den Standort des Mandanten.

5. Wählen Sie **Zuweisen** aus.

    Die Gruppe wird der Liste der lizenzierten Gruppen hinzugefügt und alle Mitglieder haben Zugriff auf die enthaltenen Azure AD-Dienste.

## <a name="remove-a-license"></a>Entfernen einer Lizenz
Sie können eine Lizenz von einem Benutzer oder einer Gruppe auf der Seite **Lizenzen** entfernen.

### <a name="to-remove-a-license-from-a-specific-user"></a>Entfernen einer Lizenz von einem bestimmten Benutzer
1. Wählen Sie auf der Seite **Lizenzierte Benutzer** für die Produktedition den Benutzer aus, der die Lizenz nicht mehr besitzen soll. Beispielsweise _Alain Charon_.

2. Wählen Sie **Lizenz entfernen** aus.

    ![Seite „Lizenzierte Benutzer“ mit hervorgehobener Option „Lizenz entfernen“](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Entfernen einer Lizenz aus einer Gruppe
1. Wählen Sie auf der Seite **Lizenzierte Gruppen** für die Produktedition die Gruppe aus, der die Lizenz nicht mehr besitzen soll. Beispielsweise _MDM-Richtlinie – Westen_.

2. Wählen Sie **Lizenz entfernen** aus.

    ![Seite „Lizenzierte Gruppe“ mit hervorgehobener Option „Lizenz entfernen“](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Lizenzen, die ein Benutzer von einer Gruppe geerbt hat, können nicht direkt entfernt werden. Stattdessen müssen Sie den Benutzer aus der Gruppe entfernen, aus der er die Lizenz erbt.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihre Lizenzen zugewiesen haben, können Sie die folgenden Prozesse ausführen:

- [Bestimmen und Beheben von Lizenzzuweisungsproblemen](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Hinzufügen von lizenzierten Benutzern zur Lizenzierung zu einer Gruppe](../users-groups-roles/licensing-groups-migrate-users.md)

- [Szenarien, Einschränkungen und bekannte Probleme mit der Verwendung von Gruppen zum Verwalten der Lizenzierung in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)
