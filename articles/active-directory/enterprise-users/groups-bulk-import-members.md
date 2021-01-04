---
title: Massenupload zum Hinzufügen oder Erstellen von Mitgliedern in einer Gruppe – Azure Active Directory | Microsoft-Dokumentation
description: Fügen Sie Gruppenmitglieder in einem Massenvorgang im Azure Active Directory Admin Center hinzu.
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
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547745"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Massenhinzufügen von Gruppenmitgliedern in Azure Active Directory

Über das Azure AD-Portal (Azure Active Directory) können Sie eine große Anzahl von Mitgliedern zu einer Gruppe hinzufügen, indem Sie mithilfe einer CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) einen Massenimport von Gruppenmitgliedern ausführen.

## <a name="understand-the-csv-template"></a>Grundlegendes zur CSV-Vorlage

Laden Sie die CSV-Vorlage für den Massenupload herunter, und füllen Sie sie aus, um Azure AD-Gruppenmitglieder in einem Vorgang hinzuzufügen. Ihre CSV-Vorlage ähnelt etwa dem folgenden Beispiel:

![Hochzuladendes Arbeitsblatt mit Anmerkungen zum Zweck und den Werten für die Zeilen und Spalten](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften:** Das Format der Spaltenüberschriften lautet &lt;*Elementname*&gt; [Eigenschaftenname] &lt;*Required (erforderlich) oder leer*&gt;. Beispiel: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Einige ältere Versionen der Vorlage können geringfügige Abweichungen aufweisen. Für Änderungen der Gruppenmitgliedschaft können Sie den Bezeichner auswählen: Mitgliedsobjekt-ID oder Benutzerprinzipalname.
- **Beispielzeile:** In der Vorlage ist eine Zeile mit Beispielen für zulässige Werte für alle Spalten enthalten. Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten zwei Zeilen der hochzuladenden Vorlage dürfen nicht entfernt oder geändert werden, da der Upload sonst nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Es wird davon abgeraten, der Vorlage neue Spalten hinzuzufügen. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Es wird empfohlen, möglichst häufig die neueste Version der CSV-Vorlage herunterzuladen.
- Fügen Sie mindestens die UPNs oder Objekt-IDs von zwei Benutzern hinzu, um die Datei erfolgreich hochzuladen.

## <a name="to-bulk-import-group-members"></a>So führen Sie den Massenimport von Gruppenmitgliedern aus

1. Melden Sie sich mit dem Konto eines Benutzeradministrators in der Organisation beim [Azure-Portal](https://portal.azure.com) an. Gruppenbesitzer können Mitglieder von Gruppen, die ihnen gehören, ebenfalls in einem Massenvorgang importieren.
1. Wählen Sie in Azure AD **Gruppen** > **Alle Gruppen** aus.
1. Öffnen Sie die Gruppe, der Sie Mitglieder hinzufügen möchten, und wählen Sie **Mitglieder** aus.
1. Wählen Sie auf der Seite **Mitglieder** die Option **Mitglieder importieren** aus.
1. Wählen Sie auf der Seite **Massenimport von Gruppenmitgliedern** die Option **Herunterladen** aus, um die CSV-Dateivorlage mit den erforderlichen Gruppenmitgliedseigenschaften zu erhalten.

    ![Der Befehl „Mitglieder importieren“ ist auf der Profilseite für die Gruppe zu finden.](./media/groups-bulk-import-members/import-panel.png)

1. Öffnen Sie die CSV-Datei, und fügen Sie für jedes in die Gruppe zu importierende Gruppenmitglied eine Zeile hinzu (die erforderlichen Werte sind entweder **Mitgliedsobjekt-ID** oder **Benutzerprinzipalname**). Speichern Sie dann die Datei.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Die CSV-Datei enthält Namen und IDs der zu importierenden Mitglieder":::

1. Navigieren Sie auf der Seite **Massenimport von Gruppenmitgliedern** unter **CSV-Datei hochladen** zur entsprechenden Datei. Wenn Sie die Datei auswählen, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird auf der Seite für den Massenimport die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn die Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zu starten, der die Gruppenmitglieder in die Gruppe importiert.
1. Nach Abschluss des Importvorgangs wird eine Benachrichtigung angezeigt, dass der Massenvorgang erfolgreich abgeschlossen wurde.

## <a name="check-import-status"></a>Überprüfen des Importstatus

Auf der Seite **Ergebnisse von Massenvorgängen** können Sie den Status aller Ihrer ausstehenden Massenanforderungen anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Wenn Sie ausführliche Informationen zu jedem Zeilenelement des Massenvorgangs erhalten möchten, wählen Sie die Werte unter der Spalte **# Erfolg**, **# Fehler** oder **Anforderungen insgesamt** aus. Wenn Fehler aufgetreten sind, werden die Fehlerursachen aufgeführt.

## <a name="bulk-import-service-limits"></a>Diensteinschränkungen beim Massenimport

Jede Massenaktivität zum Importieren einer Liste von Gruppenmitgliedern kann bis zu einer Stunde dauern. Dadurch wird der Import einer Liste von maximal 40.000 Mitgliedern ermöglicht.

## <a name="next-steps"></a>Nächste Schritte

- [Massenentfernung von Gruppenmitgliedern](groups-bulk-remove-members.md)
- [Mitglieder einer Gruppe herunterladen](groups-bulk-download-members.md)
- [Herunterladen einer Liste aller Gruppen](groups-bulk-download.md)
