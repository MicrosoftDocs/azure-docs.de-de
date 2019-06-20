---
title: Einsatzfälle von ENUM-Entitäten und SET_ENTITY-Aktionen mit einem Conversation Learner-Modell – Azure Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wann ENUM-Entitäten und SET_ENTITY-Aktionen mit einem Conversation Learner-Modell verwendet werden sollten.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476349"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Einsatzfälle von ENUM-Entitäten und SET_ENTITY-Aktionen

In diesem Tutorial wird erläutert, wann Sie ENUM-Entitäten (Enumerationsentitäten) und SET_ENTITY-Aktionen verwenden sollten.

## <a name="video"></a>Video

[![Tutorial zu „Set_Entity“ (Vorschau)](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Inhalt

In diesem Tutorial werden zwei neue Features vorgestellt. Diese sind ein neuer Entitätstyp, der als ENUM (kurz für Enumeration) bezeichnet wird, und ein neuer als SET_ENTITY bezeichneter Aktionstyp, der auf einen dieser Enumerationswerte verweisen kann und, wie der Name impliziert, die Entität auf diesen Wert festlegt. Wie Sie nachstehend erfahren, werden diese neuen Features zusammen verwendet, und es wird weiterhin erläutert, wie sie zu verwenden sind. Bevor es in die Details geht, sollte unbedingt klar sein, welches Problem sich mit diesen Features lösen lässt.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problem

Es gibt Fälle in Konversationen, in denen die Bedeutung von Wörtern vom Kontext abhängt.  Normalerweise werden die bezeichneten Schlüsselwörter über einen Sprache verstehenden Dienst gelernt und extrahiert, aber in diesen Fällen sind solche Systeme möglicherweise nicht in der Lage, mit bezeichneten Beispielen zu lernen.

Stellen Sie sich vor, Sie hören zufällig einen Teil eines Gesprächs zwischen Menschen in Ihrer Nähe, wobei Sie nur das Wort „Ja“ hören. Sie können nicht wissen, wofür das „Ja“ eine Zustimmung angibt, da Sie die zuvor gestellte Frage nicht gehört haben. Die zuvor gestellte Frage ist der Kontext, der die Bedeutung der Antwort bestimmt. Ganz ähnlich lässt sich das „Ja“, weil es eine allgemeine Antwort auf viele verschiedene Fragen ist, nicht durch Bereitstellen von Beispielen lernen, wie Sie dies mit [benutzerdefiniert trainierten](04-introduction-to-entities.md) Entitäten tun, weil das System dann lernen würde, jedes „Ja“ als diese Entität zu bezeichnen.

### <a name="example"></a>Beispiel

Das folgende Beispiel soll zur weiteren Klärung dienen:

Bot: Mögen Sie Azure Cognitive Services?
User: Ja Bot: Mögen Sie Speiseeis?
User: Ja

In den vorherigen Tutorials ging es um [benutzerdefiniert trainierte](04-introduction-to-entities.md) Entitäten, und Ihr erster Gedanke könnte sein, eine Entität namens „magCogServices“ zu erstellen und das erste „Ja“ als diese Entität zu kennzeichnen.  Das System würde aber auch das zweite „Ja“ kennzeichnen. Bei dem Versuch, die Bezeichnung des zweiten „Ja“ in „magSpeiseeis“ zu korrigieren, würde es zu einem Konflikt zwischen den beiden identischen „Ja“-Eingaben kommen, die unterschiedliche Dinge bedeuten, und es würde eine Blockierung vorliegen.

Es sind diese Fälle, in denen Sie ENUM-Entitäten und SET_ENTITY-Aktionen verwenden müssen.

## <a name="when-to-use-enums-or-setentity-actions"></a>Einsatzfälle von ENUMs und SET_ENTITY-Aktionen

Verwenden Sie die folgenden Regeln, um zu bestimmen, wann Sie ENUM-Entitäten und SET_ENTITY-Aktionen verwenden sollten:

- Die Erkennung oder Festlegung einer Entität hängt vom Kontext ab.
- Die Anzahl der mögliche Werte liegt fest („Ja“ und „Nein“ wären zwei Werte).

Mit anderen Worten, verwenden Sie diese Werte für alle ergebnisgeschlossenen Eingabeaufforderungen, etwa Bestätigungsfragen, die immer zu „Ja“ oder „Nein“ führen.

> [!NOTE]
> Derzeit gibt es eine Beschränkung auf maximal 5 Werte pro ENUM-Entität. Für jeden der Werte wird einer der Slots in der aktuellen 64-Grenze verwendet. Weitere Informationen finden Sie unter [Standardwerte und Grenzen](../cl-values-and-boundaries.md).

Beispiel: Bot: Ist Ihre Bestellung korrekt?
User: Ja

Wenn die möglichen Werte der Entität ergebnisoffen, also nicht feststehend sind, müssten Sie ein alternatives Feature, etwa [Erwartete Entität](05-expected-entity.md), verwenden.

Beispiel: Bot: Wie heißen Sie?
User: Ralf Bot: Welche Farbe ist Ihre Lieblingsfarbe?
User: Silber

Diese Eingabeaufforderungen werden als ergebnisoffen angesehen, da sie mit beliebigen Werten beantwortet werden können.

## <a name="what"></a>Was

### <a name="enum-entities"></a>ENUM-Entitäten

ENUM-Entitäten werden auf die gleiche Weise wie die anderen Entitäten erstellt. Analog zu „programmgesteuerten“ Entitäten können Sie Wörter nicht als diese Entitäten bezeichnen. Stattdessen müssen sie über Code oder SET_ENTITY-Aktionen festgelegt werden.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>„Entität festlegen“-Aktionen (SET_ENTITY-Aktionen)

Wie bereits beschrieben, wird in einer „Entität festlegen“-Aktion einfach eine Entität auf einen bekannten Enumerationswert festgelegt. Sie können dasselbe Ergebnisse erzielen, indem Sie eine API-Rückrufaktion erstellen und den Speichermanager verwenden, um die Entität auf einen Wert festzulegen. Beispiel: `memory.Set(entityName, entityValue)`. Das Schreiben dieses Codes und das Erstellen dieser Aktionen wäre mühsam und ließe sich nur schwer verwalten. Daher hat Conversation Learner spezielle Aktionen, die diese Arbeit erleichtern und automatisch die zugehörigen Aktionen generieren, wenn sie verwendet werden. Weil diese Aktionen als unabhängige Aktionen vorliegen, bleibt es weiterhin möglich, sie zusammenzustellen, ohne dass sie mit anderen Aktionen oder anderem Code in Ihrem Bot gekoppelt sind.

- „Entität festlegen“-Aktionen können nur erstellt werden, wenn es möglich ist, auf einen Wert einer Enumerationsentität zu verwiesen. Daher müssen Sie zuerst eine ENUM-Entität erstellen.
- Eine „Entität festlegen“-Aktion ist außerdem eine „Aktion ohne Wartezeit“, da sie keine sichtbare Ausgabe hat, und auf sie muss eine „Warten“-Aktion folgen, die der Benutzer sehen kann.
- Eine „Entität festlegen“-Aktion ist unveränderlich, d. h., Sie können sie nach der Erstellung nicht mehr bearbeiten.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatische Aktionserstellung

Wenn in Ihrem Modell eine ENUM-Entität vorhanden ist, erstellt Conversation Learner Platzhalteraktionen für jeden der möglichen Werte und stellt diese während des Trainings zur Auswahl bereit. Während der Auswahl wird die jeweilige Aktion automatisch für Sie erstellt.

Angenommen, Sie erstellen eine ENUM-Entität mit den Werten „Ja und „Nein“:

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Auch ohne explizites Erstellen von Aktionen für diese neue Enumeration sehen Sie zwei neue Aktionen, die während des Trainings verfügbar sind:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Erstellen eines Bots mit diesen neuen Features

### <a name="requirements"></a>Requirements (Anforderungen)

Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

Wir erstellen einen Bot, um eine Fast Food-Bestellung zu simulieren. Der Bot hat diskrete Werte für die Größen von Getränken und Pommes frites (SMALL/MEDIUM/LARGE) und Bestätigungsfragen mit YES/NO-Antworten. Jede dieser beiden Entitäten erfüllt die beiden oben aufgeführten Regeln hinsichtlich kontextabhängiger Antworten und festen Werten.

### <a name="create-the-model"></a>Erstellen des Modells

1. Klicken Sie in der Webbenutzeroberfläche auf „Importieren“.
2. Wählen Sie das Tutorial aus, das den Namen „Tutorial-Enum-Set-Entity“ hat.

Dadurch gelangen Sie auf die Modellverwaltungsseite.
Beachten Sie, dass das Modell bereits einige ENUM-Entitäten und „Entität festlegen“-Aktionen (SET_ENTITY-Aktionen) enthält.

### <a name="create-the-first-dialog"></a>Erstellen des ersten Dialogs

1. Klicken Sie im linken Navigationsbereich auf „Train Dialogs“ (Trainingsdialoge) und anschließend auf die Schaltfläche „New Train Dialog“ (Neuer Trainingsdialog).
2. Geben Sie als Benutzer Folgendes ein: „Hallo, ich möchte eine Cola und Pommes frites bestellen“.
3. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).

   > Der Benutzer hat weder für das Getränk noch für die Pommes frites eine Größe angegebenen, also muss er gefragt werden.

4. Wählen Sie die Aktion mit der folgenden Antwort aus: „What size drink would you like?“ (Welche Größe soll das Getränk haben?)
5. Geben Sie als Benutzer Folgendes ein: „large“ (groß).
6. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
7. Wählen Sie die folgende Aktion aus: SET_ENTITY - "drinkSize: LARGE"
8. Wählen Sie die Aktion mit der folgenden Antwort aus: „What size fries would you like?“ (Welche Größe sollen die Pommes frites haben?)
9. Geben Sie als Benutzer Folgendes ein: „Um, make those a medium“ (Hm, ich nehme eine mittel große Portion).
10. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
11. Wählen Sie die folgende Aktion aus: SET_ENTITY - "friesSize: MEDIUM"
12. Wählen Sie die Aktion mit der folgenden Antwort aus: „Would you like any condiments?“ (Möchten Sie irgendeine Sauce?)
13. Geben Sie als Benutzer Folgendes ein: „Yes“ (Ja).
14. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
15. Wählen Sie die folgende Aktion aus: SET_ENTITY - "condimentsConfirmation: YES"
16. Wählen Sie die Aktion mit der folgenden Antwort aus: „Ok, I have an order for a LARGE drink and MEDIUM fries. (OK, ich habe eine Bestellung für ein GROSSES Getränk und eine MITTEL große Portion Pommes frites.) Is that correct?“ (Ist das richtig?)
17. Geben Sie als Benutzer Folgendes ein: „Yes“ (Ja).
18. Klicken Sie auf die Schaltfläche „Score Actions“ (Bewertungsaktionen).
19. Wählen Sie die folgende Aktion aus: SET_ENTITY - "orderConfirmation: YES"
20. Wählen Sie die Aktion mit der folgenden Antwort aus: „Ok, your order number is 58. (OK, Ihre Bestellung hat die Nummer 58.) Please wait over there.“ (Warten Sie bitte dort drüben.)
21. Klicken Sie auf „Speichern“, um das Dialogfeld zu schließen.

Sie haben soeben Ihr erstes Dialogfeld mit ENUM-Entitäten und SET_ENTITY-Aktionen erstellt. Sie können viele weitere Kombinationen für einen Benutzer erstellen, indem Sie Teilinfomationen angeben oder mit anderen Arten von ergebnisgeschlossenen Fragen experimentieren.

> [!NOTE]
> Während des Trainings sehen Sie Platzhalter für die SET_ENTITY-Aktionen, so z. B.
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Wenn Sie aber Protokolldialogfelder erstellen oder bereitgestellte Bots verwenden, sehen Benutzer diese Platzhalter nicht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Alternative Eingaben](./10-alternative-inputs.md)
