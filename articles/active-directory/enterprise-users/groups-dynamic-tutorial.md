---
title: 'Tutorial: Hinzufügen von Benutzern zu einer dynamischen Gruppe – Azure AD | Microsoft-Dokumentation'
description: In diesem Tutorial verwenden Sie Gruppen mit Benutzermitgliedschaftsregeln, um Benutzer automatisch hinzuzufügen oder zu entfernen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc891a7b07305fc0ef55376d7b6fbb262d740bb5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547490"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Automatisches Hinzufügen oder Entfernen von Gruppenmitgliedern

In Azure Active Directory (Azure AD) können Sie Benutzer automatisch zu Sicherheitsgruppen oder Microsoft 365-Gruppen hinzufügen bzw. aus diesen Gruppen entfernen, sodass Sie diese Schritte nicht immer manuell ausführen müssen. Wenn sich die Eigenschaften eines Benutzers oder Geräts ändern, wertet Azure AD alle dynamischen Gruppenregeln in Ihrer Azure AD-Organisation aus, um zu ermitteln, ob aufgrund der Änderung Mitglieder hinzugefügt oder entfernt werden müssen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen einer automatisch aufgefüllten Gruppe mit Gastbenutzern aus einem Partnerunternehmen
> * Zuweisen von Lizenzen für partnerspezifische Features zu der Gruppe, um Gastbenutzern den Zugriff darauf zu ermöglichen
> * Bonus: Sichern der Gruppe **Alle Benutzer** durch Entfernen von Gastbenutzern, sodass Sie Ihren Mitgliedsbenutzern beispielsweise Zugriff auf rein interne Websites gewähren können

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Feature ist eine Azure AD Premium-Lizenz für Sie als globaler Administrator der Organisation erforderlich. Falls Sie keine Lizenz besitzen, wählen Sie in Azure AD **Lizenzen** > **Produkte** > **Ausprobieren/Kaufen**.

Sie müssen Benutzern keine Lizenzen zuweisen, damit sie Mitglieder in dynamischen Gruppen sein können. Sie benötigen lediglich die Mindestanzahl verfügbarer Azure AD Premium P1-Lizenzen in der Organisation, um diese Benutzer abzudecken. 

## <a name="create-a-group-of-guest-users"></a>Erstellen einer Gruppe mit Gastbenutzern

Zunächst erstellen Sie eine Gruppe für Ihre Gastbenutzer, die alle aus einem Partnerunternehmen stammen. Für sie ist eine spezielle Lizenzierung erforderlich, daher ist es häufig effizienter, zu diesem Zweck eine Gruppe zu erstellen.

1. Melden Sie sich beim Azure-Portal (https://portal.azure.com) mit einem Konto an, das als globaler Administrator für Ihre Organisation konfiguriert ist.
2. Klicken Sie auf **Azure Active Directory** > **Gruppen** > **Neue Gruppe**.
   ![Befehl auswählen, um eine neue Gruppe zu starten](./media/groups-dynamic-tutorial/new-group.png)
3. Auf dem Blatt **Gruppe**:
  
   * Wählen Sie als Gruppentyp die Option **Sicherheit** aus.
   * Geben Sie `Guest users Contoso` als Name und Beschreibung für die Gruppe ein.
   * Ändern Sie **Mitgliedschaftstyp** in **Dynamischer Benutzer**.
   
4. Wählen Sie **Besitzer** aus, und suchen Sie auf dem Blatt **Besitzer hinzufügen** nach gewünschten Besitzern. Klicken Sie auf die gewünschten Besitzer, um Sie der Auswahl hinzuzufügen.
5. Klicken Sie auf **Auswählen**, um das Blatt **Besitzer hinzufügen** zu schließen.  
6. Wählen Sie im Feld **Dynamische Mitglieder** die Option **Dynamische Abfrage bearbeiten** aus.
7. Auf dem Blatt **Dynamische Mitgliedschaftsregeln**:

   * Klicken Sie im Feld **Eigenschaften** auf den vorhandenen Wert, und wählen Sie **userType** aus. 
   * Vergewissern Sie sich, dass für das Feld **Operator** die Option **Ist gleich** ausgewählt ist.  
   * Wählen Sie das Feld **Wert**, und geben Sie **Gast** ein. 
   * Klicken Sie auf den Hyperlink **Ausdruck hinzufügen**, um eine weitere Zeile hinzuzufügen.
   * Wählen Sie im Feld **Und/Oder** die Option **Und** aus.
   * Wählen Sie im Feld **Eigenschaft** die Option **companyName** aus.
   * Vergewissern Sie sich, dass für das Feld **Operator** die Option **Ist gleich** ausgewählt ist.
   * Geben Sie im Feld **Wert** den Wert **Contoso** ein.
   * Klicken Sie auf **Speichern**, um das Blatt **Dynamische Mitgliedschaftsregeln** zu schließen.
   
8. Klicken Sie auf dem Blatt **Gruppe** auf **Erstellen**, um die Gruppe zu erstellen.

## <a name="assign-licenses"></a>Zuweisen von Lizenzen

Sie besitzen nun eine Gruppe und können die Lizenzen zuweisen, die diese Partnerbenutzer benötigen.

1. Klicken Sie in Azure AD auf **Lizenzen**, wählen Sie mindestens eine Lizenz aus, und klicken Sie dann auf **Zuweisen**.
2. Klicken Sie auf **Benutzer und Gruppen**, wählen Sie die Gruppe **Guest users Contoso** (Gastbenutzer Contoso) aus, und speichern Sie Ihre Änderungen.
3. **Zuweisungsoptionen** ermöglichen die Aktivierung und Deaktivierung der Servicepläne, die in den ausgewählten Lizenzen enthalten sind. Klicken Sie nach einer Änderung unbedingt auf **OK**, um die Änderung zu speichern.
4. Klicken Sie schließlich unten im Bereich **Lizenz zuweisen** auf **Zuweisen**, um die Zuweisung abzuschließen.

## <a name="remove-guests-from-all-users-group"></a>Entfernen von Gästen aus der Gruppe „Alle Benutzer“

Unter Umständen möchten Sie zu Verwaltungszwecken letztendlich alle Gastbenutzer nach Unternehmen ihren eigenen Gruppen zuweisen. Nun können Sie auch die Gruppe **Alle Benutzer** so ändern, dass sie nur für Mitgliedsbenutzer in Ihrer Organisation reserviert ist. Anschließend können Sie sie zum Zuweisen von Apps und Lizenzen verwenden, die spezifisch für Ihre eigene Organisation sind.

   ![Ändern der Gruppe „Alle Benutzer“ nur für Mitglieder](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

**So entfernen Sie die Gruppe der Gastbenutzer**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das als globaler Administrator für Ihre Organisation konfiguriert ist.
2. Klicken Sie auf **Azure Active Directory** > **Gruppen**. Wählen Sie die Gruppe **Guest users Contoso** (Gastbenutzer Contoso) aus, und klicken Sie auf die Ellipse (...) und dann auf **Löschen**. Wenn Sie die Gruppe löschen, werden alle zugewiesenen Lizenzen entfernt.

**So stellen Sie die Gruppe „Alle Benutzer“ wieder her**
1. Klicken Sie auf **Azure Active Directory** > **Gruppen**. Klicken Sie auf den Namen der Gruppe **Alle Benutzer**, um die Gruppe zu öffnen.
1. Klicken Sie auf **Dynamische Mitgliedschaftsregeln**, löschen Sie den gesamten Text in der Regel, und klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Erstellen einer Gruppe mit Gastbenutzern
> * Zuweisen von Lizenzen zur neuen Gruppe
> * Ändern der Gruppe „Alle Benutzer“ nur für Mitglieder

Fahren Sie mit dem nächsten Artikel fort, um mehr über die Grundlagen der gruppenbasierten Lizenzierung zu erfahren.
> [!div class="nextstepaction"]
> [Grundlagen der gruppenbasierten Lizenzierung in Azure Active Directory](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



