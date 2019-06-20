---
title: Demo eines Unterhaltungslernmodells, Pizzabestellung – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie eine Demo in einem Unterhaltungslernmodell erstellen.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389136"
---
# <a name="demo-pizza-order"></a>Demo: Pizzabestellung
Diese Demo veranschaulicht einen Bot für Pizzabestellung. Er unterstützt das Bestellen einzelner Pizzas durch:

- Erkennen von Pizzabelägen in Benutzeräußerungen
- Verwalten eines Belägebestands und entsprechendes Reagieren
- Speichern vorheriger Bestellungen und Beschleunigen der erneuten Bestellung einer identischen Pizza

## <a name="video"></a>Video

[![Demo Pizza – Vorschau](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der Pizzabestellungsbot ausgeführt werden.

    npm run demo-pizza

### <a name="open-the-demo"></a>Öffnen der Demo

Klicken Sie in der Modellliste der Webbenutzeroberfläche auf „TutorialDemo Pizza Order“. 

## <a name="entities"></a>Entitäten

Das Modell enthält drei Entitäten:

- „Toppings“ sammelt angegebene Beläge des Benutzers, falls verfügbar.
- „OutofStock“ teilt dem Benutzer mit, dass der ausgewählte Belag nicht vorrätig ist.
- „LastToppings“ enthält die Beläge aus der vorherigen Bestellung.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Aktionen

Das Modell enthält eine Reihe von Aktionen, mit denen der Benutzer nach seiner Belagauswahl, gesammelten Belägen und mehr gefragt wird.

Außerdem werden zwei API-Aufrufe bereitgestellt:

- „FinalizeOrder“ verarbeitet den Bestellvorgang.
- „UseLastToppings“ verarbeitet Informationen zu bisherigen Belägen.

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Trainingsdialoge

Das Modell enthält mehrere Trainingsdialoge.

![](../media/tutorial_pizza_dialogs.PNG)

Wir trainieren das Modell noch ein wenig mehr, indem wir einen weiteren Trainingsdialog erstellen.

1. Klicken Sie im linken Bereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Order a pizza with cheese“ (Pizza mit Käse bestellen) ein.
    - Das Wort „cheese“ (Käse) wurde von der Entitätsextraktionsfunktion extrahiert.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
4. Wählen Sie die Antwort „You have cheese on your pizza.“ (Sie haben Käse auf Ihrer Pizza.) aus.
5. Wählen Sie die Antwort „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) aus.
6. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „add mushrooms and pepper“ (Paprika und Pilze hinzufügen) ein.
7. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
8. Wählen Sie die Antwort „You have cheese, mushrooms and peppers on your pizza.“ (Sie haben Käse, Pilze und Paprika auf Ihrer Pizza.) aus.
9. Wählen Sie die Antwort „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) aus.
10. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „remove peppers and add sausage“ (Paprika entfernen und Wurst hinzufügen) ein.
11. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
12. Wählen Sie die Antwort „You have cheese, mushrooms and sausage on your pizza.“ (Sie haben Käse, Pilze und Wurst auf Ihrer Pizza.) aus.
13. Wählen Sie die Antwort „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) aus.
14. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „add yam“ (Süßkartoffel hinzufügen) ein.
15. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
    - Der Wert „yam“ (Süßkartoffel) wurde vom Rückrufcode zur Entitätserkennung der Entität „OutofStock“ hinzugefügt, da der Text keinen unterstützten Zutaten entsprach.
16. Wählen Sie die Antwort „OutOfStock“ aus.
17. Wählen Sie die Antwort „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) aus.
18. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „no“ (Nein) ein.
    - Das „no“ ist nicht als Absichtstyp markiert. Stattdessen wählen wir die entsprechende Aktion basierend auf dem aktuellen Kontext aus.
19. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
20. Wählen Sie die Antwort „FinalizeOrder“ aus.
    - Durch die Auswahl dieser Aktion wurden die aktuellen Beläge des Kunden in der Entität „LastToppings“ gespeichert, und die Entität „Toppings“ wurde vom Rückrufcode „FinalizeOrder“ gelöscht.
21. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „order another“ (Weitere Bestellung) ein.
22. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
23. Wählen Sie die Antwort „Would you like cheese, mushrooms and sausage?“ (Möchten Sie Käse, Pilze und Wurst?) aus.
    - Diese Aktion ist jetzt verfügbar, da die Entität „LastToppings“ festgelegt ist.
24. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „yes“ (Ja) ein.
25. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
26. Wählen Sie die Antwort „UseLastToppings“ aus.
27. Wählen Sie die Antwort „You have cheese, mushrooms and sausage on your pizza.“ (Sie haben Käse, Pilze und Wurst auf Ihrer Pizza.) aus.
28. Wählen Sie die Antwort „Would you like anything else?“ (Wünschen Sie sonst noch etwas?) aus.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen eines Unterhaltungslernmodul-Bots](../deploy-to-bf.md)
