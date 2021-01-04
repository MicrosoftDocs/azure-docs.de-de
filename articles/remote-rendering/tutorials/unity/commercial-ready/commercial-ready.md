---
title: Erstellen einer Azure Remote Rendering-Anwendung für gewerbliche Zwecke
description: Strategien und Überlegungen zum Erstellen einer Azure Remote Rendering-Anwendung für gewerbliche Zwecke
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 7258b37c9d92f25227eac678dde77963e644e64b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483316"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Tutorial: Erstellen einer Azure Remote Rendering-Anwendung für gewerbliche Zwecke

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Sitzungsverwaltung für kommerzielle Anwendungen
> * Nachverfolgen von Sitzungen für die Abrechnung
> * Optimieren der Benutzererfahrung in Bezug auf die Sitzungsladezeit
> * Überlegungen zur Netzwerklatenz

## <a name="prerequisites"></a>Voraussetzungen

* Dieses Tutorial baut auf dem [Tutorial zum Schützen von Azure Remote Rendering und Modellspeichern](../security/security.md) auf.

## <a name="introduction-to-commercial-readiness"></a>Einführung in Anwendungen für gewerbliche Zwecke

Azure Remote Rendering weitet die Möglichkeiten von Mixed Reality aus. Sobald die Grundlagen in Ihre Lösung integriert wurden, sind zahlreiche weitere Dinge zu bedenken, um sicherzustellen, dass Ihre Lösung sicher und skalierbar ist und einen Mehrwert liefert.

Dieses Modul beschreibt einige zusätzliche Funktionen, die Ihnen für Ihre kommerzielle Anwendung zur Verfügung stehen.

Eine umfassende Übersicht über die bewährten Methoden für eine systemübergreifende Architektur finden Sie unter:

* [Azure Architecture Center](/azure/architecture/)
* [Anleitung mit den ersten Schritten für Azure-Entwickler](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Analytics

Durch die Integration von Analysetools können Sie Ihre Lösung verwalten, nachverfolgen und optimieren.

Eine umfassende Liste der verfügbaren Analyseressourcen finden Sie unter:

* [Azure-Analysedienste](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Nachverfolgen der Nutzung für die Abrechnung

Das Nachverfolgen der Nutzung von Azure Remote Rendering durch mehrere interne Teams oder externe Clients ist ein wichtiger Aspekt, insbesondere in Situationen mit mehreren Mandanten.

Damit dies möglich ist, bietet Azure einen Dienst namens Ressourcenmarkierung, der die Nutzung des Azure Remote Rendering-Diensts den einzelnen Clients zuordnet.

Weitere Informationen zum Benennen und Markieren von Ressourcen finden Sie unter:

* [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

### <a name="diagnostics"></a>Diagnose

Leistungsstarke Tools wie die Ereignisablaufverfolgung für Windows (Event Tracing for Windows, ETW) und die Protokollierung der Ereignisablaufverfolgung (Event Trace Logging, ETL) vereinfachen das Generieren von Ablaufverfolgungsereignissen innerhalb Ihrer Anwendung und können die Diagnose von Netzwerk-, Inhaltserfassungs-, Sitzungs-, Anwendungs- und anderen Problemen erleichtern, die in einer kommerziellen Lösungsbereitstellung unter Umständen auftreten.

Weitere Informationen finden Sie unter:

* [Erstellen clientseitiger Leistungsüberwachungen](../../../how-tos/performance-tracing.md)
* [Vorgehensweise: Sammeln von Daten der Ereignisablaufverfolgung für Windows (ETW)](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Verwenden des Windows-Geräteportals: Protokollierung](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Nutzungsanalyse

Azure Application Insights liefert Ihnen Informationen dazu, wie die Benutzer Ihre Azure Remote Rendering-Anwendung nutzen. Bei jeder Aktualisierung Ihrer App können Sie beurteilen, wie gut sie für Benutzer funktioniert, und sie ggf. optimieren. Mit diesem Wissen können Sie datengesteuerte Entscheidungen über die nächsten Entwicklungszyklen treffen.

Weitere Informationen finden Sie unter:

* [Verwendungsanalyse mit Application Insights](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Strategien für schnelle Startzeiten

Ihr Anwendungsfall erfordert möglicherweise einen schnellen Start vom Anwendungsstart bis hin zur Anzeige von 3D-Modellen. Dies kann beispielsweise während einer wichtigen Besprechung der Fall sein, bei der bereits vorab alles einsatzbereit sein muss. Ein weiteres Beispiel ist die Überprüfung eines CAD-3D-Modells, bei dem eine schnelle Entwurfsiteration zwischen einer CAD-Anwendung und Mixed Reality von entscheidender Bedeutung ist, um die Effizienz sicherzustellen.

Azure Remote Rendering erfordert vorverarbeitete 3D-Modelle, und Azure benötigt derzeit einige Minuten, um eine Sitzung zu erstellen und ein Modell zum Rendern zu laden. Damit dieser Prozess so reibungslos und schnell wie möglich abläuft, ist es erforderlich, die Daten des 3D-Modells und die ARR-Sitzung vorab vorzubereiten.

Die hier genannten Vorschläge sind zurzeit nicht Standard bei Azure Remote Rendering, Sie können sie jedoch selbst implementieren, um kürzere Startzeiten zu erreichen.

### <a name="initiate-early"></a>Frühe Initialisierung

Die einfachste Option zum Verkürzen der Startzeit besteht darin, die Erstellung und Initialisierung der Sitzung so früh wie möglich im Benutzerworkflow durchzuführen. Eine Strategie besteht darin, die Sitzung zu initialisieren, sobald bekannt ist, dass eine ARR-Sitzung benötigt wird. Dies ist häufig der Fall, wenn der Benutzer mit dem Hochladen eines 3D-Modells in Azure Blob Storage beginnt, das mit Azure Remote Rendering verwendet werden soll. In dem Fall können Sitzungserstellung und -initialisierung gleichzeitig mit dem Hochladen des 3D-Modells initiiert werden, sodass beides parallel ausgeführt wird.

Dieser Prozess kann weiter optimiert werden, indem sichergestellt wird, dass sich die ausgewählten Azure Blob Storage-Eingabe- und Ausgabecontainer im gleichen regionalen Rechenzentrum wie die Azure Remote Rendering-Sitzung befinden.

### <a name="scheduling"></a>Scheduling

Wenn Sie wissen, dass Sie Azure Remote Rendering in Zukunft benötigen, können Sie ein bestimmtes Datum und eine bestimmte Uhrzeit für das Starten der Azure Remote Rendering-Sitzung festlegen.

Diese Option kann über ein Webportal angeboten werden, in dem Benutzer ein 3D-Modell hochladen und einen Zeitpunkt für die spätere Anzeige planen können. Dies ist auch ein guter Ausgangspunkt, um nach anderen Einstellungen wie [*Standard*](../../../reference/vm-sizes.md)- oder [*Premium*](../../../reference/vm-sizes.md)-Rendering zu fragen. Das *Premium*-Rendering ist möglicherweise geeignet, wenn eine Mischung aus Ressourcen angezeigt werden soll, bei der es schwieriger ist, die ideale Größe automatisch zu bestimmen, oder wenn sichergestellt werden muss, dass in der Azure-Region zu einem bestimmten Zeitpunkt virtuelle Computer verfügbar sind.

### <a name="session-pooling"></a>Sitzungspools

In äußerst anspruchsvollen Situationen sind Sitzungspools eine weitere Option, bei der immer eine oder mehrere Sitzungen erstellt und initialisiert werden. Dadurch wird ein Sitzungspool erstellt, der vom anfordernden Benutzer unmittelbar genutzt werden kann. Der Nachteil dieses Ansatzes besteht darin, dass die Abrechnung für den Dienst gleich nach der Initialisierung des virtuellen Computers beginnt. Es ist möglicherweise nicht kostengünstig, einen Sitzungspool durchgehend auszuführen. Basierend auf der Analyse ist es jedoch möglich, Spitzenauslastungen vorherzusagen, oder dieser Ansatz kann mit der obigen Planungsstrategie kombiniert werden, um vorherzusagen, wann Sitzungen benötigt werden, und den Sitzungspool entsprechend anzupassen.

Diese Strategie hilft auch bei der Optimierung der Auswahl zwischen *Standard*- und *Premium*-Sitzungen auf dynamischere Weise. Es ist nämlich viel schneller, zwischen den beiden Typen innerhalb einer einzelnen Benutzersitzung zu wechseln, z. B. wenn zuerst ein komplexes *Premium*-Modell angezeigt wird, gefolgt von einem Modell, das in einer *Standard*-Sitzung angezeigt werden kann. Wenn die Benutzersitzungen recht lang sind, führt dies ggf. zu erheblichen Kosteneinsparungen.

Weitere Informationen zu Azure Remote Rendering-Sitzungen finden Sie unter:

* [Remote Rendering-Sitzungen](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Routingstrategien für die Servergröße: Standard und Premium

Wenn Sie auswählen müssen, ob eine *Standard*- oder *Premium*-Servergröße erstellt wird, stellt dies eine Herausforderung bei der Gestaltung der Benutzererfahrung und des End-to-End-Systems dar. Die ausschließliche Verwendung von *Premium*-Sitzungen ist eine Option, *Standard*-Sitzungen verbrauchen jedoch deutlich weniger Azure-Computeressourcen und sind kostengünstiger als *Premium*. Dies ist ein Anreiz, nach Möglichkeit *Standard*-Sitzungen zu verwenden und *Premium* nur bei Bedarf zu nutzen.

Im Folgenden sind verschiedene Optionen mit unterschiedlichem Umfang beschrieben, mit denen Sie die Sitzungsauswahl verwalten können.

### <a name="use-only-standard-or-premium"></a>Ausschließliches Verwenden von Standard oder Premium

Wenn Sie genau wissen, dass Ihre Anforderungen *immer* unterhalb des Schwellenwerts zwischen *Standard* und *Premium* liegen, ist die Entscheidung einfach: Nutzen Sie einfach *Standard*. Beachten Sie jedoch, dass es bedeutende Auswirkungen auf die Benutzererfahrung hat, wenn die Gesamtkomplexität der geladenen Ressourcen für eine *Standard*-Sitzung abgelehnt wird, da sie zu komplex sind.

Wenn Sie davon ausgehen, dass ein großer Teil der Anwendungen den Schwellenwert zwischen *Standard* und *Premium* überschreitet oder der Kostenfaktor in Ihrem Anwendungsfall keine Rolle spielt, können Sie der Einfachheit halber durchgehend *Premium* wählen.

### <a name="ask-the-user"></a>Auswahl durch die Benutzer

Wenn Sie sowohl *Standard* als auch *Premium* unterstützen möchten, können Sie auch einfach die Benutzer fragen, welcher Sitzungstyp initiiert werden soll, wenn sie die anzuzeigenden 3D-Ressourcen auswählen. Das Problem bei diesem Ansatz besteht darin, dass die Benutzer die Komplexität der 3D-Ressource bzw. sogar mehrerer Ressourcen, die angezeigt werden, kennen müssen. Aus diesem Grund empfiehlt sich diese Option in der Regel nicht. Wenn die Benutzer eine falsche Auswahl treffen und *Standard* wählen, könnte die resultierende Benutzererfahrung zu einem ungünstigen Zeitpunkt kompromittiert werden.

### <a name="analyze-the-3d-model"></a>Analysieren des 3D-Modells

Ein weiterer relativ einfacher Ansatz besteht darin, die Komplexität der ausgewählten 3D-Objekte zu analysieren. Wenn die Komplexität des Modells unter dem Schwellenwert für *Standard* liegt, initiieren Sie eine *Standard*-Sitzung. Andernfalls initiieren Sie eine *Premium*-Sitzung. Hier besteht die Herausforderung darin, dass eine einzelne Sitzung letztendlich verwendet werden kann, um mehrere Modelle anzuzeigen, von denen einige möglicherweise den Komplexitätsschwellenwert einer *Standard*-Sitzung überschreiten, was dazu führt, dass diese Sitzung nicht nahtlos für eine Sequenz verschiedener 3D-Objekte verwendet werden kann.

### <a name="automatic-switching"></a>Automatischer Wechsel

Das automatische Wechseln zwischen *Standard*- und *Premium*-Sitzungen kann bei einem Systementwurf sinnvoll sein, der auch Sitzungspools umfasst. Diese Strategie ermöglicht eine weitere Optimierung der Ressourcennutzung. Wenn der Benutzer Modelle lädt, die angezeigt werden sollen, wird die Komplexität bestimmt, und die richtige Sitzungsgröße wird vom Sitzungspoooldienst angefordert.

## <a name="working-with-networks"></a>Arbeiten mit Netzwerken

### <a name="diagnostics"></a>Diagnose

Azure Remote Rendering erfordert eine schnelle Internetverbindung mit geringer Latenz. Die Qualität des Benutzernetzwerks kann einen erheblichen Einfluss auf die Qualität der Benutzererfahrung haben. Da Ihre Clients wahrscheinlich über unterschiedliche Netzwerkkonfigurationen verfügen und nur gelegentlich eine geringe Netzwerklatenz aufweisen, sind die Diagnosetools entscheidend.  

Um sicherzustellen, dass Sie eine konsistent hochwertige Erfahrung bieten können, empfiehlt es sich, serverseitige und clientseitige Analysetools in Ihre Azure Remote Rendering-Anwendungen zu integrieren. So erhalten Sie die Informationen, die Sie benötigen, um alle Netzwerkprobleme zu diagnostizieren und zu beheben, die bei Ihren Clients auftreten können.

### <a name="client-network-configurations"></a>Client-Netzwerkkonfigurationen

Eine der größten Herausforderungen bei der Entwicklung robuster Lösungen für die Zusammenarbeit, die in einer Vielzahl von Unternehmensumgebungen bereitgestellt werden, besteht in den unterschiedlichen Netzwerktopologie- und Unternehmensfirewall-Konfigurationen, die Ihre Clients ggf. verwenden.

Viele Unternehmen blockieren den gesamten Peer-to-Peer-Datenverkehr in einem LAN. Dadurch wird es schwierig, die Einfachheit und die optimierte Benutzeroberfläche der automatischen LAN-Ermittlung zu nutzen, um eine lokale gemeinsame Sitzung zwischen allen ermittelten Instanzen Ihrer Mixed Reality-Anwendung einzurichten.

Andere potenzielle Fehlerpunkte sind Router, die zum Drosseln von Bandbreite und Firewalls konfiguriert sind, die die meisten TCP/IP-Ports blockieren.

Wenn Sie beabsichtigen, Azure Remote Rendering in einem unbekannten Netzwerk zu verwenden, wird Folgendes empfohlen:

* Stellen Sie eine Checkliste bereit, mit der die Netzwerkbereitschaft vor der Besprechung überprüft werden soll.
* Stellen Sie sicher, dass das entsprechende regionale Rechenzentrum die Anforderung bedienen kann.
* Planen Sie ausreichend Zeit für die Problemdiagnose ein.
* Halten Sie einen mobilen Hotspot mit einem Datentarif mit hoher Bandbreite bereit, auf den Sie ggf. ausweichen können.

### <a name="end-to-end-bandwidth"></a>End-to-End-Bandbreite

Werten Sie die Bandbreitenkapazitäten für alle Netzwerkabschnitte aus, die zwischen der Azure Remote Rendering-VM und dem Endclient möglicherweise vorhanden sind. Beachten Sie, dass das Netzwerksegment aus dem Azure-Rechenzentrum zum Internetdienstanbieter des Clients möglicherweise eine größere Einschränkung darstellt als zwischen Internetdienstanbieter und Client. Mithilfe des Downloadgeschwindigkeitstests für Blobs können derartige Probleme diagnostiziert werden.

### <a name="bandwidth-competition"></a>Konkurrieren um Bandbreite

Beachten Sie beim Entwerfen Ihrer Mixed Reality-Anwendung, dass unterschiedliche Features der App möglicherweise mit Azure Remote Rendering um Bandbreite konkurrieren. Dieses wahrscheinlich unvorhergesehene Beispiel tritt dann ein, wenn viele Teilnehmer in einem einzelnen Raum ARR gleichzeitig zum Anzeigen eines 3D-Objekts verwenden möchten. Jeder Abschnitt des Netzwerkdatenflusses muss über ausreichend Kapazität verfügen, um die Summe aller ARR-Datenströme zusammen zu transportieren.

Weitere Beispiele hierfür sind Streamingvideos, parallele Uploads anderer verwandter Inhalte im Hintergrund und Sprach-Chats, insbesondere wenn viele Teilnehmer vorhanden sind und das System einen verteilten Peer-to-Peer-Ansatz anstelle eines Ansatzes mit einem Audiomixingserver in der Mitte nutzt.

Weitere Informationen zur Netzwerkanalyse finden Sie unter:

* [Downloadgeschwindigkeitstest für Azure Storage Blob](https://www.azurespeed.com/Azure/Download)
* [Roundtrip-Latenzstatistiken von Azure-Netzwerken](../../../../networking/azure-network-latency.md)
* [Serverseitige Leistungsabfragen](../../../overview/features/performance-queries.md)
* [Erstellen clientseitiger Leistungsüberwachungen](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Überlegungen zur Zusammenarbeit

Zu den praktischsten Anwendungsmöglichkeiten von Azure Remote Rendering zählt die Zusammenarbeit zwischen mehreren Teilnehmern, die gleichzeitig dieselbe 3D-Darstellung anzeigen. In diesen freigegebenen Sitzungen muss bedacht werden, dass alle Teilnehmer eine eindeutige Azure Remote Rendering-Sitzung benötigen, unabhängig davon, ob sie sich an derselben Stelle im selben Netzwerk befinden oder nicht.

Dies ist der Fall, da allen Teilnehmern aus unterschiedlichen Standpunkten dieselbe Oberfläche angezeigt wird, was es erforderlich macht, die gleichen 3D-Objekte gleichzeitig aus all diesen Perspektiven zu rendern.

### <a name="multiple-azure-remote-rendering-sessions"></a>Mehrere Azure Remote Rendering-Sitzungen

Wenn Sie beabsichtigen, freigegebene Benutzeroberflächen mit Azure Remote Rendering zu unterstützen, müssen die Systeme, die Sie zum Erstellen und Verwalten von ARR-Sitzungen einrichten, darauf vorbereitet werden, mehrere Sitzungen zu initiieren. Diese Sitzungen müssen möglicherweise in unterschiedlichen Azure-Rechenzentren initialisiert werden, wenn die Teilnehmer geografisch verteilt sind.

Das System muss außerdem die Möglichkeit berücksichtigen, dass sich mindestens ein Teilnehmer in einer geografischen Region befindet, die derzeit nicht von Azure Remote Rendering unterstützt wird oder in der keine Azure Remote Rendering-VM-Instanzen verfügbar sind.

Diese Verwaltung mehrerer gleichzeitiger Sitzungen kann weiter optimiert werden, wenn sie mit Sitzungspools und anderen in diesem Dokument beschriebenen Strategien kombiniert wird.

### <a name="azure-blob-storage-considerations"></a>Überlegungen zu Azure Blob Storage

Alle gleichzeitigen ARR-Sitzungen können auf denselben SAS-URI verweisen, damit das konvertierte Modell angezeigt wird. Dadurch können die gewünschten 3D-Objekte einmalig hochgeladen und konvertiert und dann über alle Sitzungen hinweg freigegeben werden. Dies trifft vor allem dann zu, wenn sich die Teilnehmer am gleichen Standort befinden und das gleiche Rechenzentrum nutzen und es keine Leistungsbedenken in Bezug darauf gibt, dass Azure Blob Storage sich in einem anderen Rechenzentrum als der Azure Remote Rendering-Server und die Benutzer befindet.

Wenn 3D-Objekte in der Regel für eine einzelne Anzeigesitzung hochgeladen und dann verworfen werden, wie z. B. bei der Entwurfsüberprüfung, ist die geografische Region von Azure Blob Storage in Bezug auf den Azure Remote Rendering-Server auch weniger kritisch.

Für 3D-Objekte, die wiederholt verwendet werden, wie z. B. für Schulungen, empfiehlt es sich jedoch, für jedes regionale Rechenzentrum, in dem Sie Azure Remote Rendering verwenden möchten, fertige 3D-Objekte im Blobspeicher aufzubewahren. Dies kann mithilfe der Azure Storage-Redundanz automatisiert werden. CDN wird auch häufig für diesen Zweck verwendet, für Azure Remote Rendering ist dies aber noch keine Option.

Weitere Informationen finden Sie unter:

* [Gemeinsam genutzte Umgebungen in Mixed Reality](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage-Redundanz](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Verwalten des Modellzugriffs

Wenn Sie das Azure Remote Rendering vollständig nutzen, müssen Sie sich die End-to-End-Infrastruktur für die Verwaltung von 3D-Modellen sorgfältig überlegen.

Ein Vorteil bei der Verwendung von Azure Remote Rendering besteht darin, dass große 3D-Objekte vor dem Anzeigen niemals direkt an das Mixed Reality-Gerät übertragen werden müssen.  Sobald ein 3D-Objekt hochgeladen und für die Verwendung mit Azure Remote Rendering konvertiert wurde, kann eine beliebige Anzahl von Benutzern diese Instanz des 3D-Modells freigeben.

### <a name="considerations-for-3d-model-access"></a>Überlegungen zum 3D-Modellzugriff

Im Folgenden finden Sie einige wichtige Überlegungen im Hinblick auf die Zugriffsstrategie für Ihre Modelle.

Bestimmen Sie basierend auf dem erwarteten Anwendungsfall den besten Ort bzw. eine Kombination von Orten, die Benutzer die 3D-Objekte für die Anzeige auswählen können. Einige häufig genutzte Optionen sind:

* Direkt innerhalb der Mixed Reality-Oberfläche
* Über ein begleitendes Webportal
* In einer begleitenden Desktop- oder mobilen Anwendung

Wenn Ihr Anwendungsfall über Verwendungsmuster verfügt, bei denen dasselbe 3D-Objekt mehrmals hochgeladen werden kann, verfolgt das Back-End nach, welche Modelle bereits für die Verwendung mit ARR konvertiert wurden, sodass ein Modell nur einmal für eine spätere Auswahl vorverarbeitet wird. Ein Beispiel für eine Entwurfsüberprüfung wäre ein Fall, in dem ein Team auf ein häufig genutztes 3D-Originalobjekt zugreifen kann. Jedes Teammitglied soll das Modell irgendwann mithilfe von ARR überprüfen. Nur das erste Anzeigen löst anschließend den Vorabverarbeitungsschritt aus. Beim nachfolgenden Anzeigen wird die dazugehörige nachverarbeitete Datei im SAS-Ausgabecontainer gesucht.

Abhängig vom Anwendungsfall möchten Sie wahrscheinlich die richtige Größe des Azure Remote Rendering-Servers (*Standard* oder *Premium*) für jedes 3D-Objekt oder jede Gruppe von Objekten, die in derselben Sitzung zusammen angezeigt werden, ermitteln und potenziell beibehalten.  

### <a name="on-device-model-selection-list"></a>Auswahlliste für Gerätemodelle

In vielen Anwendungsfällen, wie z. B. bei Schulungen, Aufgabenleitfäden oder Marketinganwendungen, ist der Satz von 3D-Objekten, die normalerweise in Azure Remote Rendering angezeigt werden, relativ statisch. In diesen Fällen kann ein fertiger Satz von 3D-Objekten vorab konvertiert und über eine Datenbank zur Verfügung gestellt werden, die die erforderlichen Informationen zum Auffüllen einer Auswahlliste mit den aufgelisteten Objekten enthält. Diese Daten können anschließend aus der Mixed Reality-Anwendung abgerufen werden, um ein Auswahlmenü aufzufüllen.

Sie können auch einen Schritt weiter gehen und zusätzlich eine Möglichkeit zum Hochladen privater 3D-Objekte der einzelnen Person oder Gruppe anbieten. Diese Liste privater Objekte kann dann mit der Liste der häufigen zusammengestellten Objekte in der Benutzeroberfläche kombiniert werden, um 3D-Objekte anzuzeigen.

### <a name="on-device-onedrive-access"></a>OneDrive-Zugriff auf dem Gerät

Da eine OneDrive-Dateiauswahl nativ in die Mixed Reality-Geräte von Microsoft integriert ist, ist das Auswählen von 3D-Objekten auf dem Gerät über OneDrive eine attraktive Option, insbesondere für Anwendungsfälle, in denen es üblich ist, unterschiedliche oder geänderte 3D-Modelle zu laden. In diesem Szenario wählt der Benutzer ein oder mehrere 3D-Objekte über die OneDrive-Dateiauswahl in Ihrer Mixed Reality-Anwendung aus. Die 3D-Objekte werden dann zu einem SAS-Eingabecontainer migriert, in einen SAS-Ausgabecontainer konvertiert und an die ARR-Sitzung angefügt. Im Idealfall ruft die Mixed Reality-Anwendung einen cloudbasierten Prozess auf, um diese Schritte auszuführen, anstatt alle Bits von OneDrive auf das Gerät zu verschieben und zu Azure Blob Storage Storage zurückzukehren.

In einem weiteren Schritt könnte für diese Vorgehensweise eine Zuordnung zwischen 3D-Objekten beibehalten werden, die zuvor angezeigt wurden, sodass die Anwendung den Konvertierungsprozess umgeht und das zugeordnete konvertierte 3D-Objekt direkt über den zugehörigen SAS-URI geladen wird, wenn das gleiche Modell erneut von OneDrive ausgewählt wird.

Weitere Informationen finden Sie unter:

* [Microsoft Power Automate-Vorlage für OneDrive für die Azure Storage-Replikation](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Übersicht über die OneDrive-Dateispeicher-API](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Direkter CAD-Zugriff

Ein überzeugender Anwendungsfall für Mixed Reality sind Entwurfsüberprüfungen der in Bearbeitung befindlichen CAD-Arbeit. In diesem Szenario ist eine schnelle Ladezeit zwischen Desktop und Mixed Reality von zentraler Bedeutung. Eine ideale Lösung umfasst etwa das Entwickeln von Plug-Ins für bestimmte CAD-Anwendungen. Diese Plug-Ins verwalten direkt jeden Aspekt des Lade-, Konvertierungs- und Anzeigeprozesses:

* Bereitstellen einer Benutzeroberfläche für Folgendes:
  * Koppeln der CAD-Anwendung mit einem bestimmten Mixed Reality-Gerät (einmalig)
  * Anfordern, dass die ausgewählte Geometrie auf dem Mixed Reality-Gerät angezeigt wird
* Starten der Azure Remote Rendering-Sitzung (sofern noch nicht ausgeführt), damit sie während des Hochladens und Konvertierens der CAD-Datei parallel ausgeführt werden kann
* Normalisieren von CAD-Geometriedaten in eines der Formate, die von Azure Remote Rendering unterstützt werden
* Übertragen der normalisierten Daten direkt an den Azure Blob Storage-Eingabecontainer
* Initiieren des Prozesses zur Modellkonvertierung
* Verknüpfen des SAS-URI des Modellausgabecontainers mit der Azure Remote Rendering-Sitzung
* Benachrichtigen Sie die gekoppelte Mixed Reality-Anwendung, dass das Model verfügbar und bereit für die Anzeige ist, und stellen Sie den SAS-URI bereit, damit die Anwendung ihn an die Sitzung anfügen kann.

Ein viel einfacherer, aber etwas weniger optimierter Ansatz könnte das Speichern des 3D-Modells auf einer lokalen Festplatte automatisieren und anschließend einen Prozess initiieren, mit dem diese gespeicherte Datei in den SAS-Eingabecontainer übertragen wird.

### <a name="azure-marketplace"></a>Azure Marketplace

Viele Unternehmensclients erfordern es aus Sicherheitsgründen, dass Ihr Azure Stack unter ihren eigenen Azure-Konten und Anmeldeinformationen bereitgestellt werden können. Damit dies möglich ist, sollten Sie Ihre verwaltete Azure-Anwendung so verpacken, dass sie im Azure Marketplace als Azure-Anwendungsangebot veröffentlicht werden kann.

Weitere Informationen finden Sie unter:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Tutorial: Veröffentlichen von verwalteten Azure-Anwendungen im Marketplace](../../../../marketplace/create-new-azure-apps-offer.md)

### <a name="security"></a>Sicherheit

Es ist wichtig, eine Azure Remote Rendering-End-to-End-Sicherheitslösung von Grund auf zu erstellen. Es gibt viele Sicherheitsaspekte, die Sie beim Entwerfen Ihrer End-to-End-Lösung beachten sollten:

* Authentifizierungsstrategien
* Zugriffsverwaltung – Gruppen, Richtlinien und Berechtigungen
* Mehrinstanzenfähigkeit
* Datenspeicherung und Übertragungsverschlüsselung
* Temporäre Verwendungstoken
* Verteilte Denial-of-Service-Angriffe (DDoS)
* Bedrohungserkennung
* VPNs und sichere Netzwerke
* Firewalls
* Verwaltung von Zertifikaten und geheimen Schlüsseln
* Sicherheitsrisiken und Schwachstellen von Anwendungen

Für die Authentifizierung empfiehlt es sich, die ARR-Authentifizierung und die Sitzungsverwaltung so weit wie möglich an einen Azure-Webdienst zu übergeben. Dies führt zu einer besseren verwalteten und sichereren Lösung.

Weitere Informationen finden Sie unter:

* [Azure AD-Dienstauthentifizierung](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Stärken Sie Ihren Sicherheitsstatus mit Azure](https://azure.microsoft.com/overview/security/)
* [Cloudsicherheit](https://azure.microsoft.com/product-categories/security/)