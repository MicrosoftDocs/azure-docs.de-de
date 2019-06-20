---
title: Migrieren von Azure Deutschland zu Azure weltweit
description: Eine Einführung in das Migrieren Ihrer Azure-Ressourcen von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 386f5af2ef186dcde2971601b44eb9cbd53883dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686372"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Übersicht über die Migrationsanleitung für Azure Deutschland

Die Artikel in diesem Abschnitt wurden erstellt, um Sie beim Migrieren Ihrer Workloads von Azure Deutschland zu Azure weltweit zu unterstützen. Im [Azure-Migrationscenter](https://azure.microsoft.com/migration/) werden zwar Tools bereitgestellt, mit denen Sie Ressourcen migrieren können, einige dieser Tools sind aber nur für Migrationen nützlich, die im selben Mandanten oder in derselben Region erfolgen.

Die beiden Regionen in Deutschland sind vollständig getrennt von Azure weltweit. Die Clouds in Azure weltweit und in Azure Deutschland haben ihre eigenen getrennten Azure Active Directory-Instanzen. Aus diesem Grund sind Azure Deutschland-Mandanten von Azure weltweit-Mandanten getrennt. In diesem Artikel sind die Migrationstools beschrieben, die Sie verwenden können, wenn Sie zwischen *verschiedenen* Mandanten migrieren.

Der Leitfaden für die Identitäts-/Mandantenmigration wurde für reine Azure-Kunden konzipiert. Wenn Sie allgemeine Azure Active Directory (Azure AD)-Mandanten für Azure und Office 365 (oder andere Microsoft-Produkte) verwenden, beinhaltet eine Identitätsmigration komplexe Vorgänge, und Sie sollten sich vor der Verwendung dieses Migrationsleitfadens zunächst an Ihren Kontomanager wenden.

## <a name="migration-process"></a>Migrationsprozess

Der Prozess, mit Sie eine Workload von Azure Deutschland zu Azure weltweit migrieren, ähnelt normalerweise dem Prozess, mit dem Anwendungen in die Cloud migriert werden. Ein Migrationsprozess besteht aus folgenden Schritte:

![Abbildung mit den vier Schritten eines Migrationsprozesses: Bewerten, Planen, Migrieren, Überprüfen](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>Bewerten

Es ist unerlässlich, dass Sie den Fußabdruck Ihres Unternehmens bezüglich Azure Deutschland verstehen, indem Sie Azure Kontobesitzer, Abonnementadministratoren, Mandantenadministratoren sowie Finanz- und Buchhaltungsteams zusammenbringen. Die Personen, die in diesen Rollen arbeiten, können ein vollständiges Bild der Azure-Nutzung für eine große Organisation bereitstellen.

Erstellen Sie in der Bewertungsphase eine Bestandsaufnahme der Ressourcen:
  - Jeder Abonnementadministrator und Mandantenadministrator sollte eine Reihe von Skripts ausführen, um Ressourcengruppen, die Ressourcen in jeder Ressourcengruppe und die Bereitstellungseinstellungen der Ressourcengruppen aufzulisten.
  - Dokumentieren Sie anwendungsübergreifende Abhängigkeiten in Azure und mit externen Systemen.
  - Dokumentieren Sie die Anzahl jeder Azure-Ressource und die Menge der Daten, die mit jeder Instanz verknüpft sind, die Sie migrieren möchten.
  - Stellen Sie sicher, dass die Anwendungsarchitekturdokumente mit der Liste der Azure-Ressourcen konsistent sind.

Am Ende dieser Phase sind Ihnen die folgenden Informationen bekannt:

- Eine vollständige Liste der Azure-Ressourcen, die verwendet werden
- Eine Liste mit den Abhängigkeiten zwischen Ressourcen
- Informationen über die Komplexität des Migrationsaufwands

### <a name="plan"></a>Plan

In der Planungsphase führen Sie die folgenden Aufgaben aus:

- Verwenden Sie die Ausgabe der Abhängigkeitsanalyse, die in der Bewertungsphase ausgeführt wurde, um zusammengehörige Komponenten zu definieren. Erwägen Sie ein gemeinsames Migrieren zusammengehöriger Komponenten in einem *Migrationspaket*.
- [Optional] Nutzen Sie die Migration als Gelegenheit, [Gartner 5-R-Kriterien](https://www.gartner.com/newsroom/id/1684114) anzuwenden und Ihre Workload zu optimieren.
- Bestimmen Sie die Zielumgebung in Azure weltweit:
  1. Bestimmen Sie den Zielmandanten in Azure weltweit (ggf müssen Sie diesen erstellen).
  1. Erstellen Sie Abonnements.
  1. Wählen Sie den Standort von Azure weltweit aus, zu dem Sie migrieren möchten.
  1. Führen Sie Testmigrationsszenarios aus, in denen Ihre Architektur in Azure Deutschland mit der Architektur in Azure weltweit übereinstimmt.
- Bestimmen Sie die geeignete Zeitachse und den geeigneten Zeitplan für die Migration. Erstellen Sie einen Benutzerakzeptanz-Testplan für jedes Migrationspaket.

### <a name="migrate"></a>Migrieren

Verwenden Sie in der Migrationsphase die Tools, Verfahren und Empfehlungen, die in den Azure Deutschland-Migrationsartikeln erläutert sind, um neue Ressourcen in Azure weltweit zu erstellen. Konfigurieren Sie anschließend die Anwendungen.

### <a name="validate"></a>Überprüfen

In der Überprüfungsphase führen Sie die folgenden Aufgaben aus:

1. Führen Sie Benutzerakzeptanztests aus.
1. Stellen Sie sicher, dass Anwendungen wie erwartet ausgeführt werden.
1. Synchronisieren Sie die aktuellen Daten in der Zielumgebung, falls zutreffend.
1. Wechseln Sie zu einer neuen Anwendungsinstanz in Azure weltweit.
1. Vergewissern Sie sich, dass die Produktionsumgebung wie erwartet funktioniert.
1. Nehmen Sie Ressourcen in Azure Deutschland außer Betrieb.

## <a name="terms"></a>Begriffe

Diese Begriffe werden in den Azure Deutschland-Migrationsartikeln verwendet:

**Quelle** beschreibt, von wo Sie Ressourcen migrieren (z. B. Azure Deutschland):

- **Quellmandantenname**: Der Name des Mandanten in Azure Deutschland (alles nach dem **\@** -Zeichen im Kontonamen). Die Mandantennamen in Azure Deutschland enden alle mit **microsoftazure.de**.
- **Quellmandanten-ID**: Die ID des Mandanten in Azure Deutschland. Die Mandanten-ID wird im Azure-Portal angezeigt, wenn Sie den Mauszeiger über den Kontonamen in der oberen rechten Ecke bewegen.
- **Quellabonnement-ID**: Die ID des Quellabonnements in Azure Deutschland. Sie können mehrere Abonnements im selben Mandanten haben. Stellen Sie immer sicher, dass Sie das richtige Abonnement verwenden.
- **Quellregion**: Entweder „Deutschland, Mitte“ (**germanycentral**) oder „Deutschland, Nordosten“ (**germanynortheast**), je nachdem, wo sich die zu migrierende Ressource befindet.

**Ziel** oder **Zielpunkt** beschreibt, wohin Sie Ressourcen migrieren:

- **Zielmandantenname**: Der Name des Mandanten in Azure weltweit.
- **Zielmandanten-ID**: Die ID des Mandanten in Azure weltweit.
- **Zielabonnement-ID**: Die Abonnement-ID für die Ressource in Azure weltweit.
- **Zielregion**: Sie können fast jede Region in Azure weltweit verwenden. Es ist wahrscheinlich, dass Sie Ihre Ressourcen zu „Europa, Westen“ (**westeurope**) oder „Europa, Norden“ (**northeurope**) migrieren möchten.

> [!NOTE]
> Vergewissern Sie sich, dass der Azure-Dienst, den Sie migrieren, in der Zielregion angeboten wird. Alle Azure-Dienste, die in Azure Deutschland verfügbar sind, stehen in „Europa, Westen“ zur Verfügung. Alle Azure-Dienste, die in Azure Deutschland verfügbar sind, sind in „Europa, Norden“ verfügbar, mit Ausnahme von Azure Machine Learning Studio und der G/GS VM-Serie in Azure Virtual Machines.

Es empfiehlt sich, das Quell- und das Zielportal in Ihrem Browser mit einem Lesezeichen zu versehen:

- Das Azure Deutschland-Portal befindet sich unter [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/).
- Das Azure weltweit-Portal befindet sich unter [ https://portal.azure.com/ ](https://portal.azure.com/).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Speicher](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
