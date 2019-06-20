---
title: Ändern des Namens oder Logos einer Unternehmensanwendung in Azure Active Directory | Microsoft-Dokumentation
description: Ändern des Namens oder Logos einer benutzerdefinierten Unternehmensanwendung in Azure Active Directory
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
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780921"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Ändern des Namens oder Logos einer Unternehmensanwendung in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie auf einfache Weise den Namen oder das Logo für eine benutzerdefinierte Unternehmensanwendung ändern. Sie müssen der Ersteller der benutzerdefinierten Anwendung sein und über die entsprechenden Berechtigungen verfügen, um diese Änderungen vorzunehmen.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Ändern des Namens oder Logos einer Unternehmensanwendung

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist. Die Seite **Azure Active Directory Admin Center** wird angezeigt.
2. Wählen Sie im linken Bereich **Unternehmensanwendungen** aus. Die Liste Ihrer Unternehmensanwendungen wird angezeigt.
3. Wählen Sie eine Anwendung aus. Die Übersichtsseite der Anwendung wird angezeigt.
4. Wählen Sie im Anwendungsübersichtsbereich unter der Kategorie **Verwaltung** die Option **Eigenschaften** aus. Die Seite **Eigenschaften** wird angezeigt.
5. Wenn Sie den Namen ändern möchten, wählen Sie das Feld **Name** aus, geben Sie einen neuen Namen ein, und drücken Sie dann die **EINGABETASTE**.
6. Wenn Sie das Logo ändern möchten, suchen Sie das Feld **Logo**, und klicken Sie auf das Ordnersymbol neben dem Feld **Datei auswählen**, das sich unter dem aktuellen Logo der Anwendung befindet.

   ![Auswählen des Befehls „Eigenschaften“](./media/change-name-or-logo-portal/change-logo.png)

   Wenn Sie das Logo nicht ändern möchten, fahren Sie mit Schritt 8 fort.
7. Wählen Sie in der Dateiauswahl die Datei aus, die Sie als neues Logo verwenden möchten. Der Name der Datei wird im Feld unter dem aktuellen Logo angezeigt.

   > [!NOTE]
   > Azure erfordert, dass das Logo eine PNG-Datei ist, die bestimmte Grenzwerte für Breite, Höhe und Dateigröße einhält.
8. Wählen Sie **Speichern** aus. Wenn Sie ein neues Logo ausgewählt haben, wird das im Feld **Logo** angezeigte Bild in das neue Logo geändert.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Anzeigen der Gruppen und Mitglieder Ihrer Organisation in Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](assign-user-or-group-access-portal.md)
* [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](remove-user-or-group-access-portal.md)
* [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App](disable-user-sign-in-portal.md)
