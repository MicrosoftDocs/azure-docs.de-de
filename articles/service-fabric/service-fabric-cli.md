---
title: Erste Schritte mit der Azure Service Fabric CLI
description: Hier erfahren Sie, wie Sie die Azure Service Fabric CLI verwenden. Es wird beschrieben, wie Sie eine Verbindung mit einem Cluster herstellen und Anwendungen verwalten.
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d5b6f183a59e3f47aa5867b5e09e06541a6a67db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803246"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Bei der Azure Service Fabric-Befehlszeilenschnittstelle (CLI) handelt es sich um ein Befehlszeilen-Hilfsprogramm zum Interagieren mit und Verwalten von Service Fabric-Entitäten. Für die Service Fabric CLI ist die Verwendung mit Windows- oder mit Linux-Clustern möglich. Die Service Fabric CLI wird auf allen Plattformen ausgeführt, die Python unterstützen.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Installation sicher, dass in Ihrer Umgebung Python und pip installiert sind. Weitere Informationen finden Sie in der [Schnellstartdokumentation zu pip](https://pip.pypa.io/en/latest/quickstart/) und der offiziellen [Python-Installationsdokumentation](https://wiki.python.org/moin/BeginnersGuide/Download).

Die CLI unterstützt die Python-Versionen 2.7, 3.5, 3.6 und 3.7. Die empfohlene Version ist Python 3.x, da für Python 2.7 bald der Support eingestellt wird.

### <a name="service-fabric-target-runtime"></a>Service Fabric-Ziellaufzeit

Die Service Fabric-Befehlszeilenschnittstelle ist zur Unterstützung der neuesten Laufzeitversion des Service Fabric SDK gedacht. Die folgende Tabelle gibt Aufschluss darüber, welche Version der Befehlszeilenschnittstelle Sie installieren sollten:

| CLI-Version   | Unterstützte Laufzeitversion |
|---------------|---------------------------|
| Neueste (~= 7)  | Neueste (~= 6.4)            |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6.2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6,0                       |
| 1.1.0         | 5.6, 5.7                  |

Sie können optional eine zu installierende Zielversion der Befehlszeilenschnittstelle angeben, indem Sie den Befehl `pip install` mit dem Suffix `==<version>` versehen. Die Syntax für Version 1.1.0 lautet beispielsweise wie folgt:

```
pip install -I sfctl==1.1.0
```

Ersetzen Sie den folgenden Befehl vom Typ `pip install` bei Bedarf durch den zuvor genannten Befehl.

Weitere Informationen zu Service Fabric-CLI-Releases finden Sie in der [GitHub-Dokumentation](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Installieren von pip, Python und der Service Fabric CLI

Es gibt viele Möglichkeiten, pip und Python auf Ihrer Plattform zu installieren. Im Anschluss sind einige Schritte aufgeführt, mit denen Sie gängige Betriebssysteme schnell mit Python 3 und pip einrichten können.

### <a name="windows"></a>Windows

Verwenden Sie für Windows 10, Windows Server 2016 und Windows Server 2012 R2 die offizielle Standardanleitung für die Installation. Mit dem Python-Installationsprogramm wird standardmäßig auch pip installiert.

1. Navigieren Sie zur offiziellen [Python-Downloadseite](https://www.python.org/downloads/), und laden Sie die neueste Version von Python 3.7 herunter.

2. Starten Sie das Installationsprogramm.

3. Wählen Sie unten in der Eingabeaufforderung die Option **Add Python 3.7 to PATH** (Python 3.7 zu PATH hinzufügen).

4. Wählen Sie **Jetzt installieren**, und schließen Sie die Installation ab.

Sie können nun ein neues Befehlsfenster öffnen und die Version von Python und PIP abrufen.

```bat
python --version
pip --version
```

Führen Sie dann den folgenden Befehl aus, um die Azure Service Fabric-CLI (sfctl) zu installieren und die CLI-Hilfeseite anzuzeigen:

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu und Windows-Subsystem für Linux

Führen Sie zum Installieren der Service Fabric-Befehlszeilenschnittstelle die folgenden Befehle aus:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Die Installation können Sie anschließend mit Folgendem testen:

```bash
sfctl -h
```

Es kann vorkommen, dass ein Fehler aufgrund eines nicht gefundenen Befehls auftritt:

`sfctl: command not found`

Vergewissern Sie sich in diesem Fall, dass über `$PATH` auf `~/.local/bin` zugegriffen werden kann:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Sollte die Installation im Windows-Subsystem für Linux aufgrund falscher Ordnerberechtigungen nicht erfolgreich sein, muss der Vorgang unter Umständen mit erhöhten Berechtigungen wiederholt werden:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)

Führen Sie zum Installieren der Service Fabric-Befehlszeilenschnittstelle unter Red Hat die folgenden Befehle aus:

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

Sie können die Schritte im Abschnitt **Ubuntu und Windows-Subsystem für Linux** durchführen, um die Installation zu testen.

<a name = "cli-mac"></a>
### <a name="macos"></a>macOS

Für MacOS empfehlen wir Ihnen die Nutzung des [HomeBrew-Paket-Managers](https://brew.sh). Wenn HomeBrew nicht bereits installiert ist, können Sie die Installation mit dem folgenden Befehl durchführen:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Installieren Sie dann über das Terminal Python 3.7, pip und die Service Fabric CLI, indem Sie die folgenden Befehle ausführen:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI-Syntax

Die Befehle verfügen immer über das Präfix `sfctl`. Allgemeine Informationen zu allen Befehlen, die Sie nutzen können, erhalten Sie mit `sfctl -h`. Hilfe zu einem einzelnen Befehl erhalten Sie mit `sfctl <command> -h`.

Befehle folgen einer wiederholbaren Struktur. Dabei steht das Ziel des Befehls vor dem Verb oder der Aktion.

```azurecli
sfctl <object> <action>
```

In diesem Beispiel ist `<object>` das Ziel für `<action>`.

## <a name="select-a-cluster"></a>Auswählen eines Clusters

Bevor Sie Vorgänge durchführen, müssen Sie einen Cluster auswählen, mit dem die Verbindung hergestellt wird. Führen Sie beispielsweise den folgenden Befehl aus, um den Cluster `testcluster.com` auszuwählen und eine Verbindung damit herzustellen:

> [!WARNING]
> Vermeiden Sie die Verwendung von ungeschützten Service Fabric-Clustern in einer Produktionsumgebung.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

Der Clusterendpunkt muss das Präfix `http` oder `https` aufweisen. Er muss den Port für das HTTP-Gateway enthalten. Der Port und die Adresse entsprechen der Service Fabric Explorer-URL.

Für Cluster, die mit einem Zertifikat gesichert sind, können Sie ein PEM-codiertes Zertifikat angeben. Das Zertifikat kann als eine einzelne Datei oder als Zertifikat und Schlüsselpaar angegeben werden. Handelt es sich um ein selbstsigniertes Zertifikat, das nicht von einer Zertifizierungsstelle signiert wurde, können Sie die Option `--no-verify` übergeben, um die Überprüfung der Zertifizierungsstelle zu umgehen.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem sicheren Cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Einfache Vorgänge

Informationen zur Clusterverbindung werden über mehrere Service Fabric CLI-Sitzungen hinweg beibehalten. Nach dem Auswählen eines Service Fabric-Clusters können Sie im Cluster einen beliebigen Service Fabric-Befehl ausführen.

Verwenden Sie beispielsweise den folgenden Befehl, um den Integritätsstatus des Service Fabric-Clusters abzurufen:

```azurecli
sfctl cluster health
```

Mit diesem Befehl wird die folgende Ausgabe zurückgegeben:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tipps und Problembehandlung

Hier sind einige Vorschläge und Tipps zum Beheben von allgemeinen Problemen angegeben.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Konvertieren eines Zertifikats aus dem PFX- in das PEM-Format

Die Service Fabric-Befehlszeilenschnittstelle unterstützt clientseitige Zertifikate als PEM-Dateien (Erweiterung „.pem“). Bei Verwendung von PFX-Dateien in Windows müssen Sie diese Zertifikate in das PEM-Format konvertieren. Mit dem folgenden Befehl konvertieren Sie eine PFX-Datei in eine PEM-Datei:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Wenn Sie eine PFX-Datei in eine PEM-Datei konvertieren möchten, können Sie den folgenden Befehl verwenden. (Hier wird kein Kennwort angegeben.)

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Weitere Informationen finden Sie in der [OpenSSL-Dokumentation](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Verbindungsprobleme

Für einige Vorgänge wird unter Umständen die folgende Meldung generiert:

`Failed to establish a new connection`

Stellen Sie sicher, dass der angegebene Clusterendpunkt verfügbar ist und lauscht. Überprüfen Sie auch, ob die Service Fabric Explorer-Benutzeroberfläche unter diesem Host und Port verfügbar ist. Verwenden Sie `sfctl cluster select`, um den Endpunkt zu aktualisieren.

### <a name="detailed-logs"></a>Detaillierte Protokolle

Detaillierte Protokolle sind häufig hilfreich, wenn Sie das Debuggen durchführen oder ein Problem melden. Mit dem `--debug`-Flag wird die Ausführlichkeit der Ausgabe erhöht.

### <a name="command-help-and-syntax"></a>Befehle – Hilfe und Syntax

Verwenden Sie das `-h`-Flag, um Hilfe zu einem bestimmten Befehl oder einer Gruppe von Befehlen zu erhalten.

```azurecli
sfctl application -h
```

Hier ist ein weiteres Beispiel angegeben:

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Aktualisieren der Service Fabric-Befehlszeilenschnittstelle 

Führen Sie zum Aktualisieren der Service Fabric-Befehlszeilenschnittstelle die folgenden Befehle aus, und ersetzen Sie dabei `pip` durch `pip3` (abhängig von der getroffenen Auswahl bei der ursprünglichen Installation):

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Anwendung mit der Azure Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
* [Erste Schritte mit Service Fabric unter Linux](service-fabric-get-started-linux.md)
