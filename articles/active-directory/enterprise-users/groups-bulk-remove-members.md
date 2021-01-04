---
title: Massenentfernung von Gruppenmitgliedern durch Hochladen einer CSV-Datei – Azure Active Directory | Microsoft-Dokumentation
description: Entfernen Sie Gruppenmitglieder in Massenvorgängen in Azure Admin Center.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874d434e545555013812714df4d1bd23de9c8543
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488945"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Massenentfernung von Gruppenmitgliedern in Azure Active Directory

Über das Azure AD-Portal (Azure Active Directory) können Sie eine große Anzahl von Mitgliedern aus einer Gruppe entfernen, indem Sie mithilfe einer CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) eine Massenentfernung von Gruppenmitgliedern ausführen.

## <a name="understand-the-csv-template"></a>Grundlegendes zur CSV-Vorlage

Laden Sie die CSV-Vorlage für den Massenupload herunter, und füllen Sie sie aus, um Azure AD-Gruppenmitglieder in einem Vorgang hinzuzufügen. Ihre CSV-Vorlage ähnelt etwa dem folgenden Beispiel:

![Hochzuladendes Arbeitsblatt mit Anmerkungen zum Zweck und den Werten für die Zeilen und Spalten](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften:** Das Format der Spaltenüberschriften lautet &lt;*Elementname*&gt; [Eigenschaftenname] &lt;*Required (erforderlich) oder leer*&gt;. Beispiel: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Einige ältere Versionen der Vorlage können geringfügige Abweichungen aufweisen. Für Änderungen der Gruppenmitgliedschaft können Sie den Bezeichner auswählen: Mitgliedsobjekt-ID oder Benutzerprinzipalname.
- **Beispielzeile:** In der Vorlage ist eine Zeile mit Beispielen für zulässige Werte für alle Spalten enthalten. Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten zwei Zeilen der hochzuladenden Vorlage dürfen nicht entfernt oder geändert werden, da der Upload sonst nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Es wird davon abgeraten, der Vorlage neue Spalten hinzuzufügen. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Es wird empfohlen, dass Sie möglichst häufig die neueste Version der CSV-Vorlage herunterladen.

## <a name="to-bulk-remove-group-members"></a>So entfernen Sie Gruppenmitglieder in einem Massenvorgang

1. Melden Sie sich mit dem Konto eines Benutzeradministrators in der Organisation beim [Azure-Portal](https://portal.azure.com) an. Gruppenbesitzer können Mitglieder von Gruppen, die ihnen gehören, ebenfalls in einem Massenvorgang entfernen.
1. Wählen Sie in Azure AD **Gruppen** > **Alle Gruppen** aus.
1. Öffnen Sie die Gruppe, aus der Sie Mitglieder entfernen möchten, und wählen Sie **Mitglieder** aus.
1. Wählen Sie auf der Seite **Mitglieder** die Option **Mitglieder entfernen** aus.
1. Wählen Sie auf der Seite **Massenentfernung von Gruppenmitgliedern** die Option **Herunterladen** aus, um die CSV-Dateivorlage mit den erforderlichen Gruppenmitgliedseigenschaften zu erhalten.

   ![Der Befehl „Mitglieder entfernen“ ist auf der Profilseite für die Gruppe zu finden.](./media/groups-bulk-remove-members/remove-panel.png)

1. Öffnen Sie die CSV-Datei, und fügen Sie für jedes aus der Gruppe zu entfernende Gruppenmitglied eine Zeile hinzu (die erforderlichen Werte sind „Mitgliedsobjekt-ID“ oder „Benutzerprinzipalname“). Speichern Sie dann die Datei.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Die CSV-Datei enthält Namen und IDs der zu entfernenden Mitglieder":::

1. Navigieren Sie auf der Seite **Massenentfernung von Gruppenmitgliedern** unter **CSV-Datei hochladen** zur entsprechenden Datei. Wenn Sie die Datei auswählen, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird auf der Seite für den Massenimport die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn die Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zu starten, der die Gruppenmitglieder aus der Gruppe entfernt.
1. Nach Abschluss des Entfernungsvorgangs wird eine Benachrichtigung angezeigt, dass der Massenvorgang erfolgreich abgeschlossen wurde.

## <a name="check-removal-status"></a>Überprüfen des Status für Entfernen

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

Wenn Sie ausführliche Informationen zu jedem Zeilenelement des Massenvorgangs erhalten möchten, wählen Sie die Werte unter der Spalte **# Erfolg**, **# Fehler** oder **Anforderungen insgesamt** aus. Wenn Fehler aufgetreten sind, werden die Fehlerursachen aufgeführt.

## <a name="bulk-removal-service-limits"></a>Diensteinschränkungen bei Massenentfernung

Jede Massenaktivität zum Entfernen einer Liste von Gruppenmitgliedern kann bis zu einer Stunde dauern. Dadurch wird das Entfernen einer Liste von mindestens 40.000 Mitgliedern ermöglicht.

## <a name="next-steps"></a>Nächste Schritte

- [Massenimport von Gruppenmitgliedern ausführen](groups-bulk-import-members.md)
- [Mitglieder einer Gruppe herunterladen](groups-bulk-download-members.md)
- [Herunterladen einer Liste aller Gruppen](groups-bulk-download.md)
