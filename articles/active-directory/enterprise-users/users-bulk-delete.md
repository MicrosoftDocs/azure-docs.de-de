---
title: Massenlöschung von Benutzern im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Löschen Sie Benutzer in einem Massenvorgang im Azure Admin Center in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574462"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Massenlöschung von Benutzern in Azure Active Directory

Über das Azure Active Directory-Portal (Azure AD-Portal) können Sie eine große Anzahl von Mitgliedern aus einer Gruppe entfernen, indem Sie mithilfe einer CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) eine Massenlöschung von Benutzern ausführen.

## <a name="understand-the-csv-template"></a>Grundlegendes zur CSV-Vorlage

Laden Sie die CSV-Vorlage herunter, und füllen Sie sie aus, um mehrere Azure AD-Benutzer gleichzeitig zu löschen. Die heruntergeladene CSV-Vorlage ähnelt möglicherweise dem folgenden Beispiel:

![Hochzuladendes Arbeitsblatt mit Anmerkungen zum Zweck und den Werten für die Zeilen und Spalten](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften:** Das Format der Spaltenüberschriften lautet &lt;*Elementname*&gt; [Eigenschaftenname] &lt;*Required (erforderlich) oder leer*&gt;. Beispiel: `User name [userPrincipalName] Required`. Einige ältere Versionen der Vorlage können geringfügige Abweichungen aufweisen.
- **Beispielzeile:** In der Vorlage ist eine Zeile mit Beispielen für zulässige Werte für alle Spalten enthalten. Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten zwei Zeilen der hochzuladenden Vorlage dürfen nicht entfernt oder geändert werden, da der Upload sonst nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Es wird davon abgeraten, neue Spalten zur Vorlage hinzuzufügen. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Es wird empfohlen, dass Sie möglichst häufig die neueste Version der CSV-Vorlage herunterladen.

## <a name="to-bulk-delete-users"></a>So löschen Sie Benutzer in einem Massenvorgang

1. [Melden Sie sich bei ihrer Azure AD-Organisation](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Wählen Sie in Azure AD **Benutzer** > **Massenlöschung** aus.
1. Wählen Sie auf der Seite **Massenlöschung von Benutzern** die Option **Download** aus, um eine gültige CSV-Datei mit Benutzereigenschaften herunterzuladen.

   ![Wählen Sie eine lokale CSV-Datei aus, in der Sie die Benutzer auflisten, die Sie löschen möchten.](./media/users-bulk-delete/bulk-delete.png)

1. Öffnen Sie die CSV-Datei, und fügen Sie eine Zeile für jeden Benutzer hinzu, den Sie löschen möchten. Der einzige erforderliche Wert ist **Benutzerprinzipalname**. Speichern Sie dann die Datei.

   ![Die CSV-Datei enthält Namen und IDs der zu löschenden Benutzer.](./media/users-bulk-delete/delete-csv-file.png)

1. Navigieren Sie auf der Seite **Massenlöschung von Benutzern** unter **CSV-Datei hochladen** zur entsprechenden Datei. Wenn Sie die Datei auswählen und auf „Senden“ klicken, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn Ihre Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zum Löschen der Benutzer zu starten.
1. Nach Abschluss des Löschvorgangs wird eine Benachrichtigung angezeigt, dass der Massenvorgang erfolgreich abgeschlossen wurde.

Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite **Ergebnisse von Massenvorgängen** herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler.

## <a name="check-status"></a>Status überprüfen

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

   [![Überprüfen des Löschstatus auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Als Nächstes können Sie entweder im Azure-Portal oder mithilfe von PowerShell überprüfen, ob die gelöschten Benutzer in der Azure AD-Organisation vorhanden sind.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Überprüfen gelöschter Benutzer im Azure-Portal

1. Melden Sie sich beim Azure-Portal mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie unter **Anzeigen** nur **Alle Benutzer** aus, und vergewissern Sie sich, dass die gelöschten Benutzer nicht mehr aufgelistet werden.

### <a name="verify-deleted-users-with-powershell"></a>Überprüfen gelöschter Benutzer mit PowerShell

Führen Sie den folgenden Befehl aus:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Vergewissern Sie sich, dass die gelöschten Benutzer nicht mehr aufgelistet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Benutzer per Massenvorgang hinzufügen](users-bulk-add.md)
- [Herunterladen einer Benutzerliste](users-bulk-download.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
