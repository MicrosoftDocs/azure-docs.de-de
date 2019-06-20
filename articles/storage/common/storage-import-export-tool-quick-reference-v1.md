---
title: Kurzübersicht über Befehle des Azure Import/Export-Tools für Importaufträge (V1) | Microsoft-Dokumentation
description: Befehlsreferenz für Befehle des Azure Import/Export-Tools, die häufig für Importaufträge verwendet werden. Dies bezieht sich auf V1 des Import/Export-Tools.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 44ee23a510bc15d5cb52a338be1652180b922db9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478435"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Kurzübersicht über häufig verwendete Befehle für Importaufträge
Dieser Abschnitt enthält eine Kurzübersicht über einige häufig verwendete Befehle. Ausführliche Informationen zur Verwendung finden Sie unter [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Vorbereiten einer Festplatte, wenn Daten bereits auf diese kopiert wurden
 Mit dem folgenden Befehl wird eine Festplatte vorbereitet, wenn Daten bereits auf diese kopiert, aber noch nicht mit BitLocker verschlüsselt wurden:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Kopieren eines einzelnen Verzeichnisses auf eine Festplatte  
 Mit dem folgenden Befehl wird ein einzelnes Quellverzeichnis auf eine Festplatte kopiert, die noch nicht mit BitLocker verschlüsselt wurde:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Kopieren von zwei Verzeichnissen auf eine Festplatte  
 Verwenden Sie die folgenden Befehle, um zwei Quellverzeichnisse auf ein Laufwerk zu kopieren:  
  
 Mit dem ersten Befehl werden das Protokollverzeichnis, der Speicherkontoschlüssel, der Buchstabe des Ziellaufwerks, die `format/encrypt`-Anforderungen sowie die allgemeinen Parameter angegeben:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Mit dem zweiten Befehl werden die Journaldatei, eine neue Sitzungs-ID und die Quell- und Zielspeicherorte angegeben:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopieren einer großen Datei auf eine Festplatte in einer zweiten Kopiersitzung  
 Mit dem folgenden Befehl wird eine einzelne große Datei auf eine Festplatte kopiert, die in einer vorherigen Kopiersitzung vorbereitet wurde:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Nächste Schritte

* [Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
