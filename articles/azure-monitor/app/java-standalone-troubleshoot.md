---
title: 'Problembehandlung: Azure Monitor für Java in Application Insights'
description: Problembehandlung bei Azure Monitor für Java in Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855545"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Problembehandlung bei Azure Monitor für Java in Application Insights

In diesem Artikel wurden einige der häufiger auftretenden Probleme mit den Schritten zum Beheben behandelt, auf die Benutzer beim Instrumentieren einer Java-Anwendung mit dem Java-Agent stoßen können.

## <a name="self-diagnostic-log-file"></a>Protokolldatei zur Selbstdiagnose

Standardmäßig wird von Java 3.0 in Application Insights eine Protokolldatei mit dem Namen `applicationinsights.log` in dem Verzeichnis erstellt, in dem sich auch die Datei `applicationinsights-agent-3.0.0.jar` befindet.

Diese Protokolldatei sollten Sie als Erstes auf Hinweise auf aufgetretene Probleme untersuchen.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Upgrade von Application Insights SDK für Java 2.x

Weitere Informationen finden Sie unter [Upgraden vom 2.x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Upgrade von der Vorschauversion 3.0

Lesen Sie sich bei einem Upgrade von der Vorschauversion 3.0 sorgfältig alle [Konfigurationsoptionen](./java-standalone-config.md) durch, da die JSON-Struktur im GA-Release 3.0 vollständig geändert wurde.

Zu diesen Änderungen zählen die folgenden:

1.  Der Name der Konfigurationsdatei selbst wurde von `ApplicationInsights.json` in `applicationinsights.json` geändert.
2.  Der Knoten `instrumentationSettings` ist nicht mehr vorhanden. Alle Inhalte in `instrumentationSettings` wurden auf die Stammebene verschoben. 
3.  Konfigurationsknoten wie `sampling`, `jmxMetrics`, `instrumentation` und `heartbeat` wurden aus `preview` auf die Stammebene verschoben.

## <a name="ssl-certificate-issues"></a>SSL-Zertifikatprobleme

Wenn Sie den standardmäßigen Java-Keystore verwenden, enthält dieser bereits alle Stammzertifikate der Zertifizierungsstelle, sodass Sie keine weiteren SSL-Zertifikate importieren müssen.

Wenn Sie einen benutzerdefinierten Java-Keystore verwenden, müssen Sie ggf. die SSL-Zertifikate für den Application Insights-Endpunkt importieren.

### <a name="some-key-terminology"></a>Wesentliche Terminologie:
Der *Keystore* ist ein Repository mit Zertifikaten, öffentlichen und privaten Schlüsseln. Normalerweise verfügen JDK-Distributionen über eine ausführbare Datei für ihre Verwaltung: `keytool`.

Das folgende Beispiel ist ein einfacher Befehl zum Importieren eines SSL-Zertifikats in den Keystore:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Schritte zum Herunterladen und Hinzufügen des SSL-Zertifikats:

1.  Öffnen Sie Ihren bevorzugten Browser, und navigieren Sie wie unten dargestellt zur `IngestionEndpoint`-URL in der Verbindungszeichenfolge, die zum Instrumentieren Ihrer Anwendung verwendet wird.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights-Verbindungszeichenfolge":::

2.  Klicken Sie im Browser auf „Websiteinformationen anzeigen“ (Schlosssymbol), und klicken Sie wie unten dargestellt auf die Option „Zertifikat“.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Erfassen des SSL-Zertifikats":::

3.  Navigieren Sie zur Registerkarte „Details“, und klicken Sie auf „In Datei kopieren“.
4.  Klicken Sie auf die Schaltfläche „Weiter“, und wählen Sie das Format „Base64-codiert X.509 (.CER)“ und dann „Weiter“ aus.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Export-Assistent für SSL-Zertifikate":::

5.  Geben Sie die Datei an, in der Sie das SSL-Zertifikat speichern möchten. Klicken Sie abschließend auf „Weiter“ und auf „Fertig stellen“. Die Meldung „Der Exportvorgang wurde erfolgreich abgeschlossen“ sollte angezeigt werden.
6.  Wenn Sie über das Zertifikat verfügen, können Sie es in einen Java-Keystore importieren. Verwenden Sie den obigen [Befehl](#some-key-terminology), um Zertifikate zu importieren.

> [!WARNING]
> Sie müssen diese Schritte wiederholen, um ein neues Zertifikat zu erhalten, bevor das aktuelle Zertifikat abläuft. Informationen zum Ablauf finden Sie auf der Registerkarte „Details“ im Popup des Zertifikats, wie unten gezeigt.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Details zum SSL-Zertifikat":::
