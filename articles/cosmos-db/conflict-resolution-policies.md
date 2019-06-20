---
title: Konflikttypen und Konfliktauflösungsrichtlinien mit mehreren Schreibregionen in Azure Cosmos DB
description: Dieser Artikel beschreibt die Konfliktkategorien und Konfliktauflösungsrichtlinien in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 98e9f5fff1b74d417ee07ed0056c8046b49baa17
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236545"
---
# <a name="conflict-types-and-resolution-policies"></a>Konflikttypen und Konfliktauflösungsrichtlinien

Konflikte und Konfliktauflösungsrichtlinien sind anwendbar, wenn Ihr Azure Cosmos DB-Konto mit mehreren Schreibregionen konfiguriert ist.

In Azure Cosmos-Konten, die mit mehreren Schreibregionen konfiguriert sind, können Updatekonflikte auftreten, wenn Schreiber gleichzeitig dasselbe Element in mehreren Regionen aktualisieren. Es können drei Typen von Updatekonflikten auftreten:

* **Konflikte beim Einfügen**: Diese Konflikte treten ggf. auf, wenn eine Anwendung gleichzeitig zwei oder mehr Elemente mit demselben eindeutigen Index in zwei oder mehr Regionen einfügt. Beispielsweise kann dieser Konflikt mit einer ID-Eigenschaft auftreten.

* **Konflikte beim Ersetzen**: Diese Konflikte treten ggf. auf, wenn eine Anwendung dasselbe Element in zwei oder mehr Regionen gleichzeitig aktualisiert.

* **Konflikte beim Löschen**: Diese Konflikte treten ggf. auf, wenn eine Anwendung ein Element in einer Region löscht und es gleichzeitig in einer anderen Region aktualisiert.

## <a name="conflict-resolution-policies"></a>Konfliktlösungsrichtlinien

Azure Cosmos DB bietet einen flexiblen, richtlinienbasierten Mechanismus zum Auflösen von Schreibkonflikten. Zwei Konfliktauflösungsrichtlinien stehen für einen Azure Cosmos-Container zur Auswahl:

- **Letzter Schreibvorgang gewinnt (LWW)** : Diese Auflösungsrichtlinie verwendet standardmäßig eine vom System definierte Zeitstempeleigenschaft. Sie basiert auf dem Protokoll zur Zeitsynchronisierung. Bei Verwendung der SQL-API können Sie auch eine andere benutzerdefinierte numerische Eigenschaft (z.B. Ihre eigenes Konzept für Zeitstempel) für die Konfliktauflösung angeben. Eine benutzerdefinierte numerische Eigenschaft wird auch als *Konfliktauflösungspfad* bezeichnet. 

  Wenn beim Einfügen oder Ersetzen ein Konflikt zwischen mindestens zwei Elementen auftritt, erhält das Element mit dem höchsten Wert für den „Konfliktauflösungspfad“ Vorrang. Wenn mehrere Elemente denselben numerischen Wert für den Konfliktauflösungspfad aufweisen, bestimmt das System den „Gewinner“. Alle Regionen konvergieren garantiert zu einem einzigen Gewinner und erhalten dieselbe Version des Elements, für das ein Commit ausgeführt wurde. Wenn Konflikte beim Löschen beteiligt sind, „gewinnt“ immer die gelöschte Version gegenüber den Konflikten beim Einfügen oder Ersetzen. Dieses Ergebnis tritt unabhängig vom Wert des Konfliktauflösungspfads ein.

  > [!NOTE]
  > „Letzter Schreibvorgang gewinnt“ (Last Write Wins, LWW) ist der standardmäßige Konfliktauflösungsmodus. Er ist für die folgenden APIs verfügbar: SQL, MongoDB, Cassandra, Gremlin und Tabellen.

  Weitere Informationen finden Sie unter [Anwendungsbeispiele für LWW-Konfliktauflösungsrichtlinien](how-to-manage-conflicts.md).

- **Benutzerdefiniert**: Diese Auflösungsrichtlinie ist für eine anwendungsdefinierte Semantik zum Beilegen von Konflikten konzipiert. Beim Festlegen dieser Richtlinie für Ihren Azure Cosmos-Container müssen Sie auch eine *gespeicherte Mergeprozedur* registrieren. Diese Prozedur wird automatisch aufgerufen, wenn Konflikte bei einer Datenbanktransaktion auf dem Server erkannt werden. Das System garantiert genau eine Ausführung der Mergeprozedur im Rahmen des Commitprotokolls.  

  Wenn Sie Ihren Container mit der Option für die benutzerdefinierte Auflösung konfigurieren und keine Mergeprozedur im Container registrieren oder die Mergeprozedur zur Laufzeit eine Ausnahme auslöst, werden die Konflikte in den *Konfliktfeed* geschrieben. Ihre Anwendung muss die Konflikte im Konfliktfeed dann manuell auflösen. Weitere Informationen finden Sie unter [Anwendungsbeispiele für benutzerdefinierte Auflösungsrichtlinien und den Konfliktfeed](how-to-manage-conflicts.md).

  > [!NOTE]
  > Die benutzerdefinierte Konfliktauflösungsrichtlinie ist nur für SQL-API-Konten verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Richtlinien für die Konfliktauflösung konfigurieren:

* [Konfigurieren von Multimaster in Ihren Anwendungen](how-to-multi-master.md)
* [Verwalten von Richtlinien für die Konfliktlösung](how-to-manage-conflicts.md)
* [Lesen aus dem Konfliktfeed](how-to-manage-conflicts.md#read-from-conflict-feed)
