---
title: Behandeln von Problemen mit dem Azure Import/Export-Tool | Microsoft-Dokumentation
description: Informationen zu einigen bei der Verwendung des Azure Import/Export-Tools aufgetretenen allgemeinen Problemen und deren Behebung.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320447"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Behandeln von Problemen mit dem Azure Import/Export-Tool
Das Microsoft Azure Import/Export-Tool zeigt Fehlermeldungen an, wenn Probleme auftreten. Dieses Thema enthält einige häufig auftretende Probleme, auf die Benutzer stoßen können.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Wie gehe ich vor, wenn bei einer Kopiersitzung ein Fehler auftritt?  
 Wenn ein Fehler in einer Kopiersitzung auftritt, gibt es zwei Optionen:  
  
 Wenn der Fehler durch Wiederholen behoben werden kann, beispielsweise wenn die Netzwerkfreigabe kurze Zeit offline war und nun wieder online ist, können Sie die Kopiersitzung fortsetzen. Wenn der Fehler nicht durch erneutes Versuchen behoben werden kann, etwa wenn Sie das falsche Quellverzeichnis der Dateien in den Befehlszeilenparametern angegeben haben, müssen Sie die Kopiersitzung abbrechen. Weitere Informationen zum Fortsetzen und Abbrechen von Kopiersitzungen finden Sie unter [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md) (Vorbereiten von Festplatten für Importaufträge).  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Ich kann eine Kopiersitzung nicht fortsetzen oder abbrechen.  
 Falls die Kopiersitzung die erste Kopiersitzung für ein Laufwerk ist, sollte die Fehlermeldung wie folgt lauten: „Die erste Kopiersitzung kann nicht fortgesetzt oder abgebrochen werden.“ In diesem Fall können Sie die alte Journaldatei löschen und den Befehl erneut ausführen.  
  
 Wenn eine Kopiersitzung nicht die erste Kopiersitzung für ein Laufwerk ist, kann sie immer fortgesetzt oder abgebrochen werden.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ich habe die Journaldatei verloren, kann ich den Auftrag trotzdem erstellen?  
 Die Journaldatei für ein Laufwerk enthält die vollständigen Informationen zum Kopieren von Daten auf dieses Laufwerk, ist erforderlich, um dem Laufwerk weitere Dateien hinzufügen, und wird verwendet, um einen Importauftrag zu erstellen. Wenn die Journaldatei verloren geht, müssen Sie alle Kopiersitzungen für das Laufwerk wiederholen.  
  
## <a name="next-steps"></a>Nächste Schritte
 
* [Einrichten des Azure Import/Export-Tools](../storage-import-export-tool-setup-v1.md)   
* [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Überprüfen des Auftragsstatus mit Protokollkopiedateien](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparieren eines Importauftrags](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparieren eines Exportauftrags](../storage-import-export-tool-repairing-an-export-job-v1.md)
