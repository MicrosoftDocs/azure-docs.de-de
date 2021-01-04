---
title: Kontinuierliche Workflows für LUIS-Apps
description: Hier wird beschrieben, wie Sie CI/CD-Workflows für DevOps für Language Understanding Intelligent Service (LUIS) implementieren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 1ce78e02c652777b524964559b579530f3e022fa
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561306"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Continuous Integration- und Continuous Delivery-Workflows für LUIS DevOps

Softwareentwickler, die eine Language Understanding-App (LUIS) entwickeln, können DevOps-Verfahren für [Quellcodeverwaltung](luis-concept-devops-sourcecontrol.md), [automatisierte Builds](luis-concept-devops-automation.md), [Tests](luis-concept-devops-testing.md) und [Releaseverwaltung](luis-concept-devops-automation.md#release-management) anwenden. In diesem Artikel werden Konzepte für das Implementieren automatisierter Builds für LUIS erläutert.

## <a name="build-automation-workflows-for-luis"></a>Erstellen von Build-Automatisierungsworkflows für LUIS

![CI-Workflows](./media/luis-concept-devops-automation/luis-automation.png)

Konfigurieren Sie in Ihrem Quellcodeverwaltungssystem (SCM) automatisierte Buildpipelines, die bei folgenden Ereignissen ausgeführt werden:

1. Ein **PR-Workflow**, der ausgelöst wird, wenn ein [Pull Request](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) ausgelöst wird. Dieser Workflow überprüft den Inhalt des PR, *bevor* die Updates im Mainbranch zusammengeführt werden.
1. Ein **CI/CD-Workflow**, der ausgelöst wird, wenn Aktualisierungen an den Mainbranch gepusht werden, z. B. nach dem Zusammenführen der Änderungen aus einem PR: Dieser Workflow stellt die Qualität aller Aktualisierungen für den Mainbranch sicher.

Im **CI/CD-Workflow** werden zwei komplementäre Entwicklungsprozesse vereinigt:

* [Continuous Integration](/azure/devops/learn/what-is-continuous-integration) (CI) ist das Entwicklungsverfahren, bei dem Code in einem freigegebenen Repository regelmäßig committet wird und daraufhin ein automatisierter Build erfolgt. In Kombination mit einem automatisierten [Testansatz](luis-concept-devops-testing.md) kann durch die Continuous Integration für jede Aktualisierung sichergestellt werden, dass die LUDown-Quelle immer noch gültig ist und in eine LUIS-App importiert werden kann; außerdem wird eine Gruppe von Tests übergeben, in denen überprüft wird, ob die trainierte App die erforderlichen Intents und Entitäten für die Lösung erkennen kann.

* [Continuous Delivery](/azure/devops/learn/what-is-continuous-delivery) (CD) führt das Konzept der Continuous Integration weiter: Die Anwendung wird automatisch in einer Umgebung bereitgestellt, in der Sie umfassendere Tests durchführen können. Anhand von CD werden wir frühzeitig und schnellstmöglich auf unvorhergesehene Probleme aufmerksam gemacht, die im Zusammenhang mit den Änderungen entstehen können. Außerdem werden wir über Lücken in unserer Testabdeckung benachrichtigt.

Continuous Integration und Continuous Delivery sollen sicherstellen, dass der „Mainbranch zu jedem Zeitpunkt bereitstellbar ist“. Für eine LUIS-App bedeutet dies, dass bei Bedarf eine beliebige Version aus der Mainbranch-LUIS-App in die Produktionsumgebung überführt werden kann.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Tools zum Entwickeln von Automatisierungs-Workflows für LUIS

Zum Erstellen von Build-Automatisierungsworkflows stehen verschiedene Build-Automatisierungstechnologien zur Verfügung. Bei allen ist es erforderlich, dass Sie Skripts mit Schritten über eine Befehlszeilenschnittstelle (CLI) oder REST-Aufrufe schreiben können, sodass sie auf einem Buildserver ausgeführt werden können.

Verwenden Sie die folgenden Tools, um Automatisierungs-Workflows für LUIS zu erstellen:

* [LUIS CLI mit Bot Framework-Tools](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) zum Arbeiten mit LUIS-Apps und -Versionen, und zum Trainieren, Testen und Veröffentlichen der Apps und Versionen im LUIS-Dienst.

* [Azure CLI](/cli/azure/?view=azure-cli-latest), um Azure-Abonnements abzufragen, LUIS-Schlüssel für Erstellung und Vorhersage abzurufen und einen Azure-[Dienstprinzipal](/cli/azure/ad/sp?view=azure-cli-latest) für die Automation-Authentifizierung zu erstellen.

* [NLU.DevOps](https://github.com/microsoft/NLU.DevOps)-Tool zum [Testen einer LUIS-App](luis-concept-devops-testing.md) und zum Analysieren der Testergebnisse.

### <a name="the-pr-workflow"></a>Der PR-Workflow

Wie bereits erwähnt, konfigurieren Sie diesen Workflow so, dass er ausgeführt wird, wenn ein Entwickler einen PR auslöst, um die Zusammenführung von Änderungen aus einem Featurebranch im Mainbranch vorzuschlagen. Sein Zweck besteht darin, die Qualität der Änderungen im PR zu überprüfen, bevor diese mit dem Mainbranch zusammengeführt werden.

Mit diesem Workflow sollen folgende Aktionen ausgeführt werden:

* Erstellen einer temporären LUIS-App durch Importieren der `.lu`-Quelle in den PR.
* Trainieren und Veröffentlichen der LUIS-App-Version.
* Ausführen aller zugehörigen [Komponententests](luis-concept-devops-testing.md).
* Übergeben Sie den Workflow nur, wenn alle Tests bestanden wurden; andernfalls schlägt er fehl.
* Bereinigen Sie die temporäre App, und löschen Sie sie.

Sofern von Ihrem SCM unterstützt, konfigurieren Sie Schutzregeln für Branches so, dass dieser Workflow erfolgreich abgeschlossen werden muss, bevor der PR abgeschlossen werden kann.

### <a name="the-main-branch-cicd-workflow"></a>Der CI/CD-Workflow für den Mainbranch

Konfigurieren Sie diesen Workflow so, dass er ausgeführt wird, nachdem die Aktualisierungen im PR im Mainbranch zusammengeführt wurden. Damit soll die Mainbranchqualität auf hohem Niveau gehalten werden, indem die Aktualisierungen getestet werden. Wenn die Updates dem Qualitätsniveau gerecht werden, stellt dieser Workflow die neue LUIS-App-Version in einer Umgebung bereit, in der Sie umfassendere Tests durchführen können.

Mit diesem Workflow sollen folgende Aktionen ausgeführt werden:

* Erstellen einer neuen Version in der primären LUIS-App (d. h. der App, die Sie für den Mainbranch verwalten) mit dem aktualisierten Quellcode

* Trainieren und Veröffentlichen der LUIS-App-Version.

  > [!NOTE]
  > Wie in [Ausführen von Tests in einem automatisierten Buildworkflow](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) erläutert, müssen Sie die getestete LUIS-App-Version veröffentlichen, damit Tools wie NLU.DevOps darauf zugreifen können. LUIS unterstützt nur zwei benannte Veröffentlichungsslots (*Staging* und *Produktion*) für eine LUIS-App, Sie können [eine Version jedoch auch direkt veröffentlichen](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) und [nach Versionen abfragen](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Verwenden Sie die direkte Versionsveröffentlichung in ihren Automatisierungsworkflows, damit Sie sich nicht nur auf die benannten Veröffentlichungsslots beschränken müssen.

* Führen Sie alle [Komponententests](luis-concept-devops-testing.md) aus.

* Führen Sie optional auch [Batchtests](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) aus, um Qualität und Genauigkeit der LUIS-App-Version zu testen und diese mit einer Baseline zu vergleichen.

* Bei erfolgreichem Abschluss der Tests:
  * Markieren Sie die Quelle im Repository.
  * Führen Sie den CD-Auftrag (Continuous Delivery) aus, um die LUIS-App-Version in Umgebungen für weitere Tests zu veröffentlichen.

### <a name="continuous-delivery-cd"></a>Continuous Delivery (CD)

Der CD-Auftrag in einem CI/CD-Workflow wird bedingt bei Erfolg des Buildprozesses und der automatisierten Komponententests ausgeführt. Dabei wird die LUIS-Anwendung automatisch in einer Umgebung bereitgestellt, in der Sie weitere Tests ausführen können.

Es wird keine bestimmte Lösung für die Bereitstellung Ihrer LUIS-App empfohlen, und Sie müssen den Prozess implementieren, der für Ihr Projekt geeignet ist. Die Repository [LUIS-DevOps-Template](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementiert hierfür eine einfache Lösung. Dabei wird [die neue LUIS-App-Version](./luis-how-to-publish-app.md) im Veröffentlichungsslot *Produktion* veröffentlicht. Diese Vorgehensweise eignet sich für ein einfaches Setup. Wenn Sie jedoch eine Reihe von verschiedenen Produktionsumgebungen gleichzeitig unterstützen müssen (wie *Entwicklung*, *Staging* und *UAT*), erweist sich die Beschränkung auf zwei benannte Veröffentlichungsslots pro App als unzureichend.

Weitere Optionen zum Bereitstellen einer App-Version:

* Belassen Sie die App-Version veröffentlicht auf dem direkten Versionsendpunkt, und implementieren Sie einen entsprechenden Prozess zum Konfigurieren von Downstream-Produktionsumgebungen mit dem direkten Versionsendpunkt.
* Verwalten Sie unterschiedliche LUIS-Apps für jede Produktionsumgebung, und schreiben Sie Automatisierungsschritte zum Importieren von `.lu` in eine neue Version der LUIS-App für die Ziel-Produktionsumgebung, um sie zu trainieren und zu veröffentlichen.
* Exportieren Sie die getestete LUIS-App-Version in einen [LUIS-Docker-Container](./luis-container-howto.md?tabs=v3), und stellen Sie den LUIS-Container in Azure [Container Instances](../../container-instances/index.yml) bereit.

## <a name="release-management"></a>Releaseverwaltung

Im Allgemeinen wird empfohlen, dass Sie Continuous Delivery nur für Nicht-Produktionsumgebungen verwenden, z. B. für die Entwicklung und das Staging. Die meisten Teams benötigen einen manuellen Überprüfungs- und Genehmigungsprozess für die Bereitstellung in einer Produktionsumgebung. Bei einer Bereitstellung in einer Produktionsumgebung sollten Sie sicherstellen, dass dies erfolgt, wenn Schlüsselpersonen des Entwicklungsteams zur Unterstützung zur Verfügung stehen oder in Zeiten mit geringem Datenverkehrsaufkommen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [DevOps für LUIS mit GitHub implementieren](luis-how-to-devops-with-github.md).
* Erfahren Sie, wie Sie einen [GitHub Actions-Workflow mit NLU.DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md) schreiben.