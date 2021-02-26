---
title: Objekt- und Ressourcenlebensdauer
description: Beschreibt die Lebensdauerverwaltung für verschiedene Typen.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593890"
---
# <a name="object-and-resource-lifetime"></a>Objekt- und Ressourcenlebensdauer

Azure Remote Rendering unterscheidet zwischen zwei Typen: **Objekten** und **Ressourcen**.

## <a name="object-lifetime"></a>Objektlebensdauer

*Objekte* werden als Dinge betrachtet, die der Benutzer nach eigenem Ermessen erstellen, ändern und zerstören kann. Objekte können frei dupliziert werden, und jede Instanz kann im Laufe der Zeit mutieren. Folglich sind [Entitäten](entities.md) und [Komponenten](components.md) Objekte.

Die Lebensdauer von Objekten unterliegt vollständig der Benutzerkontrolle. Sie ist jedoch nicht mit der Lebensdauer der clientseitigen Darstellung verknüpft. Klassen wie `Entity` und `Component` haben eine `Destroy`-Funktion, die aufgerufen werden muss, um die Zuordnung des Objekts auf dem Remoterrenderinghost aufzuheben. Außerdem zerstört `Entity.Destroy()` die Entität, ihre untergeordneten Elemente und alle Komponenten in dieser Hierarchie.

## <a name="resource-lifetime"></a>Ressourcenlebensdauer

*Ressourcen* sind Dinge, deren Lebensdauer vollständig vom Remoterenderinghost verwaltet wird. Ressourcen werden intern als Verweise gezählt. Ihre Zuordnung wird aufgehoben, wenn nicht mehr auf sie verwiesen wird.

Die meisten Ressourcen können nur indirekt erstellt werden, indem sie in der Regel aus einer Datei geladen werden. Wenn dieselbe Datei mehrmals geladen wird, gibt Azure Remote Rendering denselben Verweis zurück, und die Daten werden nicht erneut geladen.

Viele Ressourcen sind unveränderlich, z. B. [Gittermodelle](meshes.md) und [Texturen](textures.md). Einige Ressourcen sind jedoch änderbar, z. B. [Materialien](materials.md). Da Ressourcen häufig freigegeben werden, kann sich die Änderung einer Ressource möglicherweise auf mehrere Objekte auswirken. Wenn Sie beispielsweise die Farbe eines Materials ändern, ändert sich die Farbe aller Objekte, die Gittermodelle verwenden, die wiederum auf dieses Material verweisen.

### <a name="built-in-resources"></a>Integrierte Ressourcen

Azure Remote Rendering enthält einige integrierte Ressourcen, die geladen werden können, indem `builtin://` dem entsprechenden Bezeichner während des Aufrufs von `RenderingSession.Connection.LoadXYZAsync()` vorangestellt wird. Die verfügbaren integrierten Ressourcen werden in der Dokumentation zu den jeweiligen Features aufgeführt. Im Kapitel zum [Himmel](../overview/features/sky.md) werden z. B. die integrierten Himmelstexturen aufgeführt.

## <a name="general-lifetime"></a>Allgemeine Lebensdauer

Die Lebensdauer aller Objekte und Ressourcen ist an die Verbindung gebunden. Beim Trennen der Verbindung wird alles verworfen. Beim erneuten Herstellen einer Verbindung mit derselben Sitzung ist der Szenengraph leer, und alle Ressourcen sind bereinigt.

In der Praxis ist das Laden derselben Ressource in eine Sitzung nach einer Trennung in der Regel schneller als beim ersten Mal. Dies ist der Fall, weil die meisten Ressourcen beim ersten Mal aus Azure Storage heruntergeladen werden müssen, was beim zweiten Mal nicht mehr erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

* [Entitäten](entities.md)
* [Komponenten](components.md)
* [Modelle](models.md)
