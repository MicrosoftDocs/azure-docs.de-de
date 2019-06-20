---
title: Neuerungen bei Power BI-Arbeitsbereichssammlungen
description: Aktuelle Informationen zu Neuerungen bei Power BI-Arbeitsbereichssammlungen.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 9ef9416abb4aaf0927d1e072286ef83a4d8c39cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724020"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Neuerungen bei Power BI-Arbeitsbereichssammlungen

Updates für **Power BI-Arbeitsbereichssammlungen** werden in regelmäßigen Abständen veröffentlicht. Nicht jede Version umfasst jedoch neue Funktionen für Benutzer, da sich einige Versionen auf Back-End-Dienstfunktionen konzentrieren. Wir stellen hier neue Funktionen für Benutzer vor.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>17\. März 2017

**Self-service-Funktionen**

* [Create a new report from a dataset in Power BI Embedded](create-report-from-dataset.md) (Erstellen eines neuen Berichts aus einem Dataset in Power BI Embedded)
* [Save reports in Power BI Embedded](save-reports.md) (Speichern von Berichten in Power BI Embedded)
* Einbetten eines Berichts im Lese-/Bearbeitungs-/Neuerstellungsmodus 
* [Toggle between view and edit mode for reports in Power BI Embedded](toggle-mode.md) (Wechseln zwischen Ansichts- und Bearbeitungsmodus für Berichte in Power BI Embedded)

**Datenkonnektivität mit REST-APIs**

* [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Erstellen von Datasets)
* Übertragen von Daten 

**Verwaltungs-APIs**

* Klonen von Bericht und Dataset
* Binden des Berichts an ein anderes Dataset

**Beispiele**

* Aktualisiertes [Beispiel für die Einbettung von JavaScript-Berichten](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Dezember 2016

* [Neues Einbettungsbeispiel in JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Oktober 2016

* [Erweiterte Analysen mit Power BI-Arbeitsbereichssammlungen und R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31\. August 2016
In dieser Version:

* Ein ganz neues JavaScript-SDK, das [erweiterte Filter und Seitennavigation](interact-with-reports.md)unterstützt.
* Power BI-Arbeitsbereichssammlungen werden jetzt im Rechenzentrum „Kanada, Mitte“ unterstützt. Überprüfen Sie den [Status von Rechenzentren](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11\. Juli 2016
In dieser Version:

* **Gute Nachrichten!** Den Dienst „Power BI-Arbeitsbereichssammlungen“ gibt es nicht länger in der Vorschau- sondern nun in der allgemein verfügbaren Version.  
* Alle REST-APIs wurden nun von **/beta** zu **/v1.0** migriert.
* .NET- und JavaScript-SDKs wurden für **v1.0**aktualisiert.
* Power BI-API-Aufrufe können nun direkt mithilfe von API-Schlüsseln authentifiziert werden. App-Tokens werden nur für die Einbettung benötigt. Infolgedessen sind Bereitstellungs- und Entwicklertoken in APIs der Version 1.0 veraltet. Sie können jedoch in der Betaversion bis 30.12.2016 verwendet werden. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen](app-token-flow.md).
* Unterstützung der Sicherheit auf Zeilenebene für App-Token und eingebettete Berichte. Weitere Informationen finden Sie unter [Sicherheit auf Zeilenebene mit Power BI-Arbeitsbereichssammlungen](row-level-security.md).
* Aktualisierte Beispielanwendung für alle Aufrufe von **v1.0** -APIs.
* Unterstützung von Power BI-Arbeitsbereichssammlungen für Azure SDK, PowerShell und CLI.
* Benutzer können Visualisierungsdaten in eine **CSV-Datei**exportieren.
* Power BI-Arbeitsbereichssammlungen werden nun in den gleichen Sprachen/Gebietsschemas unterstützt wie Microsoft Azure. Weitere Informationen finden Sie unter [Azure – Languages](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx) (Azure – Sprachen).

