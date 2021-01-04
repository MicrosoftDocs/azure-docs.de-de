---
title: Überprüfen und Veröffentlichen eines Angebots im kommerziellen Microsoft-Marketplace
description: Verwenden Sie das Partner Center, um Ihr Angebot für die Vorschau zu übermitteln, eine Vorschau Ihres Angebots anzuzeigen und es dann im kommerziellen Microsoft-Marketplace zu veröffentlichen.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/27/2020
ms.openlocfilehash: b038834df3889a8b4b7f4a749568635e99f8408e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188750"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Überprüfen und Veröffentlichen eines Angebots im kommerziellen Marketplace

In diesem Artikel erfahren Sie, wie Sie mithilfe des Partner Centers Ihr Angebot für die Veröffentlichung übermitteln, eine Vorschau Ihres Angebots anzeigen und es dann im kommerziellen Marketplace veröffentlichen. Außerdem wird erläutert, wie Sie den Veröffentlichungsstatus überprüfen, während das Angebot die Veröffentlichungsschritte durchläuft. Sie müssen bereits ein Angebot erstellt haben, das Sie veröffentlichen möchten.

## <a name="offer-status"></a>Angebotsstatus

Sie können den Status Ihres Angebots im [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) im Dashboard des kommerziellen Marketplace auf der Registerkarte **Übersicht** überprüfen. In der Spalte **Status** wird für jedes Angebot einer der folgenden Statuswerte angezeigt.

| Status | BESCHREIBUNG |
| ------------ | ------------- |
| Entwurf | Angebot wurde erstellt, wird aber nicht veröffentlicht. |
| Veröffentlichung wird durchgeführt | Das Angebot durchläuft den Veröffentlichungsvorgang. |
| Eingreifen erforderlich | Wir haben während der Zertifizierung oder einer anderen Veröffentlichungsphase ein kritisches Problem festgestellt. |
| Vorschau | Wir haben das Angebot zertifiziert. Nun muss es abschließend durch den Herausgeber verifiziert werden. Wählen Sie **Live schalten** aus, um das Angebot live zu veröffentlichen. |
| Live | Das Angebot ist im Marketplace live geschaltet und kann von Kunden angezeigt und erworben werden. |
| Ausstehender Verkaufsstopp | Der Herausgeber hat „Verkaufsstopp“ für ein Angebot oder einen Plan ausgewählt, aber die Aktion ist noch nicht abgeschlossen. |
| Im Marketplace nicht verfügbar | Ein zuvor im Marketplace veröffentlichtes Angebot wurde entfernt. |
|||

## <a name="validation-and-publishing-steps"></a>Schritte für die Validierung und Veröffentlichung

Wenn Sie bereit sind, ein Angebot für die Veröffentlichung zu übermitteln, wählen Sie oben rechts im Portal **Überprüfen und veröffentlichen** aus. Auf der Seite **Überprüfen und veröffentlichen** wird der Status jeder Seite für Ihr Angebot angezeigt. Folgende Werte sind möglich:

   - **Nicht gestartet**: Die Seite ist unvollständig.
   - **Unvollständig**: Auf der Seite fehlen erforderliche Informationen, oder sie enthält Fehler, die korrigiert werden müssen. Sie müssen zur Seite zurückkehren und diese aktualisieren.
   - **Abgeschlossen**: Die Seite ist vollständig. Alle erforderlichen Daten wurden angegeben, und es liegen keine Fehler vor.

Wenn eine der Seiten einen anderen Status als **Abgeschlossen** aufweist, müssen Sie das Problem auf dieser Seite beheben und dann zur Seite **Überprüfen und veröffentlichen** zurückkehren, um sich zu vergewissern, dass der Status nun **Abgeschlossen** lautet. Einige Angebotstypen müssen getestet werden. In diesem Fall wird ein Feld **Hinweise zur Zertifizierung** angezeigt, in dem Sie Testanweisungen für das Zertifizierungsteam und ergänzende Hinweise angeben müssen, die das Verständnis Ihrer App erleichtern.

Wenn alle Seiten vollständig sind und Sie die geeigneten Testhinweise eingegeben haben, wählen Sie **Veröffentlichen** aus, um die Validierungs- und Veröffentlichungsprozesse zu starten. Die Phasen und der gesamte Ablauf können je nach veröffentlichtem Angebotstyp variieren. In der folgenden Tabelle ist ein möglicher Veröffentlichungsablauf dargestellt. Die einzelnen Phasen werden in den folgenden Abschnitten ausführlicher erläutert.

| Phase | Ablauf |
| ------------ | ------------- | ------------- |
| [Automatisierte Validierung](#automated-validation-phase) | Wir verarbeiten eine Reihe automatisierter Validierungen. |
| [Zertifizierung](#certification-phase) | Wir führen manuelle Validierungen durch. |
| [Erstellung einer Vorschau](#preview-creation-phase) | Die Listungsseite für Ihre Angebotsvorschau steht allen zur Verfügung, die über den Vorschaulink verfügen. Wenn Ihr Angebot über Microsoft vertrieben wird (also transaktionsfähig ist), kann nur die auf der Seite **Vorschauzielgruppe** Ihres Angebots angegebene Zielgruppe das Angebot erwerben und zu Testzwecken darauf zugreifen. |
| [Abzeichnung des Verlegers](#publisher-sign-off-phase) | Wir senden Ihnen eine E-Mail mit der Aufforderung, Ihr Angebot in der Vorschau zu überprüfen und zu genehmigen. |
| [Veröffentlichen](#publish-phase) | Wir stellen anhand einiger Schritte sicher, dass das Vorschauangebot im kommerziellen Marketplace live veröffentlicht wird. |
|||

## <a name="automated-validation-phase"></a>Phase „Automatisierte Validierung“

Der erste Schritt im Veröffentlichungsprozess sieht eine Reihe automatisierter Validierungen vor. Jeder Validierungsschritt entspricht einer Funktion, die Sie bei der Erstellung des Angebots ausgewählt haben. Jede Validierungsprüfung muss abgeschlossen sein, bevor das Angebot zum nächsten Schritt im Veröffentlichungsprozess übergehen kann.

- **Einrichten des Flows zum Kauf eines Angebots** (<10 Min.)

   Wir stellen sicher, dass Ihr Angebot erfüllt werden kann, nachdem es von Kunden im Azure-Portal erworben wurde. Dieser Schritt gilt nur für Angebote, die über Microsoft verkauft werden.

- **Validierung der Testversionsdaten** (ca. 5 Min.)

   Wir überprüfen die Daten, die Sie auf der Seite „Technische Konfiguration“ des Angebots angegeben haben. Die Funktionalität der Testversion wird überprüft und genehmigt. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Bereitstellung der Testversion** (ca. 30 Min.)

    Nach der Validierung der Daten und Funktionalität Ihrer Testversion im vorherigen Schritt werden wir Instanzen Ihrer Testversion bereitstellen und replizieren, damit sie von Kunden verwendet werden können. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Überprüfung der Leadverwaltung und Registrierung** (>15 Min.)

    Wir stellen sicher, dass Ihr Leadverwaltungssystem Kundenleads basierend auf Ihren Angaben auf der Seite **Angebotseinrichtung** empfangen kann. Dieser Schritt gilt nur für Angebote mit aktivierter Leadverwaltung.

## <a name="certification-phase"></a>Phase „Zertifizierung“

Vor der Veröffentlichung müssen Angebote, die an den kommerziellen Marketplace übermittelt werden, zertifiziert werden. Angebote werden strengen Tests unterzogen, die teils automatisch und teils manuell erfolgen. Weitere Informationen finden Sie in den [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen der während der Zertifizierung erfolgenden Validierung

Für jedes übermittelte Angebot gibt es im Zertifizierungsprozess drei Validierungsstufen.

- Eignung des Herausgebers
- Validierung von Inhalten
- Technische Validierung

#### <a name="publisher-business-eligibility"></a>Eignung des Herausgebers

Bei jedem Angebotstyp wird eine Reihe von Standardberechtigungskriterien geprüft. Zu diesen Kriterien können der MPN-Status des Herausgebers, die erworbenen Kompetenzen, die Kompetenzebenen usw. gehören.

#### <a name="content-validation"></a>Validierung von Inhalten

Die bei der Erstellung Ihres Angebots eingegebenen Informationen werden auf Qualität und Relevanz geprüft. Bei diesen Prüfungen werden Ihre Eingaben für die Listungsdetails für Marketplace, Preise, Verfügbarkeit, zugehörige Pläne usw. geprüft. Damit Ihr Angebot die Listungskriterien von Microsoft AppSource und Azure Marketplace erfüllt, wird es auf folgende Voraussetzungen überprüft:

- Titel, der das Angebot genau beschreibt
- Verständlich formulierte Beschreibungen mit umfassender Übersicht und einem Wertversprechen
- Qualitativ hochwertige Screenshots und Videos
- Erläuterung, wie das Angebot Microsoft-Plattformen und -Tools nutzt

Erfahren Sie im Artikel zu den [allgemeinen Richtlinien für die Listung](/legal/marketplace/certification-policies#100-general) mehr zu den Kriterien für die Validierung von Inhalten.

#### <a name="technical-validation"></a>Technische Validierung

Bei der technischen Validierung wird das Angebot (Paket oder Binärdatei) den folgenden Prüfungen unterzogen.

- Prüfung auf Schadsoftware
- Überwachung von Netzwerkaufrufen
- Paketanalyse
- Umfassende Überprüfung der Funktionalität des Angebots

Das Angebot wird auf verschiedenen Plattformen und mit verschiedenen Versionen getestet, um eine stabile Ausführung sicherzustellen.

### <a name="certification-failure-report"></a>Zertifizierungsfehlerbericht

Wenn Ihr Angebot eine der Listungs-, technischen oder Richtlinienprüfungen nicht besteht oder Sie nicht berechtigt sind, ein solches Angebot zu übermitteln, erhalten Sie per E-Mail einen Bericht mit Zertifizierungsfehlern.

Dieser Bericht enthält Beschreibungen aller Richtlinien, gegen die verstoßen wurde, sowie Prüfhinweise. Überprüfen Sie diesen E-Mail-Bericht, beheben Sie mögliche Probleme, aktualisieren Sie Ihr Angebot bei Bedarf, und reichen Sie es über das [Portal des kommerziellen Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) erneut im Partner Center ein. Sie können das Angebot bis zum Bestehen der Zertifizierung beliebig oft erneut einreichen.

## <a name="preview-creation-phase"></a>Phase „Erstellung einer Vorschau“

Während der Phase „Erstellung einer Vorschau“ erstellen wir eine Version Ihres Angebots, die nur für die Zielgruppe zugänglich ist, die Sie ggf. auf der Seite **Vorschauzielgruppe** Ihres Angebots angegeben haben. Personen, die nicht der Vorschauzielgruppe angehören, können erst auf die Vorschauversion Ihres Angebots zugreifen, nachdem Sie das Angebot live veröffentlicht haben.

> [!NOTE]
> Die Vorschauzielgruppe sollte nicht dazu dienen, um Personen außerhalb Ihres Unternehmens Einblick in ein Angebot zu geben. Verwenden Sie stattdessen die Option „Privates Angebot“. An diesem Punkt wurde ihr Angebot noch nicht vollständig getestet und überprüft und ist nicht für die externe Verteilung bereit.

## <a name="cancel-publishing"></a>Stornieren der Veröffentlichung

So stornieren Sie ein Angebot mit dem Status **Publish in progress** (Veröffentlichung in Bearbeitung)

1. Wählen Sie den Angebotsnamen aus, um die Seite **Offer overview** (Angebotsübersicht) zu öffnen.
1. Klicken Sie rechts oben auf der Seite auf **Cancel publish** (Veröffentlichen abbrechen).
1. Bestätigen Sie, dass Sie die Veröffentlichung des Angebots stornieren möchten.

Wenn Sie das Angebot zu einem späteren Zeitpunkt veröffentlichen möchten, müssen Sie den Veröffentlichungsprozess neu starten.

> [!NOTE]
> Sie können die Veröffentlichung eines Angebots nur dann verhindern, wenn das Angebot noch nicht durch den Herausgeber freigegeben wurde. Nach Auswahl von **Live schalten** kann die Veröffentlichung nicht mehr abgebrochen werden.

## <a name="publisher-sign-off-phase"></a>Phase „Einholen der Genehmigung des Herausgebers“

Wenn das Angebot für Ihre Prüfung und Genehmigung bereit ist, senden wir Ihnen eine E-Mail mit der Bitte, Ihre Angebotsvorschau zu prüfen und zu genehmigen. Sie können auch die Seite **Angebotsübersicht** in Ihrem Browser aktualisieren, um festzustellen, ob Ihr Angebot die Phase „Einholen der Genehmigung des Herausgebers“ erreicht hat. Wenn dies der Fall ist, sind die Schaltfläche **Live schalten** und Vorschaulinks verfügbar.

Im folgenden Screenshot ist die Seite **Angebotsübersicht** für ein SaaS-Angebot zu sehen. Die auf dieser Seite angezeigten Validierungsschritte variieren je nach Angebotstyp und der Auswahl, die Sie beim Erstellen des Angebots getroffen haben.

![Veranschaulicht die Seite „Angebotsübersicht“ für ein Angebot im Partner Center. Die Schaltfläche „Live schalten“ und Vorschaulinks sind dargestellt.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Vorschau und Genehmigung Ihres Angebots

> [!IMPORTANT]
> Um den gesamten Flow vom Kauf bis zur Einrichtung zu überprüfen, erwerben Sie Ihr Angebot, während es sich in der Vorschau befindet. Benachrichtigen Sie zunächst Microsoft mit einem [Supportticket](https://aka.ms/marketplacesupport), um sicherzustellen, dass wir keine Gebühren erheben.

Auf der Seite **Angebotsübersicht** werden unter der Schaltfläche **Live schalten** Vorschaulinks angezeigt. Abhängig von den Optionen, die Sie beim Erstellen Ihres Angebots ausgewählt haben, ist ein Link für die AppSource-Vorschau, die Azure Marketplace-Vorschau oder beide verfügbar. Wenn Sie sich für den Verkauf Ihres Angebots über Microsoft entschieden haben, können alle Mitglieder der Vorschauzielgruppe den Kauf und die Bereitstellung Ihres Angebots testen, um sicherzustellen, dass es Ihre Anforderungen in dieser Phase erfüllt.

Nachdem Sie Ihre Vorschau genehmigt haben, wählen Sie **Live schalten** aus, um Ihr Angebot live im kommerziellen Marketplace zu veröffentlichen. 

Wenn Sie nach der Vorschauphase Änderungen am Angebot vornehmen möchten, können Sie es bearbeiten und die Veröffentlichungsanforderung erneut übermitteln. Wenn Ihr Angebot bereits live geschaltet und im Marketplace für die Öffentlichkeit verfügbar ist, werden alle von Ihnen vorgenommenen Aktualisierungen erst dann live geschaltet, wenn Sie *Live schalten* auswählen. Weitere Informationen finden Sie unter [Aktualisieren eines bestehenden Angebots im kommerziellen Marketplace](partner-center-portal/update-existing-offer.md).

## <a name="publish-phase"></a>Phase „Veröffentlichung“

Nachdem Sie sich entschieden haben, Ihr Angebot live zu schalten und im kommerziellen Marketplace verfügbar zu machen, stellen wir mit einer Reihe abschließender Validierungsprüfungen sicher, dass das Liveangebot auf dieselbe Weise wie die Vorschauversion des Angebots konfiguriert ist.

- **Einrichten des Flows zum Kauf eines Angebots** (<10 Min.)

    Wir stellen sicher, dass Ihr Angebot erfüllt werden kann, nachdem es von Kunden im Azure-Portal erworben wurde. Dieser Schritt gilt nur für Angebote, die über Microsoft verkauft werden.

- **Validierung der Testversionsdaten** (ca. 5 Min.)

    Wir überprüfen die Daten, die Sie auf der Seite „Technische Konfiguration“ des Angebots angegeben haben. Die Funktionalität der Testversion wird überprüft und genehmigt. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Bereitstellung der Testversion** (ca. 30 Min.)

    Wir stellen Instanzen Ihrer Testversion bereit und replizieren diese, damit sie von Kunden verwendet werden können. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Überprüfung der Leadverwaltung und Registrierung** (>15 Min.)

    Wir stellen sicher, dass Ihr Leadverwaltungssystem Kundenleads basierend auf Ihren Angaben auf der Seite **Angebotseinrichtung** empfangen kann. Dieser Schritt gilt nur für Angebote mit aktivierter Leadverwaltung.

- **Endgültige Veröffentlichung (>30 Min.)**

    Wir sorgen dafür, dass Ihr Angebot im Marketplace öffentlich verfügbar ist.

Nachdem die Validierungsprüfungen abgeschlossen wurden, wird Ihr Angebot im Marketplace live geschaltet.

## <a name="publishing-history"></a>Veröffentlichungsverlauf

Auf der Seite **Verlauf** im Partner Center werden die Veröffentlichungsereignisse für Ihre Angebote im kommerziellen Marketplace angezeigt. Die Seite zeigt für jedes Ereignis den Benutzer, der die Aktion initiiert hat, den Ereignistyp sowie das Datum und die Uhrzeit des Ereignisses an. Die [Schritte für die Validierung und Veröffentlichung](#validation-and-publishing-steps) werden mit dem Datum und der Uhrzeit des Abschlusses aufgelistet.

Zum Anzeigen des Verlaufs Ihres Angebots gehen Sie folgendermaßen vor:

1.    Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2.    Wählen Sie im Navigationsmenü auf der linken Seite **Kommerzieller Marketplace** > **Übersicht** aus.
3.    Wählen Sie Ihr Angebot aus.
4.    Wählen Sie im Navigationsmenü auf der linken Seite die Registerkarte **Verlauf** aus. Die Seite enthält Details zu den folgenden Ereignissen, die für Ihr Angebot zutreffen:

|Ereignis    |Beschreibung    |
|---------|---------------|
|Angebot erstellt    |Das Angebot wurde im Partner Center erstellt. Ein Benutzer hat den Angebotstyp, die Angebots-ID und den Angebotsalias in **Kommerzieller Marketplace** > **Übersicht** ausgewählt.    |
|Plan erstellt: *Planname*    |Ein Benutzer hat einen neuen Plan erstellt, indem er die Plan-ID und den Plannamen auf der Registerkarte **Planübersicht** eingegeben hat.</br>*Dieses Ereignis trifft nur für Angebotstypen zu, die Pläne unterstützen*.    |
|Plan gelöscht    |Ein Benutzer hat einen nicht veröffentlichten Planentwurf gelöscht, indem er auf der Seite **Planübersicht** die Option **Entwurf löschen** ausgewählt hat.</br>*Dieses Ereignis trifft nur für Angebotstypen zu, die Pläne unterstützen*.    |
|Einstellung des Verkaufs für den Plan initiiert: *Planname*    |Ein Benutzer hat die Einstellung des Verkaufs für den Plan initiiert, indem er auf der Seite **Planübersicht** die Option **Verkauf beenden** ausgewählt hat.</br>*Dieses Ereignis trifft nur für Angebotstypen zu, die Pläne unterstützen*.    |
|Einstellung des Verkaufs für den Plan rückgängig gemacht: *Planname*    |Ein Benutzer hat die Einstellung des Verkaufs für den Plan abgebrochen, indem er auf der Seite **Planübersicht** die Option **Verkaufsstopp rückgängig machen** ausgewählt hat.</br>*Dieses Ereignis trifft nur für Angebotstypen zu, die Pläne unterstützen*.    |
|Angebot für die Vorschau übermittelt    |Ein Benutzer hat das Angebot für die Vorschau übermittelt, indem er auf der Seite **Überprüfen und veröffentlichen** die Option **Veröffentlichen** ausgewählt hat.    |
|Abbruch der Übermittlung für die Vorschau initiiert    |Ein Benutzer hat einen Abbruch der Angebotsveröffentlichung für die Vorschau angefordert, indem er nach der Übermittlung für die Vorschau auf der Seite **Angebotsübersicht** die Option **Veröffentlichung abbrechen** ausgewählt hat.</br>*Dieses Ereignis wird angezeigt, während die Abbruchanforderung verarbeitet wird*.    |
|Übermittlung für die Vorschau abgebrochen    |Ein Benutzer hat die Angebotsveröffentlichung für die Vorschau abgebrochen, indem er nach der Übermittlung für die Vorschau auf der Seite **Angebotsübersicht** die Option **Veröffentlichung abbrechen** ausgewählt hat.</br>*Dieses Ereignis wird angezeigt, nachdem die Abbruchanforderung erfolgreich verarbeitet wurde*.    |
|Liveschaltung freigegeben    |Ein Benutzer hat das Angebot im kommerziellen Marketplace veröffentlicht, indem er auf der Seite **Angebotsübersicht** die Option **Live schalten** ausgewählt hat.    |
|Abbruch der Veröffentlichung im Marketplace initiiert    |Ein Benutzer hat einen Abbruch der Angebotsveröffentlichung angefordert, indem er nach Freigabe der Liveschaltung auf der Seite **Angebotsübersicht** die Option **Veröffentlichung abbrechen** ausgewählt hat.</br>*Dieses Ereignis wird angezeigt, während die Abbruchanforderung verarbeitet wird*.    |
|Veröffentlichung im kommerziellen Marketplace abgebrochen    |Ein Benutzer hat die Angebotsveröffentlichung abgebrochen, indem er nach Freigabe der Liveschaltung auf der Seite **Angebotsübersicht** die Option **Veröffentlichung abbrechen** ausgewählt hat.</br>*Dieses Ereignis wird angezeigt, nachdem die Abbruchanforderung erfolgreich verarbeitet wurde*.    |
|Private Zielgruppe synchronisiert    |Ein Benutzer hat die private Zielgruppe aktualisiert und synchronisiert, indem er auf der Seite **Planübersicht** oder der Seite **Preise und Verfügbarkeit des Plans** die Option **Private Zielgruppe synchronisieren** ausgewählt hat.</br>*Dieses Ereignis trifft nur für Angebotstypen zu, die private Pläne unterstützen*.    |
|Verkauf des Angebots eingestellt    |Ein Benutzer hat den Verkauf des Angebots eingestellt, indem er auf der Seite **Angebotsübersicht** die Option **Verkauf beenden** ausgewählt hat.    |

> [!NOTE]
> Die Seite „Verlauf“ gibt nicht an, wann ein Angebotsentwurf gespeichert wurde.

### <a name="filter-options"></a>Filteroptionen

Mithilfe von Filtern können Sie den vollständigen Verlauf Ihres Angebots auf bestimmte Veröffentlichungsereignisse eingrenzen:

1.    Wählen Sie die Filterschaltfläche oben rechts auf der Seite aus.
2.    Wählen Sie einen Filter und dann **Anwenden** aus, um zu sehen, welche Verlaufsereignisse den von Ihnen ausgewählten Kriterien entsprechen.
3.    Wählen Sie **Filter löschen** aus, um zum vollständigen Verlauf Ihres Angebots zurückzukehren.

Es gibt vier Filter:
* Ereignisse
* Benutzer
* Date
* Seiten

Wenn Sie den Filter **Seiten** auswählen, können Sie beliebige Partner Center-Seiten auswählen, die für Ihren Angebotstyp zutreffen. Bei Verwendung des Filters **Seiten** werden alle Ereignisse vom Typ **Angebot für die Vorschau übermittelt** mit Änderungen an der von Ihnen ausgewählten Seite angezeigt.

* Bei allen Angeboten wird die Seite **Angebotseinrichtung** standardmäßig für jedes Übermittlungsereignis eingeschlossen.
* Bei Angeboten, die Pläne unterstützen, wird die Seite **Planübersicht** für jedes Übermittlungsereignis eingeschlossen.
* Bei Angeboten, die Testversionen unterstützen, wird die Seite **Testversion** für jedes Übermittlungsereignis eingeschlossen.

### <a name="users"></a>Benutzer

Wenn ein Ereignis von einem Benutzer initiiert wurde, wird der Benutzer auf der Seite „Verlauf“ entsprechend den folgenden Szenarien angezeigt:

#### <a name="the-event-was-initiated-by-the-publisher"></a>Ereignis wurde vom Herausgeber initiiert

Der Name von Benutzern mit Veröffentlichungsberechtigungen für ein Angebot wird für die von ihnen initiierten Veröffentlichungsereignisse angezeigt.

[![Anzeige des Namens von Benutzern mit Veröffentlichungsberechtigungen auf der Seite „Verlauf“](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>Ereignis wurde von Microsoft initiiert

Sie können Microsoft-Administratoren Berechtigungen erteilen, um Aktionen für Sie zu initiieren oder nach einem unerwarteten Systemfehler Korrekturmaßnahmen zu ergreifen. Der Name und das Logo von Microsoft werden für Veröffentlichungsereignisse angezeigt, die von Microsoft für Ihr Konto initiiert wurden.

[![Beispiel für die Anzeige von Ereignissen, die von Microsoft initiiert wurden, auf der Seite „Verlauf“](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>Ereignis wurde von einem nicht identifizierten Benutzer initiiert

Bei Benutzern, die keinem Konto mehr zugeordnet sind, wird der Name aus der Spalte **Initiiert von** entfernt, nachdem die Veröffentlichungsberechtigungen aufgehoben wurden.

[![Beispiel für die Anzeige von Ereignissen, die von nicht identifizierten Benutzern initiiert wurden, auf der Seite „Verlauf“](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Schritte für die Validierung und Veröffentlichung

Für Systemprozesse, die den [Schritten für die Validierung und Veröffentlichung](#validation-and-publishing-steps) entsprechen, wird kein Benutzer angezeigt. Diese Ereignisse sind entsprechend ihrem Abschlussstatus farbcodiert.

[![Beispiel für die Anzeige von Schritten für die Validierung und Veröffentlichung auf der Seite „Verlauf“](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

[Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](partner-center-portal/analytics.md)