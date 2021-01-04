---
title: Erweiterungen bei der Benutzerverwaltung (Vorschau) – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Benutzersuche, Filterung und weitere Informationen zu Benutzern in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 296679135063e2c7e1b7f0e3ffd193c8f18c3acf
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576263"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Erweiterungen bei der Benutzerverwaltung (Vorschau) – Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie die Vorschau von Erweiterungen bei der Benutzerverwaltung im Azure AD-Portal (Azure Active Directory) verwenden können. Die Seiten **Alle Benutzer** und **Gelöschte Benutzer** wurden aktualisiert und enthalten nun zusätzliche Informationen sowie eine vereinfachte Benutzersuche. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zu den Änderungen in dieser Vorschau gehören:

- Mehr sichtbare Benutzereigenschaften wie Objekt-ID, Status der Verzeichnissynchronisierung, Erstellungstyp und Identitätsaussteller
- Die Suchfunktion lässt jetzt ein Suche von Teilzeichenfolgen und einer Kombination aus Namen, E-Mail-Adressen und Objekt-IDs zu
- Verbessertes Filtern nach Benutzertyp („Mitglied“, „Gast“, „kein“), Status der Verzeichnissynchronisierung, Erstellungstyp, Name des Unternehmens und Domänenname
- Neue Sortierfunktionen für Eigenschaften wie Name und Benutzerprinzipalname
- Eine neue Gesamtanzahl von Benutzern, die bei Suchvorgängen oder Filtern aktualisiert wird

> [!NOTE]
> Diese Vorschauversion ist derzeit für Azure AD B2C-Mandanten nicht verfügbar.

## <a name="find-the-preview"></a>Verwenden der Vorschau

Die Vorschau ist standardmäßig aktiviert, sodass Sie sie sofort verwenden können. Klicken Sie auf der Seite **Alle Benutzer** auf **Previewfunktionen**, um die neuesten Features und Verbesserungen kennenzulernen. Alle Seiten, die als Teil dieser Vorschauversion aktualisiert wurden, zeigen ein Vorschautag an. Wenn Probleme auftreten, können Sie jederzeit zurück zur Legacybenutzeroberfläche wechseln:

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) an, und klicken Sie auf **Benutzer**.
1. Klicken Sie auf der Seite **Benutzer – Alle Benutzer** auf das Banner oben auf der Seite.
1. Deaktivieren Sie im Bereich **Previewfunktionen** die Option **Erweiterte Benutzerverwaltung**.

   ![Aktivieren und Deaktivieren der Option „Erweiterte Benutzerverwaltung“](./media/users-search-enhanced/enable-preview.png)

Wir freuen uns über Ihr Feedback, denn es gibt uns wertvolle Anregungen zur Verbesserung unserer Benutzeroberflächen.

## <a name="more-user-properties"></a>Erweiterte Benutzereigenschaften

Auf den Seiten **Alle Benutzer** und **Gelöschte Benutzer** wurden einige Änderungen an den Spalten vorgenommen. Den vorhandenen Spalten zur Verwaltung der Benutzerliste wurden weitere Spalten hinzugefügt.

### <a name="all-users-page"></a>Seite „Alle Benutzer“

Die folgenden Benutzereigenschaften werden auf der Seite **Alle Benutzer** angezeigt:

- Name: Der Anzeigename des Benutzers.
- Benutzerprinzipalname: Der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers.
- Benutzertyp: „Mitglied“, „Gast“, „Ohne“
- Verzeichnissynchronisierung: Gibt an, ob der Benutzer aus einem lokalen Verzeichnis synchronisiert wurde.
- Identitätsaussteller: Die Aussteller der Identität, die für die Anmeldung bei einem Benutzerkonto verwendet wird.
- Objekt-ID: Die Objekt-ID des Benutzers.
- Erstellungstyp: Gibt an, wie das Benutzerkonto erstellt wurde.
- Name des Unternehmens: Der Name des Unternehmens, dem der Benutzer zugeordnet ist.
- Einladungsstatus: Der Status der Einladung für einen Gastbenutzer.
- E-Mail-Adresse: Die E-Mail-Adresse des Benutzers.
- Letzte Anmeldung: das Datum, an dem sich der Benutzer zuletzt angemeldet hat. Diese Eigenschaft ist nur für Benutzer mit der Berechtigung zum Lesen von Überwachungsprotokollen (Reporting_ApplicationAuditLogs_Read) sichtbar.

![Neue Benutzereigenschaften auf den Seiten „Alle Benutzer“ und „Gelöschte Benutzer“](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Seite „Gelöschte Benutzer“

Auf der Seite **Gelöschte Benutzer** werden alle auf der Seite **Alle Benutzer** verfügbaren Spalten angezeigt, sowie die folgenden zusätzlichen Spalten:

- Datum der Löschung: Das Datum, an dem der Benutzer das erste Mal aus der Organisation gelöscht wurde (Benutzer sind wiederherstellbar).
- Datum der endgültigen Löschung: Das Datum, an dem der Vorgang zum permanenten Löschen des Benutzers aus der Organisation automatisch beginnt. 

> [!NOTE]
> Löschtermine werden in koordinierter Weltzeit (UTC) angezeigt.

Einige Spalten werden standardmäßig angezeigt. Wenn Sie weitere Spalten hinzufügen möchten, klicken Sie auf der Seite auf **Spalten**, wählen Sie die gewünschten Spaltennamen aus, und klicken Sie zum Speichern der Einstellungen auf **OK**.

### <a name="identity-issuers"></a>Identitätsaussteller

Wenn Sie zusätzliche Informationen zu einem Aussteller anzeigen möchten, wie etwa den Anmeldetyp und die zugewiesene Aussteller-ID, klicken Sie in der Spalte **Identitätsaussteller** auf einen beliebigen Eintrag. Die Einträge in der Spalte **Identitätsaussteller** können mehrere Werte enthalten. Sind für eine Benutzeridentität mehrere Aussteller vorhanden, wird auf den Seiten **Alle Benutzer** und **Gelöschte Benutzer** in der Spalte **Identitätsaussteller** das Wort „Mehrere“ angezeigt. Außerdem werden im Detailbereich alle Aussteller aufgelistet.

> [!NOTE]
> Die Spalte **Quelle** wurde für eine präzisere Filterung durch mehrere Spalten (wie etwa **Erstellungstyp**, **Verzeichnissynchronisierung** und **Identitätsaussteller**) ersetzt.

## <a name="user-list-search"></a>Suchen in der Benutzerliste

Wenn Sie eine Suchzeichenfolge eingeben, arbeitet die Suchfunktion nun mit „beginnt mit“ und der Suche nach Teilzeichenfolgen in Namen, E-Mail-Adressen oder Objekt-IDs. Wenn Sie beliebige dieser Attribute in das Suchfeld eingeben, wird bei der Suche automatisch in allen diesen Eigenschaften nach Übereinstimmungen gesucht. Die Suche nach Teilzeichenfolgen wird nur auf ganze Wörter angewendet. Sie können eine Suche auf der Seite **Alle Benutzer** oder **Gelöschte Benutzer** durchführen.

## <a name="user-list-filtering"></a>Filtern in der Benutzerliste

Die erweiterten Filterfunktionen bieten zusätzliche Filteroptionen für die Seiten **Alle Benutzer** und **Gelöschte Benutzer**. Sie können nun nach mehreren Eigenschaften gleichzeitig und auch nach einer höheren Anzahl von Eigenschaften filtern.

### <a name="filtering-all-users-list"></a>Filtern der Liste „Alle Benutzer“

Nach den folgenden Eigenschaften kann auf der Seite **Alle Benutzer** gefiltert werden:

- Benutzertyp: „Mitglied“, „Gast“, „kein“
- Status der Verzeichnissynchronisierung: „Ja“, „Nein“
- Erstellungstyp: „Einladung“, „Verifizierte E-Mail-Adresse“, „Lokales Konto“
- Einladungsstatus: Ausstehend, akzeptiert
- Domänenname: Geben Sie einen Domänennamen ein.
- Name des Unternehmens: Geben Sie einen Unternehmensnamen ein.
- Verwaltungseinheit: Wählen Sie diese Option aus, wenn Sie den Bereich der angezeigten Benutzer auf eine einzelne Verwaltungseinheit einschränken möchten. Weitere Informationen finden Sie unter [Verwalten von Verwaltungseinheiten (Vorschau)](../roles/administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtern der Liste „Gelöschte Benutzer“

Auf der Seite **Gelöschte Benutzer** werden Filter bereitgestellt, die auf der Seite **Alle Benutzer** nicht verfügbar sind. Nach den folgenden Eigenschaften kann auf der Seite **Gelöschte Benutzer** gefiltert werden:

- Benutzertyp: „Mitglied“, „Gast“, „kein“
- Status der Verzeichnissynchronisierung: „Ja“, „Nein“
- Erstellungstyp: „Einladung“, „Verifizierte E-Mail-Adresse“, „Lokales Konto“
- Einladungsstatus: „Akzeptanz steht aus“, „Akzeptiert“
- Datum der Löschung: Letzte 7, 14 oder 30 Tage
- Domänenname: Geben Sie einen Domänennamen ein.
- Name des Unternehmens: Geben Sie einen Unternehmensnamen ein.
- Datum der endgültigen Löschung: Letzte 7, 14 oder 30 Tage

## <a name="user-list-sorting"></a>Sortierung der Benutzerliste

Sie können jetzt auf den Seiten **Alle Benutzer** und **Gelöschte Benutzer** nach Name und Benutzerprinzipalname sortieren. Außerdem können Sie in der Liste **Gelöschte Benutzer** nach dem Datum der Löschung sortieren.

## <a name="user-list-counts"></a>Anzahl der Benutzerlisten

Sie können die Gesamtzahl der Benutzer auf den Seiten **Alle Benutzer** und **Gelöschte Benutzer** anzeigen. Während Sie die Listen durchsuchen oder filtern, wird die Anzahl aktualisiert, sodass sie die Gesamtzahl der gefundenen Benutzer widerspiegelt.

![Abbildung der Anzahl der Benutzerlisten auf der Seite „Alle Benutzer“](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen

Frage | Antwort
-------- | ------
Warum wird der gelöschte Benutzer noch angezeigt, wenn das Datum der permanenten Löschung abgelaufen ist? | Das Datum der permanenten Löschung wird in der Zeitzone UTC angezeigt, sodass es möglicherweise nicht mit Ihrer aktuellen Zeitzone übereinstimmt. Außerdem ist dieses Datum das früheste Datum, nach dem der Benutzer endgültig aus der Organisation gelöscht wird, sodass dieser Vorgang möglicherweise noch läuft. Dauerhaft gelöschte Benutzer werden automatisch aus der Liste entfernt.
Sind die Funktionen für Massenvorgänge für Benutzer und Gäste noch verfügbar? | Massenvorgänge wie die Massenerstellung, die Masseneinladung, die Massenlöschung oder der Download von Benutzern sind weiterhin für Benutzer und Gäste verfügbar. Sie wurden im Menü **Massenvorgänge** zusammengeführt. Die Option **Massenvorgänge** befindet sich oben auf der Seite **Alle Benutzer**.
Was ist mit der Spalte „Quelle“ passiert? | Die Spalte **Quelle** wurde durch andere Spalten mit ähnlichen Informationen ersetzt. Sie können nun unabhängig voneinander nach diesen Werte filtern. Diese sind z. B. **Erstellungstyp**, **Verzeichnissynchronisierung** und **Identitätsaussteller**.
Was ist mit der Spalte „Benutzername“ passiert? | Die Spalte **Benutzername** wurde in **Benutzerprinzipalname** umbenannt. Dieser Name beschreibt den Inhalt der Spalte besser. Zudem wird nun für B2B-Gäste der vollständige Benutzerprinzipalname angezeigt. Dies entspricht auch der Anzeige in MS Graph.  

## <a name="next-steps"></a>Nächste Schritte

Vorgänge von Benutzern

- [Hinzufügen oder Ändern von Profilinformationen](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Hinzufügen oder Löschen von Benutzern](../fundamentals/add-users-azure-active-directory.md)

Massenvorgänge

- [Herunterladen einer Benutzerliste](users-bulk-download.md)
- [Benutzer per Massenvorgang hinzufügen](users-bulk-add.md)
- [Massenlöschung von Benutzern](users-bulk-delete.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
