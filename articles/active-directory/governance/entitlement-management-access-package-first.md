---
title: 'Tutorial: Erstellen Ihres ersten Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory'
description: Schrittweises Tutorial zum Erstellen Ihres ersten Zugriffspakets in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion)
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873545"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Tutorial: Erstellen Ihres ersten Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Das Verwalten des Zugriffs auf alle Ressourcen, die von Mitarbeitern benötigt werden, etwa Gruppen, Anwendungen und Websites, ist eine wichtige Aufgabe für Organisationen. Sie möchten Mitarbeitern den richtigen Umfang an Zugriff gewähren, den sie benötigen, um produktiv zu sein, und Sie möchten deren Zugriff entfernen, wenn er nicht mehr benötigt wird.

In diesem Tutorial arbeiten Sie als IT-Administrator für die Woodgrove Bank. Sie wurden gebeten, ein Paket von Ressourcen für ein Webprojekt zu erstellen, das interne Benutzern selbst anfordern können. Für die Anforderungen sind Genehmigungen erforderlich, und der Zugriff eines Benutzer läuft nach 30 Tagen ab. Für dieses Tutorial gehören die Webprojekt-Ressourcen nur zu einer einzelnen Gruppe, diese könnte aber auch eine Sammlung von Gruppen, Anwendungen oder SharePoint Online-Websites sein.

![Übersicht über das Szenario](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Zugriffspakets mit einer Gruppe als Ressource
> * Bestimmen einer genehmigenden Person
> * Vorgehen, wie ein interner Benutzer das Zugriffspaket anfordern kann
> * Genehmigen der Zugriffsanforderung

Wenn Sie keine Azure AD Premium P2- oder Enterprise Mobility + Security E5-Lizenz haben, erstellen Sie eine kostenlose [Enterprise Mobility + Security E5-Testversion](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Voraussetzungen

Um Azure AD-Berechtigungsverwaltung (Vorschauversion) verwenden zu können, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

## <a name="step-1-set-up-users-and-group"></a>Schritt 1: Einrichten von Benutzern und Gruppen

Ein Ressourcenverzeichnis hat mindestens eine Ressource, die freigegeben (geteilt) werden soll. In diesem Schritt erstellen Sie eine Gruppe namens **Technik-Gruppe** im Woodgrove Bank-Verzeichnis, die die Zielressource für die Berechtigungsverwaltung ist. Außerdem richten Sie einen internen Anforderer ein.

**Erforderliche Rolle:** Globaler Administrator oder Benutzeradministrator

![Erstellen von Benutzern und Gruppen](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Melden Sie sich als globaler Administrator oder Benutzeradministrator beim [Azure-Portal](https://portal.azure.com) an.  

1. Klicken Sie im linken Navigationsmenü auf **Azure Active Directory**.

1. Erstellen Sie konfigurieren Sie die beiden folgenden Benutzer. Sie können diese Namen oder eine andere Namen verwenden. **Admin1** kann der Benutzer sein, als der Sie derzeit angemeldet sind.

    | NAME | Verzeichnisrolle | BESCHREIBUNG |
    | --- | --- | --- |
    | **Admin1** | Globaler Administrator<br/>Oder<br/>Eingeschränkter Administrator (Benutzeradministrator) | Administrator und genehmigende Person |
    | **Anforderer1** | Benutzer | Interner Anforderer |

    Für dieses Tutorial sind der Administrator und die genehmigende Person dieselbe Person, aber üblicherweise bestimmen Sie mehrere Personen als genehmigende Personen.

1. Erstellen Sie eine Azure AD-Sicherheitsgruppe namens **Technik-Gruppe** mit dem Mitgliedschaftstyp **Zugewiesen**.

    Diese Gruppe ist die Zielressource für die Berechtigungsverwaltung. Die Gruppe darf zu Beginn keine Mitglieder haben.

## <a name="step-2-create-an-access-package"></a>Schritt 2: Erstellen eines Zugriffspakets

Ein *Zugriffspaket* ist ein Bündel aller Ressourcen, die ein Benutzer benötigt, um an einem Projekt zu arbeiten oder seine Arbeit zu erledigen. Zugriffspakete sind in Containern definiert, die als *Kataloge* bezeichnet werden. In diesem Schritt erstellen Sie ein **Webprojekt-Zugriffspaket** im Katalog **Allgemein**.

**Erforderliche Rolle:** Globaler Administrator oder Benutzeradministrator

![Erstellen eines Zugriffspakets](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klicken Sie im Azure-Portal im linken Navigationsbereich auf **Azure Active Directory**.

1. Klicken Sie im linken Menü auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Zugriffspakete**.  Wenn **Zugriff verweigert** angezeigt wird, prüfen Sie, ob eine Azure AD Premium P2-Lizenz in diesem Verzeichnis vorhanden ist.

1. Klicken Sie auf **Neues Zugriffspaket**.

    ![Berechtigungsverwaltung im Azure-Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Geben Sie auf der Registerkarte **Grundeinstellungen** den Namen **Webprojekt-Zugriffspaket** und die Beschreibung **Zugriffspaket für das Technik-Webprojekt** ein.

1. Übernehmen Sie für die Dropdownliste **Katalog** die Einstellung **Allgemein**.

    ![Neues Zugriffspaket, Registerkarte „Grundeinstellungen“](./media/entitlement-management-access-package-first/basics.png)

1. Klicken Sie auf **Weiter**, um die Registerkarte **Ressourcenrollen** zu öffnen.

    Auf dieser Registerkarte wählen Sie die Berechtigungen aus, die in das Zugriffspaket aufgenommen werden sollen.

1. Klicken Sie auf **Gruppen**.

1. Suchen Sie im Bereich „Gruppen auswählen“ nach der Gruppe**Technik-Gruppe**, die Sie zuvor erstellt haben, und wählen Sie diese Gruppe aus.

    Standardmäßig werden Gruppen angezeigt, die sich innerhalb und außerhalb des Katalogs **Allgemein** befinden. Wenn Sie eine Gruppe auswählen, die sich außerhalb des Katalogs **Allgemein** befindet, wird sie dem Katalog **Allgemein** hinzugefügt.

    ![Neues Zugriffspaket, Registerkarte „Ressourcenrollen“](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klicken Sie auf **Auswählen**, um die Gruppe zur Liste hinzuzufügen.

1. Wählen Sie in der Dropdownliste **Rolle** den Eintrag **Mitglied** aus.

    ![Neues Zugriffspaket, Registerkarte „Ressourcenrollen“](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klicken Sie auf **Weiter**, um die Registerkarte **Richtlinie** zu öffnen.

1. Legen Sie die Option **Erste Richtlinie erstellen** auf **Später** fest.

    Sie erstellen die Richtlinie im nächsten Abschnitt.

    ![Neues Zugriffspaket, Registerkarte „Richtlinie“](./media/entitlement-management-access-package-first/policy.png)

1. Klicken Sie auf **Weiter**, um die Registerkarte **Überprüfen + erstellen** zu öffnen.

    ![Neues Zugriffspaket, Registerkarte „Überprüfen + erstellen“](./media/entitlement-management-access-package-first/review-create.png)

1. Überprüfen Sie die Zugriffspaketeinstellungen, und klicken Sie dann auf **Erstellen**.

    Möglicherweise wird eine Meldung angezeigt, dass das Zugriffspaket für Benutzer noch nicht sichtbar ist, weil der Katalog noch nicht aktiviert ist.

    ![Neues Zugriffspaket, Meldung über „Nicht sichtbar“](./media/entitlement-management-access-package-first/not-visible.png)

1. Klicken Sie auf **OK**.

    Nach einigen Augenblicken sollte eine Benachrichtigung angezeigt werden, dass das Zugriffspaket erfolgreich erstellt wurde.

## <a name="step-3-create-a-policy"></a>Schritt 3: Erstellen einer Richtlinie

In einer *Richtlinie* sind die Regeln oder Leitlinien für den Zugriff auf ein Zugriffspaket definiert. In diesem Schritt erstellen Sie eine Richtlinie, die es einem bestimmten Benutzer im Ressourcenverzeichnis erlaubt, das Zugriffspaket anzufordern. Sie geben außerdem an, dass Anforderungen genehmigt werden müssen, und Sie geben an, wer die genehmigende Person ist.

![Erstellen einer Zugriffspaketrichtlinie](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Erforderliche Rolle:** Globaler Administrator oder Benutzeradministrator

1. Klicken Sie im **Webprojekt-Zugriffspaket** im linken Menü auf **Richtlinien**.

    ![Liste der Zugriffspaketrichtlinien](./media/entitlement-management-access-package-first/policies-list.png)

1. Klicken Sie auf **Richtlinie hinzufügen**, um „Richtlinie erstellen“ zu öffnen.

1. Geben Sie den Namen **Richtlinie für internen Anforderer** und die Beschreibung **Ermöglicht Benutzern in diesem Verzeichnis, Zugriff auf Webprojektressourcen anzufordern** ein.

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für in Ihrem Verzeichnis befindliche Benutzer**.

    ![Richtlinie erstellen](./media/entitlement-management-access-package-first/policy-create.png)

1. Scrollen Sie nach unten zum Abschnitt **Benutzer und Gruppen auswählen**, und klicken Sie auf **Benutzer und Gruppen hinzufügen**.

1. Wählen Sie im Bereich „Benutzer und Gruppen auswählen“ den Benutzer **Anforderer1** aus, den Sie zuvor erstellt haben, und klicken Sie dann auf **Auswählen**.

1. Legen Sie im Abschnitt **Anforderung** die Option **Genehmigung anfordern** auf **Ja** fest.

1. Klicken Sie im Abschnitt **Genehmigende Personen auswählen** auf **Genehmigende Personen hinzufügen**.

1. Wählen Sie im Bereich „Genehmigende Personen auswählen“ den Administrator **Admin1** aus, den Sie zuvor erstellt haben, und klicken Sie dann auf **Auswählen**.

    Für dieses Tutorial sind der Administrator und die genehmigende Person dieselbe Person, Sie können aber eine andere Person als genehmigende Personen bestimmen.

1. Legen Sie im Abschnitt **Ablauf** die Option **Zugriffspaket läuft ab** auf **Anzahl Tage** fest.

1. Legen Sie **Zugriff läuft ab nach** auf **30** Tage fest.

1. Klicken Sie für **Richtlinie aktivieren** auf **Ja**.

    ![Erstellen von Richtlinieneinstellungen](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Klicken Sie auf **Erstellen**, um die **Richtlinie für internen Anforderer** zu erstellen.

1. Klicken Sie im linken Menü des Webprojekt-Zugriffspakets auf **Übersicht**.

1. Kopieren Sie den Wert von **Link zum Portal "Mein Zugriff"** .

    Sie verwenden diesen Link im nächsten Schritt.

    ![Übersicht über das Zugriffspaket, Link zum Portal "Mein Zugriff"](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Schritt 4: Zugriff anfordern

In diesem Schritt führen Sie die Schritte als **interner Anforderer** aus und fordern Zugriff auf das Paket an. Anforderer senden ihre Anforderungen über eine Website, die als Portal „Eigener Zugriff“ (Mein Zugriff) bezeichnet wird. Über das Portal „Mein Zugriff“ können Anforderer Anforderungen für Zugriffspakete senden, die Zugriffspakete sehen, auf die sie bereits Zugriff haben, und ihre Anforderungsverläufe anzeigen.

**Erforderliche Rolle:** Interner Anforderer

1. Melden Sie sich vom Azure-Portal ab.

1. Navigieren Sie in einem neuen Browserfenster zu dem „Link zum Portal "Mein Zugriff"“, den Sie im vorherigen Schritt kopiert haben.

1. Melden Sie beim Portal „Mein Zugriff“ als **Anforderer1** an.

    Sie sollten das **Webprojekt-Zugriffspaket** sehen.

1. Klicken Sie ggf. in der Spalte **Beschreibung** auf den Pfeil, um die Details zu dem Zugriffspaket anzuzeigen.

    ![Portal „Mein Zugriff“ – Zugriffspakete](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicken Sie auf das Häkchen, um das Paket auszuwählen.

1. Klicken Sie auf **Zugriff anfordern**, um den Bereich „Zugriff anfordern“ zu öffnen.

1. Geben Sie in das Feld **Geschäftliche Begründung** die Begründung **Arbeiten an einem Webprojekt** ein.

1. Legen Sie die Option **Für bestimmten Zeitraum anfordern?** auf **Ja** fest.

1. Legen Sie **Startdatum** auf heute und **Enddatum** auf morgen fest.

    ![Portal „Mein Zugriff“ – Zugriff anfordern](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicken Sie auf **Submit**.

1. Klicken Sie im linken Menü auf **Anforderungsverlauf**, um zu überprüfen, ob Ihre Anforderung übermittelt wurde.

## <a name="step-5-approve-access-request"></a>Schritt 5: Genehmigen der Zugriffsanforderung

In diesem Schritt melden Sie sich als der **Genehmigende Person**-Benutzer an, und genehmigen Sie die Zugriffsanforderung für den internen Anforderer. Genehmigende Personen verwenden dasselbe „Mein Zugriff“-Portal (Eigener Zugriff) wie Anforderer, um Anforderungen zu senden. Im Portal „Mein Zugriff“ können genehmigenden Personen ausstehende Genehmigungen anzeigen und Anforderungen genehmigen oder verweigern.

**Erforderliche Rolle:** Genehmigende Person

1. Melden Sie sich vom Portal „Mein Zugriff“ ab.

1. Melden Sie sich unter **Admin1** beim [Portal „Mein Zugriff“](https://myaccess.microsoft.com) an.

1. Klicken Sie im linken Menü auf **Genehmigungen**.

1. Suchen Sie auf der Registerkarte **Ausstehend** nach **Anforderer1**.

    Wenn die Anforderung von Anforderer1 nicht angezeigt wird, warten Sie einige Minuten, und versuchen Sie es dann erneut.

1. Klicken Sie auf den Link **Anzeigen**, um den Bereich „Zugriffsanforderung“ zu öffnen.

1. Klicken Sie auf **Approve**.

1. Geben Sie in das Feld **Grund** den Grund **Genehmigter Zugriff für das Webprojekt** ein.

    ![Portal „Mein Zugriff“ – Zugriffsanforderung](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klicken Sie auf **Absenden**, um Ihre Entscheidung zu übermitteln.

    Es sollte eine Meldung angezeigt werden, dass die Anforderung erfolgreich genehmigt wurde.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Schritt 6: Überprüfen, ob der Zugriff zugewiesen wurde

Nachdem Sie die Zugriffsanforderung genehmigt haben, bestätigen Sie in diesem Schritt, dass dem **internen Anforderer** das Zugriffspaket zugewiesen wurde und dass er nun Mitglied der Gruppe **Technik-Gruppe** ist.

**Erforderliche Rolle:** Globaler Administrator oder Benutzeradministrator

1. Melden Sie sich vom Portal „Mein Zugriff“ ab.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als **Admin1** an.

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Zugriffspakete**.

1. Suchen Sie nach **Webprojekt-Zugriffspaket**, und klicken Sie darauf.

1. Klicken Sie im linken Menü auf **Anforderungen**.

    Sie sollten „Anforderer1“ und die „Richtlinie für internen Anforderer“ mit dem Status **Übermittelt** sehen.

1. Klicken Sie auf die Anforderung, um die Anforderungsdetails anzuzeigen.

    ![Zugriffspaket – Anforderungsdetails](./media/entitlement-management-access-package-first/request-details.png)

1. Klicken Sie im linken Navigationsmenü auf **Azure Active Directory**.

1. Klicken Sie auf **Gruppen**, und öffnen Sie die Gruppe **Technik-Gruppe**.

1. Klicken Sie auf **Mitglieder**.

    Sie sollten sehen, dass **Anforderer1** als Mitglied (Member) aufgelistet wird.

    ![Mitglieder der Technik-Gruppe](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Schritt 7: Bereinigen von Ressourcen

In diesem Schritt entfernen Sie die von Ihnen vorgenommenen Änderungen, und Sie löschen das Zugriffspaket **Webprojekt-Zugriffspaket**.

**Erforderliche Rolle:**  Globaler Administrator oder Benutzeradministrator

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Öffnen Sie **Webprojekt-Zugriffspaket**.

1. Klicken Sie auf **Zuweisungen:** .

1. Klicken Sie für **Anforderer1** auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Zugriff entfernen**.

    Der Status ändert sich von „Übermittelt“ in „Verfallen“.

1. Klicken Sie auf **Richtlinien**.

1. Klicken Sie für **Richtlinie für internen Anforderer** auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Löschen**.

1. Klicken Sie auf **Ressourcenrollen**.

1. Klicken Sie für **Technik-Gruppe** auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Ressourcenrolle entfernen**.

1. Öffnen Sie die Liste der Zugriffspakete.

1. Klicken Sie für **Webprojekt-Zugriffspaket** auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Löschen**.

1. Löschen Sie in Azure Active Directory alle Benutzer, die Sie erstellt, z. B. **Anforderer1** und **Admin1**.

1. Löschen Sie die Gruppe **Technik-Gruppe**.

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum nächsten Artikel, um mehr über gängige Szenarioschritte in der Berechtigungsverwaltung zu erfahren.
> [!div class="nextstepaction"]
> [Gängige Szenarios](entitlement-management-scenarios.md)
