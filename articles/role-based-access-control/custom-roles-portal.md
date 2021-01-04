---
title: Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen über das Azure-Portal – Azure RBAC
description: Erfahren Sie, wie benutzerdefinierte Azure-Rollen über das Azure-Portal und die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) erstellt werden. Dies umfasst das Auflisten, Erstellen, Aktualisieren und Löschen von benutzerdefinierten Rollen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: rolyon
ms.openlocfilehash: 9ab6561b1dc0e8b977fc69d259c5f26366f355ce
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481328"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen über das Azure-Portal

Wenn sich die spezifischen Anforderungen Ihrer Organisation mit den [integrierten Azure-Rollen](built-in-roles.md) nicht erfüllen lassen, können Sie eigene benutzerdefinierte Rollen erstellen. Genau wie integrierte Rollen können auch benutzerdefinierte Rollen Benutzern, Gruppen und Dienstprinzipalen auf Abonnement- und Ressourcengruppenebene zugewiesen werden. Benutzerdefinierte Rollen werden in einem Azure AD-Verzeichnis (Azure Active Directory) gespeichert und können für mehrere Abonnements genutzt werden. Jedes Verzeichnis kann bis zu 5.000 benutzerdefinierte Rollen enthalten. Benutzerdefinierte Rollen können über das Azure-Portal, mit Azure PowerShell, über die Azure CLI oder mithilfe der REST-API erstellt werden. In diesem Artikel wird das Erstellen benutzerdefinierter Rollen über das Azure-Portal beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen von benutzerdefinierten Rollen benötigen Sie Folgendes:

- Berechtigungen zum Erstellen von benutzerdefinierten Rollen, etwa [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Schritt 1: Bestimmen der Berechtigungen, die Sie benötigen

Azure verfügt über Tausende von Berechtigungen, die Sie potenziell zu Ihrer benutzerdefinierten Rolle hinzufügen können. Nachfolgend sind vier Möglichkeiten aufgeführt, um die Berechtigungen zu ermitteln, die Sie zu Ihrer benutzerdefinierten Rolle hinzufügen sollten:

| Methode | BESCHREIBUNG |
| --- | --- |
| Überprüfen vorhandener Rollen | Überprüfen Sie vorhandene Rollen, um herauszufinden, welche Berechtigungen verwendet werden. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](built-in-roles.md). |
| Suche nach Berechtigungen anhand von Schlüsselwörtern | Wenn Sie über das Azure-Portal eine benutzerdefinierte Rolle erstellen, können Sie anhand von Schlüsselwörtern nach Berechtigungen suchen. Sie können z.B. nach Berechtigungen im Zusammenhang mit *VMs* oder mit der *Abrechnung* suchen. Diese Suchfunktion wird weiter unten näher beschrieben: [Schritt 4: Permissions](#step-4-permissions) (Weiter: Berechtigungen). |
| Herunterladen aller Berechtigungen | Wenn Sie über das Azure-Portal eine benutzerdefinierte Rolle erstellen, können Sie alle Berechtigungen als CSV-Datei herunterladen und diese Datei anschließend durchsuchen. Klicken Sie im Bereich **Berechtigungen hinzufügen** auf die Schaltfläche **Alle Berechtigungen herunterladen** , um alle Berechtigungen herunterzuladen. Weitere Informationen zum Bereich „Berechtigungen hinzufügen“ finden Sie unter [Schritt 4: Permissions](#step-4-permissions) (Weiter: Berechtigungen). |
| Anzeigen der Berechtigungen in den Dokumenten | Eine Liste der verfügbaren Berechtigungen finden Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Schritt 2: Auswahl der Vorgehensweise zum Erstellen einer Rolle

Es gibt drei Möglichkeiten, wie Sie eine benutzerdefinierte Rolle erstellen können. Sie können eine vorhandene Rolle klonen, eine Rolle von Grund auf neu erstellen oder den Vorgang mit einer JSON-Datei beginnen. Die einfachste Möglichkeit besteht darin, eine vorhandene Rolle zu ermitteln, die über die meisten benötigten Berechtigungen verfügt, und diese Rolle dann zu klonen und an Ihr Szenario anzupassen. 

### <a name="clone-a-role"></a>Klonen einer Rolle

Wenn eine vorhandene Rolle nicht über die erforderlichen Berechtigungen verfügt, können Sie sie klonen und die Berechtigungen anschließend ändern. Führen Sie diese Schritte aus, um mit dem Klonen einer Rolle zu beginnen.

1. Öffnen Sie im Azure-Portal ein Abonnement oder eine Ressourcengruppe, in dem bzw. in der es möglich sein soll, die benutzerdefinierte Rolle zuzuweisen. Öffnen Sie dann **Zugriffssteuerung (IAM)** .

    Der folgende Screenshot zeigt die geöffnete Seite „Zugriffssteuerung (IAM)“ für ein Abonnement.

    ![Die Seite „Zugriffssteuerung (IAM)“ für ein Abonnement](./media/custom-roles-portal/access-control-subscription.png)

1. Klicken Sie auf die Registerkarte **Rollen** , um eine Liste aller integrierten und benutzerdefinierten Rollen anzuzeigen.

1. Suchen Sie nach einer Rolle, die Sie klonen möchten, z.B. nach der Rolle „Abrechnungsleser“.

1. Klicken Sie am Ende der Zeile auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Klonen**.

    ![Das Kontextmenü „Klonen“](./media/custom-roles-portal/clone-menu.png)

    Dadurch wird der Editor für benutzerdefinierte Rollen mit ausgewählter Option **Rolle klonen** geöffnet.

1. Fahren Sie mit [Schritt 3: Grundlagen](#step-3-basics) fort.

### <a name="start-from-scratch"></a>Ganz von vorn beginnen

Führen Sie die folgenden Schritte aus, um eine benutzerdefinierte Rolle von Grund auf neu zu erstellen.

1. Öffnen Sie im Azure-Portal ein Abonnement oder eine Ressourcengruppe, in dem bzw. in der es möglich sein soll, die benutzerdefinierte Rolle zuzuweisen. Öffnen Sie dann **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf **Hinzufügen** und anschließend auf **Benutzerdefinierte Rolle hinzufügen**.

    ![Das Menü „Benutzerdefinierte Rolle hinzufügen“](./media/custom-roles-portal/add-custom-role-menu.png)

    Dadurch wird der Editor für benutzerdefinierte Rollen mit ausgewählter Option **Von Grund auf neu starten** geöffnet.

1. Fahren Sie mit [Schritt 3: Grundlagen](#step-3-basics) fort.

### <a name="start-from-json"></a>Verwenden einer JSON-Datei

Sie können die meisten Werte Ihrer benutzerdefinierten Rolle in einer JSON-Datei angeben. Sie können die Datei im Editor für benutzerdefinierte Rollen öffnen, Änderungen vornehmen und dann die benutzerdefinierte Rolle erstellen. Führen Sie diese Schritte aus, um die benutzerdefinierte Rolle anhand einer JSON-Datei zu erstellen.

1. Erstellen Sie eine JSON-Datei mit folgendem Format:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. Geben Sie in der JSON-Datei Werte für die verschiedenen Eigenschaften an. Im Folgenden finden Sie ein Beispiel mit hinzugefügten Werten. Informationen zu den verschiedenen Eigenschaften finden Sie unter [Grundlegendes zu Azure-Rollendefinitionen](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Öffnen Sie im Azure-Portal die Seite **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf **Hinzufügen** und anschließend auf **Benutzerdefinierte Rolle hinzufügen**.

    ![Das Menü „Benutzerdefinierte Rolle hinzufügen“](./media/custom-roles-portal/add-custom-role-menu.png)

    Dadurch wird der Editor für benutzerdefinierte Rollen geöffnet.

1. Wählen Sie auf der Registerkarte mit den Grundeinstellungen unter **Baselineberechtigungen** die Option **Von JSON aus starten** aus.

1. Klicken Sie neben dem Feld „Datei auswählen“ auf die Schaltfläche „Ordner“, um das Dialogfeld „Öffnen“ anzuzeigen.

1. Wählen Sie Ihre JSON-Datei aus, und klicken Sie dann auf **Öffnen**.

1. Fahren Sie mit [Schritt 3: Grundlagen](#step-3-basics) fort.

## <a name="step-3-basics"></a>Schritt 3: Grundlagen

Geben Sie auf der Registerkarte **Grundeinstellungen** den Namen, eine Beschreibung und die Baselineberechtigungen für Ihre benutzerdefinierte Rolle an.

1. Geben Sie im Feld **Name der benutzerdefinierten Rolle** einen Namen für die benutzerdefinierte Rolle an. Der Name muss innerhalb des Azure AD-Verzeichnisses eindeutig sein. Er kann Buchstaben, Ziffern, Leerzeichen und Sonderzeichen enthalten.

1. Geben Sie im Feld **Beschreibung** eine optionale Beschreibung für die benutzerdefinierte Rolle an. Dies wird die QuickInfo für die benutzerdefinierte Rolle.

    Die Option **Baselineberechtigungen** sollte basierend auf dem vorherigen Schritt bereits ausgewählt sein. Sie können sie jedoch ändern.

    ![Die Registerkarte „Grundeinstellungen“ mit angegebenen Werten](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Schritt 4: Berechtigungen

Auf der Registerkarte **Berechtigungen** können Sie die Berechtigungen für Ihre benutzerdefinierte Rolle angeben. Abhängig davon, ob Sie eine Rolle geklont haben oder den Vorgang anhand einer JSON-Datei durchgeführt haben, werden auf der Registerkarte „Berechtigungen“ möglicherweise bereits einige Berechtigungen aufgeführt.

![Die Registerkarte „Berechtigungen“ beim Erstellen einer benutzerdefinierten Rolle](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Hinzufügen oder Entfernen von Berechtigungen

Führen Sie diese Schritte aus, um Berechtigungen für Ihre benutzerdefinierte Rolle hinzuzufügen oder zu entfernen.

1. Um Berechtigungen hinzuzufügen, klicken Sie auf **Berechtigungen hinzufügen**. Dadurch wird der Bereich „Berechtigungen hinzufügen“ geöffnet.

    In diesem Bereich werden alle verfügbaren Berechtigungen aufgelistet (in einem Kartenformat in verschiedenen Kategorien gruppiert). Jede Kategorie stellt einen *Ressourcenanbieter* dar. Dabei handelt es sich um einen Dienst, der Azure-Ressourcen bereitstellt.

1. Geben Sie im Feld **Berechtigung suchen** eine Zeichenfolge ein, um nach Berechtigungen zu suchen. Suchen Sie z.B. nach *Rechnung* , um Berechtigungen zu finden, die mit Rechnungen in Zusammenhang stehen.

    Basierend auf Ihrer Suchzeichenfolge wird eine Liste mit Ressourcenanbieterkarten angezeigt. Eine Übersicht über die Zuordnung von Ressourcenanbietern und Azure-Diensten finden Sie unter [Ressourcenanbieter für Azure-Dienste](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Der Bereich „Berechtigungen hinzufügen“ mit Ressourcenanbietern](./media/custom-roles-portal/add-permissions-provider.png)

1. Klicken Sie auf die Karte eines Ressourcenanbieters, der möglicherweise über die Berechtigungen verfügt, die Sie Ihrer benutzerdefinierten Rolle hinzufügen möchten (z.B. **Microsoft-Abrechnung** ).

    Basierend auf Ihrer Suchzeichenfolge wird eine Liste mit Verwaltungsberechtigungen für den jeweiligen Ressourcenanbieter angezeigt.

    ![Die Liste „Berechtigungen hinzufügen“](./media/custom-roles-portal/add-permissions-list.png)

1. Wenn Sie nach Berechtigungen suchen, die sich auf die Datenebene beziehen, klicken Sie auf **Datenaktionen**. Anderenfalls behalten Sie die Einstellung **Aktionen** bei, um Berechtigungen aufzulisten, die sich auf die Verwaltungsebene beziehen. Weitere Informationen zu den Unterschieden zwischen der Verwaltungsebene und der Datenebene finden Sie unter [Verwaltungs- und Datenvorgänge](role-definitions.md#management-and-data-operations).

1. Aktualisieren Sie bei Bedarf die Suchzeichenfolge, um die Suche weiter einzuschränken.

1. Sobald Sie mindestens eine Berechtigung gefunden haben, die Sie zu Ihrer benutzerdefinierten Rolle hinzufügen möchten, fügen Sie neben der Berechtigung ein Häkchen hinzu. Fügen Sie z.B. ein Häkchen neben **Sonstiges: Rechnung herunterladen** hinzu, um die Berechtigung zum Herunterladen von Rechnungen hinzuzufügen.

1. Klicken Sie auf **Hinzufügen** , um die Berechtigung zu Ihrer Liste hinzuzufügen.

    Die Berechtigung wird als `Actions` oder `DataActions` hinzugefügt.

    ![Hinzugefügte Berechtigung](./media/custom-roles-portal/permissions-list-add.png)

1. Um Berechtigungen zu entfernen, klicken Sie auf das Symbol zum Löschen am Ende der Zeile. Da in diesem Beispiel keine Berechtigung zum Erstellen von Supporttickets erforderlich ist, kann die Berechtigung `Microsoft.Support/*` gelöscht werden.

### <a name="add-wildcard-permissions"></a>Hinzufügen von Platzhalterberechtigungen

Abhängig davon, wie Sie den Vorgang zum Erstellen von Rollen starten, enthält Ihre Liste mit Berechtigungen möglicherweise Berechtigungen mit Platzhaltern (`*`). Ein Platzhalter (`*`) erweitert eine Berechtigung auf alles, was der von Ihnen angegebenen Aktionszeichenfolge entspricht. Beispielsweise werden mit der folgenden Platzhalterzeichenfolge alle Berechtigungen für Azure Cost Management und Exporte hinzugefügt. Dies schließt auch alle zukünftigen Exportberechtigungen ein, die möglicherweise hinzugefügt werden.

```
Microsoft.CostManagement/exports/*
```

Neue Platzhalterberechtigungen können nicht über den Bereich **Berechtigungen hinzufügen** hinzugefügt werden. Wenn Sie eine Platzhalterberechtigung hinzufügen möchten, müssen Sie sie manuell auf der Registerkarte **JSON** hinzufügen. Weitere Informationen finden Sie unter [Schritt 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Ausschließen von Berechtigungen

Wenn Ihre Rolle über eine Platzhalterberechtigung (`*`) verfügt und Sie bestimmte Berechtigungen von dieser Platzhalterberechtigung ausschließen oder entfernen möchten, können Sie sie ausschließen. Angenommen, Sie haben die folgende Platzhalterberechtigung:

```
Microsoft.CostManagement/exports/*
```

Wenn Sie nicht zulassen möchten, dass ein Export gelöscht wird, können Sie die folgende Löschberechtigung ausschließen:

```
Microsoft.CostManagement/exports/delete
```

Wenn Sie eine Berechtigung ausschließen, wird sie als `NotActions` oder `NotDataActions` hinzugefügt. Die geltenden Verwaltungsberechtigungen werden ermittelt, indem alle `Actions` hinzugefügt und dann alle `NotActions` entfernt werden. Die geltenden Datenberechtigungen werden ermittelt, indem alle `DataActions` hinzugefügt und dann alle `NotDataActions` entfernt werden.

> [!NOTE]
> Das Ausschließen einer Berechtigung ist nicht mit dem Verweigern einer Berechtigung identisch. Das Ausschließen von Berechtigungen dient lediglich dazu, Berechtigungen aus einer Platzhalterberechtigung zu entfernen.

1. Zum Ausschließen oder Entfernen von Berechtigungen aus einer zulässigen Platzhalterberechtigung klicken Sie auf **Berechtigungen ausschließen** , um den Bereich „Berechtigungen ausschließen“ zu öffnen.

    In diesem Bereich geben Sie die Verwaltungs- oder Datenberechtigungen an, die ausgeschlossen oder entfernt werden.

1. Sobald Sie mindestens eine Berechtigung gefunden haben, die ausgeschlossen werden soll, fügen Sie neben der Berechtigung ein Häkchen hinzu und klicken auf die Schaltfläche **Hinzufügen**.

    ![Der Bereich „Berechtigungen ausschließen“ mit ausgewählter Berechtigung](./media/custom-roles-portal/exclude-permissions-select.png)

    Die Berechtigung wird als `NotActions` oder `NotDataActions` hinzugefügt.

    ![Ausgeschlossene Berechtigung](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Schritt 5: Zuweisbare Bereiche

Auf der Registerkarte **Zuweisbare Bereiche** geben Sie an, wo Ihre benutzerdefinierte Rolle zugewiesen werden kann (z.B. in einem Abonnement oder in einer Ressourcengruppe). Abhängig davon, wie Sie den Vorgang zum Erstellen der Rolle begonnen haben, wird auf dieser Registerkarte möglicherweise der Bereich angezeigt, in dem Sie die Seite „Zugriffssteuerung (IAM)“ geöffnet haben. Der zuweisbare Bereich kann nicht auf den Stammbereich ("/") festgelegt werden. Derzeit können keine Verwaltungsgruppen als zuweisbarer Bereich hinzugefügt werden.

1. Klicken Sie auf **Zuweisbare Bereiche hinzufügen** , um den Bereich „Zuweisbare Bereiche hinzufügen“ zu öffnen.

    ![Die Registerkarte „Zuweisbare Bereiche“](./media/custom-roles-portal/assignable-scopes.png)

1. Klicken Sie auf mindestens einen Bereich, den Sie verwenden möchten (üblicherweise Ihr Abonnement).

    ![Hinzufügen von zuweisbaren Bereichen](./media/custom-roles-portal/add-assignable-scopes.png)

1. Klicken Sie auf die Registerkarte **Hinzufügen** , um Ihren zuweisbaren Bereich hinzuzufügen.

## <a name="step-6-json"></a>Schritt 6: JSON

Auf der Registerkarte **JSON** wird Ihre benutzerdefinierte Rolle im JSON-Format angezeigt. Wenn Sie möchten, können Sie den JSON-Code direkt bearbeiten. Diese Registerkarte muss zudem verwendet werden, wenn Sie eine Platzhalterberechtigung (`*`) hinzufügen möchten.

1. Um den JSON-Code zu bearbeiten, klicken Sie auf **Bearbeiten**.

    ![Die Registerkarte „JSON“ mit benutzerdefinierter Rolle](./media/custom-roles-portal/json.png)

1. Nehmen Sie die gewünschten Änderungen am JSON-Code vor.

    Wenn der JSON-Code nicht ordnungsgemäß formatiert ist, sehen Sie eine rote Zackenlinie und einen Hinweis im vertikalen Bundsteg.

1. Klicken Sie auf **Speichern** , wenn Sie fertig sind.

## <a name="step-7-review--create"></a>Schritt 7: Bewerten + erstellen

Auf der Registerkarte **Bewerten + erstellen** können Sie die Einstellungen Ihrer benutzerdefinierten Rolle noch einmal überprüfen.

1. Überprüfen Sie die Einstellungen Ihrer benutzerdefinierten Rolle.

    ![Registerkarte „Überprüfen und erstellen“](./media/custom-roles-portal/review-create.png)

1. Klicken Sie auf **Erstellen** , um Ihre benutzerdefinierte Rolle zu erstellen.

    Nach einigen Augenblicken wird in einem Meldungsfeld angezeigt, dass Ihre benutzerdefinierte Rolle erfolgreich erstellt wurde.

    ![Meldungsfeld zum Erstellen einer benutzerdefinierten Rolle](./media/custom-roles-portal/custom-role-success.png)

    Falls Fehler ermittelt wurden, wird eine entsprechende Meldung angezeigt.

    ![Fehler beim Vorgang zum Überprüfen und Erstellen](./media/custom-roles-portal/review-create-error.png)

1. Überprüfen Sie, ob Ihre neue benutzerdefinierte Rolle in der Liste **Rollen** aufgeführt wird. Sollte Ihre benutzerdefinierte Rolle nicht angezeigt werden, klicken Sie auf **Aktualisieren**.

     Es kann einige Minuten dauern, bis Ihre benutzerdefinierte Rolle überall angezeigt wird.

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen

Führen Sie die folgenden Schritte aus, um Ihre benutzerdefinierten Rollen anzuzeigen.

1. Öffnen Sie ein Abonnement oder eine Ressourcengruppe, und öffnen Sie dann **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollen** , um eine Liste aller integrierten und benutzerdefinierten Rollen anzuzeigen.

1. Wählen Sie in der Liste **Type** die Option **CustomRole** aus, um Ihre benutzerdefinierten Rollen anzuzeigen.

    Wenn Sie die benutzerdefinierte Rolle gerade erst erstellt haben und sie nicht in der Liste aufgeführt ist, klicken Sie auf **Aktualisieren**.

    ![Liste mit benutzerdefinierten Rollen](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um Ihre Liste mit benutzerdefinierten Rollen zu öffnen.

1. Klicken Sie auf die Auslassungspunkte ( **...** ) für die benutzerdefinierte Rolle, die Sie aktualisieren möchten, und klicken Sie dann auf **Bearbeiten**. Beachten Sie, dass integrierte Rollen nicht aktualisiert werden können.

    Die benutzerdefinierte Rolle wird im Editor geöffnet.

    ![Das Menü „Benutzerdefinierte Rolle“](./media/custom-roles-portal/edit-menu.png)

1. Verwenden Sie die verschiedenen Registerkarten, um die benutzerdefinierte Rolle zu aktualisieren.

1. Wenn Sie fertig sind, klicken Sie auf die Registerkarte **Überprüfen + erstellen** , um Ihre Änderungen zu überprüfen.

1. Klicken Sie auf die Schaltfläche **Aktualisieren** , um Ihre benutzerdefinierte Rolle zu aktualisieren.

## <a name="delete-a-custom-role"></a>Löschen einer benutzerdefinierten Rolle

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um Ihre Liste mit benutzerdefinierten Rollen zu öffnen.

1. Entfernen Sie alle Rollenzuweisungen, die die benutzerdefinierte Rolle verwenden.

1. Klicken Sie auf die Auslassungspunkte ( **...** ) für die benutzerdefinierte Rolle, die Sie löschen möchten, und klicken Sie dann auf **Löschen**.

    ![Screenshot einer Liste benutzerdefinierter Rollen, die zum Löschen ausgewählt werden können.](./media/custom-roles-portal/delete-menu.png)

    Es kann einige Minuten dauern, bis Ihre benutzerdefinierte Rolle vollständig gelöscht ist.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer benutzerdefinierten Azure-Rolle mithilfe von Azure PowerShell](tutorial-custom-role-powershell.md)
- [Benutzerdefinierte Azure-Rollen](custom-roles.md)
- [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md)
