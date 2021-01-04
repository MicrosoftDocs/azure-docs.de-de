---
title: Anleitung zum Onboarding der Microsoft-Sicherheitscodeanalyse
description: Erfahren Sie mehr über den Onboarding- und Installationsprozess für die Erweiterung „Microsoft-Sicherheitscodeanalyse“. Hier finden Sie Voraussetzungen und zusätzliche Ressourcen.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4fe13c418452a7a88dcd97939d6e853039f3fb64
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517069"
---
# <a name="onboarding-and-installing"></a>Onboarding und Installation

Voraussetzungen für die Einführung der Microsoft-Sicherheitscodeanalyse:

- Ein qualifiziertes Microsoft Unified Support-Angebot, wie im folgenden Abschnitt beschrieben.
- Eine Azure DevOps-Organisation.
- Berechtigungen zum Installieren von Erweiterungen für die Azure DevOps-Organisation.
- Quellcode, der mit einer in der Cloud gehosteten Azure DevOps-Pipeline synchronisiert werden kann.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding der Erweiterung „Microsoft-Sicherheitscodeanalyse“

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Sie interessieren sich für den Kauf der Erweiterung „Microsoft-Sicherheitscodeanalyse“?

Wenn Sie eines der folgenden Supportangebote nutzen, wenden Sie sich an Ihren Technical Account Manager, um ein Zeitkontingent zu erwerben bzw. bereits erworbene Kontingente zu tauschen und Zugriff auf die Erweiterung zu erhalten:

- Unified Support Advanced-Tarif
- Unified Support Performance-Tarif
- Premier Support für Entwickler
- Premier Support für Partner
- Premier Support für Unternehmen

Wenn Sie über keine der oben genannten Supportvereinbarungen verfügen, können Sie die Erweiterung von einem unserer Partner erwerben.

**Nächste Schritte:**

Wenn Sie die oben genannten Qualifikationen erfüllen, wenden Sie sich an einen Partner aus der Liste unten, um die Erweiterung für die Microsoft-Sicherheitscodeanalyse zu erwerben. Wenden Sie sich andernfalls an den [Support für die Microsoft-Sicherheitscodeanalyse](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Partner:**

- Zonen – Kontaktinformationen: cloudsupport@zones.com
- Wortell – Kontaktinformationen: info@wortell.nl
- Logicalis – Kontaktinformationen: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Partner werden

Mit einer Premier Support für Partner-Vereinbarung möchte das Team für die Analyse des Microsoft-Sicherheitscodes Partner gewinnen. Partner helfen Azure DevOps-Kunden, ihre Entwicklung sicherer zu machen, indem sie die Erweiterung an interessierte Kunden verkaufen, die mit Microsoft keine Supportvereinbarung für Unternehmen abgeschlossen haben. Interessierte Partner können sich [hier](http://www.microsoftpartnersupport.com/msrd/opin) registrieren.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installieren der Erweiterung „Microsoft-Sicherheitscodeanalyse“

1. Nachdem die Erweiterung für Ihre Azure DevOps-Organisation freigegeben wurde, navigieren Sie zur Seite Ihrer Azure DevOps-Organisation. Eine Beispiel-URL für eine derartige Seite lautet `https://dev.azure.com/contoso`.
1. Wählen Sie oben rechts das Warenkorbsymbol neben Ihrem Namen und dann **Erweiterungen verwalten** aus.
1. Wählen Sie **Freigegeben** aus.
1. Wählen Sie die Erweiterung Microsoft-Sicherheitscodeanalyse und dann **Installieren** aus.
1. Wählen Sie in der Dropdownliste die Azure DevOps-Organisation aus, für die die Erweiterung installiert werden soll.
1. Wählen Sie **Installieren** aus. Nach Abschluss der Installation können Sie die Erweiterung verwenden.

>[!NOTE]
> Auch wenn Sie keine Zugriffsberechtigungen für die Installation der Erweiterung haben, sollten Sie mit den Installationsschritten fortfahren. Sie können den Zugriff während der Installation bei Ihrem Azure DevOps-Organisationsadministrator anfordern.

Nach der Installation der Erweiterung werden die Buildtasks für die sichere Entwicklung angezeigt und können Ihrer Azure Pipelines-Instanz hinzugefügt werden.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Hinzufügen spezifischer Buildtasks zur Azure DevOps-Pipeline

1. Öffnen Sie in Ihrer Azure DevOps-Organisation Ihr Teamprojekt.
1. Wählen Sie **Pipelines** > **Builds** aus.
1. Wählen Sie die Pipeline aus, der Sie die Buildtasks der Erweiterung hinzufügen möchten:
   - Neue Pipeline: Wählen Sie **Neu** aus, und führen Sie die beschriebenen Schritte zum Erstellen einer neuen Pipeline aus.
   - Pipeline bearbeiten: Wählen Sie eine vorhandene Pipeline aus, und wählen Sie dann **Bearbeiten** aus, um mit der Bearbeitung der Pipeline zu beginnen.
1. Wählen Sie **+** aus, und wechseln Sie dann zum Bereich **Tasks hinzufügen**.
1. Suchen Sie den Buildtask, den Sie hinzufügen möchten, in der Liste oder über das Suchfeld. Wählen Sie **Hinzufügen**.
1. Geben Sie die für den Task erforderlichen Parameter an.
1. Setzen Sie einen neuen Build in die Warteschlange.
   >[!NOTE]
   >Datei- und Ordnerpfade verhalten sich relativ zum Stammverzeichnis des Quellrepositorys. Wenn Sie die Ausgabedateien und -ordner als Parameter angeben, werden sie durch den allgemeinen Speicherort ersetzt, den wir für den Build-Agent definiert haben.

> [!TIP]
>
> - Um nach dem Build eine Analyse auszuführen, fügen Sie die Buildtasks der Microsoft-Sicherheitscodeanalyse nach dem Schritt „Buildartefakte veröffentlichen“ des Builds ein. Auf diese Weise können der Build beendet und Ergebnisse veröffentlicht werden, bevor statische Analysetools ausgeführt werden.
> - Aktivieren Sie bei Buildtasks für die sichere Entwicklung immer **Bei Fehler fortsetzen**. Selbst wenn bei einem Tool Fehler auftreten, können die anderen ausgeführt werden. Zwischen den Tools bestehen keine Abhängigkeiten.
> - Buildtasks der Microsoft-Sicherheitscodeanalyse verursachen nur einen Fehler, wenn ein Tool nicht erfolgreich ausgeführt werden kann. Sie werden allerdings selbst dann erfolgreich ausgeführt, wenn ein Tool Probleme im Code identifiziert. Mit dem Nachanalyse-Buildtask können Sie den Build so konfigurieren, dass er unterbrochen wird, wenn ein Tool Probleme im Code identifiziert.
> - Einige Azure DevOps-Buildtasks werden nicht unterstützt, wenn sie über eine Releasepipeline ausgeführt werden. Genauer gesagt werden Tasks, durch die Artefakte über eine Releasepipeline veröffentlicht werden, von Azure DevOps nicht unterstützt.
> - Eine Liste der vordefinierten Variablen in Azure DevOps Team Build, die Sie als Parameter angeben können, finden Sie unter [Azure DevOps-Buildvariablen](/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren der Buildtasks finden Sie in der [Konfigurationsanleitung](security-code-analysis-customize.md) oder im [YAML-Konfigurationshandbuch](yaml-configuration.md).

Wenn Sie weitere Fragen zur Erweiterung und zu den angebotenen Tools haben, lesen Sie unsere [Seite mit FAQs](security-code-analysis-faq.md).