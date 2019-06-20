---
title: Verwenden von mehrwertigen Entitäten in einem Conversation Learner-Modell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie mehrwertige Entitäten in einem Conversation Learner-Modell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387958"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Verwenden von mehrwertigen Entitäten in einem Unterhaltungslernmodell
In diesem Tutorial wird die mehrwertige Eigenschaft von Entitäten behandelt.

## <a name="video"></a>Video

[![Mehrwertige Entitäten: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

## <a name="details"></a>Details
Mehrwertige Entitäten akkumulieren Werte in einer Liste, anstatt einen einzelnen Wert zu speichern.  Diese Entitäten sind hilfreich, wenn Benutzer mehrere Werte angeben können. Ein Beispiel wäre etwa die Zusammenstellung einer Pizza.

Bei als mehrwertig markierten Entitäten werden die einzelnen erkannten Instanzen der Entität jeweils einer Liste im Speicher des Bots hinzugefügt. Weitere Erkennungen werden an den Wert der Entität angefügt, anstatt ihn zu überschreiben.

## <a name="steps"></a>Schritte

Beginnen Sie auf der Homepage in der Webbenutzeroberfläche.

### <a name="create-the-model"></a>Erstellen des Modells

1. Wählen Sie **Neuer Besitzer** aus.
2. Geben Sie die Zeichenfolge **MultiValueEntities** für **Name** ein.
3. Klicken Sie auf **Erstellen**.

### <a name="entity-creation"></a>Erstellung von Entitäten

1. Klicken Sie im linken Bereich auf **Entitäten** und dann auf **Neue Entität**.
2. Wählen Sie die Option **Benutzerdefiniert trainiert** für **Entitätstyp** aus.
3. Geben Sie im Feld **Entitätsname** die Bezeichnung **Beläge** ein.
4. Überprüfen Sie **Mehrwertig**, um zu ermöglichen, dass die Entität einen oder mehrere Werte kumuliert.
5. Aktivieren Sie **Negierbar**.
6. Klicken Sie auf **Erstellen**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Erstellen der ersten Aktion

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie **Hier sind Ihre Beläge: $Beläge** für **Antwort des Bots...** ein. Das führende Dollarzeichen gibt an, dass es sich um einen Entitätsverweis handelt.
3. Klicken Sie auf **Erstellen**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Erstellen der zweiten Aktion

1. Klicken Sie im linken Bereich auf **Aktionen** und dann auf **Neue Aktion**.
2. Geben Sie **Welche Beläge möchten Sie?** für **Antwort des Bots...** ein.
3. Geben Sie die **Beläge** für **Disqualifizierende Entitäten** ein.
4. Klicken Sie auf **Erstellen**.

Jetzt sind zwei Aktionen vorhanden.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Trainieren des Modells

1. Wählen Sie im linken Bereich **Dialogfelder trainieren** aus, und klicken Sie dann auf **Neues Trainingsdialogfeld**.
2. Geben Sie im linken Chatbereich **Hallo** als Äußerung des Benutzers ein.
3. Wählen Sie **Bewertungsaktionen** aus.
4. Wählen Sie **Welche Beläge möchten Sie?** aus der Liste der Aktionen aus. Das Perzentil beträgt 100 Prozent (als einzige gültige Aktion auf der Grundlage der Einschränkungen).
5. Geben Sie im linken Chatfenster **Käse und Pilze** als Äußerung des Benutzers ein.
6. Heben Sie **Käse** hervor, und wählen Sie dann **+Beläge** aus.
7. Heben Sie **Pilze** hervor, und wählen Sie dann **+Beläge** aus.
8. Wählen Sie **Bewertungsaktionen** aus.
9. Wählen Sie **Hier sind Ihre Beläge: $Beläge** aus der Liste der Aktionen aus.
10. Geben Sie im linken Chatbereich **Paprika hinzufügen** als nächste Äußerung des Benutzers ein.
11. Heben Sie **Paprika** hervor, und wählen Sie dann **+Beläge** aus.
12. Wählen Sie **Bewertungsaktionen** aus.
13. Wählen Sie **Hier sind Ihre Beläge: $Beläge** aus der Liste der Aktionen aus.
14. Geben Sie im linken Chatbereich **Käse entfernen** als dritte Äußerung des Benutzers ein.
15. Heben Sie **Käse** hervor, und wählen Sie dann **-Beläge** aus.
16. Wählen Sie **Bewertungsaktionen** aus.
17. Wählen Sie **Hier sind Ihre Beläge: $Beläge** aus der Liste der Aktionen aus.

![](../media/T07_training.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vortrainierte Entitäten](./08-pre-trained-entities.md)
