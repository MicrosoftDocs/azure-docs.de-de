---
title: Konfigurieren des einmaligen Anmeldens per Kennwort für eine Azure AD-Kataloganwendung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Anwendung für das sichere einmalige Anmelden mit Kennwort konfigurieren, wenn die Anwendung bereits im Azure AD-Anwendungskatalog aufgeführt ist.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53dbcddf2025ddc3111bc585655e2c157844b18
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190170"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurieren des einmaligen Anmeldens per Kennwort für eine Azure AD-Kataloganwendung

Wenn Sie eine Anwendung aus dem [Azure AD-Anwendungskatalog](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) hinzufügen, können Sie auswählen, wie Ihre Benutzer sich an dieser Anwendung anmelden sollen. Sie können diese Auswahl jederzeit konfigurieren, indem Sie im [Azure-Portal](https://portal.azure.com/) das Navigationselement **Einmaliges Anmelden** für eine Unternehmensanwendung auswählen.

Eine der verfügbaren Methoden für das einmalige Anmelden ist die Option [Kennwortbasiertes einmaliges Anmelden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Diese Option eignet sich hervorragend dazu, Anwendungen schnell in Azure AD zu integrieren, und ermöglicht Ihnen Folgendes:

-   Aktivieren Sie das **einmalige Anmelden für Ihre Benutzer**, indem Sie Benutzernamen und Kennwörter für die in Azure AD integrierte Anwendung sicher speichern und später in der Anwendung automatisch ausfüllen.

-   **Unterstützen Sie Anwendungen, die mehrere Anmeldefelder erfordern** für Anwendungen, die über Benutzername und Kennwort hinaus weitere Informationen zur Anmeldung erfordern.

-   **Definieren Sie die Bezeichnungen** der Eingabefelder für Benutzer und Kennwort, die Ihre Benutzer beim Eingeben ihrer Anmeldeinformationen im [Anwendungszugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) sehen.

-   Ermöglichen Sie es Ihren **Benutzern**, für vorhandene Anwendungskonten eigene Benutzernamen und Kennwörter festzulegen und diese manuell im [Anwendungszugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) einzugeben.

-   Ermöglichen Sie es einem **Mitglied der Gruppe „Business“** , mithilfe des Features [Self-Service-Anwendungszugriff](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) die Benutzernamen und Kennwörter festzulegen, die einem Benutzer zugewiesen werden.

-   Ermöglichen Sie es einem **Administrator**, beim [Zuweisen von Benutzern zu einer Anwendung](#assign-a-user-to-an-application-directly) das Feature zum Aktualisieren von Anmeldeinformationen zu verwenden, um die den Benutzern zugewiesenen Benutzernamen und Kennwörter anzugeben.

-   Ermöglichen Sie es einem **Administrator**, beim [Zuweisen von Gruppen zu einer Anwendung](#assign-an-application-to-a-group-directly) das Feature zum Aktualisieren von Anmeldeinformationen zu verwenden, um die von Gruppen von Benutzern gemeinsam genutzten Benutzernamen und Kennwörter anzugeben.

Im folgenden Abschnitt wird beschrieben, wie Sie das [kennwortbasierte einmalige Anmelden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) bei einer Anwendung aktivieren, die bereits im [Azure AD-Anwendungskatalog](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) vorhanden ist.

## <a name="overview-of-steps-required"></a>Übersicht über die erforderlichen Schritte
Um eine Anwendung aus dem Azure AD-Katalog zu konfigurieren, müssen folgende Schritte ausgeführt werden:

-   [Hinzufügen einer Anwendung aus dem Azure AD-Katalog](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurieren der Anwendung für das einmalige Anmelden per Kennwort](#configure-the-application-for-password-single-sign-on)

-   Zuweisen der Anwendung zu einem Benutzer oder einer Gruppe

    -   [Direktes Zuweisen eines Benutzers zu einer Anwendung](#assign-a-user-to-an-application-directly)

    -   [Direktes Zuweisen einer Anwendung zu einer Gruppe](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Hinzufügen einer Anwendung aus dem Azure AD-Katalog

Um eine Anwendung aus dem Azure AD-Katalog hinzuzufügen, führen Sie folgende Schritte aus:

1.  Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie oben rechts im Bereich **Unternehmensanwendungen** auf die Schaltfläche **Hinzufügen**.

6.  Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Textfeld **Namen eingeben** den Namen der Anwendung ein.

7.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

8.  Bevor Sie die Anwendung hinzufügen, können Sie im Textfeld **Name** den Namen der Anwendung ändern.

9.  Klicken Sie auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

Nach kurzer Zeit wird der Konfigurationsbereich der Anwendung angezeigt.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurieren der Anwendung für das einmalige Anmelden per Kennwort

Um das einmalige Anmelden für eine Anwendung zu konfigurieren, führen Sie folgende Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Wählen Sie den Modus **Kennwortbasiertes Anmelden** aus.

9. [Weisen Sie der Anwendung Benutzer zu](#assign-a-user-to-an-application-directly).

10. Darüber hinaus können Sie Anmeldeinformationen für einen Benutzer angeben, indem Sie die Zeile des Benutzers auswählen, auf **Anmeldeinformationen aktualisieren** klicken und den Benutzernamen und das Kennwort für den Benutzer eingeben. Andernfalls werden die Benutzer beim Start der Anwendung aufgefordert, die Anmeldeinformationen selbst einzugeben.

## <a name="assign-a-user-to-an-application-directly"></a>Direktes Zuweisen eines Benutzers zu einer Anwendung

Um einer Anwendung Benutzer direkt zuzuweisen, führen Sie folgende Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie aus der Liste die Anwendung aus, der Sie einen Benutzer zuweisen möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Benutzer und Gruppen**.

8. Klicken Sie oberhalb der Liste **Benutzer und Gruppen** auf die Schaltfläche **Hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.

9. Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Benutzer und Gruppen**.

10. Geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** den **vollständigen Namen** oder die **E-Mail-Adresse** des gewünschten Benutzers ein.

11. Zeigen Sie auf den **Benutzer** in der Liste, um ein **Kontrollkästchen** anzuzeigen. Klicken Sie auf das Kontrollkästchen neben dem Profilbild oder Logo des Benutzers, um den Benutzer zur Liste **Ausgewählt** hinzuzufügen.

12. **Optional**: Wenn Sie **mehrere Benutzer hinzufügen** möchten, geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** einen weiteren **vollständigen Namen** oder eine weitere **E-Mail-Adresse** ein, und klicken Sie auf das Kontrollkästchen, um diesen Benutzer zur Liste **Ausgewählt** hinzuzufügen.

13. Wenn Sie alle gewünschten Benutzer ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**, um sie zur Liste der Benutzer und Gruppen hinzuzufügen, die der Anwendung zugewiesen werden sollen.

14. **Optional**: Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Rolle auswählen**, um eine Rolle auszuwählen, die den ausgewählten Benutzern zugewiesen werden soll.

15. Klicken Sie auf die Schaltfläche **Zuweisen**, um die Anwendung den ausgewählten Benutzern zuzuweisen.

## <a name="assign-an-application-to-a-group-directly"></a>Direktes Zuweisen einer Anwendung zu einer Gruppe

Um einer Anwendung Gruppen direkt zuzuweisen, führen Sie folgende Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie aus der Liste die Anwendung aus, der Sie einen Benutzer zuweisen möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Benutzer und Gruppen**.

8. Klicken Sie oberhalb der Liste **Benutzer und Gruppen** auf die Schaltfläche **Hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.

9. Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Benutzer und Gruppen**.

10. Geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** den **vollständigen Gruppennamen** der gewünschten Gruppe ein.

11. Zeigen Sie auf die **Gruppe** in der Liste, um ein **Kontrollkästchen** anzuzeigen. Klicken Sie auf das Kontrollkästchen neben dem Profilbild oder Logo der Gruppe, um den Benutzer zur Liste **Ausgewählt** hinzuzufügen.

12. **Optional**: Wenn Sie **mehrere Gruppen hinzufügen** möchten, geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** einen weiteren **vollständigen Gruppennamen** ein, und klicken Sie auf das Kontrollkästchen, um diese Gruppe der Liste **Ausgewählt** hinzuzufügen.

13. Wenn Sie alle gewünschten Gruppen ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**, um sie zur Liste der Benutzer und Gruppen hinzuzufügen, die der Anwendung zugewiesen werden sollen.

14. **Optional**: Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Rolle auswählen**, um eine Rolle auszuwählen, die den ausgewählten Gruppen zugewiesen werden soll.

15. Klicken Sie auf die Schaltfläche **Zuweisen**, um die Anwendung den ausgewählten Gruppen zuzuweisen.

Nach kurzer Zeit können die ausgewählten Benutzer diese Anwendungen im Zugriffsbereich starten.

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)
