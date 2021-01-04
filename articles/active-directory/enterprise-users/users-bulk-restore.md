---
title: Massenwiederherstellung gelöschter Benutzer im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Gelöschte Benutzer in einem Massenvorgang im Azure AD Admin Center in Azure Active Directory wiederherstellen
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571503"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Massenwiederherstellung gelöschter Benutzer in Azure Active Directory

Azure Active Directory (Azure AD) unterstützt Vorgänge zur Massenwiederherstellung von Benutzern sowie das Herunterladen von Listen von Benutzern, Gruppen und Gruppenmitgliedern.

## <a name="understand-the-csv-template"></a>Grundlegendes zur CSV-Vorlage

Laden Sie die CSV-Vorlage herunter, und füllen Sie sie aus, um mehrere Azure AD-Benutzer gleichzeitig wiederherzustellen. Die heruntergeladene CSV-Vorlage ähnelt möglicherweise dem folgenden Beispiel:

![Hochzuladendes Arbeitsblatt mit Anmerkungen zum Zweck und den Werten für die Zeilen und Spalten](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften:** Das Format der Spaltenüberschriften lautet &lt;*Elementname*&gt; [Eigenschaftenname] &lt;*Required (erforderlich) oder leer*&gt;. Beispiel: `Object ID [objectId] Required`. Einige ältere Versionen der Vorlage können geringfügige Abweichungen aufweisen.
- **Beispielzeile:** In der Vorlage ist eine Zeile mit Beispielen für zulässige Werte für alle Spalten enthalten. Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten zwei Zeilen der hochzuladenden Vorlage dürfen nicht entfernt oder geändert werden, da der Upload sonst nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Es wird davon abgeraten, der Vorlage neue Spalten hinzuzufügen. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Es wird empfohlen, dass Sie möglichst häufig die neueste Version der CSV-Vorlage herunterladen.

## <a name="to-bulk-restore-users"></a>So stellen Sie Benutzer in einem Massenvorgang wieder her

1. [Melden Sie sich bei ihrer Azure AD-Organisation](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Azure AD-Organisation verfügt.
1. Wählen Sie in Azure AD **Benutzer** > **Gelöscht** aus.
1. Wählen Sie auf der Seite **Gelöschte Benutzer** die Option **Massenwiederherstellung** aus, um eine gültige CSV-Datei mit den Eigenschaften der wiederherzustellenden Benutzer hochzuladen.

    ![Auf der Seite „Gelöschte Benutzer“ den Benutzer „Massenwiederherstellung“ auswählen](./media/users-bulk-restore/bulk-restore.png)

1. Öffnen Sie die CSV-Vorlage, und fügen Sie eine Zeile für jeden Benutzer hinzu, den Sie wiederherstellen möchten. Der einzige erforderliche Wert ist **ObjectID**. Speichern Sie dann die Datei.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Auswählen einer lokalen CSV-Datei, in der Sie die Benutzer auflisten, die Sie hinzufügen möchten":::

1. Navigieren Sie auf der Seite **Massenwiederherstellung von Benutzern** unter **CSV-Datei hochladen** zur entsprechenden Datei. Wenn Sie die Datei auswählen und auf **Senden** klicken, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn Ihre Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zum Wiederherstellen der Benutzer zu starten.
1. Nach Abschluss des Wiederherstellungsvorgangs wird eine Benachrichtigung angezeigt, dass der Massenvorgang erfolgreich abgeschlossen wurde.

Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite **Ergebnisse von Massenvorgängen** herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler.

## <a name="check-status"></a>Status überprüfen

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Als Nächstes können Sie entweder im Azure-Portal oder mithilfe von PowerShell überprüfen, ob die wiederhergestellten Benutzer in der Azure AD-Organisation vorhanden sind.

## <a name="view-restored-users-in-the-azure-portal"></a>Anzeigen wiederhergestellter Benutzer im Azure-Portal

1. [Melden Sie sich beim Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie unter **Anzeigen** die Option **Alle Benutzer** aus, und überprüfen Sie, ob die wiederhergestellten Benutzer aufgelistet werden.

### <a name="view-users-with-powershell"></a>Anzeigen von Benutzern mit PowerShell

Führen Sie den folgenden Befehl aus:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Jetzt sollten die wiederhergestellten Benutzer aufgelistet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Massenimport von Benutzern](users-bulk-add.md)
- [Massenlöschung von Benutzern](users-bulk-delete.md)
- [Herunterladen einer Benutzerliste](users-bulk-download.md)
