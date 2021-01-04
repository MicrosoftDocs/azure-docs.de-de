---
title: Konfigurieren von Metrics Advisor-Warnungen
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie Ihre Metrics Advisor-Warnungen konfigurieren, indem Sie Hooks für E-Mail, Web und Azure DevOps verwenden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420919"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Gewusst wie: Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook

Nachdem mit Metrics Advisor eine Anomalie erkannt wurde, wird basierend auf den Warnungseinstellungen über einen Hook eine Warnungsbenachrichtigung ausgelöst. Eine Warnungseinstellung kann mit unterschiedlichen Konfigurationen für die Erkennung verwendet werden, und es sind verschiedene Parameter für die Anpassung Ihrer Warnungsregel verfügbar.

## <a name="create-a-hook"></a>Erstellen eines Hooks

Metrics Advisor unterstützt drei unterschiedliche Arten von Hooks: E-Mail-Hook, Webhook und Azure DevOps. Sie können das Verfahren wählen, das für Ihr jeweiliges Szenario am besten geeignet ist.       

### <a name="email-hook"></a>E-Mail-Hook

> [!Note]
> Metrics Advisor-Ressourcenadministratoren müssen die E-Mail-Einstellungen konfigurieren und SMTP-bezogene Informationen in Metrics Advisor eingeben, bevor Anomaliewarnungen gesendet werden können. Der Administrator der Ressourcengruppe oder der Abonnementadministrator muss mindestens eine Rolle des *Cognitive Services Metrics Advisor-Administrators* auf der Registerkarte zur Zugriffssteuerung der Metrics Advisor-Ressource zuweisen. [Erfahren Sie mehr über die Konfiguration der E-Mail-Einstellungen](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Für die Erstellung eines E-Mail-Hooks sind die folgenden Parameter verfügbar: 

Ein E-Mail-Hook ist der Kanal, über den Anomaliewarnungen an E-Mail-Adressen gesendet werden, die im Abschnitt **Email to** (E-Mail an) angegeben sind. Es werden zwei Arten von Benachrichtigungs-E-Mails gesendet: Warnungen vom Typ *Datenfeed nicht verfügbar* und *Incidentberichte* mit einer oder mehreren Anomalien. 

|Parameter |BESCHREIBUNG  |
|---------|---------|
| name | Name des E-Mail-Hooks |
| Email to (E-Mail an)| E-Mail-Adressen, an die eine Warnung gesendet wird|
| Externer Link | Optionales Feld, das eine angepasste Umleitung ermöglicht, z. B. für Hinweise zur Problembehandlung. |
| Titel der angepassten Anomaliewarnung | Die Titelvorlage unterstützt Folgendes: `${severity}`, `${alertSettingName}`, `${datafeedName}`, `${metricName}`, `${detectConfigName}`, `${timestamp}`, `${topDimension}`, `${incidentCount}`, `${anomalyCount}`

Wenn Sie auf **OK** klicken, wird ein E-Mail-Hook erstellt. Sie können ihn für alle Warnungseinstellungen verwenden, um Anomaliewarnungen zu erhalten. 

### <a name="web-hook"></a>Webhook

> [!Note]
> * Verwenden Sie die Anforderungsmethode **POST**.
> * Der Anforderungstext lautet etwa wie folgt:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Wenn ein Webhook erstellt oder geändert wird, wird die API testweise mit einem leeren Anforderungstext aufgerufen. Ihre API muss den HTTP-Fehlercode „200“ zurückgeben.

Ein Webhook ist der Einstiegspunkt für alle Informationen, die über den Metrics Advisor-Dienst verfügbar sind. Wenn eine Warnung ausgelöst wird, ruft er eine vom Benutzer bereitgestellte API auf. Alle Warnungen können über einen Webhook gesendet werden.

Zum Erstellen eines Webhooks müssen Sie die folgenden Informationen hinzufügen:

|Parameter |BESCHREIBUNG  |
|---------|---------|
|Endpunkt     | Die API-Adresse, die bei Auslösung einer Warnung aufgerufen werden soll.        |
|Benutzername und Kennwort | Dient zum Authentifizieren gegenüber der API-Adresse. Lassen Sie dies unverändert, falls keine Authentifizierung erforderlich ist.         |
|Header     | Benutzerdefinierte Header im API-Aufruf.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Fenster für Webhookerstellung":::

Wenn eine Benachrichtigung über einen Webhook gepusht wird, können Sie die folgenden APIs verwenden, um die Details zur Warnung zu erhalten. Legen Sie *timestamp* und *alertSettingGuid* in Ihrem API-Dienst fest, für den die Pushübertragung erfolgt, und verwenden Sie anschließend die folgenden Abfragen: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor unterstützt auch die automatische Erstellung eines Arbeitselements in Azure DevOps, um bei der Erkennung von Anomalien die Probleme bzw. Fehler nachzuverfolgen. Alle Warnungen können über Azure DevOps-Hooks gesendet werden.

Zum Erstellen eines Azure DevOps-Hooks müssen Sie die unten angegebenen Informationen hinzufügen.

|Parameter |BESCHREIBUNG  |
|---------|---------|
| name | Ein Name für den Hook. |
| Organisation | Die Organisation, zu der Ihre DevOps-Instanz gehört. |
| Projekt | Das spezifische Projekt in DevOps. |
| Access Token |  Ein Token für die Authentifizierung bei DevOps. | 

> [!Note]
> Sie müssen Schreibberechtigungen gewähren, wenn Sie möchten, dass von Metrics Advisor Arbeitselemente basierend auf Anomaliewarnungen erstellt werden. Nach dem Erstellen der Hooks können Sie diese in Ihren gesamten Warnungseinstellungen verwenden. Verwalten Sie Ihre Hooks auf der Seite **Hook Settings** (Hookeinstellungen).

## <a name="add-or-edit-alert-settings"></a>Hinzufügen oder Bearbeiten von Warnungseinstellungen

Navigieren Sie zur Seite mit den Metrikdetails. Sie finden den Abschnitt **Warnungseinstellungen** unten links auf dieser Seite. Es sind alle Warnungseinstellungen aufgeführt, die zur ausgewählten Erkennungskonfiguration gehören. Wenn eine neue Erkennungskonfiguration erstellt wird, sind keine Warnungseinstellungen vorhanden, und es werden keine Warnungen gesendet.  
Sie können die Symbole **Hinzufügen**, **Bearbeiten** und **Löschen** verwenden, um Warnungseinstellungen zu ändern.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Menüelement „Warnungseinstellungen“":::

Klicken Sie auf die Schaltfläche **Hinzufügen** oder **Bearbeiten**, um ein Fenster zum Hinzufügen bzw. Bearbeiten Ihrer Warnungseinstellungen anzuzeigen.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Hinzufügen oder Bearbeiten von Warnungseinstellungen":::

**Alert setting name** (Name der Warnungseinstellung): Der Name der jeweiligen Warnungseinstellung. Er wird im Titel der Benachrichtigungs-E-Mail angezeigt.

**Hooks**: Die Liste mit den Hooks, an die Warnungen gesendet werden sollen.

Der im obigen Screenshot gekennzeichnete Abschnitt enthält die Einstellungen für eine Erkennungskonfiguration. Sie können für die einzelnen Erkennungskonfigurationen unterschiedliche Warnungseinstellungen festlegen. Wählen Sie die Zielkonfiguration aus, indem Sie in diesem Fenster die dritte Dropdownliste verwenden. 

### <a name="filter-settings"></a>Filtereinstellungen 

Unten sind die Filtereinstellungen für eine Erkennungskonfiguration angegeben.

**Warnung für** umfasst vier Optionen zum Filtern von Anomalien:

* **Anomalies in all series** (Anomalien in allen Reihen): Alle Anomalien werden in die Warnung eingeschlossen.         
* **Anomalies in the series group** (Anomalien in der Reihengruppe): Filtert Reihen nach Dimensionswerten. Legen Sie bestimmte Werte für einige Dimensionen fest. Anomalien sind nur dann in der Warnung enthalten, wenn die Reihe mit dem angegebenen Wert übereinstimmt.       
* **Anomalies in favorite series** (Anomalien in bevorzugten Reihen): Nur die als Favorit gekennzeichneten Reihen sind in der Warnung enthalten.        |
* **Anomalies in top N of all series** (Oberste „n“ Anomalien aller Reihen): Dieser Filter wird für den Fall verwendet, dass Sie nur die Reihen benötigen, deren Wert sich innerhalb der obersten „n“ Werte befindet. Wir gehen dann um einige Zeitstempel zurück und überprüfen, ob der Wert der Reihe für diese Zeitstempel in den obersten „n“ Werten enthalten ist. Falls die Anzahl von „Obere n“ größer als die angegebene Anzahl ist, ist die Anomalie in einer Warnung enthalten.        |

**Filter anomaly options** (Anomalieoptionen filtern) ist ein weiterer Filter mit den folgenden Optionen:

- **Severity** (Schweregrad): Die Anomalie ist nur dann enthalten, wenn ihr Schweregrad innerhalb des angegebenen Bereichs liegt.
- **Snooze** (Zurückstellen): Die Anzeige von Warnungen wird für die Anomalien der nächsten „n“ Punkte (Zeitraum) beendet, wenn diese im Rahmen einer Warnung ausgelöst werden.
    - **Snooze Type** (Art der Zurückstellung): Bei **Series** (Reihe) wird bei einer ausgelösten Anomalie nur die Reihe zurückgestellt. Bei **Metric** (Metrik) werden bei einer ausgelösten Anomalie alle Reihen der Metrik zurückgestellt.
    - **Snooze Number** (Anzahl für Zurückstellung): Gibt die Anzahl von Punkten (Zeitraum) für die Zurückstellung an.
    - **Reset for non-successive** (Zurücksetzen, falls nicht aufeinanderfolgend): Wenn diese Option aktiviert ist, werden bei einer ausgelösten Anomalie nur die nächsten „N“ aufeinanderfolgenden Anomalien zurückgestellt. Falls es sich bei einem der folgenden Datenpunkte nicht um eine Anomalie handelt, wird die Zurückstellung ab diesem Punkt zurückgesetzt. Wenn diese Option nicht ausgewählt ist, werden bei einer ausgelösten Anomalie auch dann die nächsten „n“ Punkte (Zeitraum) zurückgestellt, wenn es sich bei den aufeinanderfolgenden Datenpunkten nicht um Anomalien handelt.
- **Value** (Wert) (optional): Dient zum Filtern nach Wert. Nur bei Punktwerten, die die Bedingung erfüllen, wird die Anomalie berücksichtigt. Wenn Sie den entsprechenden Wert einer anderen Metrik verwenden, sollten die Dimensionsnamen der beiden Metriken konsistent sein.

Anomalien, die nicht herausgefiltert werden, werden als Teil einer Warnung gesendet.

### <a name="add-cross-metric-settings"></a>Hinzufügen von metrikübergreifenden Einstellungen

Klicken Sie auf der Seite mit den Warnungseinstellungen auf **+ Add cross-metric settings** (+ Metrikübergreifende Einstellungen hinzufügen), um einen weiteren Abschnitt hinzuzufügen.

Die Auswahloption **Operator** stellt die logische Beziehung der einzelnen Abschnitte dar, um ermitteln zu können, ob jeweils eine Warnung gesendet wird.


|Operator  |Beschreibung  |
|---------|---------|
|UND     | Eine Warnung wird nur gesendet, wenn eine Reihe mit jedem Warnungsabschnitt übereinstimmt und es sich bei allen Datenpunkten um Anomalien handelt. Wenn die Metriken unterschiedliche Dimensionsnamen aufweisen, wird nie eine Warnung ausgelöst.         |
|oder     | Die Warnung wird gesendet, wenn mindestens ein Abschnitt Anomalien enthält.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operator für Abschnitt mit mehreren Warnungseinstellungen":::

## <a name="next-steps"></a>Nächste Schritte

- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Diagnostizieren eines Incidents](diagnose-incident.md)
- [Konfigurieren von Metriken und Optimieren der Konfigurationserkennung](configure-metrics.md)