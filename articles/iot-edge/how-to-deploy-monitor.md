---
title: Erstellen automatischer Bereitstellungen im Azure-Portal – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden des Azure-Portals zum Erstellen automatischer Bereitstellungen für Gruppen von IoT Edge-Geräten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 69ba0a882c0e52e7c0d063b8f77e7a0fe22526a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126362"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Mit Azure IoT Edge können Sie Analysen in die Edge-Ebene verlagern und erhalten eine Cloudschnittstelle, um Ihre IoT Edge-Geräte auch ohne physischen Zugriff darauf zu verwalten und zu überwachen. Die Möglichkeit zur Remoteverwaltung von Geräten wird immer wichtiger, weil Lösungen für das Internet der Dinge immer umfangreicher und komplexer werden. Azure IoT Edge wurde entwickelt, um Ihre Unternehmensziele unabhängig davon zu unterstützen, wie viele Geräte Sie hinzufügen.

Sie können einzelne Geräte verwalten und die dafür erforderlichen Modelle einzeln bereitstellen. Wenn Sie jedoch Änderungen an Geräten in großem Umfang vornehmen möchten, können Sie eine **automatische IoT Edge-Bereitstellung** erstellen, das Teil der automatischen Geräteverwaltung in IoT Hub ist. Bereitstellungen sind dynamische Prozesse, mit denen Sie mehrere Module gleichzeitig auf mehreren Geräten bereitstellen, Status und Integrität der Module nachverfolgen und bei Bedarf Änderungen vornehmen können. 

## <a name="identify-devices-using-tags"></a>Identifizieren von Geräten mithilfe von Tags

Bevor Sie eine Bereitstellung erstellen können, müssen Sie angeben können, welche Geräte Sie ansprechen möchten. Azure IoT Edge erkennt Geräte anhand von **Tags** im Gerätezwilling. Jedes Gerät kann mehrere Tags aufweisen, und Sie können diese auf jede beliebige Weise definieren, die für Ihre Lösung sinnvoll erscheint. Wenn Sie beispielsweise einen Campus mit intelligenten Gebäuden verwalten, können Sie einem Gerät etwa die folgenden Tags hinzufügen:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Weitere Informationen zu Gerätezwillingen und Tags finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Erstellen einer Bereitstellung

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 
1. Wählen Sie **IoT Edge** aus.
1. Wählen Sie **IoT Edge-Bereitstellung hinzufügen** aus.

Zum Erstellen einer Bereitstellung müssen fünf Schritte ausgeführt werden. Diese werden in den folgenden Abschnitten exemplarisch beschrieben. 

### <a name="step-1-name-and-label"></a>Schritt 1: Name und Bezeichnung

1. Geben Sie Ihrer Bereitstellung einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.
1. Sie können Bezeichnungen als Schlüssel-Wert-Paare hinzufügen, um Ihre Bereitstellungen im Blick zu behalten. Beispiel: **HostPlatform** und **Linux** oder **Version** und **3.0.1**.
1. Klicken Sie auf **Weiter**, um mit Schritt 2 fortzufahren. 

### <a name="step-2-add-modules-optional"></a>Schritt 2: Hinzufügen von Modulen (optional)

Es gibt zwei Arten von Modulen, die Sie einer Bereitstellung hinzufügen können. Das erste Modul basiert dabei auf einem Azure-Dienst wie Storage Account oder Stream Analytics. Das zweite Modul verwendet Ihren eigenen Code. Sie können einer Bereitstellung mehrere Module beider Typen hinzufügen. 

Wenn Sie eine Bereitstellung ohne Module erstellen, werden alle aktuellen Module von den Geräten entfernt. 

>[!NOTE]
>Azure Functions unterstützt die automatisierte Azure-Dienstbereitstellung noch nicht. Verwenden Sie die benutzerdefinierte Modulbereitstellung, um diesen Dienst manuell zu Ihrer Bereitstellung hinzuzufügen. 

Führen Sie die folgenden Schritte aus, um ein Modul aus Azure Stream Analytics hinzuzufügen:

1. Klicken Sie im Abschnitt **Bereitstellungsmodule** der Seite auf **Hinzufügen**.
1. Klicken Sie auf **Azure Stream Analytics-Modul**.
1. Wählen Sie im Dropdownmenü Ihr **Abonnement** aus.
1. Wählen Sie im Dropdownmenü Ihren **Edgeauftrag** aus.
1. Klicken Sie auf **Speichern**, um Ihr Modul zur Bereitstellung hinzuzufügen. 

So fügen Sie benutzerdefinierten Code als Modul oder aber ein Azure-Dienstmodul manuell hinzu:

1. Geben Sie im Abschnitt **Container Registry-Einstellungen** der Seite die Namen und Anmeldeinformationen für private Containerregistrierungen an, die die Modulimages für diese Bereitstellung enthalten. Der Edge-Agent gibt den Fehler 500 zurück, wenn die Anmeldeinformationen einer Containerregistrierung für ein Docker-Image nicht gefunden wurden.
1. Klicken Sie im Abschnitt **Bereitstellungsmodule** der Seite auf **Hinzufügen**.
1. Klicken Sie auf **IoT Edge-Modul**.
1. Geben Sie dem Modul einen **Namen**.
1. Geben Sie im Feld **Image-URI** das Containerimage für Ihr Modul ein. 
1. Legen Sie alle **Optionen zur Containererstellung** fest, die an den Container übergeben werden sollen. Weitere Informationen finden Sie unter [docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Wählen Sie über das Dropdownmenü eine **Neustartrichtlinie** aus. Sie können zwischen folgenden Optionen wählen: 
   * **Immer**: Das Modul wird stets neu gestartet, wenn es aus irgendeinem Grund beendet wird.
   * **Nie**: Das Modul wird niemals neu gestartet, wenn es aus irgendeinem Grund beendet wird.
   * **On-failed** (Bei Absturz): Das Modul wird neu gestartet, wenn es abgestürzt ist, aber nicht ordnungsgemäß heruntergefahren wurde. 
   * **On-unhealthy** (Bei Fehler): Das Modul wird neu gestartet, wenn es abstürzt oder einen Integritätsfehlerstatus zurückgibt. Die Implementierung der Integritätsstatusfunktion ist modulspezifisch unterschiedlich. 
1. Wählen Sie über das Dropdownmenü den **Gewünschten Status** für das Modul aus. Sie können zwischen folgenden Optionen wählen:
   * **Wird ausgeführt**: Dies ist die Standardoption. Das Modul wird unmittelbar nach Bereitstellung gestartet.
   * **Beendet**: Nach der Bereitstellung verbleibt das Modul im Leerlauf, bis der Start durch Sie oder ein anderes Modul ausgelöst wird.
1. Klicken Sie auf **Gewünschte Eigenschaften für Modulzwilling festlegen**, wenn Sie Tags oder andere Eigenschaften zum Modulzwilling hinzufügen möchten.
1. Geben Sie **Umgebungsvariablen** für dieses Modul ein. Umgebungsvariablen stellen zusätzliche Informationen für ein Modul bereit und erleichtern so den Konfigurationsprozess.
1. Klicken Sie auf **Speichern**, um Ihr Modul zur Bereitstellung hinzuzufügen. 

Nachdem Sie alle Module für eine Bereitstellung konfiguriert haben, klicken Sie auf **Weiter**, um mit Schritt 3 fortzufahren.

### <a name="step-3-specify-routes-optional"></a>Schritt 3: Angeben von Routen (optional)

Routen definieren, wie Module innerhalb einer Bereitstellung miteinander kommunizieren. Standardmäßig erhalten Sie vom Assistenten eine Route mit dem Namen **route**, die als **FROM /\* INTO $upstream** definiert ist. Dies bedeutet, dass alle Nachrichten, die von Modulen ausgegeben werden, an Ihren IoT Hub gesendet werden.  

Fügen Sie die Routen mit Informationen aus dem Abschnitt [Deklarieren von Routen](module-composition.md#declare-routes) hinzu, oder aktualisieren Sie diese, und wählen Sie dann **Weiter** aus, um mit dem Abschnitt zur Überprüfung fortzufahren.

### <a name="step-4-specify-metrics-optional"></a>Schritt 4: Angeben von Metriken (optional)

Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die ein Gerät nach dem Anwenden von Konfigurationsinhalten möglicherweise zurückmeldet.

1. Geben Sie unter **Metrikname** einen Namen ein.

1. Geben Sie eine Abfrage unter **Metrikkriterien** ein. Diese Abfrage basiert auf [gemeldeten Eigenschaften](module-edgeagent-edgehub.md#edgehub-reported-properties) eines IoT Edge-Hub-Modulzwillings. Die Metrik stellt die Anzahl der von der Abfrage zurückgegebenen Zeilen dar.

Beispiel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="step-5-target-devices"></a>Schritt 5: Zielgeräte

Mit der Tageigenschaft Ihrer Geräte wählen Sie bestimmte Geräte als Ziele aus, die diese Bereitstellung erhalten sollen. 

Da mehrere Bereitstellungen dasselbe Gerät als Ziel verwenden können, sollten Sie für jede Bereitstellung eine Priorität festlegen. Tritt irgendwann ein Konflikt auf, dann „gewinnt“ die Bereitstellung mit der höchsten Priorität (höhere Werte deuten auf eine höhere Priorität hin). Haben zwei Bereitstellungen dieselbe Priorität, dann wird jeweils diejenige verwendet, die später erstellt wurde. 

1. Geben Sie eine positive ganze Zahl als **Priorität** für die Bereitstellung ein. Wenn mindestens zwei Bereitstellungen auf dasselbe Gerät ausgerichtet sind, wird die Bereitstellung mit dem höchsten numerischen Wert für die Priorität angewendet.
1. Geben Sie unter **Zielbedingung** eine Bedingung ein, um festzulegen, auf welche Geräte diese Bereitstellung angewendet werden soll. Die Bedingung basiert auf den Gerätezwillingstags oder auf den gemeldeten Gerätezwillingseigenschaften und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test'` oder `properties.reported.devicemodel='4000x'`. 
1. Klicken Sie auf **Weiter**, um mit dem letzten Schritt fortzufahren.

### <a name="step-6-review-deployment"></a>Schritt 6: Bereitstellung überprüfen

Überprüfen Sie die Bereitstellungsinformationen, und klicken Sie dann auf **Senden**.

## <a name="deploy-modules-from-azure-marketplace"></a>Bereitstellen von Modulen aus Azure Marketplace

Azure Marketplace ist ein Onlinemarktplatz für Anwendungen und Dienste, in dem sie eine breite Palette an Unternehmensanwendungen und -lösungen durchsuchen können, die für die Ausführung unter Azure, einschließlich [IoT Edge-Modulen](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules), zertifiziert und optimiert sind. Sie können auch über das Azure-Portal unter **Ressource erstellen** auf den Azure Marketplace zugreifen.

Sie können ein IoT Edge-Modul entweder über Azure Marketplace oder das Azure-Portal folgendermaßen installieren:

1. Suchen Sie ein Modul, und beginnen Sie den Bereitstellungsprozess.

   * Azure-Portal: Suchen Sie ein Modul, und wählen Sie **Erstellen** aus.

   * Azure Marketplace:

     1. Suchen Sie ein Modul, und wählen Sie **Jetzt herunterladen** aus.
     1. Bestätigen Sie die Nutzungsbedingungen und Datenschutzrichtlinie des Anbieters durch Auswählen von **Weiter**.

1. Wählen Sie Ihr Abonnement und den IoT-Hub aus, der mit dem Zielgerät verbunden ist.

1. Wählen Sie **Bedarfsorientiertes Bereitstellen**.

1. Legen Sie fest, ob das Modul einer neuen Bereitstellung oder einem Klon einer vorhandenen Bereitstellung hinzugefügt werden soll; wählen Sie für die Klonoption die vorhandene Bereitstellung aus der Liste aus.

1. Wählen Sie **Erstellen**, um mit der Erstellung einer bedarfsorientierten Bereitstellung fortzufahren. Sie können die gleichen Details angeben, wie für jede Bereitstellung.

## <a name="monitor-a-deployment"></a>Überwachen einer Bereitstellung

So zeigen Sie ausführliche Informationen zu einer Bereitstellung an und überwachen die Geräte, die diese ausführen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub. 
1. Wählen Sie **IoT Edge** aus.
1. Wählen Sie **IoT Edge-Bereitstellungen** aus. 

   ![Anzeigen von IoT Edge-Bereitstellungen](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Prüfen Sie die Bereitstellungsliste. Für jede Bereitstellung können Sie die folgenden Details anzeigen:
   * **ID**: Name der Bereitstellung
   * **Zielbedingung**: Tag, das zur Definition von Zielgeräten verwendet wird
   * **Priorität**: Prioritätsnummer, die der Bereitstellung zugewiesen wurde
   * Mit **System metrics** - **Targeted** (Systemmetriken – Ziel) wird die Anzahl von Gerätezwillingen in IoT Hub angegeben, die die Zielbedingung erfüllen, und **Angewendet** gibt die Anzahl von Geräten an, auf deren Gerätezwillinge in IoT Hub die Bereitstellungsinhalte angewendet wurde. 
   * **Gerätemetriken**: Die Anzahl von Edge-Geräten in der Bereitstellung, für die von der IoT Edge-Clientruntime eine Erfolgs- oder Fehlermeldung ausgegeben wurde.
   * **Benutzerdefinierte Metriken**: Die Anzahl der Edge-Geräte in der Bereitstellung, die Daten für in der Bereitstellung definierte Metriken melden.
   * **Erstellungszeit**: Zeitstempel der Bereitstellungserstellung. Dieser Zeitstempel wird zur Konfliktlösung verwendet, wenn zwei Bereitstellungen dieselbe Priorität haben. 
1. Wählen Sie die Bereitstellung aus, die Sie überwachen möchten.  
1. Überprüfen Sie die Bereitstellungsdetails. Sie können über Registerkarten die Details der Bereitstellung überprüfen.

## <a name="modify-a-deployment"></a>Ändern einer Bereitstellung

Wenn Sie Änderungen an einer Bereitstellung vornehmen, werden diese sofort auf alle Zielgeräte repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Gerät die alte Zielbedingung nicht erfüllt, wohl aber die neue, und diese Bereitstellung die höchste Priorität für das Gerät hat, wird diese Bereitstellung auf das Gerät angewendet. 
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, die Zielbedingung nicht mehr erfüllt, wird diese Bereitstellung deinstalliert, und die Bereitstellung mit der nächsthöheren Priorität wird angewendet. 
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, diese Zielbedingung wie auch die Zielbedingungen aller anderen Bereitstellungen nicht erfüllt, erfolgt keine Änderung auf dem Gerät. Das Gerät führt die aktuellen Module im aktuellen Zustand weiter aus, wird aber nicht mehr als Teil dieser Bereitstellung verwaltet. Sobald es die Zielbedingung einer anderen Bereitstellung erfüllt, wird diese Bereitstellung deinstalliert, und die neue Bereitstellung wird angewendet. 

Gehen Sie wie folgt vor, um Änderungen an einer Bereitstellung vorzunehmen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub. 
1. Wählen Sie **IoT Edge** aus.
1. Wählen Sie **IoT Edge-Bereitstellungen** aus. 

   ![Anzeigen von IoT Edge-Bereitstellungen](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wählen Sie die Bereitstellung aus, die Sie ändern möchten. 
1. Nehmen Sie die gewünschten Änderungen an den folgenden Feldern vor: 
   * Zielbedingung
   * Metriken: Sie können definierte Metriken ändern oder löschen oder neue Metriken hinzufügen.
   * Bezeichnungen
   * Priorität
1. Wählen Sie **Speichern** aus.
1. Führen Sie die unter [Überwachen eine Bereitstellung](#monitor-a-deployment) beschriebenen Schritte durch, um den Rollout der Änderungen zu beobachten. 

## <a name="delete-a-deployment"></a>Löschen einer Bereitstellung

Wenn Sie eine Bereitstellung löschen, übernehmen alle Geräte die Bereitstellung mit der jeweils nächsthöheren Priorität. Wenn Ihre Geräte die Zielbedingung keiner anderen Bereitstellung erfüllen, werden die Module beim Löschen der Bereitstellung nicht entfernt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub. 
1. Wählen Sie **IoT Edge** aus.
1. Wählen Sie **IoT Edge-Bereitstellungen** aus. 

   ![Anzeigen von IoT Edge-Bereitstellungen](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Wählen Sie die zu löschende Bereitstellung durch Aktivieren des zugehörigen Kontrollkästchens aus. 
1. Klicken Sie auf **Löschen**.
1. Eine Meldung informiert Sie darüber, dass diese Aktion diese Bereitstellung löschen wird und der vorherige Status auf allen Geräten wiederhergestellt wird.  Dies bedeutet, dass eine Bereitstellung mit einer niedrigeren Priorität angewendet wird.  Wenn keine andere Zielbereitstellung für das Gerät vorhanden ist, werden keine Module entfernt. Wenn Sie alle Module vom Gerät entfernen möchten, erstellen Sie eine Bereitstellung ohne Module, und stellen Sie sie auf den gleichen Geräten bereit. Wählen Sie **Yes** (Ja), um fortzufahren. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das [Bereitstellen von Modulen auf Edge-Geräten](module-deployment-monitoring.md).
