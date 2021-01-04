---
title: Erstellen eines Überprüfungsregelsatzes
description: Erstellen Sie einen Überprüfungsregelsatz in Azure Purview, um die Datenquellen Ihrer Organisation schnell überprüfen zu können.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550854"
---
# <a name="create-a-scan-rule-set"></a>Erstellen eines Überprüfungsregelsatzes

In einem Azure Purview-Katalog können Sie Überprüfungsregelsätze erstellen, damit Sie die Datenquellen Ihrer Organisation schnell überprüfen können.

Ein Überprüfungsregelsatz ist ein Container zum Gruppieren eines Satzes mit Überprüfungsregeln, damit Sie diese leicht einer Überprüfung zuordnen können. Beispielsweise können Sie einen Standard-Überprüfungsregelsatz für jeden Datenquellentyp erstellen und diese Sätze dann standardmäßig für alle Überprüfungen in Ihrem Unternehmen verwenden. Ggf. möchten Sie auch zulassen, dass Benutzer mit den passenden Berechtigungen je nach geschäftlicher Anforderung weitere Überprüfungsregelsätze mit anderen Konfigurationen erstellen können.

## <a name="steps-to-create-a-scan-rule-set"></a>Schritte zum Erstellen eines Überprüfungsregelsatzes

Erstellen Sie wie folgt einen Überprüfungsregelsatz:

1. Wählen Sie in Ihrem Azure Purview-Katalog die Option **Verwaltungscenter** aus.

1. Wählen Sie im linken Bereich die Option **Scan rule sets** (Überprüfungsregelsätze) und dann **Neu** aus.

1. Wählen Sie auf der Seite **New scan rule set** (Neuer Überprüfungsregelsatz) in der Dropdownliste **Quellentyp** die Datenquellen aus, die für die Überprüfungsfunktion des Katalogs unterstützt werden. Sie können einen Überprüfungsregelsatz für jeden Datenquellentyp erstellen, den Sie überprüfen möchten.

1. Geben Sie unter **Name** einen Namen für Ihren Überprüfungsregelsatz ein. Die maximale Länge beträgt 63 Zeichen, und Leerzeichen sind nicht zulässig. Geben Sie optional unter **Beschreibung** eine Beschreibung ein. Die maximale Länge beträgt 256 Zeichen.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Screenshot: Seite „Scan rule sets“ (Überprüfungsregelsätze)" border="true":::

1. Wählen Sie **Weiter**.

   Die Seite **Select file types** (Dateitypen auswählen) wird angezeigt. Beachten Sie, dass die Optionen für Dateitypen auf dieser Seite basierend auf dem Datenquellentyp variieren, den Sie auf der vorherigen Seite ausgewählt haben. Alle Dateitypen sind standardmäßig aktiviert.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Screenshot: Seite „Select file types“ (Dateitypen auswählen)":::

   Wenn Sie auf dieser Seite die Option **Document file types** (Dokumentdateitypen) auswählen, können Sie die folgenden Office-Dateitypen ein- und ausschließen: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX und XLT.

1. Sie können eine Kachel für den Dateityp aktivieren oder deaktivieren, indem Sie das entsprechende Kontrollkästchen aktivieren oder deaktivieren. Wenn Sie eine Datenquelle vom Typ „Data Lake“ auswählen (z. B. Azure Data Lake Storage Gen2 oder Azure Blob), können Sie die Dateitypen aktivieren, für die das Schema extrahiert und klassifiziert werden soll.

1. Für bestimmte Datenquellentypen können Sie auch [einen benutzerdefinierten Dateityp erstellen](#create-a-custom-file-type).

1. Wählen Sie **Weiter**.

   Die Seite **Select classification rules** (Klassifizierungsregeln auswählen) wird angezeigt. Auf dieser Seite werden die ausgewählten **Systemregeln** und **benutzerdefinierten Regeln** sowie die Gesamtzahl der ausgewählten Klassifizierungsregeln angezeigt. Standardmäßig sind alle Kontrollkästchen für die **Systemregeln** ausgewählt.

1. Für die Regeln, die Sie ein- oder ausschließen möchten, können Sie die Klassifizierungsregel-Kontrollkästchen für die **Systemregeln** global nach Kategorie aktivieren bzw. deaktivieren.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Screenshot: Seite mit Auswahl der Klassifizierungsregeln":::

1. Sie können den Kategorieknoten erweitern und einzelne Kontrollkästchen aktivieren oder deaktivieren. Falls sich für die Regel für **Argentina.DNI Number** beispielsweise eine hohe Zahl von False Positives ergibt, können Sie das entsprechende Kontrollkästchen deaktivieren.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Screenshot: Auswählen von Systemregeln":::

1. Wählen Sie **Erstellen** aus, um die Erstellung des Überprüfungsregelsatzes abzuschließen.

### <a name="create-a-custom-file-type"></a>Erstellen eines benutzerdefinierten Dateityps

Azure Purview unterstützt das Hinzufügen einer benutzerdefinierten Erweiterung und das Definieren eines benutzerdefinierten Spaltentrennzeichens in einem Überprüfungsregelsatz.

Erstellen Sie wie folgt einen benutzerdefinierten Dateityp:

1. Führen Sie die Schritte 1 bis 5 unter [Schritte zum Erstellen eines Überprüfungsregelsatzes](#steps-to-create-a-scan-rule-set) aus, oder bearbeiten Sie einen vorhandenen Überprüfungsregelsatz.

1. Wählen Sie auf der Seite **Select file types** (Dateitypen auswählen) die Option **New file type** (Neuer Dateityp) aus, um einen neuen benutzerdefinierten Dateityp zu erstellen.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Screenshot: Auswählen von „New file type“ (Neuer Dateityp) auf der Seite „Select file types“ (Dateitypen auswählen)":::

1. Geben Sie eine **Dateierweiterung** und optional eine **Beschreibung** ein.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Screenshot: Seite „New custom file type“ (Neuer benutzerdefinierter Dateityp)" border="true":::

1. Wählen Sie eine der unten angegebenen Optionen für **File contents within** (Dateiinhalt) aus, um den Typ des Dateiinhalts Ihrer Datei anzugeben:

   - Wählen Sie **Benutzerdefiniertes Trennzeichen** aus, und geben Sie Ihr eigenes **benutzerdefiniertes Trennzeichen ein** (nur ein Zeichen).

   - Wählen Sie die Option **System File Type** (Systemdateityp) und dann in der Dropdownliste **System file type** (Systemdateityp) einen Systemdateityp (z. B. XML) aus.

1. Wählen Sie **Erstellen** aus, um die benutzerdefinierte Datei zu speichern.

   Die Seite **Select file types** (Dateitypen auswählen) wird wieder angezeigt, und der neue benutzerdefinierte Dateityp wird als neue Kachel eingefügt.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Screenshot: Kachel für neuen benutzerdefinierten Dateityp auf der Seite „Select file types“ (Dateitypen auswählen)":::

1. Wählen Sie auf der Kachel für den neuen Dateityp die Option **Bearbeiten** aus, falls Sie ihn ändern oder löschen möchten.

1. Wählen Sie **Weiter** aus, um die Konfiguration des Überprüfungsregelsatzes abzuschließen.

## <a name="system-scan-rule-sets"></a>Überprüfungsregelsätze des Systems

Bei Überprüfungsregelsätzen des Systems handelt es sich um von Microsoft definierte Überprüfungsregelsätze, die für jeden Azure Purview-Katalog automatisch erstellt werden. Jedem Überprüfungsregelsatz des Systems ist ein bestimmter Datenquellentyp zugeordnet. Wenn Sie eine Überprüfung erstellen, können Sie diese einem Überprüfungsregelsatz des Systems zuordnen. Jedes Mal, wenn von Microsoft ein Update dieser Systemregelsätze durchgeführt wird, können Sie sie in Ihrem Katalog aktualisieren und das Update auf alle zugeordneten Überprüfungen anwenden.

1. Wählen Sie zum Anzeigen der Liste mit den Überprüfungsregelsätzen des Systems unter **Verwaltungscenter** die Option **Scan rule sets** (Überprüfungsregelsätze) und dann die Registerkarte **System** aus.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Screenshot: Liste mit Überprüfungsregelsätzen des Systems" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Jeder Überprüfungsregelsatz des Systems verfügt über einen **Namen**, einen **Quellentyp** und eine **Version**. Wenn Sie die Versionsnummer eines Überprüfungsregelsatzes in der Spalte **Version** auswählen, werden die Regeln angezeigt, die der aktuellen Version und den vorherigen Versionen (falls vorhanden) zugeordnet sind bzw. waren.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Screenshot: Seite mit Überprüfungsregelsätzen des Systems" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Wenn ein Update für einen Überprüfungsregelsatz des Systems verfügbar ist, können Sie in der Spalte **Version** die Option **Update** auswählen. Wählen Sie auf der Seite mit den Überprüfungsregelsätzen des Systems in der Dropdownliste **Select a new version to update** (Neue Version für Update auswählen) eine Version aus. Die Seite enthält eine Liste mit Klassifizierungsregeln des Systems, die der neuen und aktuellen Version zugeordnet sind.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Screenshot: Ändern der Version eines Überprüfungsregelsatzes des Systems" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Zuordnen einer Überprüfung zu einem Überprüfungsregelsatz des Systems

Wenn Sie eine [Überprüfung erstellen](tutorial-scan-data.md#scan-data-into-the-catalog), können Sie diese wie folgt einem Überprüfungsregelsatz des Systems zuordnen:

1. Wählen Sie auf der Seite **Select a scan rule set** (Überprüfungsregelsatz auswählen) den Überprüfungsregelsatz des Systems aus.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Screenshot: Auswählen eines Überprüfungsregelsatzes des Systems für eine Überprüfung" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Wählen Sie **Weiter** und dann **Speichern und ausführen** aus.
