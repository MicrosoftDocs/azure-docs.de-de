---
title: Grundlegendes zum Lebenszyklus einer Blaupause
description: Erfahren Sie mehr über den Lebenszyklus einer Blaupause und Details zu jeder einzelnen Phase.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a57085fa37efd56a46b740d8cbc4278dc53cf39f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60683188"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Grundlegendes zum Lebenszyklus von Azure Blueprints

Wie viele Ressourcen in Azure weist eine Blaupause in Azure Blueprints einen typischen und natürlichen Lebenszyklus auf. Sie werden erstellt, bereitgestellt und schließlich gelöscht, wenn sie nicht mehr benötigt werden oder nicht mehr von Relevanz sind.
Blueprints unterstützt Vorgänge für Standardlebenszyklen. Auf deren Grundlage werden dann zusätzliche Statusebenen bereitgestellt, die allgemeine Continuous Integration- und Continuous Deployment-Pipelines für Organisationen unterstützen, die ihre Infrastruktur als Code verwalten – ein zentraler Aspekt von DevOps.

Um einen Überblick über Blaupausen und ihre einzelnen Phasen zu vermitteln, wird ein Standardlebenszyklus vorgestellt:

> [!div class="checklist"]
> - Erstellen und Bearbeiten einer Blaupause
> - Veröffentlichen der Blaupause
> - Erstellen und Bearbeiten einer neuen Blaupausenversion
> - Veröffentlichen einer neuen Blaupausenversion
> - Löschen einer bestimmten Blaupausenversion
> - Löschen der Blaupause

## <a name="creating-and-editing-a-blueprint"></a>Erstellen und Bearbeiten einer Blaupause

Wenn Sie eine Blaupause erstellen, fügen Sie Artefakte hinzu, speichern sie in einer Verwaltungsgruppe oder einem Abonnement und geben einen eindeutigen Namen und eine eindeutige Version an. Die Blaupause befindet nun im Modus **Entwurf** und kann noch nicht zugewiesen werden. Im Modus **Entwurf** kann sie jedoch weiter aktualisiert und geändert werden.

Für eine Blaupause im Modus **Entwurf**, die bislang noch nicht veröffentlicht wurde, wird auf der Seite **Blaupausendefinitionen** ein anderes Symbol angezeigt als für Blaupausen, die bereits **veröffentlicht** wurden. Die **aktuelle Version** dieser bislang noch nicht veröffentlichten Blaupausen wird als **Entwurf** angezeigt.

Erstellen und bearbeiten Sie eine Blaupause mit dem [Azure-Portal](../create-blueprint-portal.md#create-a-blueprint) oder der [REST-API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Veröffentlichen einer Blaupause

Nachdem Sie alle geplanten Änderungen an einer Blaupause im Modus **Entwurf** vorgenommen haben, kann sie **veröffentlicht** und für die Zuweisung verfügbar gemacht werden. Die Blaupausenversion mit dem Modus **Veröffentlicht** kann nicht geändert werden.
Einmal **veröffentlicht**, wird die Blaupause mit einem anderen Symbol als Blaupausen im Modus **Entwurf** angezeigt. Die Versionsnummer wird in der Spalte **Neueste Version** angezeigt.

Veröffentlichen Sie eine Blaupause mit dem [Azure-Portal](../create-blueprint-portal.md#publish-a-blueprint) oder der [REST-API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Erstellen und Bearbeiten einer neuen Blaupausenversion

**Veröffentlichte** Blaupausenversionen können nicht geändert werden. Sie können der vorhandenen Blaupause jedoch eine neue Version hinzufügen und diese nach Bedarf anpassen. Wenn Sie eine vorhandene Blaupause ändern möchten, bearbeiten Sie sie. Nach dem Speichern der neuen Änderungen verfügt die Blaupause über **nicht veröffentlichte Änderungen**. Bei diesen Änderungen handelt es sich um eine neue **Entwurfsversion** der Blaupause.

Eine Blaupause kann im [Azure-Portal](../create-blueprint-portal.md#edit-a-blueprint) bearbeitet werden.

## <a name="publishing-a-new-version-of-the-blueprint"></a>Veröffentlichen einer neuen Blaupausenversion

Jede bearbeitete Version einer Blaupause muss **veröffentlicht** werden, damit sie zugewiesen werden kann. Wenn an einer Blaupause **nicht veröffentlichte Änderungen** vorgenommen wurden, aber noch keine **Veröffentlichung** durchgeführt wurde, ist auf der Bearbeitungsseite der Blaupause die Schaltfläche **Blaupause veröffentlichen** verfügbar. Sollte die Schaltfläche nicht angezeigt werden, wurde die Blaupause bereits **veröffentlicht** und besitzt keine **nicht veröffentlichten Änderungen**.

> [!NOTE]
> Eine Blaupause kann mehrere Versionen mit dem Status **Veröffentlicht** aufweisen, die Abonnements zugewiesen werden können.

Führen Sie zum Veröffentlichen einer Blaupause mit **nicht veröffentlichten Änderungen** die gleichen Schritte aus wie zum Veröffentlichen einer neuen Blaupause.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Löschen einer bestimmten Blaupausenversion

Jede Version einer Blaupause ist ein eindeutiges Objekt und kann separat mit dem Status **Veröffentlicht** versehen werden. Somit kann jede Version einer Blaupause auch gelöscht werden. Das Löschen einer Blaupausenversion hat keine Auswirkungen auf andere Versionen dieser Blaupause.

> [!NOTE]
> Es ist nicht möglich, Blaupausen mit aktiven Zuweisungen zu löschen. Löschen Sie zuerst die Zuweisungen und dann die Version, die entfernt werden soll.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie auf der linken Seite **Blaupausendefinitionen** aus, und verwenden Sie die Filteroptionen, um die Blaupause ausfindig zu machen, deren Version gelöscht werden soll. Klicken Sie auf diese, um die Bearbeitungsseite zu öffnen.

1. Klicken Sie auf die Registerkarte **Veröffentlichte Versionen**, und suchen Sie die Version, die gelöscht werden soll.

1. Klicken Sie mit der rechten Maustaste auf die zu löschende Version, und wählen Sie **Diese Version löschen** aus.

## <a name="deleting-the-blueprint"></a>Löschen der Blaupause

Die Hauptblaupause kann ebenfalls gelöscht werden. Wenn Sie die zentrale Blaupause löschen, werden alle Blaupausenversionen dieser Blaupause gelöscht – auch Blaupausen mit dem Status **Entwurf** oder **Veröffentlicht**. Wie beim Löschen einer Blaupausenversion werden auch beim Löschen der Hauptblaupause nicht die vorhandenen Zuweisungen der Blaupausenversionen entfernt.

> [!NOTE]
> Es ist nicht möglich, Blaupausen mit aktiven Zuweisungen zu löschen. Löschen Sie zuerst die Zuweisungen und dann die Version, die entfernt werden soll.

Löschen Sie eine Blaupause mit dem [Azure-Portal](../create-blueprint-portal.md#delete-a-blueprint) oder der [REST-API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Arbeitsaufträge

Es gibt mehrere Punkte im Lebenszyklus, an denen einem Abonnement eine Blaupause zugewiesen werden kann. Wenn sich eine Blaupausenversion im Modus **Veröffentlicht** befindet, kann diese Version einem Abonnement zugewiesen werden. Dank dieses Lebenszyklus können Versionen einer Blaupause verwendet und aktiv zugewiesen werden, während eine neuere Version entwickelt wird.

Wenn Blaupausenversionen zugewiesen werden, sollte unbedingt beachtet werden, welchen Elementen sie zugewiesen werden und welche Parameter ihnen zugewiesen wurden. Die Parameter können entweder statisch oder dynamisch sein. Weitere Informationen finden Sie im Artikel zu [statischen und dynamischen Parametern](parameters.md).

### <a name="updating-assignments"></a>Aktualisieren von Zuweisungen

Wenn eine Blaupause zugewiesen ist, kann die Zuweisung aktualisiert werden. Es gibt verschiedene Gründe für das Aktualisieren einer vorhandenen Zuweisung:

- Hinzufügen oder Entfernen einer [Ressourcensperre](resource-locking.md)
- Ändern des Wertes von [dynamischen Parametern](parameters.md#dynamic-parameters)
- Durchführen eines Upgrades auf eine neuere Blaupausenversion mit dem Status **Veröffentlicht**

Erfahren Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).