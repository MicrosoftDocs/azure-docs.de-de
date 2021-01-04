---
title: CI/CD für Custom Speech – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Wenden Sie DevOps mit Custom Speech und CI/CD-Workflows an. Implementieren Sie eine vorhandene DevOps-Lösung für Ihr eigenes Projekt.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f82ea154d5949f4d229ac76e7a7ce2a89d15ac13
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025666"
---
# <a name="cicd-for-custom-speech"></a>CI/CD für Custom Speech

Implementieren Sie die automatisierte Trainings-, Test- und Releaseverwaltung, um eine kontinuierliche Verbesserung der Custom Speech-Modelle zu ermöglichen, während Sie Aktualisierungen auf Trainings- und Testdaten anwenden. Durch die effektive Implementierung von CI/CD-Workflows können Sie sicherstellen, dass der Endpunkt für das leistungsfähigste Custom Speech-Modell immer verfügbar ist.

[Continuous Integration](/azure/devops/learn/what-is-continuous-integration) (CI) ist das Entwicklungsverfahren, bei dem Aktualisierungen in einem freigegebenen Repository regelmäßig committet werden und daraufhin ein automatisierter Build erfolgt. CI-Workflows für Custom Speech trainieren ein neues Modell aus seinen Datenquellen und führen automatisierte Tests an dem neuen Modell durch, um sicherzustellen, dass es bessere Leistungen als das vorherige Modell erbringt.

[Continuous Delivery](/azure/devops/learn/what-is-continuous-delivery) (CD) übernimmt Modelle aus dem CI-Prozess und erstellt einen Endpunkt für jedes verbesserte Custom Speech-Modell. CD macht Endpunkte leicht verfügbar, die in Lösungen integriert werden können.

Benutzerdefinierte CI/CD-Lösungen sind möglich, aber für eine robuste, vordefinierte Lösung verwenden Sie das [Speech DevOps-Vorlagenrepository](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), das CI/CD-Workflows mit GitHub Actions ausführt.

## <a name="cicd-workflows-for-custom-speech"></a>CI/CD-Workflows für Custom Speech

Der Zweck dieser Workflows besteht darin sicherzustellen, dass jedes Custom Speech-Modell eine höhere Erkennungsgenauigkeit aufweist als der vorherige Build. Wenn die Aktualisierungen für die Test- und/oder Trainingsdaten die Genauigkeit erhöhen, erstellen diese Workflows einen neuen Custom Speech-Endpunkt.

Git-Server wie GitHub und Azure DevOps können automatisierte Workflows ausführen, wenn bestimmte Git-Ereignisse auftreten, z. B. Zusammenführungen oder Pull Requests. Beispielsweise kann ein CI-Workflow ausgelöst werden, wenn Aktualisierungen von Testdaten an den *Masterbranch* gesendet werden. Verschiedene Git-Server werden über unterschiedliche Tools verfügen, gestatten aber die Skripterstellung von Befehlen der Befehlszeilenschnittstelle (CLI), sodass sie auf einem Buildserver ausgeführt werden können.

Auf diesem Weg sollten die Workflows Daten, Tests, Testdateien, Modelle und Endpunkte so benennen und speichern, dass sie bis zu dem Commit oder der Version, von dem bzw. der sie stammen, zurückverfolgt werden können. Es ist auch hilfreich, diese Ressourcen so zu benennen, dass leicht erkennbar ist, welche nach der Aktualisierung der Testdaten und welche nach der Aktualisierung der Trainingsdaten erstellt wurden.

### <a name="ci-workflow-for-testing-data-updates"></a>CI-Workflow für Aktualisierungen von Testdaten

Der Hauptzweck der CI/CD-Workflows besteht darin, ein neues Modell mit den Trainingsdaten zu erstellen und dieses Modell unter Verwendung der Testdaten zu testen, um festzustellen, ob sich die [Wort-Fehler-Rate](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (Word Error Rate, WER) im Vergleich zum vorherigen leistungsstärksten Modell (dem „Vergleichstestmodell“) verbessert hat. Wenn das neue Modell eine bessere Leistung erzielt, wird es zum neuen Vergleichstestmodell, mit dem zukünftige Modelle verglichen werden.

Der CI-Workflow zum Testen von Datenaktualisierungen sollte das aktuelle Vergleichstestmodell mit den aktualisierten Testdaten erneut testen, um die überarbeitete Wort-Fehler-Rate (WER) zu berechnen. Dadurch wird sichergestellt, dass beim Vergleich der WER eines neuen Modells mit der WER des Vergleichsmodells beide Modelle mit denselben Testdaten getestet wurden und Sie somit Gleiches mit Gleichem vergleichen.

Dieser Workflow sollte bei Aktualisierungen der Testdaten ausgelöst werden und wie folgt vorgehen:

- Vergleichstestmodell mit den aktualisierten Testdaten testen.
- Testausgabe, die die Wort-Fehler-Rate (WER) des Vergleichstestmodells enthält, mithilfe der aktualisierten Daten speichern.
- Die Wort-Fehler-Rate aus diesen Tests wird zur neuen Wort-Fehler-Rate des Vergleichstestmodells, die von zukünftigen Modellen übertroffen werden muss.
- Der CD-Workflow wird bei Aktualisierungen von Testdaten nicht ausgeführt.

### <a name="ci-workflow-for-training-data-updates"></a>CI-Workflow für Aktualisierungen von Trainingsdaten

Aktualisierungen von Trainingsdaten bezeichnen Aktualisierungen für das benutzerdefinierte Modell.

Dieser Workflow sollte bei Aktualisierungen der Trainingsdaten ausgelöst werden und wie folgt vorgehen:

- Ein neues Modell mit den aktualisierten Trainingsdaten trainieren.
- Das neue Modell anhand der Testdaten testen.
- Die Testausgabe speichern, die die Wort-Fehler-Rate enthält.
- Die Wort-Fehler-Rate des neuen Modells mit der Wort-Fehler-Rate des Testvergleichsmodells vergleichen.
- Wenn sich die Wort-Fehler-Rate nicht verbessert, wird der Workflow beendet.
- Wenn sich die Wort-Fehler-Rate verbessert, wird der CD-Workflow ausgeführt, um einen Custom Speech-Endpunkt zu erstellen.

### <a name="cd-workflow"></a>CD-Workflow

Nachdem eine Aktualisierung der Trainingsdaten die Erkennung eines Modells verbessert hat, sollte der CD-Workflow automatisch ausgeführt werden, um einen neuen Endpunkt für dieses Modell zu erstellen und diesen Endpunkt so verfügbar zu machen, dass er in einer Lösung verwendet werden kann.

#### <a name="release-management"></a>Releaseverwaltung

Die meisten Teams benötigen einen manuellen Überprüfungs- und Genehmigungsprozess für die Bereitstellung in einer Produktionsumgebung. Bei einer Bereitstellung in einer Produktionsumgebung sollten Sie sicherstellen, dass dies erfolgt, wenn Schlüsselpersonen des Entwicklungsteams zur Unterstützung zur Verfügung stehen oder in Zeiten mit geringem Datenverkehrsaufkommen.

### <a name="tools-for-custom-speech-workflows"></a>Tools für Custom Speech-Workflows

Verwenden Sie die folgenden Tools für CI/CD-Automatisierungsworkflows für Custom Speech:

- [Azure CLI](/cli/azure/?view=azure-cli-latest) zum Erstellen einer Azure-Dienstprinzipalauthentifizierung, zum Abfragen von Azure-Abonnements und zum Speichern von Testergebnissen in Azure-Blob.
- [Azure Speech-Befehlszeilenschnittstelle](spx-overview.md) zum Interagieren mit dem Speech-Dienst über die Befehlszeile oder einen automatisierten Workflow.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps-Lösung für Custom Speech mit GitHub Actions

Eine bereits implementierte DevOps-Lösung für Custom Speech finden Sie im [Speech DevOps-Vorlagenrepository](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Erstellen Sie eine Kopie der Vorlage, und beginnen Sie die Entwicklung benutzerdefinierter Modelle mit einem robusten DevOps-System, das Tests, Training und Versionsverwaltung unter Verwendung von GitHub Actions umfasst. Das Repository bietet Beispieltests und Trainingsdaten, um beim Setup und der Erläuterung des Workflows zu helfen. Ersetzen Sie nach dem anfänglichen Setup die Beispieldaten durch Ihre Projektdaten.

Das [Speech DevOps-Vorlagenrepository](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) stellt die Infrastruktur und detaillierte Anleitungen für Folgendes bereit:

- Kopieren Sie das Vorlagenrepository in Ihr GitHub-Konto, erstellen Sie dann Azure-Ressourcen und einen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) für die CI/CD-Workflows für GitHub Actions.
- Durchlaufen Sie die „[innere Schleife der Entwicklung](https://mitchdenny.com/the-inner-loop/)“. Aktualisieren Sie Trainings- und Testdaten von einem Featurebranch, testen Sie die Änderungen mit einem temporären Entwicklungsmodell, und lösen Sie einen Pull Request aus, um die Änderungen vorzuschlagen und zu überprüfen.
- Wenn Trainingsdaten in einem Pull Request für *master* aktualisiert werden, trainieren Sie Modelle mit dem CI-Workflow für GitHub Actions.
- Führen Sie automatisierte Genauigkeitsprüfungen durch, um die [Wort-Fehler-Rate](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) eines Modells zu ermitteln. Speichern Sie die Testergebnisse in Azure-Blob.
- Führen Sie den CD-Workflow aus, um einen Endpunkt zu erstellen, wenn sich die Wort-Fehler-Rate verbessert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über DevOps mit Speech:

- Verwenden Sie das [Speech DevOps-Vorlagenrepository](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), um DevOps für Custom Speech mit GitHub Actions zu implementieren.