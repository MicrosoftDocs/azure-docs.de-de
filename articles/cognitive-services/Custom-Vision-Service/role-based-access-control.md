---
title: 'Rollenbasierte Zugriffssteuerung in Azure: Custom Vision'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung in Azure für Ihre Custom Vision-Projekte konfigurieren können.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616891"
---
# <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Custom Vision unterstützt die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), ein Autorisierungssystem für die Verwaltung des individuellen Zugriffs auf Azure-Ressourcen. Mithilfe der Azure RBAC können Sie verschiedenen Teammitgliedern verschiedene Berechtigungsebenen für Ihre Custom Vision-Projekte zuweisen. Weitere Information zur Azure RBAC finden Sie in der [Azure RBAC-Dokumentation](../../role-based-access-control/index.yml).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Hinzufügen einer Rollenzuweisung zu einer Custom Vision-Ressource

Azure RBAC kann einer Custom Vision-Ressource zugewiesen werden. Wenn Sie Zugriff auf eine Azure-Ressource gewähren möchten, fügen Sie eine Rollenzuweisung hinzu.
1. Wählen Sie im [Azure-Portal](https://ms.portal.azure.com/) die Option **Alle Dienste**. 
1. Klicken Sie dann auf **Cognitive Services**, und navigieren Sie zu Ihrer spezifischen Custom Vision-Trainingsressource.
   > [!NOTE]
   > Sie können die Azure RBAC auch für ganze Ressourcengruppen, Abonnements oder Verwaltungsgruppen einrichten. Dazu wählen Sie die Ebene im gewünschten Bereich aus und navigieren dann zum gewünschten Element. Sie klicken also z. B. auf **Ressourcengruppen** und navigieren dann zur gewünschten Ressourcengruppe.
1. Klicken Sie im linken Navigationsbereich auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.
1. Wählen Sie **Hinzufügen** -> **Rollenzuweisung hinzufügen**.
1. Wählen Sie in der Dropdownliste **Rolle** eine Rolle aus, die Sie hinzufügen möchten.
1. Wählen Sie in der Liste **Auswählen** einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität aus. Wird der Sicherheitsprinzipal in der Liste nicht angezeigt, können Sie im Feld „Auswählen“ einen Begriff eingeben, um das Verzeichnis nach Anzeigenamen, E-Mail-Adressen und Objektbezeichnern zu durchsuchen.
1. Wählen Sie **Speichern** aus, um die Rolle zuzuweisen.

Innerhalb weniger Minuten wird dem Ziel die ausgewählte Rolle für den ausgewählten Bereich zugewiesen.

## <a name="custom-vision-role-types"></a>Rollentypen in Custom Vision

Verwenden Sie die folgende Tabelle, um die Zugriffsanforderungen für Ihre Custom Vision-Ressourcen zu ermitteln.

|Role  |Berechtigungen  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Vollzugriff auf Projekte, einschließlich der Möglichkeit zum Erstellen, Bearbeiten oder Löschen eines Projekts.        |
|`Cognitive Services Custom Vision Trainer`     | Vollzugriff ohne der Möglichkeit zum Erstellen oder Löschen eines Projekts. Trainer können Projekte anzeigen und bearbeiten und die Modelle trainieren, veröffentlichen, die Veröffentlichung von Projekten aufheben oder sie exportieren.        |
|`Cognitive Services Custom Vision Labeler`     | Die Möglichkeit zum Hochladen, Bearbeiten oder Löschen von Trainingsbildern und zum Erstellen, Hinzufügen, Entfernen oder Löschen von Tags. Beschriftungsersteller können Projekte anzeigen, jedoch nichts außer Trainingsbildern und Tags aktualisieren.         |
|`Cognitive Services Custom Vision Deployment`     | Die Möglichkeit zum Veröffentlichen, Aufheben der Veröffentlichung oder Exportieren von Modellen. Bereitsteller können Projekte anzeigen, jedoch Projekte, Trainingsbilder oder Tags nicht aktualisieren.        |
|`Cognitive Services Custom Vision Reader`     | Die Möglichkeit zur Anzeige von Projekten. Leser können keine Änderungen vornehmen.        |