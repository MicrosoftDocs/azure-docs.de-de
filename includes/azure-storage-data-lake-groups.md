---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017674"
---
Verwenden Sie in einem ACL-Eintrag immer [Azure AD-Sicherheitsgruppen](../articles/active-directory/fundamentals/active-directory-manage-groups.md) als den zugewiesenen Prinzipal. Widerstehen Sie der Möglichkeit, einzelne Benutzer oder Dienstprinzipale direkt zuzuweisen. Die Verwendung dieser Struktur ermöglicht Ihnen, Benutzer oder Dienstprinzipale hinzuzufügen und zu entfernen, ohne dass Sie ACLs erneut auf eine gesamte Verzeichnisstruktur anwenden müssen. Stattdessen können Sie einfach Benutzer und Dienstprinzipale zur entsprechenden Azure AD-Sicherheitsgruppe hinzufügen oder aus dieser entfernen. 

Es gibt viele verschiedene Möglichkeiten zum Einrichten von Gruppen. Angenommen, Sie haben ein Verzeichnis namens **/LogData**, das Protokolldaten enthält, die von Ihrem Server generiert werden. Azure Data Factory (ADF) erfasst Daten in diesem Ordner. Bestimmte Benutzer aus dem IT-Support-Team laden Protokolle hoch und verwalten andere Benutzer dieses Ordners, und in verschiedenen Databricks-Clustern werden die Protokolle aus diesem Ordner analysiert. 

Um diese Aktivitäten zu aktivieren, können Sie eine `LogsWriter`-Gruppe und eine `LogsReader`-Gruppe erstellen. Anschließend können Sie die Berechtigungen wie folgt zuweisen:

- Fügen Sie die `LogsWriter`-Gruppe zur ACL des Verzeichnisses **/LogData** mit `rwx`-Berechtigungen hinzu.
- Fügen Sie die `LogsReader`-Gruppe zur ACL des Verzeichnisses **/LogData** mit `r-x`-Berechtigungen hinzu.
- Fügen Sie das Dienstprinzipalobjekt oder die verwaltete Dienstidentität (Managed Service Identity, MSI) für ADF zur `LogsWriters`-Gruppe hinzu.
- Fügen Sie Benutzer aus dem IT-Support-Team zur `LogsWriter`-Gruppe hinzu.
- Fügen Sie das Dienstprinzipalobjekt oder die MSI für Databricks zur `LogsReader`-Gruppe hinzu.

Wenn ein Benutzer des IT-Support-Teams das Unternehmen verlässt, können Sie ihn einfach aus der `LogsWriter`-Gruppe entfernen. Wenn Sie diesen Benutzer nicht zu einer Gruppe hinzugefügt, sondern stattdessen einen dedizierten ACL-Eintrag für den Benutzer hinzugefügt hätten, müssten Sie diesen ACL-Eintrag aus dem **/LogData-** -Verzeichnis entfernen. Außerdem müssten Sie den Eintrag aus allen Unterverzeichnissen und Dateien in der gesamten Verzeichnishierarchie des **/LogData**-Verzeichnisses entfernen. 

Weitere Informationen dazu, wie eine Gruppe erstellt wird und Mitglieder hinzugefügt werden, finden Sie unter [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md).