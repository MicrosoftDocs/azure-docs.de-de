---
title: Herstellen einer Verbindung mit 3270-Apps auf IBM-Mainframes
description: Integrieren und Automatisieren von bildschirmgesteuerten 3270-Apps mit Azure mithilfe von Azure Logic Apps und IBM 3270-Connector
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573431"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrieren von bildschirmgesteuerten 3270-Apps auf IBM Mainframes mit Azure mithilfe von Azure Logic Apps und IBM 3270-Connector

Mit Azure Logic Apps und dem IBM 3270-Connector können Sie auf IBM Mainframe-Apps zugreifen und diese ausführen, die Sie normalerweise mittels Navigation in 3270-Emulatorbildschirmen steuern. Auf diese Weise können Sie Ihre IBM Mainframe-Apps in Azure, Microsoft und andere Apps, Dienste und Systeme integrieren, indem Sie automatisierte Workflows mit Azure Logic Apps erstellen. Der Connector kommuniziert mit IBM Mainframes unter Verwendung des TN3270-Protokolls, und er ist in allen Azure Logic Apps-Regionen verfügbar, außer in Azure Government und Azure China 21Vianet. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

Dieser Artikel beschreibt folgende Aspekte der Verwendung des 3270-Connectors: 

* Gründe für die Verwendung des IBM 3270-Connectors in Azure Logic Apps und wie der Connector bildschirmgesteuerte 3270-Apps ausführt

* Die Voraussetzungen und die Einrichtung für die Verwendung des 3270-Connectors

* Die Schritte zum Hinzufügen von 3270-Connectoraktionen zu Ihrer Logik-App

## <a name="why-use-this-connector"></a>Gründe für die Verwendung dieses Connectors

Für den Zugriff auf Apps auf IBM Mainframes verwenden Sie in der Regel einen 3270-Terminalemulator, häufig auch als „grüner Bildschirm“ (green screen) bezeichnet. Diese Methode ist ein lange bewährtes Verfahren, hat aber ihre Grenzen. Zwar hilft Ihnen Host Integration Server (HIS) bei der direkten Arbeit mit diesen Apps, doch manchmal ist ein Trennen von Bildschirm und Geschäftslogik eventuell nicht möglich. Oder vielleicht haben Sie keine Informationen mehr, wie die Hostanwendungen funktionieren.

Um diese Szenarien zu erweitern, arbeitet der IBM 3270-Connector in Azure Logic Apps mit dem 3270-Designtool, mit dem Sie die Hostbildschirme aufzeichnen oder „erfassen“, die für eine bestimmte Aufgabe verwendet werden, den Navigationsfluss für diese Aufgabe über Ihre Mainframe-App definieren sowie die Methoden mit Eingabe-und Ausgabeparametern für diese Aufgabe definieren. Das Designtool konvertiert diese Informationen in Metadaten, die der 3270-Connector verwendet, wenn eine Aktion aufgerufen wird, die die Aufgabe von Ihrer Logik-App darstellt.

Nachdem Sie die Metadatendatei aus dem Designtool generiert haben, fügen Sie diese Datei einem Integrationskonto in Azure hinzu. Auf diese Weise kann Ihre Logik-App auf die Metadaten Ihrer App zugreifen, wenn Sie eine 3270-Connectoraktion hinzufügen. Der Connector liest die Metadatendatei aus Ihrem Integrationskonto, erledigt die Navigation durch die 3270-Bildschirme und präsentiert dynamisch die Parameter für die 3270-Connectoraktion. Sie können dann der Hostanwendung Daten bereitstellen, und der Connector gibt die Ergebnisse an Ihre Logik-App zurück. Auf diese Weise können Sie Ihre Legacy-Apps in Azure, Microsoft und andere Apps, Dienste und Systeme integrieren, die von Azure Logic Apps unterstützt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Empfohlen: Eine [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Sie können diese Umgebung als Ort zum Erstellen und Ausführen Ihrer Logik-App auswählen. Eine ISE ermöglicht den Zugriff von Ihrer Logik-App auf Ressourcen, die in virtuellen Azure-Netzwerken geschützt sind.

* Die für die Automatisierung und Ausführung Ihrer bildschirmgesteuerten 3270-Apps zu verwendende Logik-App

  Der IBM 3270-Connector verfügt über keine Trigger. Verwenden Sie deshalb einen anderen Trigger, um Ihre Logik-App zu starten, z. B. den Trigger **Serie**. Sie können dann 3270-Connectoraktionen hinzufügen. Zu Anfang [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Wenn Sie eine ISE verwenden, wählen Sie diese als Ort für Ihre Logik-App aus.

* [Laden Sie das 3270-Designtool herunter, und installieren Sie es](https://aka.ms/3270-design-tool-download).
Die einzige Voraussetzung ist [Microsoft .NET Framework 4.8](https://aka.ms/net-framework-download).

  Dieses Tool hilft Ihnen bei der Aufzeichnung der Bildschirme, Navigationspfade, Methoden und Parameter für die Aufgaben in Ihrer App, die Sie als 3270-Connectoraktionen hinzufügen und ausführen. Das Tool generiert eine HIDX-Datei (Host Integration Designer XML), die die notwendigen Metadaten für den Connector bereitstellt, um damit Ihre Mainframe-App zu steuern.
  
  Nach dem Herunterladen und Installieren dieses Tools führen Sie diese Schritte aus, um Ihren Host zu verbinden:

  1. Öffnen Sie das 3270-Designtool. Wählen Sie im Menü **Session** (Sitzung) den Eintrag **Host Sessions** (Hostsitzungen) aus.
  
  1. Geben Sie Ihre TN3270-Hostserverinformationen an.

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das der Ort ist, wo Sie Ihre HIDX-Datei als Zuordnung speichern, damit Ihre Logik-App auf die Metadaten und Methodendefinitionen in dieser Datei zugreifen kann. 

  Stellen Sie sicher, dass Ihr Integrationskonto mit der Logik-App verknüpft ist, die Sie verwenden. Wenn Sie eine ISE verwenden, stellen Sie außerdem sicher, dass der Ort Ihres Integrationskontos dieselbe ISE ist, die Ihre Logik-App verwendet.

* Zugreifen auf den TN3270-Server, der Ihre Mainframe-App hostet

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Übersicht über das Erstellen von Metadaten

In einer bildschirmgesteuerten 3270-App sind die Bildschirme und Datenfelder für Ihre Szenarien eindeutig. Daher benötigt der 3270-Connector diese Informationen über Ihre App, die Sie als Metadaten bereitstellen können. Diese Metadaten beschreiben Informationen, die Ihrer Logik-App helfen, Bildschirme zu identifizieren und zu erkennen, sie beschreiben, wie Sie zwischen Bildschirmen navigieren, wo Sie Daten eingeben, und wo Ergebnisse zu erwarten sind. Um diese Metadaten anzugeben und zu generieren, verwenden Sie das 3270-Designtool, das Sie durch diese spezifischen *Modi*, oder auch Phasen, führt, wie weiter unten ausführlicher beschrieben:

* **Capture** (Erfassen): In diesem Modus zeichnen Sie die Bildschirme auf, die für das Durchführen einer bestimmten Aufgabe mit Ihrer Mainframe-App erforderlich sind, z. B. das Abrufen eines Kontostands.

* **Navigation**: In diesem Modus geben Sie den Plan oder Pfad dafür an, wie durch die Bildschirme Ihrer Mainframe-App für die spezifische Aufgabe zu navigieren ist.

* **Methods** (Methoden): In diesem Modus definieren Sie die Methode, z. B. `GetBalance`, die den Bildschirmnavigationspfad beschreibt. Sie wählen auch die Felder auf jedem Bildschirm aus, die zu Ein- und Ausgabeparametern der Methode werden.

### <a name="unsupported-elements"></a>Nicht unterstützte Elemente

Das Designtool unterstützt folgende Elemente nicht:

* Partielle IBM BMS-Zuordnungen (Basic Mapping Support, BMS): Wenn Sie eine BMS-Zuordnung importieren, ignoriert das Designtool partielle Bildschirmdefinitionen.

* Menüverarbeitung

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Screenshoterfassung

In diesem Modus markieren Sie ein Element in jedem 3270-Bildschirm, das den Bildschirm eindeutig identifiziert. Beispielsweise können Sie eine Textzeile oder einen komplexeren Satz von Bedingungen wie bestimmten Text und ein nicht leeres Feld angeben. Sie können diese Bildschirme entweder über eine Liveverbindung auf dem Hostserver aufzeichnen oder diese Informationen aus einer IBM BMS-Zuordnung importieren. Die Liveverbindung verwendet einen TN3270-Emulator für die Verbindung mit dem Host. Jede Connectoraktion muss einer einzelnen Aufgabe zugeordnet sein, die mit dem Herstellen einer Verbindung mit Ihrer Sitzung beginnt und mit dem Trennen von Ihrer Sitzung endet.

1. Falls Sie dies noch nicht getan haben, öffnen Sie das 3270-Designtool. Wählen Sie auf der Symbolleiste **Capture** (Erfassen) aus, um den Erfassungsmodus zu starten.

1. Wählen Sie im Menü **Session** (Sitzung) den Eintrag **Connect** (Verbinden) aus.

1. Zum Starten der Aufzeichnung wählen Sie im Menü **Recording** (Aufzeichnung) die Option **Start Recording** (Aufzeichnung starten) aus. (Tastatur: STRG+E)

1. Durchlaufen Sie im Bereich **Capture** (Erfassen), beginnend mit dem ersten Bildschirm in Ihrer App, Ihre App für die spezifische Aufgabe, die Sie aufzeichnen möchten.

1. Nachdem Sie die Aufgabe abgeschlossen haben, melden Sie sich bei Ihrer App ab, wie Sie es normalerweise machen würden.

1. Wählen Sie im Menü **Session** (Sitzung) den Eintrag **Disconnect** (Trennen) aus.

1. Zum Beenden der Aufzeichnung wählen Sie im Menü **Recording** (Aufzeichnung) die Option **Stop Recording** (Aufzeichnung beenden) aus. (Tastatur: STRG+UMSCHALT+E)

   Nachdem Sie die Bildschirme für eine Aufgabe erfasst haben, werden im Designtool Miniaturansichten angezeigt, die diese Bildschirme darstellen. Ein paar Anmerkungen zu diesen Miniaturansichten:

   * Zusammen mit Ihren erfassten Bildschirmen finden Sie einen Bildschirm namens „Empty“ (Leer) vor.

     Wenn Sie zuerst eine Verbindung mit [CICS](https://www.ibm.com/it-infrastructure/z/cics) herstellen, müssen Sie die Taste „Clear“ (Löschen) senden, bevor Sie den Namen für die auszuführende Transaktion eingeben können. Der Bildschirm, in dem Sie die Taste „Clear“ (Löschen) senden, verfügt über keine *Erkennungsattribute* wie einen Bildschirmtitel, die Sie mithilfe des Bildschirmerkennungs-Editors hinzufügen können. Um diesen Bildschirm darzustellen, enthalten die Miniaturansichten einen Bildschirm namens „Empty“ (Leer). Sie können diesen Bildschirm später verwenden, um den Bildschirm darzustellen, in dem Sie den Transaktionsnamen eingeben.

   * Standardmäßig wird für einen erfassten Bildschirm das erste Wort auf dem Bildschirm als Name verwendet. Wenn der Name bereits vorhanden ist, fügt das Designtool dem Namen einen Unterstrich und eine Zahl an, z. B. „WBGB“ und „WBGB_1“.

1. Um einem erfassten Bildschirm einen aussagekräftigeren Namen zu geben, gehen Sie folgendermaßen vor:

   1. Wählen Sie im Bereich **Host Screens** (Hostbildschirme) den Bildschirm aus, den Sie umbenennen möchten.

   1. Im selben Bereich am unteren Rand finden Sie die Eigenschaft **Screen Name** (Bildschirmname).

   1. Ändern Sie den aktuellen Bildschirmnamen in einen aussagekräftigeren Namen.

1. Geben Sie nun die Felder zur Identifizierung der einzelnen Bildschirme an.

   Der 3270-Datenstrom enthält keine Standardbezeichner für die Bildschirme, weshalb Sie in jedem Bildschirm eindeutigen Text auswählen müssen. Für komplexe Szenarien können Sie mehrere Bedingungen angeben, z. B. eindeutigen Text und ein Feld mit einer bestimmten Bedingung.

Nach Abschluss der Auswahl der Erkennungsfelder wechseln Sie in den nächsten Modus.

### <a name="conditions-for-identifying-repeated-screens"></a>Bedingungen für das Identifizieren sich wiederholender Bildschirme

Damit der Connector zwischen Bildschirmen navigieren und diese unterscheiden kann, suchen Sie in der Regel eindeutigen Text in einem Bildschirm, den Sie als Bezeichner für die erfassten Bildschirme verwenden können. Bei sich wiederholenden Bildschirmen benötigen Sie möglicherweise weitere Identifizierungsmethoden. Nehmen wir beispielsweise an, dass Sie zwei Bildschirme haben, die identisch aussehen, außer dass der eine Bildschirm einen gültigen Wert zurückgibt, während der anderen Bildschirm eine Fehlermeldung zurückgibt.

Im Designtool können Sie *Erkennungsattribute* hinzufügen, z. B. einen Bildschirmtitel wie „Kontostand abrufen“, indem Sie den Bildschirmerkennungs-Editor verwenden. Wenn Sie einen Forkpfad (Verzweigung) haben und beide Branches denselben Bildschirm, aber mit unterschiedlichen Ergebnissen zurückgegeben, benötigen Sie andere Erkennungsattribute. Zur Laufzeit verwendet der Connector diese Attribute, um den aktuellen Branch und die aktuelle Fork zu bestimmen. Folgende Bedingungen können Sie verwenden:

* Spezifischer Wert: Dieser Wert entspricht der angegebenen Zeichenfolge an der angegebenen Position.
* KEIN spezifischer Wert: Dieser Wert entspricht nicht der angegebenen Zeichenfolge an der angegebenen Position.
* Leer: Dieses Feld ist leer.
* NICHT leer: Dieses Feld ist nicht leer.

Weitere Informationen finden Sie weiter unten in diesem Thema im [Beispielnavigationsplan](#example-plan).

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigationspläne definieren

In diesem Modus definieren Sie den Fluss oder die Schritte zum Navigieren durch die Bildschirme Ihrer Mainframe-App für Ihre spezifische Aufgabe. Beispielsweise kann es durchaus mehr als einen Pfad geben, den Ihre App nehmen kann, wobei der eine Pfad das richtige Ergebnis erzeugt, während der andere Pfad einen Fehler erzeugt. Geben Sie für jeden Bildschirm die Tastatureingaben an, die notwendig sind, um zum nächsten Bildschirm zu wechseln, z. B. `CICSPROD <enter>`.

> [!TIP]
> Wenn Sie mehrere Aufgaben automatisieren, die dieselben Bildschirme zum Verbinden und Trennen verwenden, bietet das Designtool spezielle Plantypen für Verbinden (Connect) und Trennen (Disconnect). Wenn Sie diese Pläne definieren, können Sie sie am Anfang und Ende Ihres Navigationsplans hinzufügen.

### <a name="guidelines-for-plan-definitions"></a>Richtlinien für Plandefinitionen

* Nehmen Sie alle Bildschirme auf, beginnend mit dem Verbinden und endend mit dem Trennen der Verbindung.

* Sie können einen eigenständigen Plan erstellen oder die „Connect“- und „Disconnect“-Pläne verwenden, bei denen Sie eine Reihe von Bildschirmen wiederverwenden können, die allen Ihren Transaktionen gemeinsam sind.

  * Der letzte Bildschirm in Ihrem „Connect“-Plan muss mit dem ersten Bildschirm in Ihrem Navigationsplan identisch sein.

  * Der erste Bildschirm in Ihrem „Disconnect“-Plan muss mit dem letzten Bildschirm in Ihrem Navigationsplan identisch sein.

* Ihre erfassten Bildschirme können zahlreiche, sich wiederholende Bildschirme enthalten. Wählen und verwenden Sie also von jedem sich wiederholenden Bildschirme in Ihrem Plan nur eine Instanz aus. Hier sind einige Beispiele für sich wiederholende Bildschirme:

  * Beispielsweise der Anmeldebildschirm, der Bildschirm **MSG-10**.
  * Der Begrüßungsbildschirm für CICS.
  * Der Bildschirm „Clear“ (Löschen) oder **Empty** (Leer).

<a name="create-plans"></a>

### <a name="create-plans"></a>Pläne erstellen

1. Wählen Sie auf der Symbolleiste des 3270-Designtools **Navigation** aus, um in den Navigationsmodus zu wechseln.

1. Um Ihren Plan zu starten, wählen Sie im Bereich **Navigation** die Option **New Plan** (Neuer Plan) aus.

1. Geben Sie unter **Choose New Plan Name** (Namen für neuen Plan auswählen) einen Namen für Ihren Plan ein. Wählen Sie in der Liste **Type** (Typ) den Plantyp aus:

   | Plantyp | Beschreibung |
   |-----------|-------------|
   | **Prozess** | Für eigenständige oder kombinierte Pläne |
   | **Herstellen einer Verbindung** | Für „Connect“-Pläne |
   | **Disconnect** (Trennen) | Für „Disconnect“-Pläne |
   |||

1. Ziehen Sie aus dem Bereich **Host Screens** (Hostbildschirme) die erfassten Miniaturansichten auf die Oberfläche des Navigationsplans im Bereich **Navigation**.

   Verwenden Sie zur Repräsentation des leeren Bildschirms, in den Sie den Transaktionsnamen eingeben, den Bildschirm „Empty“ (Leer).

1. Ordnen Sie die Bildschirme in der Reihenfolge an, die die Aufgabe beschreibt, die Sie definieren.

1. Wählen Sie zum Definieren des Flowpfads zwischen den Bildschirmen, einschließlich Forks und Zusammenführungen (Joins), auf der Symbolleiste des Designtools **Flow** aus.

1. Wählen Sie den ersten Bildschirm im Fluss aus. Ziehen und zeichnen Sie eine Verbindung zum nächsten Bildschirm im Fluss.

1. Geben Sie für jeden Bildschirm die Werte für die Eigenschaft **AID Key** (AID-Schlüssel; Attention Identifier, Aufmerksamkeitsbezeichner) und für die Eigenschaft **Fixed Text** (Fester Text) an, wodurch der Fluss zum nächsten Bildschirm wechselt.

   Eventuell verfügen Sie nur über den AID-Schlüssel oder sowohl über den AID-Schlüssel als auch den festen Text.

Nachdem Sie Ihren Navigationsplan fertig gestellt haben, können Sie [im nächsten Modus Methoden definieren](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Beispiel

In diesem Beispiel gehen wir davon aus, dass Sie eine CICS-Transaktion namens „WBGB“ ausführen, die diese Schritte umfasst: 

* Im ersten Bildschirm geben Sie einen Namen und ein Konto ein.
* Im zweiten Bildschirm erhalten Sie den Kontostand.
* Sie beenden und gelangen zum Bildschirm „Empty“ (Leer).
* Sie melden Sie sich bei CICS ab und gelangen zum Bildschirm „MSG-10“.

Wir gehen außerdem davon aus, dass Sie diese Schritte wiederholen, dabei aber falsche Daten eingeben, damit Sie den Bildschirm erfassen können, der den Fehler anzeigt. Dies sind die Bildschirme, die Sie erfassen:

* MSG-10
* CICS Welcome (Begrüßungsbildschirm)
* Empty
* WBGB_1 (Eingabe)
* WBGB_2 (Fehler)
* Empty_1
* MSG-10_1

Obwohl hier viele Bildschirme eindeutige Namen erhalten, sind einige Bildschirme identisch, z. B. „MSG-10“ und „Empty“ (Leer). Verwenden Sie bei einem sich wiederholenden Bildschirm nur eine Instanz für den Bildschirm in Ihrem Plan. Folgende Beispiele zeigen, wie ein eigenständiger Plan, ein „Connect“-Plan, ein „Disconnect“-Plan und ein kombinierter Plan aussehen könnten:

* Eigenständiger Plan

  ![Eigenständiger Navigationsplan](./media/connectors-create-api-3270/standalone-plan.png)

* „Connect“-Plan (Verbinden)

  ![„Connect“-Plan (Verbinden)](./media/connectors-create-api-3270/connect-plan.png)

* „Disconnect“-Plan (Trennen)

  ![„Disconnect“-Plan (Trennen)](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinierter Plan

  ![Kombinierter Plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Beispiel: Identifizieren sich wiederholender Bildschirme

Damit der Connector zwischen Bildschirmen navigieren und diese unterscheiden kann, suchen Sie in der Regel eindeutigen Text in einem Bildschirm, den Sie als Bezeichner für die erfassten Bildschirme verwenden können. Bei sich wiederholenden Bildschirmen benötigen Sie möglicherweise weitere Identifizierungsmethoden. Der Beispielplan umfasst eine Fork (Verzweigung), bei der Sie Bildschirme erhalten können, die ähnlich aussehen. Ein Bildschirm gibt einen Kontostand zurück, während der anderen Bildschirm eine Fehlermeldung zurückgibt.

Das Designtool ermöglicht es Ihnen, Erkennungsattribute hinzuzufügen, z. B. einen Bildschirmtitel wie „Kontostand abrufen“, indem Sie den Bildschirmerkennungs-Editor verwenden. Im dem Fall mit ähnlichen Bildschirmen benötigen Sie weitere Attribute. Zur Laufzeit verwendet der Connector diese Attribute, um den Branch und die Fork zu bestimmen.

* Im Branch, der mit einer gültigen Eingabe zurückgegeben wird, wobei es sich um den Bildschirm mit dem Kontostand handelt, können Sie ein Feld hinzufügen, das eine „nicht leere“ Bedingung aufweist.

* Im Branch, der mit einem Fehler zurückgegeben wird, können Sie ein Feld hinzufügen, das eine „leere“ Bedingung aufweist.

<a name="define-method"></a>

## <a name="define-methods"></a>Methoden definieren

In diesem Modus definieren Sie eine Methode, die Ihrem Navigationsplan zugeordnet ist. Für jeden Methodenparameter geben Sie den Datentyp wie Zeichenfolge, ganze Zahl, Datum oder Uhrzeit usw. an. Wenn Sie fertig sind, können Sie Ihre Methode auf dem Livehost testen und bestätigen, dass die Methode wie erwartet funktioniert. Dann generieren Sie die Metadatendatei oder HIDX-Datei (Host Integration Designer XML), die jetzt die Methodendefinitionen enthält, die zum Erstellen und Ausführen einer Aktion für den IBM 3270-Connector verwendet werden sollen.

1. Wählen Sie auf der Symbolleiste des 3270-Designtools **Methods** (Methoden) aus, um in den Methodenmodus zu wechseln. 

1. Wählen Sie im Bereich **Navigation** den Bildschirm aus, der die gewünschten Eingabefelder enthält.

1. Um den ersten Eingabeparameter für Ihre Methode hinzuzufügen, gehen Sie folgendermaßen vor:

   1. Wählen Sie im Bereich **Capture** (Erfassen) des 3270-Emulatorbildschirms das ganze Feld aus, nicht nur den Text im Feld, das Sie als erste Eingabe verwenden möchten.

      > [!TIP]
      > Um alle Felder anzuzeigen und sicherzustellen, dass Sie das vollständige Feld auswählen, wählen Sie im Menü **View** (Ansicht) **All Fields** (Alle Felder) aus.

   1. Wählen Sie auf der Symbolleiste des Designtools **Input Field** (Eingabefeld) aus. 

   Um weitere Eingabeparameter hinzuzufügen, wiederholen Sie die vorherigen Schritte für jeden Parameter.

1. Um den ersten Ausgabeparameter für Ihre Methode hinzuzufügen, gehen Sie folgendermaßen vor:

   1. Wählen Sie im Bereich **Capture** (Erfassen) des 3270-Emulatorbildschirms das ganze Feld aus, nicht nur den Text im Feld, das Sie als erste Ausgabe verwenden möchten.

      > [!TIP]
      > Um alle Felder anzuzeigen und sicherzustellen, dass Sie das vollständige Feld auswählen, wählen Sie im Menü **View** (Ansicht) **All Fields** (Alle Felder) aus.

   1. Wählen Sie auf der Symbolleiste des Designtools **Output Field** (Ausgabefeld) aus.

   Um weitere Ausgabeparameter hinzuzufügen, wiederholen Sie die vorherigen Schritte für jeden Parameter.

1. Nachdem Sie alle Parameter Ihrer Methode hinzugefügt haben, definieren Sie diese Eigenschaften für jeden Parameter:

   | Eigenschaftenname | Mögliche Werte | 
   |---------------|-----------------|
   | **Datentyp** | Byte, Date Time, Decimal, Int, Long, Short, String |
   | **Field Fill Technique** (Feldauffüllmethode) | Parameter unterstützen folgende Auffülltypen, wobei erforderlichenfalls mit Leerzeichen aufgefüllt wird: <p><p>- **Type**: Zeichen sequenziell in das Feld eingeben. <p>- **Fill**: Den Inhalt des Felds durch Zeichen ersetzen, wobei erforderlichenfalls mit Leerzeichen aufgefüllt wird. <p>- **EraseEofType**: Das Feld löschen und dann Zeichen sequenziell in das Feld eingeben. |
   | **Format String** (Formatzeichenfolge) | Manche Parameterdatentypen verwenden eine Formatzeichenfolge, die dem 3270-Connector mitteilt, wie Text aus dem Bildschirm in einen .NET-Datentyp zu konvertieren ist: <p><p>- **DateTime**: Die Formatzeichenfolge „DateTime“ richtet sich nach den [benutzerdefinierten .NET-Formatzeichenfolgen für Datum und Uhrzeit](/dotnet/standard/base-types/custom-date-and-time-format-strings). Z. B. verwendet das Datum `06/30/2019` die Formatzeichenfolge `MM/dd/yyyy`. <p>- **Decimal**: Die Formatzeichenfolge „Decimal“ verwendet die [COBOL Picture-Klausel](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm). Z. B. verwendet die Zahl `100.35` die Formatzeichenfolge `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Speichern und Anzeigen von Metadaten

Nachdem Sie Ihre Methode definiert haben, aber bevor Sie Ihre Methode testen, speichern Sie alle Informationen, die Sie bisher definiert haben, in einer RAP-Datei (.rap).
Sie können in jedem Modus jederzeit in diese RAP-Datei speichern. Das Designtool umfasst außerdem eine RAP-Beispieldatei, die Sie öffnen und sich ansehen können, indem Sie an dieser Stelle zum Installationsordner des Designtools wechseln und die Datei „WoodgroveBank.rap“ öffnen:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Wenn Sie jedoch versuchen, Änderungen an der RAP-Beispieldatei zu speichern oder eine HIDX-Datei aus der RAP-Beispieldatei zu generieren, während die Datei im Installationsordner des Designtools verbleibt, erhalten Sie eventuell einen Fehler „Zugriff verweigert“. Standardmäßig wird das Designtool in Ihrem Ordner „Programme“ ohne erhöhte Rechte installiert. Wenn eine Fehlermeldung angezeigt wird, probieren Sie eine dieser Lösungen aus:

* Kopieren Sie die Beispieldatei an einen anderen Speicherort.
* Führen Sie das Designtool als Administrator aus.
* Machen Sie sich selbst zum Besitzer des SDK-Ordners.

## <a name="test-your-method"></a>Testen Ihrer Methode

1. Um Ihre Methode auf dem Livehost auszuführen, während Sie sich noch im Methodenmodus befinden, drücken Sie die F5-Taste, oder wählen Sie auf der Symbolleiste des Designtools **Test** aus.

   > [!TIP]
   > Sie können den Modus jederzeit wechseln. Wählen Sie im Menü **File** (Datei) den Eintrag **Mode** (Modus) und anschließend den gewünschten Modus aus.

1. Geben Sie die Werte Ihrer Parameter ein, und wählen Sie **OK** aus.

1. Um mit dem nächsten Bildschirm fortzufahren, wählen Sie **Next** (Weiter) aus.

1. Wenn Sie fertig sind, wählen Sie **Done** (Fertig) aus, woraufhin Ihre Ausgabeparameterwerte angezeigt werden.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generieren und Hochladen der HIDX-Datei

Wenn Sie fertig sind, generieren Sie die HIDX-Datei, damit Sie sie in Ihr Integrationskonto hochladen können. Das 3270-Designtool erstellt die HIDX-Datei in einem neuen Unterordner, in dem Sie Ihre RAP-Datei gespeichert haben.

1. Wählen Sie im 3270-Designtool im Menü **Tools** (Extras) die Option **Generate Definitions** (Definitionen generieren) aus. (Tastatur: F6)

1. Wechseln Sie zu dem Ordner, der Ihre RAP-Datei enthält, und öffnen Sie den Unterordner, den das Tool nach dem Generieren Ihrer HIDX-Datei erstellt hat. Vergewissern Sie sich, dass das Tool die HIDX-Datei erstellt hat.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie Ihr Integrationskonto.

1. Fügen Sie Ihre HIDX-Datei als Zuordnung zu Ihrem Integrationskonto hinzu, indem Sie [diese ähnlichen Schritte zum Hinzufügen von Zuordnungen befolgen](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), wobei Sie aber beim Auswählen des Zuordnungstyps **HIDX** auswählen.

Weiter unten in diesem Thema, wenn Sie Ihrer Logik-App zum ersten Mal eine IBM 3270-Aktion hinzufügen, werden Sie aufgefordert, eine Verbindung zwischen Ihrer Logik-App und dem Hostserver herzustellen, indem Sie die Verbindungsinformationen bereitstellen, z. B. die Namen für Ihr Integrationskonto und den Hostserver. Nachdem Sie die Verbindung erstellt haben, können Sie Ihre zuvor hinzugefügte HIDX-Datei, die auszuführende Methode und die zu verwendenden Parameter auswählen.

Wenn Sie alle diese Schritte abgeschlossen haben, können Sie die Aktion, die Sie in Ihrer Logik-App erstellen, verwenden, um sich mit Ihrem IBM Mainframe zu verbinden, Bildschirme für Ihre App zu steuern, Daten einzugeben, Ergebnisse zurückzugeben und so weiter. Sie können Ihrer Logik-App außerdem noch weitere Aktionen für die Integration in andere Apps, Dienste und Systeme hinzufügen.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Ausführen von IBM 3270-Aktionen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Wählen Sie im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** **>** **Aktion hinzufügen** aus. 

1. Wählen Sie unter dem Suchfeld die Option **Enterprise** aus. Geben Sie im Suchfeld den Begriff `3270` als Filter ein. Wählen Sie in der Aktionsliste die Aktion **Führt ein Mainframeprogramm über eine TN3270-Verbindung aus** aus.

   ![Auswählen einer 3270-Aktion](./media/connectors-create-api-3270/select-3270-action.png)

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Wenn noch keine Verbindung besteht, geben Sie die erforderlichen Informationen für Ihre Verbindung an, und wählen Sie **Erstellen** aus.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Verbindungsname** | Ja | <*connection-name*> | Name der Verbindung |
   | **ID des Integrationskontos** | Ja | <*integration-account-name*> | Name Ihres Integrationskontos |
   | **SAS-URL des Integrationskontos** | Ja | <*integration-account-SAS-URL*> | Die SAS-URL (Shared Access Signature) Ihres Integrationskontos, die Sie aus den Einstellungen Ihres Integrationskontos im Azure-Portal generieren können. <p>1. Wählen Sie in Ihrem Integrationskontomenü unter **Einstellungen** den Eintrag **Rückruf-URL** aus. <br>2. Kopieren Sie im rechten Bereich den Wert von **Generierte Rückruf-URL**. |
   | **Server** | Ja | <*TN3270-server-name*> | Der Servernamen für Ihren TN3270-Dienst |
   | **Port** | Nein | <*TN3270-server-port*> | Der von Ihrem TN3270-Server verwendete Port. Wird das Feld nicht ausgefüllt, verwendet der Connector `23` als Standardwert. |
   | **Gerätetyp** | Nein | <*IBM-terminal-model*> | Der Name oder die Nummer des Modells für das zu emulierende IBM-Terminal. Wird das Feld nicht ausgefüllt, verwendet der Connector Standardwerte. |
   | **Codepage** | Nein | <*code-page-number*> | Die Codepagenummer für den Host. Wird das Feld nicht ausgefüllt, verwendet der Connector `37` als Standardwert. |
   | **Name der logischen Einheit** | Nein | <*logical-unit-name*> | Der Name der spezifischen logischen Einheit, die vom Host angefordert werden soll. |
   | **SSL aktivieren?** | Nein | „Ein“ oder „Aus“ | Aktivieren oder Deaktivieren der TLS-Verschlüsselung |
   | **SSL-Zertifikat des Hosts überprüfen?** | Nein | „Ein“ oder „Aus“ | Aktivieren oder deaktivieren Sie die Überprüfung des Serverzertifikats. |
   ||||

   Beispiel:

   ![Verbindungseigenschaften](./media/connectors-create-api-3270/connection-properties.png)

1. Geben Sie die erforderlichen Informationen für die Aktion ein:

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **HIDX-Name** | Ja | <*HIDX-file-name*> | Wählen Sie die zu verwendende 3270 HIDX-Datei aus. |
   | **Methodenname** | Ja | <*method-name*> | Wählen Sie die Methode in der HIDX-Datei aus, die Sie verwenden möchten. Nachdem Sie eine Methode ausgewählt haben, wird die Liste **Neuen Parameter hinzufügen** angezeigt, sodass Sie Parameter auswählen können, die mit dieser Methode verwendet werden sollen. |
   ||||

   Beispiel:

   **HIDX-Datei auswählen**

   ![Wählen Sie die HIDX-Datei aus.](./media/connectors-create-api-3270/select-hidx-file.png)

   **Methode auswählen**

   ![Wählen Sie die Methode aus.](./media/connectors-create-api-3270/select-method.png)

   **Parameter auswählen**

   ![Wählen Sie die Parameter aus.](./media/connectors-create-api-3270/add-parameters.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App und führen diese aus.

   Nachdem die Ausführung Ihrer Logik-App abgeschlossen wurde, werden die Schritte der Ausführung angezeigt. 
   Erfolgreiche Schritte weisen Häkchen auf, während nicht erfolgreiche Schritte den Buchstaben „X“ aufweisen.

1. Um die Eingaben und Ausgaben für jeden einzelnen Schritt anzuzeigen, erweitern Sie den jeweiligen Schritt.

1. Um die Ausgaben zu überprüfen, wählen Sie **Unformatierte Ausgaben anzeigen** aus.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/si3270/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)

