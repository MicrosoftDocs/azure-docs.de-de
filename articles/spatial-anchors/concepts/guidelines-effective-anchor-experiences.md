---
title: Richtlinien für effektive Ankerfunktionen, die Azure Spatial Anchors verwenden | Microsoft-Dokumentation
description: Richtlinien und Überlegungen zum effektiven Erstellen und Suchen von Ankern mit Azure Spatial Anchors.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60566032"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Erstellen von effektiven Ankerfunktionen mithilfe von Azure Spatial Anchors

Dieser Artikel enthält Richtlinien und Überlegungen, die Sie beim effektiven Erstellen und Suchen von Ankern mithilfe von Azure Spatial Anchors unterstützen.

## <a name="good-anchors"></a>Gute Anker

Spatial Anchors unterstützt Sie beim Erstellen guter Anker. Es ist wichtig, Zeit zu investieren, um Ihre Benutzer entweder zu schulen oder anzuleiten, damit diese auf Ihrer Benutzeroberfläche gute Anker erstellen können. Indem Sie mit einer solchen Investition dafür sorgen, dass unter Berücksichtigung folgender Aspekte von vorneherein gute Anker entstehen, helfen Sie den Endbenutzern dabei, Anker zuverlässig zu finden:

- Auf verschiedenen Geräten
- Zu unterschiedlichen Zeiten
- In verschiedenen Lichtverhältnissen
- Aus den gewünschten Blickwinkeln im Raum

## <a name="static-and-dynamic-locations"></a>Statische und dynamische Positionen

Beim Entwerfen von Ankerfunktionen ist die Auswahl der Positionen wichtig. Sollen die Positionen statisch sein und von einem Administrator des Raums definiert werden? Oder sollen sie dynamisch sein und vom Benutzer definiert werden?

In einem Einzelhandelsgeschäft kann es sinnvoll sein, eine statische Benutzeroberfläche zu nutzen, um Kunden anzulocken. Entwickler eines Mixed Reality-Brettspiels dagegen möchten es wahrscheinlich dem Benutzer überlassen, wo er spielen möchte.

Bei statischen Positionen können Sie Administratoren anweisen, Zeit zu investieren, um den Raum mit guten Ankern zu betreuen.

Bei dynamischen Positionen müssen Sie überlegen, wie Sie Benutzer auf der Benutzeroberfläche schulen oder anleiten, damit diese gute Anker erstellen können.

## <a name="stable-visual-features"></a>Stabile visuelle Merkmale

Visuelle Nachverfolgungssysteme, die auf Mixed Reality- und Augmented Reality-Geräten zum Einsatz kommen, nutzen visuelle Merkmale der Umgebung. So erzielen Sie ein möglichst zuverlässiges Benutzererlebnis:  

- *Ja*: Erstellen Sie Anker an Positionen, die stabile visuelle Merkmale aufweisen (also Merkmale, die sich nicht häufig ändern).

- *Nein*: Erstellen Sie keine Anker auf leeren Oberflächen, die keinerlei Unterscheidungsmerkmale aufweisen.

- *Nein*: Erstellen Sie keine Anker auf stark reflektierenden Materialien.

- *Nein*: Erstellen Sie keine Anker auf Oberflächen mit sich wiederholenden Mustern, wie etwa einem Teppich oder einer Tapete.

![Beispiele für gute und schlechte Umgebungen für Anker](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Verschiedene Blickwinkel

Wenn Sie einen Anker erstellen, denken Sie über die Personen nach, die später versuchen werden, diesen Anker zu finden.

Stellen Sie sich z.B. einen Anker mitten in einem Raum mit zwei Türen vor. Wahrscheinlich sollen Benutzer den Raum aus beiden Türen betreten können. Beim Erstellen des Ankers müssen Sie dessen Position aus beiden Türöffnungen scannen. Sie ändern den Blickwinkel, um Umgebungsdaten rund um den Anker zu erfassen, sodass Benutzer den Anker von beiden Türen aus finden können.

Allgemein gilt beim Erstellen von Ankern Folgendes: Scannen Sie den Anker aus dem Blickwinkel der Personen, die ihn finden möchten. Wenn Sie also z.B. virtuelle Inhalte auf eine Skulptur im Außenbereich platzieren, ist es sinnvoll, beim Scannen um die Skulptur herumzulaufen und basierend auf diesen Daten den Anker zu erstellen. Wenn Ihr Anker sich in der Ecke eines Raums befindet, können Benutzer sich dem Anker nur aus einer Richtung nähern. Beim Erstellen dieses Ankers können Sie ihn nur aus diesem Blickwinkel scannen.

## <a name="multiple-anchors"></a>Mehrere Anker

Die Lichtverhältnisse können sich auf die visuellen Merkmale auswirken, die von einer App erkannt werden. Anker, die bei starkem natürlichem Licht erstellt wurden, lassen sich bei künstlichem Licht möglicherweise sehr schwer finden (und umgekehrt).  

Wenn Sie dieses Problem haben, kann es hilfreich sein, zwei Anker zu erstellen. Erstellen Sie an der gleichen Position einen Anker bei Tageslicht und einen weiteren bei künstlichem Licht. Ihre App kann dann beide Anker abfragen. Wenn einer der beiden Anker gefunden wird, kann die App den Anker darstellen. 

Mehrere Anker können auch in Umgebungen hilfreich sein, in denen sich die visuellen Merkmale verändern, weil sich die meisten Objekte bewegen. Wenn ein Anker aufgrund erheblicher Änderungen in der Umgebung nur noch schwer auffindbar ist, können Sie den Anker durch einen neuen ersetzen. Dies kann beispielsweise in einem Einzelhandelsgeschäft der Fall sein, in dem die Einrichtung alle paar Monate geändert wird.

## <a name="targets-and-rooms"></a>Ziele und Räume

In vielen Fällen ist ein Anker ein Einstiegspunkt in das Benutzererlebnis für Ihre App. Dieser Schritt sollte zügig und zuverlässig ablaufen, damit Benutzer Ihre App nutzen können. Lassen Sie sich beim Entwurf Ihrer Anker Zeit, und planen Sie sorgfältig, auf welche Weise Benutzer Ihre Anker finden sollen. Wenn Sie darüber nachdenken, wie Anker gefunden werden sollen, sind zwei Szenarien nützlich: *Ziele* und *Räume*.

### <a name="targets"></a>Ziele

Im Szenario „Ziele“ ist die Position eines Ankers bekannt. Ein Beispiel: In einer fiktiven Mixed Reality-App für einen Malkurs hängt eine Benutzerin eine virtuelle Leinwand an die Wand. Sie weist die anderen Benutzer im Raum an, ihre Geräte auf die gleiche Stelle an der Wand zu richten, um den Anker zu lokalisieren und mit dem Malen zu beginnen.  

Ein anderes Beispiel für ein Zielszenario ist ein Schild in einem Café: „Scannen, um Angebote zu erhalten“. Das Café hat hier einen Anker platziert. Wenn Benutzer das Schild scannen, finden sie den Anker und betreten das Augmented Reality-Erlebnis, um Angebote zu suchen.

In einem Zielszenario können auch Fotos hilfreich sein. Wenn Sie Benutzern auf ihren Geräten ein Foto des angestrebten Ziels zeigen, können die Benutzer schnell erkennen, was sie in der realen Welt scannen müssen. Sie können Benutzer z.B. per GPS zum allgemeinen Areal eines angestrebten Ziels lotsen. Wenn die Benutzer dort ankommen, zeigt Ihre App ein Foto des Ziels. Die Benutzer sehen sich um, finden das Ziel und scannen es, um den Anker zu finden.

![Abbildung eines Ankers mit dem Foto des Ziels auf dem Mobilgerät eines Benutzers](./media/start-here-edit.png)

### <a name="rooms"></a>Räume

Im Szenario mit Räumen geben Benutzer einen Standort ein, einfach weil sie wissen, dass dort irgendwo ein Anker sein muss. Benutzer scannen den Standort mit ihrem Gerät und finden den Anker schnell.

In diesem Szenario müssen Sie in der Regel gut betreute Anker erstellen, wie unter „Verschiedene Blickwinkel“ erläutert. Wenn Sie den Raum beim Erstellen des Ankers aus mehreren Blickwinkeln gescannt haben, können Benutzer von fast jedem Standort im Raum aus finden.

![Abbildung: So kann ein Benutzer einen Raum scannen, um einen Anker zu finden.](./media/scan-room.png)

Grundsätzlich gilt: Sie verbringen also beim Erstellen eines Ankers Zeit mit dem Scannen der Örtlichkeit, damit Benutzer den Anker später schnell finden. Diesen wichtigen Kompromiss müssen Sie beim Erstellen des Benutzererlebnisses unbedingt berücksichtigen.

Das oben beschriebene Beispiel der Malkurs-App mit Mixed Reality funktioniert nicht gut als Raumszenario. Hier möchte die Benutzerin, die den Anker platziert, dass andere Benutzer schnell einsteigen können. Diese Benutzer sollen nicht warten müssen, bis der Raum sorgfältig gescannt wurde. Da alle Benutzer wissen, wohin sie ihre Geräte richten müssen, um die Anker zu finden, funktioniert dieses Beispiel besser als Zielszenario.

## <a name="anchor-location"></a>Ankerposition

Visuelle Nachverfolgungssysteme benötigen visuelle Merkmale in einer Umgebung. Je mehr visuelle Merkmale in einem Scan enthalten sind, desto höher ist die Wahrscheinlichkeit, dass ein Anker gefunden wird.

Befolgen Sie die allgemeinen Richtlinien in diesem Abschnitt, um ein Benutzererlebnis zu schaffen, das sinnvolle Scans der Umgebung unterstützt.

Wenn ein Benutzer nicht innerhalb weniger Sekunden einen Anker findet, sollte die App den Benutzer auffordern, das Gerät zu bewegen, um andere Blickwinkel zu erfassen. Die App kann Benutzern auch vorschlagen, sich in der Umgebung zu bewegen, um aus anderen Blickwinkeln nach dem Anker zu suchen. Je mehr Blickwinkel eines Merkmals von einem Gerät erkannt werden, desto besser.

Fordern Sie bei Zielszenarien die Benutzer auf, sich um das Ziel herum zu bewegen, um es aus anderen Blickwinkeln zu betrachten. Anders gesagt: Fordern Sie Benutzer so lange auf, das Ziel aus neuen Blickwinkeln zu erfassen, bis der Anker gefunden wurde.

In Raumszenarien sollten Sie Benutzer auffordern, den Raum langsam zu scannen. Sie können Benutzer z.B. anweisen, den Raum mit einer 180- oder sogar 360-Grad-Drehung zu erfassen. Oder Sie fordern die Benutzer auf, den Raum aus einem neuen Blickwinkel zu betrachten. 

Die sinnvollste Methode ist ein Raumscan. Bei einem Raumscan werden mehr Merkmale der Umgebung erfasst als z.B. beim Scannen einer nahe gelegenen Wand. Der Scan einer nahe gelegenen Wand erfasst nicht so viele nützliche visuelle Merkmale der Umgebung.

Bei der Suche nach einem Anker ist es nicht hilfreich, das Gerät mehrmals von links nach rechts zu schwenken. Damit werden nur die gleichen Punkte aus dem gleichen Blickwinkel erfasst.

## <a name="experience-tests"></a>Testen des Benutzererlebnisses

In diesem Artikel wurden allgemeine Richtlinien erläutert. Mit Spatial Anchors können Sie Apps schreiben, die mit der realen Welt interagieren. Sie sollten daher Zeit investieren, um die Ankerszenarien Ihrer App in realen Umgebungen zu testen. Dies gilt insbesondere für Umgebungen, in denen Ihre Benutzer die App wahrscheinlich verwenden.
