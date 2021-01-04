---
title: Schutz von Daten in Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie Ihre privaten Daten verschlüsseln, die von einem Azure Stream Analytics-Auftrag genutzt werden.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4436289d544de057acef132117346ac53c20b5a7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576497"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Schutz von Daten in Azure Stream Analytics 

Azure Stream Analytics ist eine vollständig verwaltete Platform-as-a-Service-Lösung, mit der Sie Echtzeit-Analysepipelines erstellen können. Alle aufwändigen Aufgaben, z. B. die Clusterbereitstellung, die Skalierung von Knoten zur Erfüllung Ihrer Nutzungsanforderungen und die Verwaltung interner Prüfpunkte, werden im Hintergrund verwaltet.

## <a name="private-data-assets-that-are-stored"></a>Gespeicherte private Datenressourcen

Azure Stream Analytics speichert die folgenden Metadaten und Daten zur Ausführung: 

* Abfragedefinition und deren zugehörige Konfiguration  

* Benutzerdefinierte Funktionen oder Aggregate  

* Von der Stream Analytics-Runtime benötigte Prüfpunkte

* Momentaufnahmen von Verweisdaten 

* Verbindungsdetails der Ressourcen, die von Ihrem Stream Analytics-Auftrag verwendet werden

Sie können sich weiter über die [Complianceangebote von Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) informieren, um Ihre Konformitätsverpflichtungen für regulierte Branchen oder Umgebungen zu erfüllen. 

## <a name="in-region-data-residency"></a>Regionsbezogene Data Residency
Azure Stream Analytics speichert Kundendaten und andere Metadaten, die oben beschrieben sind. Kundendaten werden von Azure Stream Analytics standardmäßig in einer einzigen Region gespeichert, sodass dieser Dienst automatisch die Anforderungen hinsichtlich regionsbezogener Data Residency erfüllt, einschließlich der im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen Anforderungen.
Außerdem können Sie auswählen, dass alle Datenassets (Kundendaten und andere Metadaten), die sich auf Ihren Stream Analytics-Auftrag beziehen, in einer einzigen Region gespeichert werden, indem Sie sie in einem Speicherkonto Ihrer Wahl verschlüsseln.

## <a name="encrypt-your-data"></a>Verschlüsseln Ihrer Daten

Für Stream Analytics werden für die gesamte Infrastruktur automatisch die bestmöglichen Verschlüsselungsstandards genutzt, um Ihre Daten zu verschlüsseln und zu schützen. Sie können bei Stream Analytics darauf vertrauen, dass Ihre gesamten Daten sicher gespeichert werden, sodass Sie sich nicht um die Verwaltung der Infrastruktur kümmern müssen.

Falls Sie für die Verschlüsselung Ihrer Daten kundenseitig verwaltete Schlüssel verwenden möchten, können Sie Ihr eigenes Speicherkonto (Universell V1 oder V2) zum Speichern der privaten Datenressourcen nutzen, die von der Stream Analytics-Runtime benötigt werden. Ihr Speicherkonto kann bei Bedarf verschlüsselt werden. Von der Stream Analytics-Infrastruktur werden Ihre privaten Datenressourcen nicht dauerhaft gespeichert. 

Die entsprechende Einstellung muss beim Erstellen eines Stream Analytics-Auftrags konfiguriert werden und ist während des Lebenszyklus des Auftrags nicht änderbar. Eine Änderung oder Löschung von Speicher, der von Ihrer Stream Analytics-Instanz genutzt wird, ist nicht zu empfehlen. Wenn Sie Ihr Speicherkonto löschen, werden alle privaten Datenressourcen dauerhaft gelöscht, und dies führt dazu, dass für Ihren Auftrag ein Fehler auftritt. 

Die Aktualisierung oder Rotation von Schlüsseln für Ihr Speicherkonto ist mit dem Stream Analytics-Portal nicht möglich. Sie können die Schlüssel mit den REST-APIs aktualisieren.


### <a name="configure-storage-account-for-private-data"></a>Konfigurieren des Speicherkontos für private Daten 

Verschlüsseln Sie Ihr Speicherkonto, um Ihre Daten zu schützen und den Speicherort Ihrer privaten Daten explizit auszuwählen. 

Sie können sich weiter über die [Complianceangebote von Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) informieren, um Ihre Konformitätsverpflichtungen für regulierte Branchen oder Umgebungen zu erfüllen. 

Führen Sie die folgenden Schritte aus, um Ihr Speicherkonto für private Datenressourcen zu konfigurieren. Diese Konfiguration erfolgt über Ihren Stream Analytics-Auftrag und nicht über Ihr Speicherkonto.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**. 

1. Wählen Sie in der Ergebnisliste **Analytics** > **Stream Analytics-Auftrag** aus. 

1. Fügen Sie auf der Seite des Stream Analytics-Auftrags die erforderlichen Details ein, z. B. Name, Region und Skalierung. 

1. Aktivieren Sie das Kontrollkästchen *Secure all private data assets needed by this job in my Storage account* (Alle privaten Datenressourcen für diesen Auftrag unter meinem Speicherkonto sichern).

1. Wählen Sie in Ihrem Abonnement ein Speicherkonto aus. Beachten Sie, dass diese Einstellung während des Lebenszyklus des Auftrags nicht geändert werden kann. Außerdem können Sie diese Option nicht mehr hinzufügen, nachdem der Auftrag erstellt wurde.

1. Um sich mit einer Verbindungszeichenfolge zu authentifizieren, wählen Sie in der Dropdownliste „Authentifizierungsmodus“ die Option **Verbindungszeichenfolge** aus. Der Speicherkontoschlüssel wird automatisch aus Ihrem Abonnement ausgefüllt.

   ![Einstellungen des Speicherkontos für private Daten](./media/data-protection/storage-account-create.png)

1. Für die Authentifizierung mit einer verwalteten Identität (Vorschau) wählen Sie in der Dropdownliste „Authentifizierungsmodus“ die Option **Verwaltete Identität** aus. Wenn Sie „Verwaltete Identität“ auswählen, müssen Sie Ihren Stream Analytics-Auftrag der Zugriffssteuerungsliste des Speicherkontos hinzufügen. Wenn Sie dem Auftrag keinen Zugriff gewähren, kann er keine Vorgänge ausführen. Weitere Informationen zum Gewähren von Zugriff finden Sie unter [Verwenden von Azure RBAC zum Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Einstellungen für private Datenspeicherkonten mit Authentifizierung durch verwaltete Identitäten":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Von Stream Analytics gespeicherte private Datenressourcen

Alle privaten Daten, die von Stream Analytics dauerhaft aufbewahrt werden müssen, werden unter Ihrem Speicherkonto gespeichert. Beispiele für private Datenressourcen: 

* Von Ihnen erstellte Abfragen und zugehörige Konfigurationen  

* Benutzerdefinierte Funktionen 

* Von der Stream Analytics-Runtime benötigte Prüfpunkte

* Momentaufnahmen von Verweisdaten 

Verbindungsdetails Ihrer Ressourcen, die von Ihrem Stream Analytics-Auftrag genutzt werden, werden ebenfalls gespeichert. Verschlüsseln Sie Ihr Speicherkonto, um Ihre gesamten Daten zu schützen. 

Sie können sich weiter über die [Complianceangebote von Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) informieren, um Ihre Konformitätsverpflichtungen für regulierte Branchen oder Umgebungen zu erfüllen. 

## <a name="enables-data-residency"></a>Ermöglicht Data Residency 
Sie können dieses Feature verwenden, um Ihre Data Residency-Anforderungen durchzusetzen, indem Sie ein entsprechendes Speicherkonto bereitstellen.

## <a name="known-issues"></a>Bekannte Probleme
Es gibt ein bekanntes Problem, bei dem ein Auftrag, bei dem ein vom Kunden verwalteter Schlüssel verwendet wird, zu Fehlern führt, wenn die verwaltete Identität zur Authentifizierung bei Ein- oder Ausgaben verwendet wird. 

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Sie ein Azure Storage-Konto.](../storage/common/storage-account-create.md)
* [Grundlegendes zu Eingaben für Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Begriffe zu Prüfpunkten und zur Wiedergabe bei Azure Stream Analytics-Aufträgen](stream-analytics-concepts-checkpoint-replay.md)
* [Verwenden von Referenzdaten für Suchvorgänge in Stream Analytics](stream-analytics-use-reference-data.md)
