---
title: Behandeln von Problemen mit Azure Data Share
description: Hier erfahren Sie, wie Sie Probleme mit Einladungen und Fehlern beim Erstellen oder Empfangen von Datenfreigaben mit Azure Data Share behandeln.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.openlocfilehash: e29c640494a18bb3be2125a5b53b4f943521fe6c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579146"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Behandeln allgemeiner Probleme in Azure Data Share 

In diesem Artikel erfahren Sie, wie Sie allgemeine Probleme mit Azure Data Share behandeln. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-Einladungen 

Wenn ein neuer Benutzer in der gesendeten E-Mail-Einladung auf **Einladung akzeptieren** klickt, kann es vorkommen, dass eine leere Einladungsliste angezeigt wird. 

![Keine Einladungen](media/no-invites.png)

Dies könnte die folgenden Gründe haben:

* **Der Azure Data Share-Dienst ist im Azure-Mandanten nicht als Ressourcenanbieter eines Azure-Abonnements registriert.** Dieses Problem tritt auf, wenn es in Ihrem Azure-Mandanten keine Data Share-Ressource gibt. Wenn Sie eine Azure Data Share-Ressource erstellen, wird der Ressourcenanbieter in Ihrem Azure-Abonnement automatisch registriert. Sie können den Data Share-Dienst auch manuell registrieren, indem Sie die nachstehenden Schritte ausführen. Für diese Schritte benötigen Sie die Rolle „Azure-Mitwirkender“.

    1. Navigieren Sie im Azure-Portal zu **Abonnements**.
    1. Wählen Sie das Abonnement aus, das Sie zum Erstellen einer Azure Data Share-Ressource verwenden möchten.
    1. Klicken Sie auf **Ressourcenanbieter**.
    1. Suchen Sie nach **Microsoft.DataShare**.
    1. Klicken Sie auf **Registrieren**. 

    Für diese Schritte benötigen Sie die Rolle [Azure-Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) für das Azure-Abonnement. 

* **Die Einladung wird an Ihren E-Mail-Alias statt an Ihre E-Mail-Adresse für die Anmeldung bei Azure gesendet.** Wenn Sie den Azure Data Share-Dienst registriert oder bereits eine Data Share-Ressource im Azure-Mandanten erstellt haben, die Einladung aber immer noch nicht angezeigt wird, liegt dies möglicherweise daran, dass der Anbieter Ihren E-Mail-Alias statt Ihrer E-Mail-Adresse für die Anmeldung bei Azure als Empfänger eingegeben hat. Wenden Sie sich an Ihren Datenanbieter, und stellen Sie sicher, dass die Einladung an Ihre E-Mail-Adresse für die Anmeldung bei Azure und nicht an Ihren E-Mail-Alias gesendet wurde.

* **Die Einladung wurde bereits akzeptiert.** Der Link in der E-Mail führt Sie im Azure-Portal zur Data Share-Einladungsseite, auf der nur ausstehende Einladungen aufgeführt sind. Wenn Sie die Einladung bereits akzeptiert haben, wird sie auf der Data Share-Einladungsseite nicht mehr angezeigt. Fahren Sie mit Ihrer Data Share-Ressource fort, mit der Sie die Einladung zum Anzeigen von empfangenen Freigaben und Konfigurieren Ihrer Zieleinstellung für den Azure Data Explorer-Cluster akzeptiert haben.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fehler beim Erstellen oder Empfangen einer neuen Freigabe

„Fehler beim Hinzufügen von Datasets“

„Fehler beim Zuordnen von Datasets“

„Data Share-Ressource ‚x‘ kann kein Zugriff auf ‚y‘ gewährt werden.“

„Sie besitzen nicht die erforderlichen Berechtigungen für ‚x‘.“

„Es konnten keine Schreibberechtigungen für das Azure Data Share-Konto mindestens einer Ihrer ausgewählten Ressourcen hinzugefügt werden.“

Wenn beim Erstellen einer neuen Freigabe, Hinzufügen von Datasets oder Zuordnen von Datasets einer der vorstehenden Fehler angezeigt wird, könnte dies daran liegen, dass die Berechtigungen für den Azure-Datenspeicher nicht ausreichen. Informationen zu den erforderlichen Berechtigungen finden Sie unter [Rollen und Anforderungen](concepts-roles-permissions.md). 

Sie benötigen eine Schreibberechtigung zum Freigeben oder Empfangen von Daten aus einem Azure-Datenspeicher, die in der Rolle **Mitwirkender** normalerweise enthalten ist. 

Wenn Sie zum ersten Mal Daten aus dem Azure-Datenspeicher freigeben oder empfangen, benötigen Sie außerdem die Berechtigung *Microsoft.Authorization/role assignments/write*, die normalerweise in der Rolle **Besitzer** enthalten ist. Selbst wenn Sie die Azure Data Store-Ressource erstellt haben, werden Sie dadurch NICHT automatisch zu deren Besitzer. Bei der entsprechenden Berechtigung gewährt der Azure Data Share-Dienst der verwalteten Identität der Data Share-Ressource automatisch Zugriff auf den Datenspeicher. Dieser Vorgang könnte einige Minuten dauern. Wenn aufgrund dieser Verzögerung ein Fehler auftritt, versuchen Sie es in einigen Minuten erneut.

Für SQL-basierte Freigabe sind zusätzliche Berechtigungen erforderlich. Eine ausführliche Liste der Voraussetzungen finden Sie unter [Freigeben aus SQL-Quellen](how-to-share-from-sql.md).

## <a name="snapshot-failed"></a>Fehler bei Momentaufnahme
Die Momentaufnahme kann aus einer Vielzahl von Gründen fehlschlagen. Die ausführliche Fehlermeldung können Sie finden, indem Sie auf die Startzeit der Momentaufnahme und dann auf den Status jedes Datasets klicken. Im Folgenden finden Sie häufige Ursachen, warum keine Momentaufnahme möglich ist:

* Data Share verfügt nicht über die Berechtigung zum Lesen aus dem Quelldatenspeicher oder zum Schreiben in den Zieldatenspeicher. Weitere Informationen zu den erforderlichen Berechtigungen finden Sie unter [Rollen und Anforderungen](concepts-roles-permissions.md). Wenn Sie zum ersten Mal eine Momentaufnahme erstellen, könnte es einige Minuten dauern, bis die Data Share-Ressource Zugriff auf den Azure-Datenspeicher erhält. Warten Sie einige Minuten, und versuchen Sie es erneut.
* Die Data Share-Verbindung mit dem Quell- oder Zieldatenspeicher wird durch die Firewall blockiert.
* Das freigegebene Dataset oder der Quell- oder Zieldatenspeicher wird gelöscht.

Bei SQL-Quellen gibt es die folgenden zusätzlichen Ursachen für Momentaufnahmefehler. 

* Das SQL-Quell- oder -Zielskript zum Erteilen der Data Share-Berechtigung wird nicht ausgeführt. Möglicherweise wird es auch für Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW) mithilfe der SQL-Authentifizierung anstelle der Azure Active Directory-Authentifizierung ausgeführt.  
* Der SQL-Quell- oder -Zieldatenspeicher wurde angehalten.
* SQL-Datentypen werden weder bei der Erstellung der Momentaufnahme noch vom Zieldatenspeicher unterstützt. Ausführliche Informationen finden Sie unter [Freigeben aus SQL-Quellen](how-to-share-from-sql.md#supported-data-types).
* Der SQL-Quell- oder -Zieldatenspeicher ist durch andere Prozesse gesperrt. Azure Data Share wendet keine Sperren auf den SQL-Quell- und -Zieldatenspeicher an. Vorhandene Sperren für den SQL-Quell- und -Zieldatenspeicher führen jedoch zu einem Momentaufnahmefehler.
* Auf die SQL-Zieltabelle wird mit einer Fremdschlüsseleinschränkung verwiesen. Wenn bei der Momentaufnahme eine Zieltabelle mit demselben Namen vorhanden ist, löscht Azure Data Share die Tabelle und erstellt eine neue Tabelle. Wenn auf die SQL-Zieltabelle mit einer Fremdschlüsseleinschränkung verwiesen wird, kann die Tabelle nicht gelöscht werden.
* Die CSV-Zieldatei wird generiert, aber die Daten können nicht in Excel gelesen werden. Dies kann der Fall sein, wenn die SQL-Quelltabelle Daten mit nicht englischen Zeichen enthält. Wählen Sie in Excel die Registerkarte „Daten abrufen“ aus, wählen Sie die CSV-Datei aus, wählen Sie als Dateiursprung „65001: Unicode (UTF-8)“ aus, und laden Sie die Daten.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zum [Freigeben Ihrer Daten](share-your-data.md). 

Weitere Informationen zum Empfangen von Daten finden Sie im Tutorial [Akzeptieren und Empfangen von Daten](subscribe-to-data-share.md).