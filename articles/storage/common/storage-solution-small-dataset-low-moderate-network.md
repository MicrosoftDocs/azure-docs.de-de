---
title: Azure-Datenübertragungsoptionen für kleine Datasets mit geringer bis mittlerer Netzwerkbandbreite | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Lösung für die Datenübertragung auswählen, wenn Sie eine geringe bis mittlere Netzwerkbandbreite in Ihrer Umgebung nutzen und planen, kleine Datasets zu übertragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023226"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Datenübertragung kleiner Datasets mit geringer bis mittlerer Netzwerkbandbreite
 
Dieser Artikel bietet einen Überblick über die Datenübertragungslösungen, wenn Sie in Ihrer Umgebung eine geringe bis mittlere Netzwerkbandbreite nutzen und planen, kleine Datasets zu übertragen. Der Artikel beschreibt auch die empfohlenen Datenübertragungsoptionen sowie die jeweilige Schlüsselfunktionsmatrix für dieses Szenario.

Eine Übersicht über alle verfügbaren Datenübertragungsoptionen finden Sie unter [Auswählen einer Azure-Datenübertragungslösung](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Kleine Datasets beziehen sich auf Datengrößen im GB- bis TB-Bereich. Geringe bis mittlere Netzwerkbandbreite bedeutet 45 MBit/s (T3-Verbindung im Datencenter) bis 1 GBit/s an.

- Wenn Sie nur wenige Dateien übertragen und die Datenübertragung nicht automatisieren müssen, sollten Sie die Tools mit einer grafischen Benutzeroberfläche in Betracht ziehen.
- Wenn Sie mit Systemverwaltung vertraut sind, können Sie auch die Befehlszeile oder programmgesteuerte Tools bzw. Skripts verwenden.

## <a name="recommended-options"></a>Empfohlene Optionen

Die folgenden Optionen werden in diesem Szenario empfohlen:

- **Tools mit grafischer Benutzeroberflächen** wie Azure Storage-Explorer und Azure Storage im Azure-Portal. Diese bieten eine einfache Möglichkeit zum Anzeigen von Daten und schnellen Übertragen einiger Dateien.

    - **Azure Storage-Explorer** ist ein plattformübergreifendes Tool zur Verwaltung der Inhalte Ihrer Azure-Speicherkonten. Mit diesem Tool können Sie Blobs, Dateien, Warteschlangen, Tabellen und Azure Cosmos DB-Entitäten hochladen, herunterladen und verwalten. Verwenden Sie es zusammen mit Blob Storage, um Blobs und Ordner zu verwalten und Blobs zwischen Ihrem lokalen Dateisystem und Blob Storage oder zwischen Speicherkonten hoch- und herunterzuladen.
    - **Azure-Portal**: Azure Storage im Azure-Portal bietet eine webbasierte Schnittstelle zum Durchsuchen von Dateien und Hochladen einzelner neuer Dateien. Diese Option empfiehlt sich, wenn Sie Ihre Dateien schnell erkunden möchten, ohne Tools zu installieren oder Befehle auszuführen, oder einfach einige neue Dateien hochladen möchten.

- **Skripterstellung/programmgesteuerte Tools** wie AzCopy, PowerShell. Azure CLI und Azure Storage REST-APIs.

    - **AzCopy**: Verwenden Sie dieses Befehlszeilentool, um Daten einfach und schnell in und aus Azure Blobs, Files und Table Storage mit optimaler Leistung zu kopieren. AzCopy unterstützt Nebenläufigkeit und Parallelität sowie die Fortsetzung unterbrochener Kopiervorgänge.
    - **Azure PowerShell**: Benutzer, die mit Systemverwaltung vertraut sind, können das Azure Storage-Modul in Azure PowerShell zum Übertragen von Daten verwenden.
    - **Azure CLI**: Verwenden Sie dieses plattformübergreifende Tool zur Verwaltung von Azure-Diensten und zum Hochladen von Daten in Azure Storage.
    - **Azure Storage REST-APIs/SDKs**: Beim Erstellen einer Anwendung können Sie die Anwendung mithilfe von Azure Storage REST-APIs/SDKs entwickeln und die in mehreren Sprachen angebotenen Azure-Clientbibliotheken verwenden.


## <a name="comparison-of-key-capabilities"></a>Vergleich der Schlüsselfunktionen

In der folgenden Tabelle werden die Unterschiede der Schlüsselfunktionen zusammengefasst.

| Funktion | Azure Storage-Explorer | Azure-Portal | AzCopy<br>Azure PowerShell<br>Azure-Befehlszeilenschnittstelle | Azure Storage REST-APIs oder SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Verfügbarkeit | Herunterladen und Installieren <br>Eigenständiges Tool | Webbasierte Tools zu Durchsuchen im Azure-Portal | Befehlszeilentool |Programmierbare Schnittstellen in .NET, Java, Python, JavaScript, C++, Go, Ruby und PHP |
| Grafische Benutzeroberfläche | Ja | Ja | Nein | Nein |
| Unterstützte Plattformen | Windows, Mac, Linux | Webbasiert |Windows, Mac, Linux |Alle Plattformen |
| Zulässige Blob Storage-Vorgänge<br>für Blobs und Ordner | Upload<br>Download<br>Verwalten | Upload<br>Download<br>Verwalten |Upload<br>Download<br>Verwalten | Ja, anpassbar |
| Zulässige Data Lake Gen1-Speichervorgänge<br>für Dateien und Ordner | Upload<br>Download<br>Verwalten | Nein |Upload<br>Download<br>Verwalten                   | Nein |
| Zulässige File Storage-Vorgänge<br>für Dateien und Verzeichnisse | Upload<br>Download<br>Verwalten | Upload<br>Download<br>Verwalten   |Upload<br>Download<br>Verwalten | Ja, anpassbar |
| Zulässige Table Storage-Vorgänge<br>für Tabellen |Verwalten | Nein |Tabellenunterstützung in AzCopy v7 |Ja, anpassbar|
| Zulässige Queue Storage-Vorgänge | Verwalten | Nein  |Nein | Ja, anpassbar|


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Übertragen von Daten mit Azure Storage-Explorer](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md).
- [Übertragen von Daten mit AzCopy](./storage-use-azcopy-v10.md)