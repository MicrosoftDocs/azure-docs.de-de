---
title: Arbeiten mit Gebäudeplänen in Azure Maps Creator (Vorschau)
description: In diesem Artikel werden Konzepte vorgestellt, die für die Azure Maps Creator-Dienste (Vorschau) gelten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903563"
---
# <a name="creator-preview-for-indoor-maps"></a>Creator (Vorschau) für Gebäudepläne


> [!IMPORTANT]
> Azure Maps Creator-Dienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


In diesem Artikel werden Konzepte und Tools vorgestellt, die für Azure Maps Creator gelten. Lesen Sie diesen Artikel, bevor Sie mit der Verwendung der Azure Maps Creator-API und des SDK beginnen.

Sie können Creator verwenden, um Anwendungen mit Kartenfunktionen zu entwickeln, die auf Daten von Gebäudeplänen basieren. In diesem Artikel wird beschrieben, wie Sie Ihre Kartendaten hochladen, konvertieren, erstellen und verwenden. Der gesamte Workflow ist in der folgenden Abbildung dargestellt.

![Workflow für Creator-Kartendaten](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Erstellen von Azure Maps Creator (Vorschau) 

Damit Sie Creator-Dienste (Vorschau) verwenden können, muss Azure Maps Creator in einem Azure Maps-Konto erstellt werden. Informationen zum Erstellen von Azure Maps Creator in Azure Maps finden Sie unter [Verwalten von Azure Maps Creator](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Hochladen eines Zeichnungspakets

Mit Creator (Vorschau) werden Daten von Gebäudeplänen durch Konvertieren eines hochgeladenen Zeichnungspakets erfasst. Das Zeichnungspaket stellt eine gebaute oder umgebaute Einrichtung dar. Informationen zum den Anforderungen für Zeichnungspakete finden Sie unter [Anforderungen für Zeichnungspakete](drawing-requirements.md).

Verwenden Sie die [Datenupload-API von Azure Maps (Vorschau)](/rest/api/maps/data/uploadpreview), um ein Zeichnungspaket hochzuladen.  Nach dem erfolgreichen Upload gibt die Datenupload-API einen Benutzerdatenbezeichner (`udid`) zurück. `udid` wird im nächsten Schritt verwendet, um das hochgeladene Paket in Gebäudeplandaten zu konvertieren.

## <a name="convert-a-drawing-package"></a>Konvertieren eines Zeichnungspakets

Das hochgeladene Zeichnungspaket wird vom [Azure Maps-Konvertierungsdienst](/rest/api/maps/conversion) in Gebäudeplandaten konvertiert. Der Konvertierungsdienst überprüft auch das Paket. Validierungsprobleme werden in zwei Typen unterteilt: Fehler und Warnungen. Wenn Fehler erkannt werden, tritt beim Konvertierungsvorgang ein Fehler auf. Wenn Warnungen erkannt werden, ist die Konvertierung dennoch erfolgreich. Es empfiehlt sich jedoch, alle Warnungen zu überprüfen und zu beheben. Im Falle einer Warnung wurde ein Teil der Konvertierung ignoriert oder automatisch korrigiert. Wird die Warnung nicht behoben, kann dies Fehler in späteren Prozessen zur Folge haben. Weitere Informationen finden Sie unter [Warnungen und Fehler im Zusammenhang mit Zeichnungspaketen](drawing-conversion-error-codes.md).

Wenn ein Fehler auftritt, stellt der Konvertierungsdienst einen Link zur eigenständigen Webanwendung [Azure Maps-Schnellansicht für Zeichnungsfehler](drawing-error-visualizer.md) bereit. Mithilfe der Schnellansicht für Zeichnungsfehler können Sie [Warnungen und Fehler im Zusammenhang mit Zeichnungspaketen](drawing-conversion-error-codes.md) überprüfen, die während des Konvertierungsvorgangs aufgetreten sind. Nachdem Sie die Fehler korrigiert haben, können Sie versuchen, das Paket hochzuladen und zu konvertieren.

## <a name="create-indoor-map-data"></a>Erstellen von Gebäudeplandaten

Azure Maps Creator (Vorschau) umfasst drei Dienste:

* [Datasetdienst](/rest/api/maps/dataset/createpreview).
Verwenden Sie den Datasetdienst, um aus einem konvertierten Zeichnungspaket ein Dataset zu erstellen.
* [Kachelsetdienst](/rest/api/maps/tileset/createpreview).
Verwenden Sie den Kachelsetdienst, um eine vektorbasierte Darstellung eines Datasets zu erstellen. Anwendungen können ein Kachelset verwenden, um eine auf visuellen Kacheln basierende Ansicht des Datasets darzustellen.
* [Featurezustandsdienst](/rest/api/maps/featurestate). Verwenden Sie den Featurezustandsdienst, um dynamische Kartenstile zu unterstützen. Dynamische Kartenstile ermöglichen es, dass Anwendungen Echtzeitereignisse in vom IoT-System bereitgestellten Bereichen darstellen.

### <a name="datasets"></a>Datasets

Bei einem Dataset handelt es sich um eine Sammlung von Gebäudeplanfeatures. Die Gebäudeplanfeatures stellen Einrichtungen dar, die in einem konvertierten Zeichnungspaket definiert sind. Wenn Sie ein Dataset mit dem [Datasetdienst](/rest/api/maps/dataset/createpreview)erstellt haben, können Sie eine beliebige Anzahl von [Kachelsets](#tilesets) oder [Featurezustandssets](#feature-statesets) erstellen.

Der [Datasetdienst](/rest/api/maps/dataset/createpreview) ermöglicht es Entwicklern, einem vorhandenen Dataset jederzeit Einrichtungen hinzuzufügen oder sie zu entfernen. Weitere Informationen zum Aktualisieren eines vorhandenen Datasets mithilfe der API finden Sie unter den Anfügeoptionen des [Datasetdiensts](/rest/api/maps/dataset/createpreview). Ein Beispiel für die Aktualisierung eines Datasets finden Sie unter [Datenpflege](#data-maintenance).

### <a name="tilesets"></a>Kachelsets

Ein Kachelset ist eine Sammlung von Vektordaten, die einen Satz von einheitlichen Rasterkacheln darstellt. Entwickler können den [Kachelsetdienst](/rest/api/maps/tileset/createpreview) verwenden, um Kachelsets aus einem Dataset zu erstellen.

Erstellen Sie mehrere Kachelsets aus dem gleichen Dataset, um unterschiedliche Inhaltsebenen darzustellen. Sie können beispielsweise ein Kachelset mit Möbeln und Ausstattung und ein weiteres ohne Möbel und Ausstattung erstellen.  Oder Sie erstellen ein Kachelset mit den letzten Datenaktualisierungen und eines ohne die letzten Datenaktualisierungen.

Zusätzlich zu den Vektordaten stellt das Kachelset Metadaten für die Kartenrenderingoptimierung bereit. Kachelsetmetadaten enthalten beispielsweise einen Mindest- und Höchstzoomfaktor für das Kachelset. Die Metadaten bieten auch einen Begrenzungsrahmen, der den geografischen Bereich des Kachelsets definiert. Der Begrenzungsrahmen ermöglicht es einer Anwendung, den richtigen Mittelpunkt programmgesteuert festzulegen. Weitere Informationen zu Kachelsetmetadaten finden Sie unter [Kachelsetlisten-API](/rest/api/maps/tileset/listpreview).

Nachdem ein Kachelset erstellt wurde, kann es vom [Render V2-Dienst](#render-v2-service) abgerufen werden.

Wenn ein Kachelset veraltet ist und nicht mehr genutzt wird, können Sie das Kachelset löschen. Weitere Informationen zum Löschen von Kachelsets finden Sie unter [Datenpflege](#data-maintenance).

>[!NOTE]
>Ein Kachelset ist vom Dataset unabhängig, aus dem es erstellt wurde. Wenn Sie Kachelsets aus einem Dataset erstellen und dieses Dataset anschließend aktualisieren, werden die Kachelsets nicht aktualisiert. Um Änderungen in einem Dataset widerzuspiegeln, müssen Sie neue Kachelsets erstellen. Wenn Sie ein Kachelset löschen, ist das Dataset davon nicht betroffen.

### <a name="feature-statesets"></a>Featurezustandssets

Featurezustandssets sind Sammlungen dynamischer Eigenschaften (*Zustände*), die Datasetfeatures wie z. B. Räumen oder Ausstattung zugewiesen sind. Ein Beispiel für einen *Zustand* ist etwa die Temperatur oder die Belegung. Jeder *Zustand* ist ein Schlüssel-Wert-Paar, das den Namen der Eigenschaft, den Wert und den Zeitstempel der letzten Aktualisierung enthält.

Mit dem [Featurezustandsdiesnt](/rest/api/maps/featurestate/createstatesetpreview) können Sie einen Featurezustand für ein Dataset erstellen und verwalten. Das Zustandsset wird durch einen oder mehrere *Zustände* definiert. Zu jedem Feature wie etwa einem Raum kann ein *Zustand* hinzugefügt werden.

Der Wert jedes *Zustands* in einem Zustandsset kann von IoT-Geräten oder anderen Anwendungen aktualisiert oder abgerufen werden.  Mithilfe der [API zur Featurezustandsaktualisierung](/rest/api/maps/featurestate/updatestatespreview) können Geräte, die die Raumbelegung messen, systematisch die Zustandsänderung eines Raums veröffentlichen.

Eine Anwendung kann ein Featurezustandsset verwenden, um Features in einer Einrichtung dem aktuellen Zustand und dem jeweiligen Kartenstil entsprechend dynamisch zu rendern. Weitere Informationen zur Verwendung von Featurezustandssets zum Festlegen des Stils von Features in einer Renderingkarte finden Sie unter [Web SDK-Modul für Gebäude](#indoor-maps-module).

>[!NOTE]
>Wie bei Kachelsets wirkt sich das Ändern eines Datasets nicht auf das vorhandene Featurezustandsset aus, und das Löschen eines Featurezustandssets hat keine Auswirkung auf das Dataset, an das es angefügt ist.

## <a name="using-indoor-maps"></a>Verwenden von Gebäudeplänen

### <a name="render-v2-service"></a>Render V2-Dienst

Die [Render V2-Dienst-API zum Abrufen von Kartenkacheln (Vorschau)](/rest/api/maps/renderv2/getmaptilepreview) von Azure Maps wurde erweitert und unterstützt jetzt Creator-Kartenkacheln (Vorschau).

Die Render V2-Dienst-API zum Abrufen von Kartenkacheln ermöglicht es Anwendungen, Kachelsets anzufordern. Die Kachelsets können anschließend in ein Kartensteuerelement oder SDK integriert werden. Ein Beispiel für ein Kartensteuerelement, das den Render V2-Dienst verwendet, finden Sie unter [Modul für Gebäudepläne](#indoor-maps-module).

### <a name="web-feature-service-api"></a>Web Feature Service-API

Datasets können mithilfe der [Web Feature Service-API (WFS)](/rest/api/maps/wfs) abgefragt werden. WFS hält die [Features der Open Geospatial Consortium-API](http://docs.opengeospatial.org/DRAFTS/17-069r1.html) ein. Mit der WFS-API können Sie Features innerhalb des Datasets abfragen. Beispielsweise können Sie WFS verwenden, um alle mittelgroßen Besprechungsräume einer bestimmten Einrichtung und in einem bestimmten Stockwerk zu finden.

### <a name="indoor-maps-module"></a>Modul für Gebäudepläne

Das [Azure Maps Web-SDK](./index.yml) umfasst das Modul für Gebäudepläne. Dieses Modul bietet erweiterte Funktionen für die Azure Maps-Bibliothek für *Kartensteuerelemente*. Das Modul für Gebäudepläne rendert Gebäudepläne, die in Creator (Vorschau) erstellt wurden. Es integriert Widgets, wie z. B. die *Stockwerkauswahl*, mit der Benutzer die unterschiedlichen Stockwerke visualisieren können.

Das Modul für Gebäudepläne ermöglicht es Ihnen, Webanwendungen zu erstellen, die die Integration von Gebäudeplandaten in andere [Azure Maps-Dienste](./index.yml) ermöglichen. Eine häufige Anwendungseinrichtung besteht etwa darin, Kenntnisse aus anderen Karten, wie Straßen, Bilder, Wetterdaten und Verkehr, zu Gebäudeplänen hinzuzufügen.

Das Modul für Gebäudepläne unterstützt auch dynamische Kartenstile. Eine Schritt-für-Schritt-Anleitung für die Implementierung dynamischer Stile für Featurezustandssets in einer Anwendung finden Sie unter [Verwenden des Moduls für Gebäudepläne](how-to-use-indoor-module.md).

### <a name="azure-maps-integration"></a>Integration von Azure Maps

Wenn Sie mit der Entwicklung von Lösungen für Gebäudepläne beginnen, haben Sie verschiedene Möglichkeiten, um vorhandene Azure Maps-Funktionen zu integrieren. Die Assetnachverfolgung oder Sicherheitsszenarien können beispielsweise mithilfe der [Geofence-API von Azure Maps](/rest/api/maps/spatial/postgeofence) in Creator-Gebäudeplänen implementiert werden. Mithilfe der Geofence-API kann beispielsweise bestimmt werden, ob ein Mitarbeiter bestimmte Bereiche betritt oder verlässt. Weitere Informationen zum Herstellen einer Verbindung von Azure Maps mit IoT-Telemetrie finden Sie [hier](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Datenpflege

 Die Azure Maps Creator-API zum Auflisten, Aktualisieren und Löschen (Vorschau) ermöglicht Ihnen, Ihre Datasets, Kachelsets und Featurezustandssets aufzulisten, zu aktualisieren und zu löschen.

>[!NOTE]
>Wenn Sie eine Liste von Elementen überprüfen und entscheiden, diese zu löschen, müssen Sie die Auswirkung dieses Löschvorgangs auf alle abhängigen APIs oder Anwendungen berücksichtigen. Wenn Sie z. B. ein Kachelset löschen möchten, das aktuell von einer Anwendung mithilfe der [Render v2-API zum Abrufen von Kartenkacheln](/rest/api/maps/renderv2/getmaptilepreview) verwendet wird, führt das Löschen dieses Kachelsets dazu, dass beim Rendern dieses Kachelsets ein Anwendungsfehler auftritt.

### <a name="example-updating-a-dataset"></a>Beispiel: Aktualisieren eines Datasets

Im folgenden Beispiel wird gezeigt, wie Sie ein Dataset aktualisieren, ein neues Kachelset erstellen und ein altes Kachelset löschen.

1. Führen Sie die Schritte in den Abschnitten [Hochladen eines Zeichnungspakets](#upload-a-drawing-package) und [Konvertieren eines Zeichnungspakets](#convert-a-drawing-package) aus, um das neue Zeichnungspaket hochzuladen und zu konvertieren.

2. Verwenden Sie die [Dataseterstellungs-API](/rest/api/maps/dataset/createpreview), um die konvertierten Daten an das vorhandene Dataset anzufügen.

3. Verwenden Sie die [Kachelseterstellungs-API](/rest/api/maps/tileset/createpreview), um ein neues Kachelset aus dem aktualisierten Dataset zu generieren. Speichern Sie die neue tilesetId für Schritt 4.

4. Aktualisieren Sie den Kachelsetbezeichner in der Anwendung, um die Visualisierung des aktualisierten Campusdatasets zu aktivieren. Wenn das alte Kachelset nicht mehr verwendet wird, können Sie es löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen von Gebäudeplänen mithilfe von Creator (Vorschau)](tutorial-creator-indoor-maps.md)