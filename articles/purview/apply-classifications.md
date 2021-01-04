---
title: Anwenden von Klassifizierungen auf Ressourcen (Vorschauversion)
description: In diesem Dokument wird beschrieben, wie Sie Klassifizierungen auf Ressourcen anwenden.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550895"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Anwenden von Klassifizierungen auf Ressourcen in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Klassifizierungen auf Ressourcen anwenden.

## <a name="introduction"></a>Einführung

Klassifizierungen können vom System bereitgestellt oder vom Benutzer definiert werden. Systemklassifizierungen sind in Purview standardmäßig enthalten. Benutzerdefinierte Klassifizierungen können basierend auf einem Muster für reguläre Ausdrücke erstellt werden. Klassifizierungen können entweder automatisch oder manuell auf Ressourcen angewendet werden.

In diesem Dokument wird beschrieben, wie Sie Klassifizierungen auf Ihre Daten anwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie benutzerdefinierte Klassifizierungen gemäß Ihren Anforderungen.
- Richten Sie die Überprüfung für Ihre Datenquellen ein.

## <a name="apply-classifications"></a>Klassifizierungen anwenden
In Azure Purview können Sie System- oder benutzerdefinierte Klassifizierungen auf eine Datei-, Tabellen- oder Spaltenressource anwenden. In diesem Artikel werden die Schritte zum manuellen Anwenden von Klassifizierungen auf Ihre Ressourcen beschrieben.

### <a name="apply-classification-to-a-file-asset"></a>Anwenden der Klassifizierung auf eine Dateiressource
In Azure Purview können Dokumentationsdateien per Scanvorgang überprüft und automatisch klassifiziert werden. Wenn Sie beispielsweise über eine Datei mit dem Namen *multiple.docx* verfügen, die eine Personalausweisnummer für ein Land enthält, fügt Azure Purview der Detailseite der Dateiressource die Klassifizierung **EU-Personalausweisnummer** hinzu.

Bei einigen Szenarien kann es vorkommen, dass Sie Ihrer Dateiressource manuell Klassifizierungen hinzufügen möchten. Wenn Sie über mehrere Dateien verfügen, die in einem Ressourcensatz gruppiert sind, fügen Sie die Klassifizierungen auf der Ressourcensatzebene hinzu.

Führen Sie die folgenden Schritte aus, um eine benutzerdefinierte oder Systemklassifizierung dem Ressourcensatz einer Partition hinzuzufügen:

1. Suchen Sie nach der Partition, und navigieren Sie zur Seite mit den Ressourcendetails.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Screenshot: Seite mit Ressourcendetails":::

1. Zeigen Sie auf der Registerkarte **Übersicht** den Abschnitt **Klassifizierungen** an, um zu ermitteln, ob bereits Klassifizierungen vorhanden sind. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie in der Dropdownliste **Klassifizierungen** die spezifischen Klassifizierungen aus, die für Sie von Interesse sind. Beispiele hierfür sind **Credit Card Number** (Systemklassifizierung) und **CustomerAccountID** (benutzerdefinierte Klassifizierung).

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Screenshot: Auswählen von Klassifizierungen für eine Ressource":::

1. Wählen Sie **Speichern** aus.

1. Vergewissern Sie sich auf der Registerkarte **Übersicht**, dass die von Ihnen ausgewählten Klassifizierungen im Abschnitt **Klassifizierungen** angezeigt werden.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Screenshot: Bestätigen der Hinzufügung von Klassifizierungen zu einer Ressource":::

### <a name="apply-classification-to-a-table-asset"></a>Anwenden der Klassifizierung auf eine Tabellenressource

Beim Überprüfen Ihrer Datenquellen werden von Azure Purview nicht automatisch Klassifizierungen für Tabellenressourcen zugewiesen. Wenn Ihre Tabellenressource eine Klassifizierung aufweisen soll, müssen Sie sie manuell hinzufügen.

Fügen Sie einer Tabellenressource wie folgt eine Klassifizierung hinzu:

1. Suchen Sie nach einer Tabellenressource, die für Sie interessant ist. Ein Beispiel hierfür ist die Tabelle **Kunde**.

1. Vergewissern Sie sich, dass der Tabelle keine Klassifizierungen zugewiesen sind. Wählen Sie **Bearbeiten** aus.

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Screenshot: Anzeigen und Bearbeiten der Klassifizierungen einer Tabellenressource":::

1. Wählen Sie in der Dropdownliste **Klassifizierungen** eine oder mehrere Klassifizierungen aus. In diesem Beispiel wird eine benutzerdefinierte Klassifizierung mit dem Namen **CustomerInfo** verwendet, aber Sie können für diesen Schritt beliebige Klassifizierungen auswählen.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Screenshot: Auswählen von Klassifizierungen für eine Tabellenressource":::

1. Wählen Sie **Speichern** aus, um die Klassifizierungen zu speichern.

1. Vergewissern Sie sich auf der Seite **Übersicht**, dass Ihre neuen Klassifizierungen von Azure Purview hinzugefügt wurden.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Screenshot: Bestätigen, dass Klassifizierungen einer Tabellenressource hinzugefügt wurden":::

### <a name="add-classification-to-a-column-asset"></a>Hinzufügen einer Klassifizierung zu einer Spaltenressource

Azure Purview überprüft automatisch alle Spaltenressourcen und fügt dafür Klassifizierungen hinzu. Falls Sie die Klassifizierung ändern möchten, können Sie dies auf Spaltenebene durchführen.

Fügen Sie einer Spalte wie folgt eine Klassifizierung hinzu:

1. Suchen Sie nach der Spaltenressource, und wählen Sie sie aus. Wählen Sie anschließend auf der Registerkarte **Übersicht** die Option **Bearbeiten** aus.

1. Wählen Sie die Registerkarte **Schema** aus.

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Screenshot: Bearbeiten des Schemas einer Spalte":::

1. Ermitteln Sie die Spalten, an denen Sie interessiert sind, und wählen Sie die Option **Add a classification** (Klassifizierung hinzufügen) aus. In diesem Beispiel wird die Klassifizierung **Common Passwords** (Häufige Kennwörter) der Spalte **PasswordHash** hinzugefügt.

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Screenshot: Hinzufügen einer Klassifizierung zu einer Spalte":::

1. Wählen Sie **Speichern** aus.

1. Wählen Sie die Registerkarte **Schema** aus, und vergewissern Sie sich, dass die Klassifizierung der Spalte hinzugefügt wurde.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Screenshot: Bestätigen, dass eine Klassifizierung einem Spaltenschema hinzugefügt wurde":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Auswirkungen von erneuten Überprüfungen bei vorhandenen Klassifizierungen

Die erstmalige Anwendung der Klassifizierungen erfolgt basierend auf einer Stichprobenüberprüfung Ihrer Daten und einem Abgleich mit dem festgelegten Muster der regulären Ausdrücke. Wenn bei einer erneuten Überprüfung neue Klassifizierungen angewendet werden, erhält die Spalte zusätzliche Klassifizierungen. Vorhandene Klassifizierungen verbleiben in der Spalte und müssen manuell entfernt werden.

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Erstellen einer benutzerdefinierten Klassifizierung finden Sie unter [Benutzerdefinierte Klassifizierungen in Azure Purview](create-a-custom-classification-and-classification-rule.md).