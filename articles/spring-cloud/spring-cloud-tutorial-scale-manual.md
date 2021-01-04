---
title: Skalieren einer Anwendung in Azure Spring Cloud | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie im Azure-Portal eine Anwendung mit Azure Spring Cloud skalieren.
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 5632f9a6126615255306cc89425bd08a9ffa9753
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531801"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skalieren einer Anwendung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In dieser Dokumentation wird veranschaulicht, wie Sie mithilfe des Azure Spring Cloud-Dashboards im Azure-Portal eine beliebige Microserviceanwendung skalieren.

Skalieren Sie Ihre Anwendung zentral hoch und herunter, indem Sie die Anzahl der virtuellen CPUs (vCPUs) und die Arbeitsspeichermenge ändern. Sie können Ihre Anwendung horizontal herunterskalieren oder erweitern, indem Sie die Anzahl von Anwendungsinstanzen ändern.

Anschließend wissen Sie, wie jede Anwendung in Ihrem Dienst schnell manuell geändert werden kann. Die Skalierung wird in Sekunden wirksam und erfordert weder Codeänderungen noch die erneute Bereitstellung.

## <a name="prerequisites"></a>Voraussetzungen

Um die folgenden Verfahren ausführen zu können, benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 
* Eine bereitgestellte Azure Spring Cloud-Dienstinstanz.  Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](spring-cloud-quickstart.md).
* Mindestens eine bereits in Ihrer Dienstinstanz erstellte Anwendung.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigieren zur Seite „Skalieren“ im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zur Seite **Übersicht** von Azure Spring Cloud.

1. Wählen Sie die Ressourcengruppe aus, die Ihren Dienst enthält.

1. Wählen Sie im Menü auf der linken Seite unter der Überschrift **Einstellungen** die Registerkarte **Apps** aus.

1. Wählen Sie die Anwendung aus, die Sie skalieren möchten. In diesem Beispiel wird die Anwendung mit dem Namen **account-service** skaliert. Dann sollte die Seite **Übersicht** der Anwendung angezeigt werden.

1. Wechseln Sie im Menü auf der linken Seite unter der Überschrift **Einstellungen** zur Registerkarte **Skalieren**. Es sollten Optionen für die Skalierung der Attribute angezeigt werden. Diese werden im folgenden Abschnitt veranschaulicht.

## <a name="scale-your-application"></a>Skalieren Ihrer Anwendung

Wenn Sie die Skalierungsattribute ändern, beachten Sie die folgenden Hinweise:

* **CPUs**: Pro Anwendungsinstanz sind maximal vier CPUs zulässig. Die Gesamtanzahl von CPUs für eine Anwendung ist der hier festgelegte Wert multipliziert mit der Anzahl von Anwendungsinstanzen.

* **Arbeitsspeicher/GB**: Die pro Anwendungsinstanz zulässige maximale Arbeitsspeichermenge beträgt 8 GB. Die Gesamtmenge des Arbeitsspeichers für eine Anwendung ist der hier festgelegte Wert multipliziert mit der Anzahl von Anwendungsinstanzen.

* **Anzahl von App-Instanzen**: Im Standard-Tarif können Sie maximal 20 Instanzen aufskalieren. Dieser Wert ändert die Anzahl separat ausgeführter Instanzen der Microserviceanwendung.

Wählen Sie unbedingt **Speichern** aus, um Ihre Skalierungseinstellungen zu übernehmen.

![Der Skalierungsdienst im Azure-Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Nach wenigen Sekunden werden die von Ihnen vorgenommenen Änderungen auf der Seite **Übersicht** angezeigt. Weitere Details können Sie auf der Registerkarte **Anwendungsinstanzen** einsehen. Für die Skalierung sind weder Codeänderungen noch eine erneute Bereitstellung erforderlich.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade auf den Standard-Tarif
Wenn Sie sich im Tarif „Basic“ befinden und durch eins oder mehrere dieser [Limits](spring-cloud-quotas.md) eingeschränkt sind, können Sie ein Upgrade auf den Tarif „Standard“ durchführen. Wechseln Sie zu diesem Zweck zum Menü „Preise“, indem Sie zuerst die Tarifspalte „Standard“ auswählen und dann auf die Schaltfläche **Upgrade** klicken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel wurde erläutert, wie Sie eine Azure Spring Cloud-Anwendung manuell skalieren. Informationen zur Überwachung einer Anwendung durch Einrichten von Warnungen finden Sie unter [Einrichten der Autoskalierung für Microserviceanwendungen](spring-cloud-tutorial-setup-autoscale.md).

> [!div class="nextstepaction"]
> [Informationen zum Einrichten von Warnungen](spring-cloud-tutorial-alerts-action-groups.md)
