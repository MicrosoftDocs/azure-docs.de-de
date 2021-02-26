---
title: Verbinden von Agari Phishing Defense and Brand Protection-Lösungen mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Agari Phishing Defense and Brand Protection-Connector so einsetzen, dass seine Protokolle in Azure Sentinel übertragen werden. Zeigen Sie Agari-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3684b5a8069a6b683ae562c527af89814362a7f9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806985"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Verbinden von Agari Phishing Defense and Brand Protection-Lösungen mit Azure Sentinel

> [!IMPORTANT]
> Der Agari Phishing Defense and Brand Protection-Connector ist derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit dem Agari Phishing Defense and Brand Protection-Connector lassen sich Protokolle Ihrer Agari Phishing Defense and Brand Protection-Lösungen problemlos mit Azure Sentinel verbinden, sodass Sie die Daten in Arbeitsmappen anzeigen, zum Erstellen benutzerdefinierter Warnungen abfragen und zur Verbesserung der Untersuchung von Problemen integrieren können. Die Lösungen von Agari lassen sich über Azure Functions und die REST-API in Azure Sentinel integrieren.

Darüber hinaus kommen Benutzer von Brand Protection und Phishing Response in den Genuss der gemeinsamen Nutzung von Threat Intelligence-Daten über die Security Graph-API.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verbinden von Agari Phishing Defense and Brand Protection-Lösungen mit Azure Sentinel müssen die folgenden Voraussetzungen erfüllt sein:

- Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

- Lese- und Schreibberechtigungen für Azure Functions, um eine Funktions-App erstellen zu können. [Informieren Sie sich ausführlicher über Azure Functions](../azure-functions/index.yml).

- Stellen Sie sicher, dass Sie **Client-ID** und **Geheimschlüssel** zur Hand haben (identisch für alle Agari-Lösungen). Anweisungen finden Sie auf der [Agari-Website für Entwickler](https://developers.agari.com/agari-platform/docs/quick-start).

## <a name="configure-and-connect-agari-solutions"></a>Konfigurieren und Verbinden von Agari-Lösungen 

Mit Agari-Lösungen lassen sich Protokolle über eine Azure Functions-App direkt in Azure Sentinel integrieren und exportieren.

> [!NOTE]
> Dieser Connector verbindet sich über Azure Functions mit den Lösungen von Agari, um deren Protokolle in Azure Sentinel zu übertragen. Dies kann zusätzliche Datenerfassungskosten verursachen. Ausführliche Informationen finden Sie auf der Seite mit den [Preisen von Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

1. **Tragen Sie Ihre Anmeldeinformationen für die Agari-API zusammen:** 

    Stellen Sie sicher, dass über die **Client-ID** und **geheimen Schlüssel** für Agari verfügen. Anweisungen finden Sie auf der [Agari-Website für Entwickler](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **(Optional) Aktivieren Sie die Security Graph-API:** 

    Die Agari-Funktions-App ermöglicht den Austausch von Bedrohungsdaten mit Azure Sentinel über die Security Graph-API. Hierfür muss der [Sentinel Threat Intelligence Platforms-Connector](connect-threat-intelligence.md) aktiviert werden. Außerdem muss [eine Anwendung in Azure Active Directory registriert werden](/graph/auth-register-app-v2).

    Dadurch erhalten Sie drei Informationen, die Sie beim Bereitstellen der Funktions-App unten verwenden können: die **Mandanten-ID von Graph**, die **Client-ID von Graph** und das **Clientgeheimnis von Graph**.

1. **Stellen Sie den Connector und die zugehörige Azure-Funktions-App bereit:** 

    1. Wählen Sie im Azure Sentinel-Portal **Datenconnectors** aus. Wählen Sie **Agari Phishing Defense and Brand Protection (Preview)** und **Connectorseite öffnen** aus.

    1. Kopieren Sie unter **Konfiguration** die **Arbeitsbereich-ID** von Azure Sentinel und den **Primärschlüssel**, und fügen Sie so ein, dass Sie diese Informationen zur Hand haben.

    1. Wählen Sie **Bereitstellung in Azure** aus. (Möglicherweise müssen Sie nach unten scrollen, um die Schaltfläche zu finden.)

    1. Der Bildschirm **Benutzerdefinierte Bereitstellung** wird angezeigt.

        - Geben Sie die **Client-ID** und das **Clientgeheimnis** (geheime Schlüssel) von Agari ein.

        - Fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** von Azure Sentinel ein, die Sie zuvor kopiert haben.

        - Wählen Sie für die Agari-Lösungen, für die Sie aktive Abonnements haben, **Wahr** oder **Falsch** aus.

        - Wenn Sie eine Azure-Anwendung für die Freigabe von Indikatoren für Sicherheitsrisiken für Azure Sentinel mithilfe der Security Graph-API erstellt haben, wählen Sie **True** für **Enable Security Graph Sharing** (Freigabe für Security Graph aktivieren) aus. Geben Sie die **Graph tenant ID** (Mandanten-ID für Graph), die **Graph client ID** (Graph-Client-ID) und das **Graph client secret** (Clientgeheimnis für Graph) ein.

    1. Klicken Sie auf **Überprüfen + erstellen**. Klicken Sie nach Abschluss der Prüfung auf **Erstellen**.

1. **Weisen Sie Ihrer Funktions-App die erforderlichen Berechtigungen zu:**

    Der Agari-Connector speichert Zeitstempel für Protokollzugriffe in einer Umgebungsvariablen. Damit die Anwendung in diese Variable schreiben kann, müssen der vom System zugewiesenen Identität Berechtigungen zugewiesen werden.

    1. Navigieren Sie im Azure-Portal zu **Funktions-App**.

    1. Wählen Sie auf dem Blatt **Funktions-App** in der Liste Ihre Funktions-App und dann im Navigationsmenü der Funktions-App **Identität** unter **Einstellungen** aus.

    1. Legen Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** auf **Ein** fest. 

    1. Wählen Sie **Speichern** aus, woraufhin die Schaltfläche **Azure-Rollenzuweisungen** angezeigt wird. Klicken Sie auf diese Schaltfläche.

    1. Wählen Sie auf dem Bildschirm **Azure-Rollenzuweisungen** die Option **Rollenzuweisung hinzufügen** aus. Legen Sie **Bereich** auf **Abonnement** fest. Wählen Sie in der Dropdownliste **Abonnement** Ihr Abonnement aus, und legen Sie **Rolle** auf **App Configuration-Datenbesitzer** fest. 

    1. Wählen Sie **Speichern** aus.

## <a name="find-your-data"></a>Suchen von Daten

Nach erfolgreichem Verbindungsaufbau werden die Daten in **Protokolle** unter *Benutzerdefinierte Protokolle* in den folgenden Tabellen angezeigt: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Um Daten aus Agari-Lösungen abzufragen, geben Sie in das Abfragefenster einen der oben genannten Tabellennamen ein.

Einige hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie die Agari Phishing Defense and Brand Protection-Lösungen mit Azure Sentinel verbinden können. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
