---
title: Einrichten der Hochverfügbarkeit
description: Erhöhen Sie die Resilienz ihrer Defender für IoT-Bereitstellung, indem Sie die lokale Verwaltungskonsole als Hochverfügbarkeits-Appliance installieren. Mit einer Hochverfügbarkeitsbereitstellung wird sichergestellt, dass Ihre verwalteten Sensoren laufend an eine aktive lokale Verwaltungskonsole berichten können.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8b647d5e97439db4805a7346a02db3f845dc9e37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523888"
---
# <a name="about-high-availability"></a>Informationen zur Hochverfügbarkeit

Erhöhen Sie die Resilienz ihrer Defender für IoT-Bereitstellung, indem Sie die lokale Verwaltungskonsole als Hochverfügbarkeits-Appliance installieren. Mit einer Hochverfügbarkeitsbereitstellung wird sichergestellt, dass Ihre verwalteten Sensoren laufend an eine aktive lokale Verwaltungskonsole berichten können.

Diese Bereitstellung wird mit einem Paar lokaler Verwaltungskonsolen implementiert, das sich aus einer primären und sekundären Appliance zusammensetzt.

## <a name="about-primary-and-secondary-communication"></a>Informationen zur Kommunikation zwischen primärer und sekundärer Appliance

Bei der Kopplung einer primären und sekundären lokalen Verwaltungskonsole passiert Folgendes:

- Zum Erstellen einer sicheren Verbindung zwischen der primären und sekundären Appliance wird für die lokale Verwaltungskonsole ein SLL-Zertifikat verwendet. Bei dem SLL-Zertifikat kann es sich um das selbstsignierte, standardmäßig installierte Zertifikat oder ein vom Kunden installiertes Zertifikat handeln.

- Die Daten der primären lokalen Verwaltungskonsole werden automatisch alle 10 Minuten auf der sekundären lokalen Verwaltungskonsole gesichert. Gesichert werden die Konfigurationen und Gerätedaten der lokalen Verwaltungskonsole. PCAP-Dateien und -Protokolle werden nicht in die Sicherung einbezogen. Sie können PCAP-Dateien und Protokolle manuell sichern und wiederherstellen.

- Die Einrichtung der primären Verwaltungskonsole (z. B. Systemeinstellungen) wird auf der sekundären Konsole dupliziert. Werden diese Einstellungen auf der primären Verwaltungskonsole aktualisiert, werden sie automatisch auch auf der sekundären Konsole aktualisiert.

- Bevor die Lizenz der sekundären Verwaltungskonsole abläuft, sollten Sie diese als primär definieren, um die Lizenz zu aktualisieren.

## <a name="about-failover-and-failback"></a>Informationen zu Failover und Failback

Wenn ein Sensor keine Verbindung mit der primären lokalen Verwaltungskonsole herstellen kann, stellt er automatisch eine Verbindung mit der sekundären Konsole her. Ihr System wird gleichzeitig von der primären und der sekundären Verwaltungskonsole unterstützt, wenn weniger als die Hälfte der Sensoren mit der sekundären Konsole kommunizieren. Die sekundäre Konsole übernimmt, wenn mehr als die Hälfte der Sensoren mit ihr kommuniziert. Ein Failover von der primären zur sekundären Verwaltungskonsole dauert ungefähr drei Minuten. Bei einem Failover wird die primäre lokale Verwaltungskonsole eingefroren. In einem solchen Fall können Sie sich mit den gleichen Anmeldeinformationen bei der sekundären Verwaltungskonsole anmelden.

Während des Failovers versuchen die Sensoren weiterhin, mit der primären Appliance zu kommunizieren. Wenn mehr als die Hälfte der verwalteten Sensoren erfolgreich mit der primären Verwaltungskonsole kommunizieren kann, wird die primäre Appliance wiederhergestellt. Bei Wiederherstellung der primären Verwaltungskonsole wird auf der sekundären Konsole die folgende Meldung angezeigt:

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Screenshot einer Meldung, die auf der sekundären Konsole angezeigt wird, wenn die primäre Verwaltungskonsole wiederhergestellt wird.":::

Melden Sie sich nach der Umleitung wieder bei der primären Appliance an.

## <a name="high-availability-setup-overview"></a>Übersicht über das Einrichten der Hochverfügbarkeit

Die Installations- und Konfigurationsverfahren lassen sich in vier Hauptphasen unterteilen:

1. Installieren einer lokalen Verwaltungskonsole als primäre Appliance. 

2. Konfigurieren der lokalen Verwaltungskonsole als primäre Appliance, zum Beispiel Einstellungen für geplante Sicherungen, VLAN-Einstellungen usw. Einzelheiten dazu finden Sie im Benutzerhandbuch für die lokale Verwaltungskonsole. Alle Einstellungen werden nach der Kopplung automatisch auf die sekundäre Appliance angewendet.

3. Installieren einer lokalen Verwaltungskonsole als sekundäre Appliance. Weitere Informationen finden Sie unter [Informationen zur Installation von Defender für IoT](how-to-install-software.md).

4. Koppeln Sie, wie [hier](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)beschrieben, die primäre und sekundäre Verwaltungskonsolen-Appliance. Von der primären lokalen Verwaltungskonsole müssen mindestens zwei Sensoren verwaltet werden, damit Sie die Einrichtung vornehmen können.

## <a name="high-availability-requirements"></a>Anforderungen für die Hochverfügbarkeit

Stellen Sie sicher, dass für die Hochverfügbarkeit die folgenden Anforderungen erfüllt sind:

- Zertifikatanforderungen

- Software- und Hardwareanforderungen

- Netzwerk-Zugriffsanforderungen

### <a name="software-and-hardware-requirements"></a>Software- und Hardwareanforderungen

- Für die primäre und die sekundäre Verwaltungskonsolen-Appliance müssen identische Hardwaremodelle und Softwareversionen ausgeführt werden.

- Das Hochverfügbarkeitssystem kann nur von Defender für IoT-Benutzern mithilfe von CLI-Tools eingerichtet werden.

### <a name="network-access-requirements"></a>Netzwerkzugriffsanforderungen

Sie müssen überprüfen, ob die Sicherheitsrichtlinie Ihres Unternehmens Ihnen den Zugriff auf die folgenden Dienste auf der primären und sekundären Verwaltungskonsole erlaubt. Diese Dienste ermöglichen auch die Verbindung zwischen den Sensoren und der sekundären lokalen Verwaltungskonsole:

|Port|Dienst|BESCHREIBUNG|
|----|-------|-----------|
|**443 oder TCP**|HTTPS|Erlaubt den Zugriff auf die Webkonsole der lokalen Verwaltungskonsole.|
|**22 oder TCP**|SSH|Synchronisiert die Daten zwischen der primären und sekundären lokalen Verwaltungskonsolen-Appliance.|
|**123 oder UDP**|NTP| Die NTP-Zeitsynchronisierung für die lokale Verwaltungskonsole. Vergewissern Sie sich, dass für die aktive und passive Appliance dieselbe Zeitzone definiert ist.|

## <a name="create-the-primary-and-secondary-pair"></a>Erstellen der Kopplung aus primärer und sekundärer Appliance

Vergewissern Sie sich vor Beginn des Verfahrens, dass die primäre und sekundäre lokale Verwaltungskonsolen-Appliance eingeschaltet sind.  

### <a name="on-the-primary"></a>Primäre Appliance

1. Melden Sie sich an der CLI als Defender für IoT-Benutzer an.

2. Führen Sie auf der primären Appliance den folgenden Befehl aus:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>In diesem Dokument wird der „Principal“ der lokalen Verwaltungskonsole als „primär“ und der „Agent“ als „sekundär“ bezeichnet.

3. Geben Sie im Feld ```<Secondary ip>``` die IP-Adresse der sekundären Appliance ein, und drücken Sie die EINGABETASTE. Die IP-Adresse wird dann überprüft, und das SSL-Zertifikat wird auf die primäre Appliance heruntergeladen. Mit der Eingabe der IP-Adresse werden auch die Sensoren der sekundären Appliance zugeordnet.

4. Führen Sie den folgenden Befehl auf der primären Appliance aus, um sicherzustellen, dass das Zertifikat ordnungsgemäß installiert ist:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Führen Sie auf der primären Appliance den folgenden Befehl aus. **„sudo“ darf nicht für die Ausführung verwendet werden.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Dadurch wird die Verbindung zwischen der primären und sekundären Appliance zu Sicherungs- und Wiederherstellungszwecken ermöglicht.

6. Geben Sie die IP-Adresse der sekundären Appliance ein, und drücken Sie die EINGABETASTE.

### <a name="on-the-secondary"></a>Sekundäre Appliance

1. Melden Sie sich an der CLI als Defender für IoT-Benutzer an.

2. Führen Sie auf der sekundären Appliance den folgenden Befehl aus. **„sudo“ darf nicht für die Ausführung verwendet werden**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Dadurch wird die Verbindung zwischen der primären und sekundären Appliance zu Sicherungs- und Wiederherstellungszwecken ermöglicht.

3. Geben Sie die IP-Adresse der primären Appliance ein, und drücken Sie die EINGABETASTE.

### <a name="track-high-availability-activity"></a>Nachverfolgen von Aktivitäten im Zusammenhang mit der Hochverfügbarkeit

Die Kernprotokolle der Anwendung können exportiert und an das Defender für IoT-Supportteam gesendet werden, um Probleme mit der Hochverfügbarkeit zu beheben.  

So greifen Sie auf die Kernprotokolle zu

1. Wählen Sie im Fenster **Systemeinstellungen** die Option **Exportieren** aus.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Update der lokalen Verwaltungskonsole auf Hochverfügbarkeit

Führen Sie das Hochverfügbarkeits-Update in der folgenden Reihenfolge aus. Stellen Sie sicher, dass jeder Schritt beendet ist, bevor Sie mit einem neuen Schritt beginnen.

So führen Sie das Hochverfügbarkeits-Update aus

1. Aktualisieren Sie die primäre lokale Verwaltungskonsole.

2. Aktualisieren Sie die sekundäre lokale Verwaltungskonsole.

3. Aktualisieren Sie die Sensoren.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren und Einrichten der lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md)
