---
title: Erstellen und Verwalten von Integration Runtimes
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Integration Runtimes in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550363"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Erstellen und Verwalten einer selbstgehosteten Integration Runtime

In diesem Artikel wird beschrieben, wie Sie eine selbstgehostete Integration Runtime (SHIR) erstellen und verwalten, um das Überprüfen von Datenquellen zu unterstützen.

## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

1. Wählen Sie auf der Startseite von Purview Studio im linken Navigationsbereich die Option **Verwaltungscenter** aus.

2. Wählen Sie im linken Bereich unter **Sources and scanning** (Quellen und Überprüfung) die Option **Integration Runtimes** und dann **+ Neu** aus.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Klicken auf „Integration Runtimes“":::

3. Wählen Sie auf der Seite **Integration Runtime-Setup** die Option **Selbstgehostet** aus, um eine selbstgehostete IR zu erstellen, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Erstellen einer neuen SHIR":::

4. Geben Sie einen Namen für Ihre IR ein, und wählen Sie Erstellen aus.

5. Führen Sie auf der Seite **Integration Runtime settings** (Integration Runtime-Einstellungen) die Schritte im Abschnitt **Manual setup** (Manuelles Setup) aus. Sie müssen die Integration Runtime von der Downloadwebsite auf einen virtuellen oder physischen Computer herunterladen, den Sie für die Ausführung verwenden möchten.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Abrufen des Schlüssels":::

    a. Kopieren Sie den Authentifizierungsschlüssel, und fügen Sie ihn ein.
        
    b. Laden Sie die selbstgehostete Integration Runtime von [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf einen lokalen Windows-Computer herunter. Führen Sie das Installationsprogramm aus.
        
    c. Fügen Sie auf der Seite **Integration Runtime (selbstgehostet) registrieren** einen der beiden Schlüssel ein, die Sie zuvor gespeichert haben, und wählen Sie **Registrieren** aus.

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Einfügen des Schlüssels":::

    d. Klicken Sie auf der Seite **Neuer Knoten der Integrationslaufzeit (selbstgehostet)** auf **Fertig stellen**.

6. Nachdem die selbstgehostete Integration Runtime erfolgreich registriert wurde, wird das folgende Fenster angezeigt:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Erfolgreiche Registrierung":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Verwalten einer selbstgehosteten Integration Runtime

Sie können eine selbstgehostete Integration Runtime bearbeiten, indem Sie im **Verwaltungscenter** zu **Integration Runtimes** navigieren, die IR auswählen und dann auf „Bearbeiten“ klicken. Sie können die Beschreibung jetzt aktualisieren, den Schlüssel kopieren oder neue Schlüssel generieren.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Bearbeiten der IR":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Bearbeiten von Details der IR":::

Sie können eine selbstgehostete Integration Runtime löschen, indem Sie im Verwaltungscenter zu **Integration Runtimes** navigieren, die IR auswählen und dann auf **Löschen** klicken. Nachdem eine IR gelöscht wurde, treten für alle Überprüfungen, die darauf basieren, Fehler auf.

## <a name="next-steps"></a>Nächste Schritte

* [So werden gelöschte Ressourcen bei Scans erkannt](concept-detect-deleted-assets.md)
