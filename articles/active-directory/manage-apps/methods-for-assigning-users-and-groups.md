---
title: Zuweisen von Benutzern und Gruppen zu einer Anwendung | Microsoft-Dokumentation
description: Zuweisen von Benutzern zu der Anwendung und Gewähren des Zugriffs darauf
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7402778ec8170e60a623255d0d331f12d8389227
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824305"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory
In diesem Artikel wird das Zuweisen von Benutzern oder Gruppen zu einer Anwendung in Azure Active Directory (Azure AD) veranschaulicht. Benutzer müssen zuerst zu einer Anwendung zugewiesen werden, bevor ein Administrator ihnen Zugriff für Folgendes gewähren kann:

-   Zugriff auf eine Anwendung durch **direktes Navigieren zur URL der Anwendung** (wird auch als vom Dienstanbieter initiierte Anmeldung bezeichnet).

-   Zugriff auf eine Anwendung über die **URL für den Benutzerzugriff** auf der Seite **Eigenschaften** einer Anwendung (wird auch als IdP-initiierte Anmeldung bezeichnet).

-   Eine Anwendung wird in ihrem [Anwendungszugriffsbereich](https://myapps.microsoft.com/) oder in der mobilen Anwendung angezeigt.

-   Eine Anwendung wird in ihrem [Startprogramm für Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) angezeigt.

Die Verfügbarkeit der gruppenbasierten Zuweisung richtet sich nach Ihrer Lizenzvereinbarung. Die gruppenbasierte Zuweisung wird nur für Sicherheitsgruppen unterstützt, und geschachtelte Gruppenmitgliedschaften werden derzeit nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie einer Anwendung Benutzer und Gruppen zuweisen können, müssen Sie die Benutzerzuweisung erfordern. So fordern Sie eine Benutzerzuweisung an

1. Melden Sie sich beim Azure-Portal mit dem Administratorkonto an.
2. Klicken Sie im Hauptmenü auf den Eintrag **Alle Dienste**.
3. Wählen Sie das Verzeichnis aus, das Sie für die Anwendung verwenden.
4. Klicken Sie auf die Registerkarte **Unternehmensanwendungen**.
5. Wählen Sie die Anwendung aus der Liste der Programme aus, die diesem Verzeichnis zugeordnet sind.
6. Klicken Sie auf die Registerkarte **Eigenschaften**.
7. Legen Sie **Benutzerzuweisung erforderlich?** auf „Ja“ fest.
8. Klicken Sie oben auf der Seite auf die Schaltfläche **Speichern**.

## <a name="assign-users"></a>Benutzer zuweisen

Um einer Anwendung Benutzer direkt zuzuweisen, führen Sie folgende Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

    * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6.  Wählen Sie aus der Liste die Anwendung aus, der Sie einen Benutzer zuweisen möchten.

7.  Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Benutzer und Gruppen**.

8.  Klicken Sie oberhalb der Liste **Benutzer und Gruppen** auf die Schaltfläche **Hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.

9.  Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Benutzer und Gruppen**.

10. Geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** den **vollständigen Namen** oder die **E-Mail-Adresse** des gewünschten Benutzers ein.

11. Zeigen Sie auf den **Benutzer** in der Liste, um ein **Kontrollkästchen** anzuzeigen. Klicken Sie auf das Kontrollkästchen neben dem Profilbild oder Logo des Benutzers, um den Benutzer zur Liste **Ausgewählt** hinzuzufügen.

12. **Optional**: Wenn Sie **mehrere Benutzer hinzufügen** möchten, geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** einen weiteren **vollständigen Namen** oder eine weitere **E-Mail-Adresse** ein, und klicken Sie auf das Kontrollkästchen, um diesen Benutzer zur Liste **Ausgewählt** hinzuzufügen.

13. Wenn Sie alle gewünschten Benutzer ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**, um sie zur Liste der Benutzer und Gruppen hinzuzufügen, die der Anwendung zugewiesen werden sollen.

14. **Optional**: Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Rolle auswählen**, um eine Rolle auszuwählen, die den ausgewählten Benutzern zugewiesen werden soll.

15. Klicken Sie auf die Schaltfläche **Zuweisen**, um die Anwendung den ausgewählten Benutzern zuzuweisen.

Nach kurzer Zeit können die ausgewählten Benutzer diese Anwendungen mit den Verfahren starten, die im Abschnitt mit der Lösungsbeschreibung beschrieben wurden.

## <a name="assign-groups"></a>Zuweisen von Gruppen

Führen Sie die folgenden Schritte aus, um einer Anwendung eine oder mehrere Gruppen direkt zuzuweisen:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

    * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6.  Wählen Sie aus der Liste die Anwendung aus, der Sie einen Benutzer zuweisen möchten.

7.  Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Benutzer und Gruppen**.

8.  Klicken Sie oberhalb der Liste **Benutzer und Gruppen** auf die Schaltfläche **Hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.

9.  Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Benutzer und Gruppen**.

10. Geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** den **vollständigen Gruppennamen** der gewünschten Gruppe ein.

11. Zeigen Sie auf die **Gruppe** in der Liste, um ein **Kontrollkästchen** anzuzeigen. Klicken Sie auf das Kontrollkästchen neben dem Profilbild oder Logo der Gruppe, um den Benutzer zur Liste **Ausgewählt** hinzuzufügen.

12. **Optional**: Wenn Sie **mehrere Gruppen hinzufügen** möchten, geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** einen weiteren **vollständigen Gruppennamen** ein, und klicken Sie auf das Kontrollkästchen, um diese Gruppe der Liste **Ausgewählt** hinzuzufügen.

13. Wenn Sie alle gewünschten Gruppen ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**, um sie zur Liste der Benutzer und Gruppen hinzuzufügen, die der Anwendung zugewiesen werden sollen.

14. **Optional**: Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Rolle auswählen**, um eine Rolle auszuwählen, die den ausgewählten Gruppen zugewiesen werden soll.

15. Klicken Sie auf die Schaltfläche **Zuweisen**, um die Anwendung den ausgewählten Gruppen zuzuweisen.

Nach kurzer Zeit können die Benutzer in den ausgewählten Gruppen diese Anwendungen mit den Verfahren starten, die im Abschnitt mit der Lösungsbeschreibung beschrieben wurden. Bei dynamischen Gruppen kann bei der Verarbeitung dieser Zuweisungen eine zusätzliche Verzögerung entstehen, bevor sie für Benutzer in den zugewiesenen Gruppen angezeigt werden.

## <a name="enable-self-service-application-access"></a>Aktivieren von Self-Service-Anwendungszugriff

Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Sie können festlegen, dass die Mitarbeiter der Geschäftseinheit direkt über ihren Zugriffsbereich die den Benutzern zugewiesenen Anmeldeinformationen für Anwendungen mit einmaligem Anmelden per Kennwort verwalten können.

Führen Sie die folgenden Schritte aus, um den Self-Service-Anwendungszugriff auf eine Anwendung zu aktivieren:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie in der Liste die Anwendung aus, für die Sie den Self-Service-Zugriff aktivieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Self-Service**.

8. Um den Self-Service-Anwendungszugriff auf die Anwendung zu aktivieren, legen Sie **Benutzern das Anfordern des Zugriffs auf diese Anwendung erlauben?** auf **Ja** fest.

9. Um dann die Gruppe auszuwählen, zu der Benutzer, die Zugriff auf diese Anwendung anfordern, hinzugefügt werden sollen, klicken Sie auf das Auswahlfeld neben **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?**, und wählen Sie eine Gruppe aus.

10. **Optional**: Wenn eine Genehmigung des Unternehmens erforderlich sein soll, damit Benutzer Zugriff erhalten, legen Sie **Genehmigung anfordern, bevor Zugriff auf diese Anwendung gewährt wird?** auf **Ja** fest.

11. **Optional: nur für Anwendungen mit einmaligem Anmelden per Kennwort:** Wenn Sie möchten, dass die genehmigenden Personen des Unternehmens die für genehmigte Benutzer an die Anwendung gesendeten Kennwörter angeben können, legen Sie **Genehmigenden Personen das Festlegen von Benutzerkennwörtern für diese Anwendung gestatten?** auf **Ja** fest.

12. **Optional**: Um die genehmigenden Personen des Unternehmens anzugeben, die den Zugriff auf die Anwendung genehmigen können, klicken Sie auf die Auswahl neben **Wer darf den Zugriff auf diese Anwendung genehmigen?**. Hier können Sie bis zu 10 genehmigende Personen auswählen.

    >[!NOTE]
    >Gruppen werden nicht unterstützt.
    >
    >

13. **Optional**: Wenn Sie **für Anwendungen, die Rollen verfügbar machen**, den für den Self-Service genehmigten Benutzern eine Rolle zuweisen möchten, klicken Sie auf die Auswahl neben **Welcher Rolle sollen Benutzer in dieser Anwendung zugewiesen werden?**, und wählen Sie die Rolle aus, die den Benutzern zugewiesen werden soll.

14. Klicken Sie abschließend oben im Bereich auf die Schaltfläche **Speichern**.

Nachdem Sie die Self-Service-Anwendungskonfiguration abgeschlossen haben, können Benutzer in ihrem [Anwendungszugriffsbereich](https://myapps.microsoft.com/) auf die Schaltfläche **+Hinzufügen** klicken und die Apps suchen, für die Sie den Self-Service-Zugriff aktiviert haben. Den genehmigenden Personen des Unternehmens wird im [Zugriffsbereich](https://myapps.microsoft.com/) zudem eine Benachrichtigung angezeigt. Sie können festlegen, dass sie in einer E-Mail darüber benachrichtigt werden, dass ein Benutzer den Zugriff auf eine Anwendung angefordert hat, der zu genehmigen ist. 

Diese Genehmigungen unterstützen nur Workflows mit einzelnen Genehmigungen, d.h., wenn Sie mehrere genehmigende Personen angeben, kann jede einzelne genehmigende Person den Zugriff auf die Anwendung genehmigen.

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)
