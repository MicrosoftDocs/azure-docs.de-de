---
title: Ankerbeziehungen und Wegeleitsysteme
description: Enthält eine Beschreibung des konzeptionellen Modells von Ankerbeziehungen. Es wird beschrieben, wie Sie Anker in einem Bereich verbinden und die Nearby-API verwenden, um die Anforderungen eines Wegeleitsystem-Szenarios zu erfüllen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8c321eae9451715077aa50d163b62b51ec88ce1a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496884"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Ankerbeziehungen und Wegeleitsysteme in Azure Spatial Anchors

Mit Ankerbeziehungen können Sie in einem Bereich verbundene Anker erstellen und dann Fragen wie diese stellen:

* Befinden sich Anker in der Nähe?
* Wie weit sind sie entfernt?

## <a name="examples"></a>Beispiele

Sie können beispielsweise in folgenden Fällen verbundene Anker verwenden:

* Eine Mitarbeiterin oder ein Mitarbeiter muss eine Aufgabe erledigen, für die in einem Werk verschiedene Orte besucht werden müssen. Das Werk verfügt an jedem Ort über Raumanker. Mit einer HoloLens- oder mobilen App wird der Mitarbeiter von einem Ort zum nächsten geführt. Die App fragt zuerst nach den Raumankern in der Nähe und führt den Mitarbeiter dann zum nächsten Ort. In der App werden die grobe Richtung und die Entfernung bis zum nächsten Ort angezeigt.

* Ein Museum erstellt Raumanker für Ausstellungsstücke. Zusammen ergeben diese Anker eine einstündige Tour entlang der wichtigsten Ausstellungsstücke des Museums. An einem Ausstellungsstück können Besucher auf ihrem mobilen Gerät die Mixed Reality-App des Museums öffnen. Indem sie die Kamera ihres Smartphones auf den Bereich richten, können sie die allgemeine Richtung und Entfernung zu den anderen Ausstellungsstücken der Tour anzeigen. Wenn ein Benutzer in Richtung eines Ausstellungsstücks geht, aktualisiert die App die allgemeine Richtung und Entfernung, um dem Benutzer den Weg zu weisen.

## <a name="set-up-way-finding"></a>Einrichten des Wegeleitsystems

In einer App, für die die Sichtrichtung und die Entfernung zwischen Ankern verwendet wird, um Hilfestellung zu leisten, wird ein *Wegeleitsystem* genutzt. Ein Wegeleitsystem unterscheidet sich von Wegbeschreibungen. Bei Wegbeschreibungen werden Benutzer um Wände herum, durch Türen und über Stockwerke geleitet. Bei Wegeleitsystemen erhält der Benutzer Hinweise zur allgemeinen Richtung, in der das Ziel liegt. Aber auch eigene Rückschlüsse oder Ortskenntnisse erleichtern dem Benutzer die Navigation zum Ziel.

Erstellen Sie zum Entwickeln eines Wegeleitsystems zunächst einen Bereich für die Umgebung, und entwickeln Sie dann eine App, mit der Benutzer interagieren können. Dies sind die grundlegenden Schritte:

1. **Planen des Bereichs**: Entscheiden Sie, welche Orte innerhalb des Bereichs Teil der Wegeleitsystem-Umgebung sein sollen. In unseren Szenarien kann ggf. der Werksleiter oder der für die Museumstour verantwortliche Mitarbeiter die Entscheidung treffen, welche Orte in das Wegeleitsystem aufgenommen werden sollen.
2. **Verbinden der Anker**: Besuchen Sie die ausgewählten Orte, um Raumanker zu erstellen. Sie können dies in einem Administratormodus der Endbenutzer-App oder in einer völlig anderen App durchführen. Sie verbinden jeden Anker mit den anderen Ankern bzw. setzen sie miteinander in Beziehung. Der Dienst verwaltet diese Beziehungen.
3. **Starten der Endbenutzererfahrung**: Benutzer führen die App aus, um einen Anker zu finden, der sich an allen gewählten Orten befinden kann. In Ihrem Gesamtentwurf sollten die Orte bestimmt werden, an denen Benutzer mit der Verwendung der Erfahrung beginnen können.
4. **Suchen von Ankern in der Nähe**: Nachdem der Benutzer einen Anker gefunden hat, kann die App die in der Nähe befindlichen Anker anfordern. Bei diesem Verfahren wird der Bezug des Geräts zu diesen Ankern zurückgegeben.
5. **Führen des Benutzers**: Die App kann diesen Bezug für alle Anker nutzen, um Hilfestellung zur allgemeinen Richtung und Entfernung des Benutzers zu geben. Im Kamerafeed der App können beispielsweise ein Symbol und ein Pfeil angezeigt werden, um die einzelnen potenziellen Ziele darzustellen. Dies ist in der folgenden Abbildung zu sehen.
6. **Optimierung der Benutzerführung**: Während der Benutzer läuft, kann die App regelmäßig einen neuen Bezug zwischen dem Gerät und dem Zielanker berechnen. Die App optimiert ständig die Hinweise zur Benutzerführung, damit der Benutzer sein Ziel erreicht.

    ![Beispiel für die Benutzerführung in einer App im Rahmen eines Wegeleitsystems](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Verbinden der Anker

Zum Erstellen eines Wegeleitsystems müssen Sie zunächst die Anker an den gewählten Orten anordnen. In diesem Abschnitt gehen wir davon aus, dass der Administrator der App dies bereits durchgeführt hat.

### <a name="connect-anchors-in-a-single-session"></a>Verbinden von Ankern in nur einer Sitzung

Verbinden Sie die Anker wie folgt:

1. Gehen Sie zum ersten Ort, und erstellen Sie Anker A, indem Sie ein CloudSpatialAnchorSession-Element verwenden.
2. Gehen Sie zum zweiten Ort. Mit der zugrunde liegenden MR/AR-Plattform wird die Bewegung nachverfolgt.
3. Erstellen Sie Anker B, indem Sie dasselbe CloudSpatialAnchorSession-Element verwenden. Anker A und B sind jetzt verbunden. Diese Beziehung wird vom Spatial Anchors-Dienst verwaltet.
4. Fahren Sie mit dieser Vorgehensweise für die restlichen Anker fort.

### <a name="connect-anchors-in-multiple-sessions"></a>Verbinden von Ankern in mehreren Sitzungen

Sie können Raumanker auch in mehreren Sitzungen verbinden. Mit dieser Methode können Sie zunächst einige Anker und später dann weitere Anker erstellen und verbinden.

Verbinden Sie Anker wie folgt in mehreren Sitzungen:

1. Die App erstellt einige Anker mit einem CloudSpatialAnchorSession-Element.
2. Später findet die App dann einen dieser Anker (z. B. Anker A), indem ein neues CloudSpatialAnchorSession-Element verwendet wird.
3. Gehen Sie zu einem neuen Ort. Mit der zugrunde liegenden Mixed Reality- oder Augmented Reality-Plattform wird die Bewegung nachverfolgt.
4. Erstellen Sie Anker C, indem Sie dasselbe CloudSpatialAnchorSession-Element verwenden. Die Anker A, B und C sind jetzt verbunden. Diese Beziehung wird vom Spatial Anchors-Dienst verwaltet.

Sie können dieses Verfahren später für weitere Anker und weitere Sitzungen fortsetzen.

### <a name="verify-anchor-connections"></a>Überprüfen von Ankerverbindungen

Die App kann überprüfen, ob zwei Anker verbunden sind, indem sie eine Abfrage für in der Nähe befindliche Anker durchführt. Hierfür werden `NearAnchorCriteria` für `CloudSpatialAnchorWatcher` festgelegt. Wenn das Ergebnis der Abfrage den Zielanker enthält, wird die Ankerverbindung bestätigt. Falls die Anker nicht verbunden sind, kann die App versuchen, die Verbindung wiederherzustellen.

Hier sind einige Gründe angegeben, warum beim Verbinden von Ankern ggf. ein Fehler auftritt:

* Die zugrunde liegende Mixed Reality- oder Augmented Reality-Plattform konnte die Nachverfolgung während des Verbindungsprozesses für die Anker nicht fortführen.
* Aufgrund eines Netzwerkfehlers während der Kommunikation mit dem Spatial Anchors-Dienst konnte die Ankerverbindung nicht aufrechterhalten werden.

### <a name="find-sample-code"></a>Zugreifen auf Beispielcode

Informationen zum Zugreifen auf Beispielcode, mit dem veranschaulicht wird, wie Anker verbunden und Abfragen für den Bereich in der Nähe durchgeführt werden, finden Sie auf der Seite mit den [Spatial Anchors-Beispiel-Apps](https://github.com/Azure/azure-spatial-anchors-samples).
