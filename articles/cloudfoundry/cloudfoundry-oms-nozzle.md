---
title: Bereitstellen von Azure Log Analytics Nozzle zur Überwachung von Cloud Foundry
description: Enthält eine Schritt-für-Schritt-Anleitung zur Bereitstellung von Cloud Foundry Loggregator Nozzle für Azure Log Analytics. Verwenden Sie Nozzle zum Überwachen der Systemintegrität und Leistungsmetriken von Cloud Foundry.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: fde0afcd37cd464b0b87e5ccd257d4a7a684eeb0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021587"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Bereitstellen von Azure Log Analytics Nozzle zur Überwachung des Cloud Foundry-Systems

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) ist ein Dienst in Azure. Er dient zum Erfassen und Analysieren von Daten, die von Ihren cloudbasierten und lokalen Umgebungen generiert werden.

Log Analytics Nozzle (die Nozzle-Komponente) ist eine Cloud Foundry-Komponente (CF), die Metriken aus der Firehose-Komponente von [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) an Azure Monitor-Protokolle weiterleitet. Mithilfe der Nozzle-Komponente können Sie Integritäts- und Leistungsmetriken für Ihr CF-System über mehrere Bereitstellungen hinweg sammeln, anzeigen und analysieren.

In diesem Dokument erfahren Sie, wie Sie die Nozzle-Komponente in Ihrer CF-Umgebung bereitstellen und dann über die Konsole von Azure Monitor-Protokolle auf die Daten zugreifen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Schritte sind Voraussetzungen für die Bereitstellung der Nozzle-Komponente:

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Bereitstellen einer CF- oder Pivotal Cloud Foundry-Umgebung in Azure

Sie können die Nozzle-Komponente entweder mit einer Open-Source-CF-Bereitstellung (Cloud Foundry) oder mit einer PCF-Bereitstellung (Pivotal Cloud Foundry) verwenden.

* [Deploy Cloud Foundry on Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) (Bereitstellen von Cloud Foundry in Azure)

* [Installing Pivotal Cloud Foundry on Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html) (Installieren von Pivotal Cloud Foundry in Azure)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installieren der CF-Befehlszeilentools zum Bereitstellen der Nozzle-Komponente

Die Nozzle-Komponente wird als Anwendung in der CF-Umgebung ausgeführt. Sie benötigen die CF-Befehlszeilenschnittstelle, um die Anwendung bereitzustellen.

Außerdem ist für die Nozzle-Komponente eine Zugriffsberechtigung für Loggregator Firehose und den Cloud Controller erforderlich. Zum Erstellen und Konfigurieren des Benutzers benötigen Sie den UAA-Dienst (User Account and Authentication, Benutzerkonto und Authentifizierung).

* [Installing the cf CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html) (Installieren der CF-Befehlszeilenschnittstelle)

* [CloudFoundry UAA Command Line Client](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md) (Cloud Foundry-UAA-Befehlszeilenclient)

Stellen Sie vor dem Einrichten des UAA-Befehlszeilenclients sicher, dass RubyGems installiert ist.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Erstellen eines Log Analytics-Arbeitsbereichs in Azure

Sie können den Log Analytics-Arbeitsbereich manuell oder mit einer Vorlage erstellen. Die Vorlage stellt ein Setup von vorkonfigurierten KPI-Ansichten und -Warnungen für die Konsole von Azure Monitor-Protokolle bereit. 

#### <a name="to-create-the-workspace-manually"></a>Gehen Sie wie folgt vor, um den Arbeitsbereich manuell zu erstellen:

1. Durchsuchen Sie im Azure-Portal die Marketplace-Dienstliste, und wählen Sie dann Log Analytics-Arbeitsbereiche aus.
2. Wählen Sie die Option **Erstellen** und anschließend Optionen für die folgenden Elemente aus:

   * **Log Analytics-Arbeitsbereich**: Geben Sie einen Namen für den Arbeitsbereich ein.
   * **Abonnement**: Wählen Sie das Abonnement aus, das Ihrer CF-Bereitstellung entspricht, falls Sie über mehrere Abonnements verfügen.
   * **Ressourcengruppe**: Sie können eine neue Ressourcengruppe erstellen oder die gleiche wie für Ihre CF-Bereitstellung verwenden.
   * **Standort**: Geben Sie den Standort ein.
   * **Tarif:** Wählen Sie **OK** aus, um den Vorgang abzuschließen.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Monitor-Protokolle](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Gehen Sie wie folgt vor, um den Log Analytics-Arbeitsbereich über die Überwachungsvorlage aus dem Azure Marketplace zu erstellen:

1. Öffnen Sie das Azure-Portal.
1. Klicken Sie oben links auf das Symbol „+“ oder auf „Ressource erstellen“.
1. Geben Sie im Suchfenster „Cloud Foundry“ ein, und wählen Sie „Cloud Foundry-Überwachungslösung“ aus.
1. Die Titelseite der Vorlage der Cloud Foundry-Überwachungslösung wird geladen. Klicken Sie auf „Erstellen“, um die Vorlage zu starten.
1. Geben Sie die erforderlichen Parameter ein:
    * **Abonnement**: Wählen Sie ein Azure-Abonnement für den Log Analytics-Arbeitsbereich aus, in der Regel identisch mit der Cloud Foundry-Bereitstellung.
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe für den Log Analytics-Arbeitsbereich.
    * **Ressourcengruppenstandort**: Wählen Sie den Standort der Ressourcengruppe aus.
    * **OMS_Workspace_Name**: Geben Sie einen Arbeitsbereichnamen ein. Wenn der Arbeitsbereich noch nicht vorhanden ist, wird die Vorlage einen neuen erstellen.
    * **OMS_Workspace_Region**: Wählen Sie den Speicherort für den Arbeitsbereich aus.
    * **OMS_Workspace_Pricing_Tier**: Wählen Sie das SKU für den Log Analytics-Arbeitsbereich aus. Sie können diese [Preisübersicht](https://azure.microsoft.com/pricing/details/log-analytics/) als Referenz nutzen.
    * **Rechtliche Bedingungen**: Klicken Sie auf „Rechtliche Bedingungen“ und dann auf „Erstellen“, um die rechtlichen Bedingungen zu akzeptieren.
1. Wenn Sie alle Parameter angegeben haben, klicken Sie auf „Erstellen“, um die Vorlage bereitzustellen. Wenn die Bereitstellung abgeschlossen ist, wird der Status auf der Registerkarte „Benachrichtigung“ angezeigt.


## <a name="deploy-the-nozzle"></a>Bereitstellen der Nozzle-Komponente

Sie haben verschiedene Möglichkeiten, um die Nozzle-Komponente bereitzustellen: als PCF-Datei oder als CF-Anwendung.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Bereitstellen der Nozzle-Komponente als PCF Operations Manager-Kachel

Gehen Sie wie folgt vor, um [Azure Log Analytics Nozzle für PCF zu installieren und zu konfigurieren](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Das ist der vereinfachte Ansatz, die PCF Operations Manager-Kachel wird die Nozzle-Komponente automatisch konfigurieren und pushen. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Manuelles Bereitstellen der Nozzle-Komponente als CF-Anwendung

Wenn Sie PCF Ops Manager nicht verwenden, stellen Sie die Nozzle-Komponente als Anwendung bereit. In den folgenden Abschnitten wird dieser Prozess beschrieben.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Anmelden an Ihrer CF-Bereitstellung über die CF-Befehlszeilenschnittstelle als Administrator

Führen Sie den folgenden Befehl aus:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

„SYSTEM_DOMAIN“ steht für Ihren CF-Domänennamen. Diesen können Sie ermitteln, indem Sie in Ihrer CF-Bereitstellungsmanifestdatei nach „SYSTEM_DOMAIN“ suchen. 

„CF_User“ ist der CF-Administratorname. Zum Ermitteln des Namens und Kennworts können Sie den Abschnitt „scim“ in Ihrer CF-Bereitstellungsmanifestdatei nach dem Namen und nach „cf_admin_password“ durchsuchen.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Erstellen Sie einen CF-Benutzer, und gewähren Sie die erforderlichen Berechtigungen.

Führen Sie die folgenden Befehle aus:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

„SYSTEM_DOMAIN“ steht für Ihren CF-Domänennamen. Diesen können Sie ermitteln, indem Sie in Ihrer CF-Bereitstellungsmanifestdatei nach „SYSTEM_DOMAIN“ suchen.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Herunterladen der neuesten Version von Log Analytics Nozzle

Führen Sie den folgenden Befehl aus:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Nun können Sie die Umgebungsvariablen in der Datei „manifest.yml“ in Ihrem aktuellen Verzeichnis festlegen. Unten ist das App-Manifest für die Nozzle-Komponente angegeben. Ersetzen Sie die Werte durch die spezifischen Informationen Ihres Log Analytics-Arbeitsbereichs.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Übertragen der Anwendung von Ihrem Entwicklungscomputer per Pushvorgang

Vergewissern Sie sich, dass Sie sich im Ordner „oms-log-analytics-firehose-nozzle“ befinden. Führen Sie den folgenden Befehl aus:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Überprüfen der Nozzle-Installation

### <a name="from-apps-manager-for-pcf"></a>Über Apps Manager (für PCF)

1. Melden Sie sich bei Operations Manager an, und vergewissern Sie sich, dass die Kachel auf dem Installationsdashboard angezeigt wird.
2. Melden Sie sich bei Apps Manager an, und vergewissern Sie sich, dass der für die Nozzle-Komponente erstellte Bereich im Nutzungsbericht aufgeführt wird und der Status normal ist.

### <a name="from-your-development-computer"></a>Über Ihren Entwicklungscomputer

Geben Sie im Fenster der CF-Befehlszeilenschnittstelle Folgendes ein:
```
cf apps
```
Vergewissern Sie sich, dass die OMS-Nozzle-Anwendung ausgeführt wird.

## <a name="view-the-data-in-the-azure-portal"></a>Anzeigen der Daten im Azure-Portal

Wenn Sie die Überwachungslösung über die Marketplace-Vorlage bereitgestellt haben, wechseln Sie zum Azure-Portal, und suchen Sie nach der Lösung. Sie finden sie in der Ressourcengruppe, die Sie in der Vorlage angegeben haben. Klicken Sie auf die Lösung, und navigieren Sie zu „Log Analytics-Konsole“. Dort werden die vorkonfigurierten Ansichten mit den wichtigsten Cloud Foundry-System-KPIs, Anwendungsdaten, Warnungen und VM-Integritätsmetriken aufgeführt. 

Wenn Sie den Log Analytics-Arbeitsbereich manuell erstellt haben, gehen Sie wie folgt vor, um Ansichten und Warnungen zu erstellen:

### <a name="1-import-the-oms-view"></a>1. Importieren der OMS-Ansicht

Navigieren Sie über das OMS-Portal zu **Ansicht-Designer** > **Importieren** > **Durchsuchen**, und wählen Sie einige OMSVIEW-Dateien aus. Wählen Sie beispielsweise *Cloud Foundry.omsview* aus, und speichern Sie die Ansicht. Auf der Seite **Übersicht** wird nun eine Kachel angezeigt. Wählen Sie diese Kachel aus, um visualisierte Metriken anzuzeigen.

Sie können diese Ansichten über den **Ansicht-Designer** anpassen oder neue Ansichten erstellen.

*„Cloud Foundry.omsview“* ist eine Vorschauversion der Cloud Foundry-OMS-Ansichtsvorlage. Dies ist eine vollständig konfigurierte Standardvorlage. Wenn Sie Vorschläge oder Feedback für die Vorlage haben, können Sie diese im [Abschnitt zu den Problemen](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues) hinterlassen.

### <a name="2-create-alert-rules"></a>2. Erstellen von Warnungsregeln

Sie können [die Warnungen erstellen](../azure-monitor/platform/alerts-overview.md) und die Abfragen und Schwellenwerte nach Bedarf anpassen. Hier sind empfohlene Warnungen angegeben:

| Suchabfrage                                                                  | Warnung generieren basierend auf | BESCHREIBUNG                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Anzahl von Ergebnissen < 1   | **bbs.Domain.cf-apps** gibt an, ob die Domäne „cf-apps“ aktuell ist. Dies bedeutet, dass CF-App-Anforderungen von Cloud Controller für die Ausführung mit „bbs.LRPsDesired“ (von Diego gewünschte KIs) synchronisiert werden. Wenn keine Daten empfangen werden, bedeutet dies, dass die Domäne „cf-apps“ im angegebenen Zeitraum nicht auf dem neuesten Stand ist. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Anzahl von Ergebnissen > 0   | Für Diego-Zellen bedeutet der Wert 0 fehlerfrei und der Wert 1 fehlerhaft. Legen Sie die Warnung fest, wenn im angegebenen Zeitfenster mehrere fehlerhafte Diego-Zellen erkannt werden. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Anzahl von Ergebnissen > 0 | Bei 1 ist das System fehlerfrei, bei 0 nicht. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Anzahl von Ergebnissen > 0   | Consul gibt in regelmäßigen Abständen seinen Integritätsstatus aus. Bei 0 ist das System fehlerfrei. Bei 1 hat der Routen-Emitter den Ausfall von Consul festgestellt. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Anzahl von Ergebnissen > 0 | Die Deltaanzahl von Nachrichten, die von Doppler aufgrund eines Rückstaus vorsätzlich gelöscht wurden. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Anzahl von Ergebnissen > 0   | Loggregator gibt **LGR** aus, um auf Probleme mit dem Protokollierungsprozess hinzuweisen. Ein Beispiel für diese Art von Problem ist eine zu hohe Ausgabe von Protokollmeldungen. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Anzahl von Ergebnissen > 0   | Wenn die Nozzle-Komponente von Loggregator eine Warnung wegen eines langsamen Consumers empfängt, sendet sie das ValueMetric-Element **slowConsumerAlert** an Azure Monitor-Protokolle. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Anzahl von Ergebnissen > 0   | Wenn die Deltaanzahl verloren gegangener Ereignisse einen Schwellenwert erreicht, liegt unter Umständen ein Problem mit der Nozzle-Ausführung vor. |

## <a name="scale"></a>Skalieren

Sie können die Nozzle-Komponente und den Loggregator skalieren.

### <a name="scale-the-nozzle"></a>Skalieren der Nozzle-Komponente

Sie sollten mindestens mit zwei Instanzen der Nozzle-Komponente beginnen. Die Firehose-Komponente verteilt die Workload auf alle Instanzen der Nozzle-Komponente.
Richten Sie die Warnung **slowConsumerAlert** (im vorherigen Abschnitt „Erstellen von Warnungsregeln“ aufgeführt) ein, um sicherzustellen, dass die Nozzle-Komponente den Datenverkehr von der Firehose-Komponente bewältigen kann. Befolgen Sie nach Erhalt der Warnung den [Leitfaden für eine langsame Nozzle-Komponente](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz), um zu ermitteln, ob eine Skalierung erforderlich ist.
Verwenden Sie zum Hochskalieren der Nozzle-Komponente entweder Apps Manager oder die CF-Befehlszeilenschnittstelle, um die Instanzanzahl oder die Arbeitsspeicher- bzw. Datenträgerressourcen für die Nozzle-Komponente zu erhöhen.

### <a name="scale-the-loggregator"></a>Skalieren von Loggregator

Loggregator sendet eine Protokollmeldung **LGR**, um auf Probleme mit dem Protokollierungsprozess hinzuweisen. Sie können die Warnung überwachen, um zu ermitteln, ob Loggregator zentral hochskaliert werden muss.
Erhöhen Sie zum Hochskalieren von Loggregator entweder die Doppler-Puffergröße, oder fügen Sie im CF-Manifest zusätzliche Doppler-Serverinstanzen hinzu. Weitere Informationen finden Sie im [Leitfaden zum Skalieren von Loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualisieren

Wenn Sie die Nozzle-Komponente mit einer neueren Version aktualisieren möchten, laden Sie die neue Nozzle-Version herunter, führen die Schritte im vorherigen Abschnitt „Bereitstellen der Nozzle-Komponente“ aus und übermitteln die Anwendung per Pushvorgang erneut.

### <a name="remove-the-nozzle-from-ops-manager"></a>Entfernen der Nozzle-Komponente aus Ops Manager

1. Melden Sie sich bei Ops Manager an.
2. Suchen Sie nach der Kachel **Microsoft Azure Log Analytics Nozzle für PCF**.
3. Wählen Sie das Papierkorbsymbol, und bestätigen Sie den Löschvorgang.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Entfernen der Nozzle-Komponente vom Entwicklungscomputer

Geben Sie im Fenster der CF-Befehlszeilenschnittstelle Folgendes ein:
```
cf delete <App Name> -r
```

Wenn Sie die Nozzle-Komponente entfernen, werden die Daten im OMS-Portal nicht automatisch entfernt. Sie laufen gemäß Ihrer Aufbewahrungseinstellung für Azure Monitor-Protokolle ab.

## <a name="support-and-feedback"></a>Support und Feedback

Azure Log Analytics Nozzle ist eine Open-Source-Komponente. Senden Sie Ihre Fragen und Ihr Feedback über den [GitHub-Abschnitt](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Wählen Sie „Virtueller Computer mit Cloud Foundry“ als Dienstkategorie, wenn Sie eine Azure-Supportanfrage erstellen möchten. 

## <a name="next-step"></a>Nächster Schritt

Seit PCF 2.0 werden VM-Leistungsmetriken vom System Metrics Forwarder an Azure Log Analytics Nozzle übertragen und in den Log Analytics-Arbeitsbereich integriert. Sie benötigen keinen Log Analytics-Agent für die VM-Leistungsmetriken mehr. Jedoch können Sie den Log Analytics-Agent weiterhin verwenden, um Syslog-Informationen zu erfassen. Der Log Analytics-Agent wird als Bosh-Add-On für Ihre CF-VMs installiert. 

Ausführliche Informationen finden Sie unter [Deploy Log Analytics agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Bereitstellen des Log Analytics-Agents für Ihre Cloud Foundry-Bereitstellung).