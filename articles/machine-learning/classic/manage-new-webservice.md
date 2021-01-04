---
title: 'ML Studio (Classic): Verwalten von Webdiensten – Azure'
description: Verwalten Sie Ihre neuen und klassischen Webdienste mit maschinellem Lernen im Microsoft Azure Machine Learning Web Services-Portal. Da klassische und neue Webdienste auf unterschiedlichen zugrunde liegenden Technologien basieren, gibt es für beide geringfügig andere Verwaltungsfunktionen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 37f10cc8eb3d606d99eb5b3f821a486e9e4ea2fe
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322347"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Verwalten eines Webdiensts im Azure Machine Learning Studio (klassisch) Web Services-Portal

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Im Microsoft Azure Machine Learning Web Services-Portal können Sie Ihre neuen und klassischen Machine Learning-Webdienste verwalten. Da klassische und neue Webdienste auf unterschiedlichen zugrunde liegenden Technologien basieren, gibt es für beide geringfügig andere Verwaltungsfunktionen.

Das Machine Learning Web Services-Portal bietet diese Möglichkeiten:

* Überwachen der Nutzung des Webdiensts
* Konfigurieren der Beschreibung, Aktualisieren der Schlüssel für den Webdienst (nur neu) und des Schlüssels Ihres Speicherkontos (nur neu), Aktivieren der Protokollierung und Aktivieren oder Deaktivieren von Beispieldaten
* Löschen des Webdiensts
* Erstellen, Löschen und Aktualisieren von Abrechnungsplänen (nur neu)
* Hinzufügen und Löschen von Endpunkten (nur klassisch)

>[!NOTE]
>Klassische Webdienste können Sie auch in [Machine Learning Studio (klassisch)](https://studio.azureml.net) auf der Registerkarte **Webdienste** verwalten.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Berechtigungen zum Verwalten von neuen Resource Manager-basierten Webdiensten

Neue Webdienste werden als Azure-Ressourcen bereitgestellt. Daher benötigen Sie die erforderlichen Berechtigungen zum Bereitstellen und Verwalten neuer Webdienste.  Zum Bereitstellen oder Verwalten neuer Webdienste muss Ihnen in dem Abonnement, für das der Webdienst bereitgestellt wird, eine Teilnehmer- oder Administratorrolle zugewiesen sein. Wenn Sie andere Benutzer in einen Machine Learning-Arbeitsbereich einladen, müssen Sie ihnen eine Teilnehmer- oder Administratorrolle im Abonnement zuweisen, damit sie Webdienste bereitstellen oder verwalten können. 

Wenn die Benutzer nicht über die korrekten Berechtigungen verfügen, um auf Ressourcen im Azure Machine Learning Web Services-Portal zuzugreifen, erhalten Sie beim Versuch einen Webdienst bereitzustellen folgende Fehlermeldung:

*Web Service deployment failed (Bereitstellung des Webdiensts ist fehlgeschlagen). This account does not have sufficient access to the Azure subscription that contains the Workspace (Dieses Konto besitzt keine ausreichenden Zugriffsberechtigungen für das Azure-Abonnement mit dem Workspace). In order to deploy a Web Service to Azure, the same account must be invited to the Workspace and be given access to the Azure subscription that contains the Workspace.* (Für die Bereitstellung eines Webdiensts in Azure muss das gleiche Konto für den Workspace eingeladen werden und Zugriff auf das Azure-Abonnement mit dem Workspace erhalten).

Weitere Informationen zum Erstellen eines Arbeitsbereichs finden Sie unter [Erstellen und Freigeben eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](create-workspace.md).

Weitere Informationen zu Festlegen von Zugriffsberechtigungen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Verwalten neuer Webdienste
So verwalten Sie Ihre neuen Webdienste

1. Melden Sie sich mit Ihrem Microsoft Azure-Konto beim [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)-Portal an. Verwenden Sie hierzu das Konto, das dem Azure-Abonnement zugeordnet ist.
2. Klicken Sie im Menü auf **Web Services**.

Eine Liste der für Ihr Abonnement bereitgestellten Webdienste wird angezeigt. 

Um einen Webdienst zu verwalten, klicken Sie auf „Web Services“. Auf der Seite „Web Services“ können Sie folgende Aktionen ausführen:

* Auf den Webdienst klicken, um ihn zu verwalten
* Auf den Abrechnungsplan des Webdiensts klicken, um ihn zu aktualisieren
* einen Webdienst löschen
* einen Webdienst kopieren und in einer anderen Region bereitstellen

Wenn Sie auf einen Webdienst klicken, wird die Seite „Quickstart“ des Webdiensts geöffnet. Die Seite „Quickstart“ des Webdienst bietet zwei Menüoptionen, die Ihnen das Verwalten des Webdiensts ermöglichen:

* **DASHBOARD** : Hier können Sie die Nutzung des Webdiensts anzeigen.
* **CONFIGURE** : Ermöglicht das Hinzufügen von beschreibendem Text, das Aktualisieren des Schlüssels des Speicherkontos, das dem Webdienst zugeordnet ist, sowie das Aktivieren und Deaktivieren von Beispieldaten.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Überwachen der Nutzung des Webdiensts
Klicken Sie auf die Registerkarte **DASHBOARD** .

Im Dashboard können Sie die Gesamtnutzung des Webdiensts über einen Zeitraum anzeigen. In der Dropdownliste „Period“ rechts oben in den Nutzungsdiagrammen können Sie den Zeitraum auswählen. Im Dashboard werden die folgenden Informationen angezeigt:

* **Requests Over Time** zeigt ein Diagramm der Anzahl der Anforderungen im ausgewählten Zeitraum an. Es dient zum Bestimmen von Nutzungsspitzen.
* **Request-Response Requests** zeigt die Gesamtanzahl der „Request-Response“-Aufrufe, die der Dienst im ausgewählten Zeitraum empfangen hat, und die Anzahl der Fehler an.
* **Average Request-Response Compute Time** zeigt die Durchschnittszeit an, die zum Ausführen der empfangenen Anforderungen benötigt wurde.
* **Batch Requests** zeigt die Gesamtanzahl der Batchanforderungen, die der Dienst im ausgewählten Zeitraum empfangen hat, und die Anzahl der Fehler an.
* **Average Job Latency** zeigt die Durchschnittszeit an, die zum Ausführen der empfangenen Anforderungen benötigt wurde.
* **Errors** zeigt die kumulierte Anzahl der Fehler, die bei Aufrufen des Webdiensts aufgetreten sind.
* **Services Costs** zeigt die Kosten für den Abrechnungsplan, der dem Dienst zugeordnet ist.

### <a name="configuring-the-web-service"></a>Konfigurieren des Webdiensts
Klicken Sie auf die Menüoption **CONFIGURE** .

Sie können die folgenden Eigenschaften aktualisieren:

* In **Description** können Sie eine Beschreibung des Webdiensts eingeben.
* In **Title** können Sie einen Titel für den Webdienst eingeben.
* **Keys** können Sie den primären und die sekundären API-Schlüssel rotieren lassen.
* In **Storage account key** können Sie den Schlüssel des Speicherkontos aktualisieren, das dem Webdienst zugeordnet ist. 
* **Enable Sample data** ermöglicht das Bereitstellen von Beispieldaten, die Sie mit dem Request-Response-Dienst testen können. Wenn Sie den Webdienst in Machine Learning Studio (klassisch) erstellt haben, stammen die Beispieldaten aus den Daten, mit denen Sie Ihr Modell trainiert haben. Wenn Sie den Dienst programmgesteuert erstellt haben, stammen die Daten aus den Beispieldaten, die Sie als Teil des JSON-Pakets bereitgestellt haben.

### <a name="managing-billing-plans"></a>Verwalten von Abrechnungsplänen
Klicken Sie auf der Seite „Quickstart“ des Webdiensts auf die Menüoption **Plans** . Sie können auch auf den Plan klicken, der einem bestimmten Webdienst zugeordnet ist, um diesen Plan zu verwalten.

* **New** ermöglicht das Erstellen eines neuen Plans.
* **Add/Remove Plan instance** können Sie einen vorhandenen Plan aufskalieren, um Kapazität hinzuzufügen.
* **Upgrade/Downgrade** können Sie einen vorhandenen Plan hochskalieren, um Kapazität hinzuzufügen.
* **Delete** ermöglicht das Löschen eines Plans.

Klicken Sie auf einen Plan, um dessen Dashboard anzuzeigen. Das Dashboard zeigt eine Momentaufnahme der Plannutzung in einem ausgewählten Zeitraum. Klicken Sie rechts oben im Dashboard auf die Dropdownliste **Period** , um den anzuzeigenden Zeitraum auszuwählen. 

Das Dashboard des Plans enthält die folgenden Informationen:

* **Plan Description** zeigt Informationen zu den Kosten und zur Kapazität des Plans.
* **Plan Usage** zeigt die Anzahl der Transaktionen und Computestunden, die für den Plan in Rechnung gestellt wurden.
* **Web Services** zeigt die Anzahl der Webdienste, die den Plan verwenden.
* **Top Web Service By Calls** zeigt die vier Webdienste, die die meisten Aufrufe vornehmen, die für den Plan in Rechnung gestellt werden.
* **Top Web Services by Compute Hrs** zeigt die vier Webdienste, die die meisten Computeressourcen nutzen, die für den Plan in Rechnung gestellt werden.

## <a name="manage-classic-web-services"></a>Verwalten von klassischen Webdiensten
> [!NOTE]
> Die Verfahren in diesem Abschnitt gelten für das Verwalten klassischer Webdienste im Azure Machine Learning Web Services-Portal. Informationen zum Verwalten klassischer Webdienste über Machine Learning Studio (klassisch) und das Azure-Portal finden Sie unter [Verwalten eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](manage-workspace.md).
> 
> 

So verwalten Sie Ihre klassischen Webdienste

1. Melden Sie sich mit Ihrem Microsoft Azure-Konto beim [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)-Portal an. Verwenden Sie hierzu das Konto, das dem Azure-Abonnement zugeordnet ist.
2. Klicken Sie im Menü auf **Classic Web Services**.

Klicken Sie zum Verwalten eines klassischen Webdiensts auf **Classic Web Services**. Auf der Seite „Classic Web Services“ können Sie folgende Aktionen ausführen:

* auf den Webdienst klicken, um die zugehörigen Endpunkte anzuzeigen
* einen Webdienst löschen

Wenn Sie einen klassischen Webdienst verwalten, verwalten Sie die einzelnen Endpunkte getrennt. Wenn Sie auf der Seite „Web Services“ auf einen Webdienst klicken, wird eine Liste der Endpunkte geöffnet, die dem Dienst zugeordnet sind. 

Auf der Seite „Classic Web Service endpoint“ können Sie Endpunkte für den Dienst hinzufügen und löschen. Weitere Informationen zum Hinzufügen von Endpunkten finden Sie unter [Erstellen von Endpunkten](create-endpoint.md).

Klicken Sie auf einen der Endpunkte, um die Webdienstseite „Quickstart“ zu öffnen. Die Seite „Quickstart“ bietet zwei Menüoptionen, die Ihnen das Verwalten des Webdiensts ermöglichen:

* **DASHBOARD** : Hier können Sie die Nutzung des Webdiensts anzeigen.
* **CONFIGURE** : Ermöglicht das Hinzufügen von beschreibendem Text, das Ein- und Ausschalten der Fehlerprotokollierung, das Aktualisieren des Schlüssels des Speicherkontos, das dem Webdienst zugeordnet ist, sowie das Aktivieren und Deaktivieren von Beispieldaten.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Überwachen der Nutzung des Webdiensts
Klicken Sie auf die Registerkarte **DASHBOARD** .

Im Dashboard können Sie die Gesamtnutzung des Webdiensts über einen Zeitraum anzeigen. In der Dropdownliste „Period“ rechts oben in den Nutzungsdiagrammen können Sie den Zeitraum auswählen. Im Dashboard werden die folgenden Informationen angezeigt:

* **Requests Over Time** zeigt ein Diagramm der Anzahl der Anforderungen im ausgewählten Zeitraum an. Es dient zum Bestimmen von Nutzungsspitzen.
* **Request-Response Requests** zeigt die Gesamtanzahl der „Request-Response“-Aufrufe, die der Dienst im ausgewählten Zeitraum empfangen hat, und die Anzahl der Fehler an.
* **Average Request-Response Compute Time** zeigt die Durchschnittszeit an, die zum Ausführen der empfangenen Anforderungen benötigt wurde.
* **Batch Requests** zeigt die Gesamtanzahl der Batchanforderungen, die der Dienst im ausgewählten Zeitraum empfangen hat, und die Anzahl der Fehler an.
* **Average Job Latency** zeigt die Durchschnittszeit an, die zum Ausführen der empfangenen Anforderungen benötigt wurde.
* **Errors** zeigt die kumulierte Anzahl der Fehler, die bei Aufrufen des Webdiensts aufgetreten sind.
* **Services Costs** zeigt die Kosten für den Abrechnungsplan, der dem Dienst zugeordnet ist.

### <a name="configuring-the-web-service"></a>Konfigurieren des Webdiensts
Klicken Sie auf die Menüoption **CONFIGURE** .

Sie können die folgenden Eigenschaften aktualisieren:

* In **Description** können Sie eine Beschreibung des Webdiensts eingeben. „Description“ ist ein Pflichtfeld.
* **Logging** ermöglicht Ihnen das Aktivieren bzw. Deaktivieren der Fehlerprotokollierung für den Endpunkt. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Protokollierung für Machine Learning-Webdienste](web-services-logging.md).
* **Enable Sample data** ermöglicht das Bereitstellen von Beispieldaten, die Sie mit dem Request-Response-Dienst testen können. Wenn Sie den Webdienst in Machine Learning Studio (klassisch) erstellt haben, stammen die Beispieldaten aus den Daten, mit denen Sie Ihr Modell trainiert haben. Wenn Sie den Dienst programmgesteuert erstellt haben, stammen die Daten aus den Beispieldaten, die Sie als Teil des JSON-Pakets bereitgestellt haben.