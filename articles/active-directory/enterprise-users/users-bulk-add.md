---
title: Massenerstellung von Benutzern im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Fügen Sie Benutzer in einem Massenvorgang im Azure Admin Center in Azure Active Directory hinzu.
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547813"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Massenerstellung von Benutzern in Azure Active Directory

Azure Active Directory (Azure AD) unterstützt Vorgänge der Massenerstellung und -löschung von Benutzern sowie das Herunterladen von Benutzerlisten. Füllen Sie einfach die CSV-Vorlage aus, die Sie im Azure AD-Portal herunterladen können.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Zur Massenerstellung von Benutzern im Verwaltungsportal müssen Sie als globaler Administrator oder Benutzeradministrator angemeldet sein.

## <a name="understand-the-csv-template"></a>Grundlegendes zur CSV-Vorlage

Laden Sie die CSV-Vorlage für den Massenupload herunter, und füllen Sie sie aus, um mehrere Azure AD-Benutzer gleichzeitig zu erstellen. Die heruntergeladene CSV-Vorlage ähnelt etwa dem folgenden Beispiel:

![Hochzuladendes Arbeitsblatt mit Anmerkungen zum Zweck und den Werten für die Zeilen und Spalten](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Wenn Sie nur einen Eintrag mithilfe der CSV-Vorlage hinzufügen, müssen Sie Zeile 3 beibehalten und den neuen Eintrag zu Zeile 4 hinzufügen.

### <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften:** Das Format der Spaltenüberschriften lautet &lt;*Elementname*&gt; [Eigenschaftenname] &lt;*Required (erforderlich) oder leer*&gt;. Beispiel: `Name [displayName] Required`. Einige ältere Versionen der Vorlage können geringfügige Abweichungen aufweisen.
- **Beispielzeile:** In der Vorlage ist eine Zeile mit Beispielen für zulässige Werte für alle Spalten enthalten. Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten zwei Zeilen der hochzuladenden Vorlage dürfen nicht entfernt oder geändert werden, da der Upload sonst nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Es wird davon abgeraten, neue Spalten zur Vorlage hinzuzufügen. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Es wird empfohlen, möglichst häufig die neueste Version der CSV-Vorlage herunterzuladen.
- Vergewissern Sie sich unbedingt, dass vor und nach allen Feldern keine unbeabsichtigten Leerräume vorhanden sind. Beim **Benutzerprinzipalnamen** würde ein solcher Leerraum einen Importfehler verursachen.

## <a name="to-create-users-in-bulk"></a>So erstellen Sie Benutzer in einem Massenvorgang

1. [Melden Sie sich bei ihrer Azure AD-Organisation](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Wählen Sie in Azure AD **Benutzer** > **Massenerstellung** aus.
1. Wählen Sie auf der Seite **Massenerstellung von Benutzern** die Option **Herunterladen** aus, um eine gültige CSV-Datei mit Benutzereigenschaften herunterzuladen. Fügen Sie dann die Benutzer hinzu, die Sie erstellen möchten.

   ![Wählen Sie eine lokale CSV-Datei aus, in der Sie die Benutzer auflisten, die Sie hinzufügen möchten.](./media/users-bulk-add/upload-button.png)

1. Öffnen Sie die CSV-Datei, und fügen Sie eine Zeile für jeden Benutzer hinzu, den Sie erstellen möchten. Die einzigen erforderlichen Werte sind **Name**, **Benutzerprinzipalname**, **Anfängliches Kennwort** und **Anmeldung blockieren (Ja/Nein)** . Speichern Sie dann die Datei.

   [![Die CSV-Datei enthält die Namen und IDs der zu erstellenden Benutzer](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Navigieren Sie auf der Seite **Massenerstellung von Benutzern** unter „CSV-Datei hochladen“ zur entsprechenden Datei. Wenn Sie die Datei auswählen und auf **Senden** klicken, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn Ihre Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zum Importieren der neuen Benutzer zu starten.
1. Nach Abschluss des Importvorgangs wird eine Benachrichtigung mit dem Auftragsstatus des Massenvorgangs angezeigt.

Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite **Ergebnisse von Massenvorgängen** herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler. Die Dateiübermittlung muss mit der angegebenen Vorlage übereinstimmen und die genauen Spaltennamen enthalten.

## <a name="check-status"></a>Status überprüfen

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

   [![Überprüfen des Erstellungsstatus auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Als Nächstes können Sie entweder im Azure-Portal oder mithilfe von PowerShell überprüfen, ob die erstellten Benutzer in der Azure AD-Organisation vorhanden sind.

## <a name="verify-users-in-the-azure-portal"></a>Überprüfen von Benutzern im Azure-Portal

1. [Melden Sie sich beim Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie unter **Anzeigen** die Option **Alle Benutzer** aus, und überprüfen Sie, ob die erstellten Benutzer aufgelistet werden.

### <a name="verify-users-with-powershell"></a>Überprüfen von Benutzern mit PowerShell

Führen Sie den folgenden Befehl aus:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Jetzt sollten die erstellten Benutzer aufgelistet werden.

## <a name="bulk-import-service-limits"></a>Diensteinschränkungen beim Massenimport

Jede Massenaktivität zum Erstellen von Benutzern kann bis zu einer Stunde dauern. Dies ermöglicht die Massenerstellung von mindestens 50.000 Benutzern.

## <a name="next-steps"></a>Nächste Schritte

- [Massenlöschung von Benutzern](users-bulk-delete.md)
- [Herunterladen einer Benutzerliste](users-bulk-download.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
