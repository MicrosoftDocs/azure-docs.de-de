---
title: Adaptive Anwendungssteuerungen in Azure Security Center
description: In diesem Dokument erfahren Sie, wie Sie die adaptive Anwendungssteuerung in Azure Security Center für Zulassungslisten von Anwendungen verwenden, die auf Azure-Computern ausgeführt werden.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: a7ad5f661d4eca07deb0942c7684b89ac59931a2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834390"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers

Informieren Sie sich über die Vorteile der adaptiven Anwendungssteuerungen von Azure Security Center und darüber, wie Sie Ihre Sicherheit mit diesem datengesteuerten, intelligenten Feature verbessern können.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Was sind adaptive Anwendungssteuerungen in Security Center?

Adaptive Anwendungssteuerungen sind eine intelligente und automatisierte Lösung zum Definieren von Zulassungslisten bekannter, sicherer Anwendungen für Ihre Computer. 

Häufig verfügen Organisationen über Sammlungen von Computern, die routinemäßig dieselben Prozesse ausführen. Security Center nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren und eine Liste bekanntermaßen sicherer Software zu erstellen. Zulassungslisten basieren auf Ihren spezifischen Azure-Workloads, und Sie können die Empfehlungen mithilfe der unten stehenden Anweisungen weiter anpassen.

Wenn Sie adaptive Anwendungssteuerungen aktiviert und konfiguriert haben, erhalten Sie Sicherheitswarnungen, wenn eine Anwendung ausgeführt wird, die nicht als sicher definiert ist.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Was sind die Vorteile von adaptiven Anwendungssteuerungen?

Durch die Definition von Listen mit bekannten sicheren Anwendungen und das Generieren von Warnungen, wenn etwas anderes ausgeführt wird, können Sie mehrere Härtungsziele erreichen:

- Erkennen potenzieller Schadsoftware, auch wenn sie von Antischadsoftware-Lösungen übersehen wird
- Verbessern der Konformität mit lokalen Sicherheitsrichtlinien, die die ausschließliche Verwendung lizenzierter Software vorschreiben
- Vermeiden des Ausführens alter oder nicht unterstützter Anwendungen
- Verhindern bestimmter Software, die von Ihrer Organisation gesperrt wurde
- Erhöhen der Überwachung von Apps, die auf sensible Daten zugreifen

Derzeit sind keine Erzwingungsoptionen verfügbar. Adaptive Anwendungssteuerungen sollen Sicherheitswarnungen ausgeben, wenn eine andere als die von Ihnen als sicher definierte Anwendung ausgeführt wird.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Erfordert [Azure Defender für Server](defender-for-servers-introduction.md)|
|Unterstützte Computer:|![Ja](./media/icons/yes-icon.png) Azure- und Nicht-Azure-Computer, auf denen Windows und Linux ausgeführt wird<br>![Ja](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml)-Computer|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsleseberechtigter**- und **Leseberechtigter**-Rollen können Gruppen und Listen der bekannten sicheren Anwendungen anzeigen<br>**Mitwirkender**- und **Sicherheitsadministrator**-Rollen können Gruppen und Listen der bekannten sicheren Anwendungen bearbeiten|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, andere Gov-Clouds)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Aktivieren von Anwendungssteuerelementen auf einer Gruppe von Computern

Wenn Security Center Gruppen von Computern in ihren Abonnements identifiziert hat, die konsistent einen ähnlichen Satz von Anwendungen ausführen, werden Sie mit der folgenden Empfehlung aufgefordert: **Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein**.

Wählen Sie die Empfehlung aus, oder öffnen Sie die Seite der adaptiven Anwendungssteuerung, um die Liste der empfohlenen, bekannten sicheren Anwendungen und Gruppen von Computern anzuzeigen.

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerungen** aus.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Öffnen von „Adaptive Anwendungssteuerungen“ über das Azure-Dashboard" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Die Seite **Adaptive Anwendungssteuerung** wird geöffnet, auf der Ihre VMs auf den folgenden Registerkarten gruppiert sind:

    - **Konfiguriert**: Gruppen von Computern, die bereits über eine definierte Zulassungsliste von Anwendungen verfügen. Für jede Gruppe zeigt die konfigurierte Registerkarte Folgendes an:
        - die Anzahl von Computern in der Gruppe
        - letzte Warnungen

    - **Empfohlen**: Gruppen von Computern, auf denen die gleichen Anwendungen konsistent ausgeführt werden, und für die keine Zulassungsliste konfiguriert ist. Sie sollten die adaptive Anwendungssteuerung für diese Gruppen zu aktivieren.
    
      > [!TIP]
      > Wenn ein Gruppenname mit dem Präfix „REVIEWGROUP“ angezeigt wird, enthält er Computer mit einer teilweise konsistenten Liste von Anwendungen. Security Center kann kein Muster sehen, empfiehlt aber, diese Gruppe zu überprüfen, um festzustellen, ob _Sie_ einige Regeln für die adaptive Anwendungssteuerung wie in [Bearbeiten der Regel für die adaptive Anwendungssteuerung einer Gruppe](#edit-a-groups-adaptive-application-controls-rule) beschrieben manuell definieren können.
      >
      > Sie können auch Computer aus dieser Gruppe in andere Gruppen verschieben, wie in [Verschieben eines Computers aus einer Gruppe in eine andere](#move-a-machine-from-one-group-to-another) beschrieben.

    - **Keine Empfehlung**: Computer ohne definierte Zulassungsliste von Anwendungen, die die Funktion nicht unterstützen. Ihr Computer befindet sich aus folgenden Gründen möglicherweise auf dieser Registerkarte:
      - Ein Log Analytics-Agent fehlt.
      - Der Log Analytics-Agent sendet keine Ereignisse.
      - Es handelt sich um einen Windows-Computer mit einer bereits vorhandenen [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview)-Richtlinie, die entweder durch ein Gruppenrichtlinienobjekt oder eine lokale Sicherheitsrichtlinie aktiviert ist.

      > [!TIP]
      > Security Center benötigt mindestens die Daten von zwei Wochen, um die eindeutigen Empfehlungen pro Gruppe von Computern zu definieren. Computer, die vor kurzem erstellt wurden oder zu Abonnements gehören, die erst kürzlich mit Azure Defender aktiviert wurden, werden auf der Registerkarte **Keine Empfehlung** angezeigt.


1. Öffnen Sie die Registerkarte **Empfohlen**. Die Gruppen von Computern mit empfohlenen Zulassungslisten werden angezeigt.

   ![Registerkarte „Empfohlen“](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Wählen Sie eine Gruppe aus. 

1. Um die neue Regel zu konfigurieren, überprüfen Sie die verschiedenen Abschnitte dieser Seite **Konfigurieren von Regeln zur Anwendungssteuerung** und der Inhalte, die für diese bestimmte Gruppe von Computern eindeutig sein werden:

   ![Konfigurieren einer neuen Regel](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Computer auswählen**: Standardmäßig werden alle Computer in der identifizierten Gruppe ausgewählt. Heben Sie die Auswahl von Computern auf, die Sie von dieser Regel ausnehmen möchten.
   
   1. **Empfohlene Anwendungen**: Lesen Sie diese Liste mit Anwendungen, die auf den Computern in dieser Gruppe gängig sind, und für die ein Zulassen der Ausführung empfohlen wird.
   
   1. **Weitere Anwendungen**: Überprüfen Sie diese Liste von Anwendungen, die auf den Computern innerhalb dieser Gruppe entweder seltener vorkommen, oder die als nutzbar bekannt sind. Ein Warnsymbol gibt an, dass eine bestimmte Anwendung von einem Angreifer zur Umgehung einer Anwendungszulassungsliste verwendet werden könnte. Sie sollten diese Anwendungen sorgfältig überprüfen.

      > [!TIP]
      > Beide Anwendungslisten enthalten die Option, eine bestimmte Anwendung auf bestimmte Benutzer zu beschränken. Wenden Sie nach Möglichkeit das Prinzip der geringsten Berechtigungen an.
      > 
      > Anwendungen werden von ihren Herausgebern definiert. Wenn eine Anwendung keine Informationen über den Herausgeber aufweist (nicht signiert ist), wird eine Pfadregel für den vollständigen Pfad der jeweiligen Anwendung erstellt.

   1. Wählen Sie **Überwachung** aus, um die Regel anzuwenden. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Bearbeiten der Regeln für die adaptive Anwendungssteuerung einer Gruppe

Sie können die Zulassungsliste für eine Gruppe von Computern aufgrund bekannter Änderungen in Ihrer Organisation bearbeiten. 

So bearbeiten Sie die Regeln für eine Gruppe von Computern:

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerungen** aus.

1. Wählen Sie auf der Registerkarte **Konfiguriert** die Gruppe mit der Regel aus, die Sie bearbeiten möchten.

1. Lesen Sie die verschiedenen Abschnitte der Seite **Konfigurieren von Regeln zur Anwendungssteuerung**, wie unter [Aktivieren der adaptiven Anwendungssteuerung für eine Gruppe von Computern](#enable-application-controls-on-a-group-of-machines) beschrieben.

1. Fügen Sie optional eine oder mehrere benutzerdefinierte Regeln hinzu:

   1. Wählen Sie **Regel hinzufügen** aus.

      ![Hinzufügen einer benutzerdefinierten Regel](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Falls Sie einen bekannten sicheren Pfad definieren, ändern Sie den **Regeltyp** in „Pfad“, und geben Sie einen einzelnen Pfad ein. Sie können Platzhalter in den Pfad einschließen.
   
      > [!TIP]
      > Einige Szenarien, in denen Platzhalter in einem Pfad nützlich sein können:
      > 
      > * Verwenden eines Platzhalters am Ende eines Pfads, um alle ausführbaren Dateien in diesem Ordner und Unterordnern zuzulassen.
      > * Verwenden eines Platzhalters in der Mitte eines Pfads zum Aktivieren des Namens einer bekannten ausführbaren Datei mit einem sich ändernden Ordnernamen (z. B. persönliche Benutzerordner mit einer bekannten ausführbaren Datei, automatisch generierte Ordnernamen usw.).
  
   1. Definieren Sie die zulässigen Benutzer und die geschützten Dateitypen.

   1. Wenn Sie die Regel definiert haben, wählen Sie **Hinzufügen** aus.

1. Wählen Sie **Speichern** aus, um die Änderungen zu übernehmen.


## <a name="review-and-edit-a-groups-settings"></a>Überprüfen und Bearbeiten der Einstellungen einer Gruppe

1. Zum Anzeigen der Details und Einstellungen der Gruppe wählen Sie **Gruppeneinstellungen** aus.

    In diesem Bereich werden der Name der Gruppe (der geändert werden kann), der Betriebssystemtyp, der Standort und andere relevante Details angezeigt.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Seite „Gruppeneinstellungen“ für adaptive Anwendungssteuerungen" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Ändern Sie optional den Namen der Gruppe oder den jeweiligen Schutzmodus für Dateitypen.

1. Wählen Sie **Anwenden** und dann **Speichern** aus.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Antwort auf die Empfehlung „Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden“

Diese Empfehlung wird angezeigt, wenn das maschinelle Lernen von Security Center das potenziell legitime Verhalten identifiziert, das bisher noch nicht zugelassen wurde. Die Empfehlung schlägt neue Regeln für vorhandene Definitionen vor, um die Anzahl der falsch positiven Warnungen zu reduzieren.

So beheben Sie die Probleme:

1. Wählen Sie auf der Empfehlungenseite die Empfehlung **Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden**, um Gruppen mit neu identifizierten, potenziell legitimen Verhalten anzuzeigen.

1. Wählen Sie die Gruppe mit der Regel aus, die Sie bearbeiten möchten.

1. Lesen Sie die verschiedenen Abschnitte der Seite **Konfigurieren von Regeln zur Anwendungssteuerung**, wie unter [Aktivieren der adaptiven Anwendungssteuerung für eine Gruppe von Computern](#enable-application-controls-on-a-group-of-machines) beschrieben.

1. Wählen Sie **Überwachung** aus, um die Änderungen zu übernehmen.




## <a name="audit-alerts-and-violations"></a>Überwachen von Warnungen und Verstößen

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerungen** aus.

1. Wenn Sie Gruppen mit Computern anzeigen möchten, bei denen kürzlich Warnungen aufgetreten sind, überprüfen Sie die auf der Registerkarte **Konfiguriert** aufgelisteten Gruppen.

1. Wählen Sie eine Gruppe zur weiteren Untersuchung aus.

   ![Recent alerts (Letzte Warnungen)](./media/security-center-adaptive-application/recent-alerts.png)

1. Wählen Sie eine Warnung aus, um weitere Informationen und die Liste der betroffenen Computer zu erhalten.



## <a name="move-a-machine-from-one-group-to-another"></a>Verschieben eines Computers aus einer Gruppe in eine andere

Wenn Sie einen Computer aus einer Gruppe in eine andere Gruppen verschieben, ändert sich die angewandte Anwendungssteuerungsrichtlinie in die Einstellungen der Gruppe, in die die Verschiebung erfolgt. Sie können einen Computer auch aus einer konfigurierten Gruppe in eine nicht konfigurierte Gruppe verschieben. Dadurch werden alle auf den Computer angewendeten Anwendungssteuerungsregeln entfernt.

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerungen** aus.

1. Wählen Sie auf der Seite **Adaptive Anwendungssteuerung** auf der Registerkarte **Konfiguriert** die Gruppe aus, die den Computer enthält, der verschoben werden soll.

1. Öffnen Sie die Liste **Konfigurierte Computer**.

1. Öffnen Sie das Menü des Computers über die drei Punkte am Ende der Zeile, und wählen Sie **Verschieben** aus. Der Bereich **Computer in eine andere Gruppe verschieben** wird geöffnet.

1. Wählen Sie die Zielgruppe und dann **Computer** verschieben aus.

1. Wählen Sie zum Speichern der Änderungen **Speichern** aus.





## <a name="manage-application-controls-via-the-rest-api"></a>Verwalten der Anwendungssteuerung über die REST-API 

Verwenden Sie die REST-API, um Ihre adaptive Anwendungssteuerung programmgesteuert zu verwalten. 

Die vollständige API-Dokumentation finden Sie [hier](/rest/api/securitycenter/adaptiveapplicationcontrols).

Einige der Funktionen, die über die REST-API verfügbar sind:

* **List**: Ruft alle Ihre Gruppenempfehlungen ab und stellt einen JSON-Code mit einem Objekt für jede Gruppe bereit.

* **Get**: Ruft den JSON-Code mit den vollständigen Empfehlungsdaten (d. h. Liste von Computern, Herausgeber-/Pfadregeln usw.) ab.

* **Put**: Konfiguriert Ihre Regel (verwenden Sie den JSON-Code, den Sie mit **Get** abgerufen haben, als Text für diese Anforderung).
 
   > [!IMPORTANT]
   > Die **Put**-Funktion erwartet weniger Parameter als der vom Get-Befehl zurückgegebene JSON-Code enthält.
   >
   > Entfernen Sie die folgenden Eigenschaften, bevor Sie den JSON-Code in der Put-Anforderung verwenden: recommendationStatus, configurationStatus, issues, location und sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Häufig gestellte Fragen zu adaptiven Anwendungssteuerungen

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Gibt es Optionen zur Erzwingung der Anwendungssteuerung?
Derzeit sind keine Erzwingungsoptionen verfügbar. Adaptive Anwendungssteuerungen sollen **Sicherheitswarnungen** ausgeben, wenn eine andere als die von Ihnen als sicher definierte Anwendung ausgeführt wird. Sie bieten eine Reihe von Vorteilen ([Was sind die Vorteile adaptiver Anwendungssteuerungen?](#what-are-the-benefits-of-adaptive-application-controls)) und sind, wie auf dieser Seite gezeigt, überaus anpassbar.

 

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie mithilfe der adaptiven Anwendungssteuerung in Azure Security Center Zulassungslisten von Anwendungen definieren, die auf Azure- und Nicht-Azure-Computern ausgeführt werden. Weitere Informationen zu einigen anderen Cloudworkload-Schutzfeatures in Security Center finden Sie unter:

* [Grundlegendes zum Just-In-Time(JIT)-VM-Zugriff](just-in-time-explained.md)
* [Integration von Security Center in Azure Kubernetes Service](defender-for-kubernetes-introduction.md)