---
title: Threat Modeling Tool-Releases – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Dokumentation der Anmerkungen zu dieser Version für das Threat Modeling Tool.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586413"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool, allgemein verfügbares Release 7.1.50911.2: 12.09.2018

Wir freuen uns, ankündigen zu können, dass das Microsoft Threat Modeling Tool jetzt als unterstützte Version mit allgemeiner Verfügbarkeit (GA-Version) zum Download bereitsteht. Dieses Release enthält wichtige Datenschutz- und Sicherheitsupdates sowie Fehlerkorrekturen, Funktionsupdates und Stabilitätsverbesserungen. Vorhandene Benutzer der Preview-Version 2017 werden beim Öffnen des Clients durch die ClickOnce-Technologie aufgefordert, auf die neueste Version zu aktualisieren. Neue Benutzer des Tools [klicken hier, um den Client herunterzuladen](https://aka.ms/threatmodelingtool).

Mit diesem Release beenden wir den Support für die Preview-Version 2017 und empfehlen allen Benutzern der Preview, auf die GA-Version zu aktualisieren. Am oder nach dem 15. Oktober 2018 legen wir die mindestens erforderliche ClickOnce-Version für das Threat Modeling Tool fest, und alle Preview-Clients müssen ein Upgrade vornehmen.

Das Microsoft Threat Modeling Tool 2016, das im [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168) erhältlich ist, wird weiterhin bis 1. Oktober 2019 unterstützt, aber ausschließlich mit kritischen Sicherheitsfixes.

## <a name="feature-changes"></a>Funktionsänderungen

### <a name="azure-stencil-updates"></a>Updates für Azure-Schablonen

Der Schablonensatz, der im Lieferumfang dieses Release enthalten ist, wurde um zusätzliche Azure-Schablonen und ihnen zugeordnete Bedrohungen und Abhilfemaßnahmen erweitert. Signifikante Änderungen wurden in den Kernbereichen „Azure App Services“, „Azure-Datenbankangebote“ und „Azure Storage“ vorgenommen.

![Updates für Azure-Schablonen](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-Integrationsfunktion entfernt

Die Funktionen „Auf OneDrive speichern“, „Auf OneDrive öffnen“ und „Link teilen“ der Preview wurden entfernt. Benutzern von OneDrive wird empfohlen, den Client [OneDrive für Windows](https://onedrive.live.com/about/en-us/download/) von Microsoft zu verwenden, um über das Standarddialogfeld zum Speichern und Öffnen von Dateien auf ihre auf OneDrive gespeicherten Dateien zuzugreifen.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Bemerkenswerte behobene Fehler, die von Kunden gemeldet wurden

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>In der TMT-Vorschau stürzt das Tool bei Verwendung der Standardvorlage ab

- Wenn eine generische Schablone (z. B. „Generischer Datenfluss“) zur Zeichenoberfläche hinzugefügt wird und Bedrohungen generiert, kann das Tool abstürzen. Dieses Problem wurde behoben.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>In der TMT-Vorschau sind die Risikostufen falsch, wenn ich einen Bericht speichere oder die Bedrohungen kopiere

- Wenn ein Benutzer die Risikostufe bestimmter Bedrohungen ändert und dann einen Bericht speichert oder die Risiken kopiert, wird die Risikostufe möglicherweise auf „Hoch“ zurückgesetzt. Dieses Problem wurde behoben.

## <a name="known-issues-and-faq"></a>Bekannte Probleme und FAQ

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Bei Benutzern hochauflösender Bildschirme kann Text in den Bedrohungseigenschaften sehr klein dargestellt werden.

#### <a name="issue"></a>Problem

In der Analyseansicht des Tools wird, wenn der Benutzer einen Bildschirm mit hoher Auflösung besitzt, der in Windows standardmäßig darauf festgelegt ist, zur Verbesserung der Lesbarkeit die Ansicht zu vergrößern, der Abschnitt „Mögliche Gegenmaßnahmen“ mit kleinem Text dargestellt.

![Bekanntes Problem bei hochauflösenden Bildschirmen](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Problemumgehung

Der Benutzer kann auf den Text der Gegenmaßnahme klicken und die Standard-Zoomsteuerelement von Windows (Strg+Mausrad nach oben) verwenden, um die Vergrößerung dieses Abschnitts zu erhöhen.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Dateien im Abschnitt „Zuletzt geöffnete Modelle“ des Hauptfensters können eventuell nicht geöffnet werden.

#### <a name="issue"></a>Problem

Die Funktion „Auf OneDrive öffnen“ der Preview-Version wurde entfernt. Benutzer mit „Zuletzt geöffneten Modellen“, die auf OneDrive gespeichert wurden, erhalten folgenden Fehler.

![OneDrive-Funktion entfernt](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Problemumgehung

Benutzern von OneDrive wird empfohlen, den Client [OneDrive für Windows](https://onedrive.live.com/about/en-us/download/) von Microsoft zu verwenden, um über das Standarddialogfeld „Als Modell öffnen“ auf ihre auf OneDrive gespeicherten Dateien zuzugreifen.

![OneDrive-Funktion entfernt](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Meine Organisation verwendet die 2016-Version des Tools, kann ich den Azure-Schablonensatz verwenden?

Ja, das ist möglich. Der [Azure-Schablonensatz ist auf GitHub verfügbar](https://github.com/Microsoft/threat-modeling-templates/) und kann in die 2016-Version des Tools geladen werden. Um ein neues Modell mit dem Azure-Schablonensatz zu erstellen, verwenden Sie das Dialogfeld „Vorlage für neue Modelle“ im Hauptmenü-Bildschirm. TMT-2016 kann die in den Feldern „Mögliche Gegenmaßnahmen“ des Azure-Schablonensatzes gefundenen Links nicht rendern, weshalb Ihnen Links möglicherweise als HTML-Tags angezeigt werden.

![Updates für Azure-Schablonen im 2016-Client](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systemanforderungen

- Unterstützte Betriebssysteme
  - Microsoft Windows 10
- Erforderliche .NET-Version
  - .NET 3.5.2
- Zusätzliche Anforderungen
  - Eine Internetverbindung ist erforderlich, um Updates für das Tool sowie Vorlagen zu erhalten.

## <a name="documentation-and-feedback"></a>Dokumentation und Feedback

- Dokumentation für das Threat Modeling Tool befindet sich auf [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) und umfasst Informationen [zur Verwendung des Tools](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nächste Schritte

Laden Sie die aktuelle Version des [Microsoft Threat Modeling Tools](https://aka.ms/threatmodelingtool) herunter.
