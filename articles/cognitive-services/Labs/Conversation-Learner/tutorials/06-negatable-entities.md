---
title: Verwenden von negierbaren Entitäten in einem Conversation Learner-Modell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie negierbare Entitäten in einem Conversation Learner-Modell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388806"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Verwenden von negierbaren Entitäten in einem Conversation Learner-Modell

In diesem Tutorial wird die Eigenschaft „Negierbar“ von Entitäten veranschaulicht.

## <a name="video"></a>Video

[![Negierbare Entitäten: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details
Die Eigenschaft „Negierbar“ einer Entität ermöglicht es Ihnen, sowohl normale (positive) als auch negative Instanzen der Entität zu kennzeichnen, nach positiven und negativen Modellen zu lehren und den Wert einer vorhandenen Entität zu löschen. Entitäten, bei denen die Eigenschaften „Negierbar“ festgelegt ist, werden in Conversation Learner als „Verneinbare Entitäten“ bezeichnet.

## <a name="steps"></a>Schritte

Beginnen Sie auf der Homepage in der Webbenutzeroberfläche.

### <a name="create-the-model"></a>Erstellen des Modells

1. Wählen Sie **Neues Modell** aus.
2. Geben Sie **NegatableEntity** als **Name** ein.
3. Klicken Sie auf **Erstellen**.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf **Entitäten** und dann auf **Neue Entität**.
2. Wählen Sie die Option **Benutzerdefiniert trainiert** als **Entitätstyp** aus.
3. Geben Sie im Feld **Entitätsname** die Zeichenfolge **name** ein.
4. Aktivieren Sie **Negierbar**, damit Benutzer einen Entitätswert angeben oder angeben können, dass etwas *kein* Entitätswert ist, wodurch der entsprechen de Entitätswert gelöscht wird.
5. Klicken Sie auf **Erstellen**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Erstellen der ersten Aktion

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie **I don't know your name** (Ich kenne Ihren Namen nicht) als **Antwort des Bots** ein.
3. Geben Sie die **name** für **Disqualifizierende Entitäten** ein.
4. Klicken Sie auf **Erstellen**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Erstellen der zweiten Aktion

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie **I know your name. It is $name.** (Ich kenne Ihren Namen. Er lautet $name) als **Antwort des Bots** ein.
3. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Die Entität **name** wurde durch einen Verweis in der Antwortäußerung automatisch den **erforderlichen Entitäten** hinzugefügt.

Jetzt sind zwei Aktionen vorhanden.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Trainieren des Modells

1. Wählen Sie im linken Bereich **Train Dialogs** (Trainingsdialogfelder) aus, und klicken Sie dann auf **New Train Dialog** (Neues Trainingsdialogfeld).
2. Geben Sie im linken Chatbereich **hello** (Hallo) als Äußerung des Benutzers ein.
3. Wählen Sie **Score Actions** (Aktionen bewerten) aus.
4. Wählen Sie **I don't know your name** (Ich kenne Ihren Namen nicht) in der Liste „Aktionen“ aus. Das Perzentil beträgt 100 Prozent (als einzige gültige Aktion auf der Grundlage der Einschränkungen).
5. Geben Sie im linken Chatbereich **My name ist Frank** (Mein Name ist Frank) als Äußerung des Benutzers ein.
6. Markieren Sie **Frank**, und wählen Sie dann **+name** aus. Negierbare Entitäten haben zwei Instanzen: (+) Plus fügt den Wert hinzu oder überschreibt ihn, (-) Minus entfernt den Wert.
7. Wählen Sie **Score Actions** (Aktionen bewerten) aus. Die Entität **name** ist im Speicher des Modells jetzt als **Frank** definiert, daher ist die Aktion **I know your name. It is $name** (Ich kenne Ihren Namen. Er lautet $name) verfügbar.
8. Wählen Sie **Ich kenne Ihren Namen. It is $name.** (Ich kenne Ihren Namen. Er lautet $name) in der Liste „Aktionen“ aus.
9. Geben Sie **My name is not Frank.** (Mein Name ist nicht Frank) im linken Chatbereich als Äußerung des Benutzers ein.
10. Markieren Sie **Frank**, und wählen Sie dann **-name** zum Löschen des Werts aus der Entität **name** aus.
11. Wählen Sie **Score Actions** (Aktionen bewerten) aus.
12. Wählen Sie **I don't know your name** (Ich kenne Ihren Namen nicht) in der Liste „Aktionen“ aus.
13. Geben Sie **Mein Name ist Susan.** im linken Chatbereich als dritte Äußerung des Benutzers ein.
14. Markieren Sie **Susan** und dann **+name**. 

![](../media/T06_training.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Mehrwertige Entitäten](./07-multi-value-entities.md)
