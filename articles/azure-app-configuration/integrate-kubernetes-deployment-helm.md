---
title: Integrieren von Azure App Configuration in eine Kubernetes-Bereitstellung mit Helm
description: Hier erfahren Sie, wie Sie dynamische Konfigurationen in einer Kubernetes-Bereitstellung mit Helm verwenden.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979826"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrieren in eine Kubernetes-Bereitstellung mit Helm

Helm ermöglicht das Definieren, Installieren und Upgraden von in Kubernetes ausgeführten Anwendungen. Ein Helm-Chart enthält die Informationen, die zum Erstellen einer Instanz einer Kubernetes-Anwendung erforderlich sind. Die Konfiguration wird außerhalb des Charts in einer Datei namens *values.yaml* gespeichert. 

Im Rahmen des Releaseprozesses wird das Chart von Helm mit der passenden Konfiguration zusammengeführt, um die Anwendung auszuführen. So können beispielsweise in *values.yaml* definierte Variablen als Umgebungsvariablen innerhalb der ausgeführten Container referenziert werden. Helm unterstützt auch die Erstellung von Kubernetes-Geheimnissen. Diese können als Datenvolumes eingebunden oder als Umgebungsvariablen verfügbar gemacht werden.

Sie können die in *values.yaml* gespeicherten Werte überschreiben, indem Sie beim Ausführen von Helm zusätzliche YAML-basierte Konfigurationsdateien über die Befehlszeile angeben. Azure App Configuration unterstützt das Exportieren von Konfigurationswerten in YAML-Dateien. Durch die Integration dieser Exportfunktion in Ihre Bereitstellung können von Ihren Kubernetes-Anwendungen in App Configuration gespeicherte Konfigurationswerte genutzt werden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Verwenden von Werten aus App Configuration beim Bereitstellen einer Anwendung in Kubernetes mit Helm
> * Erstellen eines Kubernetes-Geheimnisses auf der Grundlage eines Key Vault-Verweises in App Configuration

In diesem Tutorial werden Grundkenntnisse im Zusammenhang mit der Verwaltung von Kubernetes mit Helm vorausgesetzt. Weitere Informationen zum Installieren von Anwendungen mit Helm in Azure Kubernetes Service finden Sie [hier](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Voraussetzungen

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (Version 2.4.0 oder höher).
- Installieren Sie [Helm](https://helm.sh/docs/intro/install/) (Version 2.14.0 oder höher).
- Ein Kubernetes-Cluster.

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** > **Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | settings.color | White |
    | settings.message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hinzufügen eines Key Vault-Verweises zu App Configuration
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und fügen Sie [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) ein Geheimnis mit dem Namen **Password** und dem Wert **myPassword** hinzu. 
2. Wählen Sie die Instanz des App Configuration-Speichers aus, die Sie im vorherigen Abschnitt erstellt haben.

3. Wählen Sie **Konfigurations-Explorer** aus.

4. Wählen Sie **+ Erstellen** > **Schlüsseltresorverweis** aus, und geben Sie dann die folgenden Werte an:
    - **Schlüssel**: Wählen Sie **secrets.password** aus.
    - **Bezeichnung:** Lassen Sie diesen Wert leer.
    - **Abonnement**, **Ressourcengruppe** und **Schlüsseltresor**: Geben Sie die Werte ein, die den Werten des im vorherigen Schritt erstellten Schlüsseltresors entsprechen.
    - **Geheimnis**: Wählen Sie das Geheimnis mit dem Namen **Password** aus, das Sie im vorherigen Abschnitt erstellt haben.

## <a name="create-helm-chart"></a>Erstellen des Helm-Charts ##
Erstellen Sie zunächst mithilfe des folgenden Befehls ein exemplarisches Helm-Chart:
```console
helm create mychart
```

Von Helm wird ein neues Verzeichnis mit dem Namen „mychart“ und der unten gezeigten Struktur erstellt. 

> [!TIP]
> Weitere Informationen finden Sie in [diesem Chartleitfaden](https://helm.sh/docs/chart_template_guide/getting_started/).

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Aktualisieren Sie als Nächstes den Abschnitt **spec:template:spec:containers** der Datei *deployment.yaml*. Im folgenden Codeausschnitt werden dem Container zwei Umgebungsvariablen hinzugefügt. Die zugehörigen Werte werden zur Bereitstellungszeit dynamisch festgelegt.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Nach der Aktualisierung sollte die Datei *deployment.yaml* wie folgt aussehen:

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Fügen Sie im Ordner „templates“ eine Datei namens *secrets.yaml* hinzu, um vertrauliche Daten als Kubernetes-Geheimnisse zu speichern.

> [!TIP]
> Weitere Informationen zur Verwendung von Kubernetes-Geheimnissen finden Sie [hier](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Aktualisieren Sie abschließend die Datei *values.yaml* mit folgendem Inhalt, um optional Standardwerte der Konfigurationseinstellungen und Geheimnisse anzugeben, auf die in den Dateien *deployment.yaml* und *secrets.yaml* verwiesen wird. Die tatsächlichen Werte werden durch die aus App Configuration gepullte Konfiguration überschrieben.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Übergeben der Konfiguration aus App Configuration in der Helm-Installation ##
Laden Sie zunächst die Konfiguration aus App Configuration in eine Datei namens *myConfig.yaml* herunter. Verwenden Sie einen Schlüsselfilter, um nur die Schlüssel herunterzuladen, die mit **settings.** beginnen. Sollte der Schlüsselfilter in Ihrem Fall nicht ausreichen, um Schlüssel von Key Vault-Verweisen auszuschließen, können Sie das Argument **--skip-keyvault** verwenden, um sie auszuschließen. 

> [!TIP]
> Weitere Informationen zum Exportbefehl finden Sie [hier](/cli/azure/appconfig/kv#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Laden Sie als Nächstes Geheimnisse in eine Datei namens *mySecrets.yaml* herunter. Das Befehlszeilenargument **--resolve-keyvault** dient zum Auflösen der Key Vault-Verweise, indem die tatsächlichen Werte in Key Vault abgerufen werden. Dieser Befehl muss mit Anmeldeinformationen ausgeführt werden, die über Zugriffsberechtigungen für die entsprechende Key Vault-Instanz verfügen.

> [!WARNING]
> Die Datei enthält vertrauliche Informationen. Bewahren Sie sie daher an einem sicheren Ort auf, und führen Sie eine Bereinigung durch, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Verwenden Sie das Argument **-f** von „helm upgrade“, um die beiden von Ihnen erstellten Konfigurationsdateien zu übergeben. Dadurch werden die in *values.yaml* definierten Konfigurationswerte mit den aus App Configuration exportierten Werten überschrieben.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Sie können auch das Argument **--set** von „helm upgrade“ verwenden, um literale Schlüsselwerte zu übergeben. Durch die Verwendung des Arguments **--set** lässt sich die Speicherung vertraulicher Daten auf dem Datenträger vermeiden. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Vergewissern Sie sich, dass die Konfigurationen und Geheimnisse erfolgreich festgelegt wurden, indem Sie auf das [Kubernetes-Dashboard](../aks/kubernetes-dashboard.md) zugreifen. Wie Sie sehen, wurden die Umgebungsvariablen des Containers mit den Werten **color** und **message** aus App Configuration aufgefüllt.

![Schnellstartanleitung: Lokales Starten der App](./media/kubernetes-dashboard-env-variables.png)

Ein als Key Vault-Verweis in App Configuration gespeichertes Geheimnis (**password**) wurde auch zu Kubernetes-Geheimnissen hinzugefügt. 

![Screenshot, auf dem das Kennwort im Abschnitt „Daten“ hervorgehoben ist](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Azure App Configuration-Daten für die Verwendung in einer Kubernetes-Bereitstellung mit Helm exportiert. Fahren Sie mit den Azure CLI-Beispielen fort, um mehr über die Verwendung von App Configuration zu erfahren.

> [!div class="nextstepaction"]
> [Azure-Befehlszeilenschnittstelle](/cli/azure/appconfig)
