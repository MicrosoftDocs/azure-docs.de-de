---
title: Erstellen und Verwalten eines Katalogs in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie einen neuen Container für Ressourcen und Zugriffspakete in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) erstellen.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64540679"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Erstellen und Verwalten eines Katalogs in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Erstellen eines Katalogs

Ein Katalog ist ein Container für Ressourcen und Zugriffspakete. Sie erstellen einen Katalog, wenn Sie zugehörige Ressourcen und Zugriffspakete gruppieren möchten. Der Benutzer, der den Katalog erstellt, ist der erste Katalogbesitzer. Ein Katalogbesitzer kann weitere Katalogbesitzer hinzufügen.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogersteller

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**.

    ![Berechtigungsverwaltungskataloge im Azure-Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicken Sie auf **Neuer Katalog**.

1. Geben Sie einen eindeutigen Namen sowie eine Beschreibung für den Katalog ein.

    Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

1. Wenn Sie möchten, dass die Zugriffspakete in diesem Katalog unmittelbar nach der Erstellung von Benutzern angefordert werden können, legen Sie **Aktiviert** auf **Ja** fest.

1. Wenn Sie zulassen möchten, dass Benutzer in ausgewählten externen Verzeichnissen Zugriffspakete in diesem Katalog anfordern können, legen Sie **Für externe Benutzer aktiviert** auf **Ja** fest.

    ![Bereich „Neuer Katalog“](./media/entitlement-management-catalog-create/new-catalog.png)

1. Klicken Sie auf **Erstellen**, um den Katalog zu erstellen.

## <a name="add-resources-to-a-catalog"></a>Hinzufügen von Ressourcen zu einem Katalog

Um Ressourcen in ein Zugriffspaket einschließen zu können, müssen die Ressourcen in einem Katalog vorhanden sein. Bei den Typen von Ressourcen, die Sie hinzufügen können, handelt es sich um Gruppen, Anwendungen und SharePoint Online-Websites.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Ressourcen hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Klicken Sie auf **Ressourcen hinzufügen**.

1. Klicken Sie auf einen Ressourcentyp: **Gruppen**, **Anwendungen** oder **SharePoint-Websites**.

    Wenn Sie ein Katalogersteller sind, können Sie Ihrem Katalog Office 365-Gruppen oder Azure AD-Sicherheitsgruppen hinzufügen, deren Besitzer Sie sind. Wenn Sie Benutzern eine Gruppe zuweisen möchten, die sich nicht in Ihrem Besitz befindet, müssen Sie einen Benutzeradministrator bitten, Ihrem Katalog diese Gruppe hinzuzufügen.

    Wenn Sie ein Katalogersteller sind, können Sie Ihrem Katalog Azure AD-Unternehmensanwendungen hinzufügen, deren Besitzer Sie sind. Dies schließt SaaS-Anwendungen und Ihre eigenen Anwendungen im Verbund mit Azure AD ein. Wenn Sie Benutzern eine Anwendung zuweisen möchten, die sich nicht in Ihrem Besitz befindet, müssen Sie einen Benutzeradministrator bitten, Ihrem Katalog diese Anwendung hinzuzufügen. Sobald die Anwendung im Katalog enthalten ist, können Sie eine der Rollen der Anwendung in einem Zugriffspaket auswählen.

1. Wählen Sie eine oder mehrere Ressourcen des Typs aus, die Sie dem Katalog hinzufügen möchten.

1. Klicken Sie abschließend auf **Hinzufügen**.

    Diese Ressourcen können jetzt in Zugriffspakete im Katalog aufgenommen werden.

## <a name="remove-resources-from-a-catalog"></a>Entfernen von Ressourcen aus einem Katalog

Sie können Ressourcen aus einem Katalog entfernen. Eine Ressource kann nur aus einem Katalog entfernt werden, wenn sie nicht in einem der Zugriffspakete des Katalogs verwendet wird.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, aus dem Sie Ressourcen entfernen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Wählen Sie die Ressourcen aus, die Sie entfernen möchten.

1. Klicken Sie auf **Entfernen**. Oder klicken Sie auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Ressource entfernen**.

## <a name="add-catalog-owners-or-access-package-managers"></a>Hinzufügen von Katalogbesitzern oder Zugriffspaketmanagern

Wenn Sie die Verwaltung des Katalogs oder der Zugriffspakete im Katalog delegieren möchten, fügen Sie Katalogbesitzer oder Zugriffspaketmanager hinzu. Der Benutzer, der einen Katalog erstellt, ist der erste Katalogbesitzer.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Administratoren hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Rollen und Administratoren**.

1. Klicken Sie auf **Besitzer hinzufügen** oder **Zugriffspaketmanager hinzufügen**, um die Mitglieder für diese Rollen auszuwählen.

1. Klicken Sie auf **Auswählen**, um diese Mitglieder hinzuzufügen.

## <a name="edit-a-catalog"></a>Bearbeiten eines Katalogs

Sie können den Namen und die Beschreibung eines Katalogs bearbeiten. Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie bearbeiten möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Bearbeiten**.

1. Bearbeiten Sie den Namen oder die Beschreibung des Katalogs.

1. Klicken Sie auf **Speichern**.

## <a name="delete-a-catalog"></a>Löschen eines Katalogs

Sie können einen Katalog nur löschen, wenn er keine Zugriffspakete enthält.

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie löschen möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Löschen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Verwalten eines Zugriffspakets](entitlement-management-access-package-create.md)
