---
title: Workloadschutz für Ihre Kubernetes-Workloads
description: Erfahren Sie, wie Sie die Sicherheitsempfehlungen von Azure Security Center für den Kubernetes-Workloadschutz verwenden.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: f024457ed9f20e6bab0852a53ab6a60ac85ed25a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546260"
---
# <a name="protect-your-kubernetes-workloads"></a>Schützen Ihrer Kubernetes-Workloads

Auf dieser Seite wird beschrieben, wie Sie die Sicherheitsempfehlungen von Azure Security Center für den Kubernetes-Workloadschutz verwenden.

Weitere Informationen zu diesen Features finden Sie unter [Bewährte Methoden zum Workloadschutz mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

Security Center bietet noch weitere Features für die Containersicherheit, wenn Sie Azure Defender aktivieren. Dies betrifft insbesondere:

- Scannen Ihrer Containerregistrierungen auf Sicherheitsrisiken mit [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)
- Abrufen von Warnungen zur Bedrohungserkennung in Echtzeit für Ihre K8s-Cluster mit [Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Eine Liste *aller* Sicherheitsempfehlungen für Kubernetes-Cluster und -Knoten finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu Empfehlungen.



## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** oder **Sicherheitsadministrator** zum Bearbeiten einer Zuweisung<br>**Leser** zum Anzeigen der Empfehlungen|
|Umgebungsanforderungen:|Kubernetes Version 1.14 oder höher ist erforderlich.<br>Keine PodSecurityPolicy-Ressource (altes PSP-Modell) in den Clustern<br>Windows-Knoten werden nicht unterstützt.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||


## <a name="set-up-your-workload-protection"></a>Einrichten des Workloadschutzes

Azure Security Center enthält einige Empfehlungen, die verfügbar sind, wenn Sie das **Azure Policy-Add-On für Kubernetes** installieren.

### <a name="step-1-deploy-the-add-on"></a>Schritt 1: Bereitstellen des Add-Ons

Installieren Sie zum Konfigurieren der Empfehlungen das **Azure Policy-Add-On für Kubernetes**. 

- Sie können dieses Add-On auch automatisch bereitstellen, wie unter [Aktivieren der automatischen Bereitstellung von Erweiterungen](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions) erläutert. Wenn die automatische Bereitstellung für das Add-On auf „Ein“ festgelegt ist, wird die Erweiterung standardmäßig in allen vorhandenen und zukünftigen Clustern aktiviert (sofern diese die Add-On-Installationsanforderungen erfüllen).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Verwenden des Security Center-Tools zur automatischen Bereitstellung zur Installation des Richtlinien-Add-Ons für Kubernetes":::

- Gehen Sie folgendermaßen vor, um das Add-On manuell bereitzustellen:

    1. Suchen Sie auf der Seite „Empfehlungen“ nach der Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**":::

        > [!TIP]
        > Die Empfehlung ist in fünf verschiedenen Sicherheitskontrollen enthalten, und es spielt keine Rolle, welche Sie im nächsten Schritt auswählen.

    1. Wählen Sie die Empfehlung in einer der Sicherheitskontrollen aus, um die Ressourcen anzuzeigen, in denen Sie das Add-On installieren können.
    1. Wählen Sie den entsprechenden Cluster aus, und klicken Sie auf **Korrigieren**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Detailseite der Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Schritt 2: Anzeigen und Konfigurieren des Pakets aus 13 Empfehlungen

1. Ungefähr 30 Minuten nach Abschluss der Installation des Add-Ons zeigt Security Center den Integritätsstatus der Cluster für die folgenden Empfehlungen an (jeweils in der relevanten Sicherheitskontrolle):

    > [!TIP]
    > Einige Empfehlungen weisen Parameter auf, die über Azure Policy angepasst werden müssen, damit sie effektiv verwendet werden können. Um beispielsweise von der Empfehlung **Containerimages sollten nur von vertrauenswürdigen Registrierungen bereitgestellt werden** zu profitieren, müssen Sie Ihre vertrauenswürdigen Registrierungen definieren.
    > 
    > Wenn Sie die erforderlichen Parameter für die Empfehlungen, die konfiguriert werden müssen, nicht eingeben, werden Ihre Workloads als fehlerhaft angezeigt.

    | Name der Empfehlung                                                         | Sicherheitskontrolle                         | Konfiguration erforderlich |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Für Container müssen CPU- und Arbeitsspeicherlimits erzwungen werden                          | Anwendungen vor DDoS-Angriffen schützen | Nein                     |
    | Privilegierte Container müssen vermieden werden                                     | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Unveränderliches (schreibgeschütztes) Stammdateisystem für Container erzwingen     | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Container mit Rechteausweitung müssen vermieden werden                       | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Das Ausführen von Containern als Root-Benutzer muss vermieden werden                           | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Container mit Freigabe sensibler Hostnamespaces vermeiden              | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Linux-Funktionen mit den niedrigsten Berechtigungen für Container erzwingen       | Zugriff und Berechtigungen verwalten            | **Ja**                |
    | Verwendung von HostPath-Volumeeinbindungen von Pods auf eine bekannte Liste beschränken    | Zugriff und Berechtigungen verwalten            | **Ja**                |
    | Container dürfen nur an zulässigen Ports lauschen                              | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Dienste dürfen nur an zulässigen Ports lauschen                                | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Verwendung von Hostnetzwerken und -ports einschränken                     | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Überschreiben oder Deaktivieren des AppArmor-Profils für Container einschränken | Optimieren von Sicherheitskonfigurationen        | **Ja**                |
    | Containerimages sollten nur von vertrauenswürdigen Registrierungen bereitgestellt werden            | Sicherheitsrisiken beheben                | **Ja**                |
    |||


1. Passen Sie bei Empfehlungen, die dies erfordern, die Parameter an:

    1. Wählen Sie im Menü von Security Center **Sicherheitsrichtlinie** aus.
    1. Wählen Sie das relevante Abonnement aus.
    1. Wählen Sie im Abschnitt **Security Center-Standardrichtlinie** die Option **Effektive Richtlinie anzeigen** aus.
    1. Wählen Sie „ASC-Standard“ aus.
    1. Öffnen Sie die Registerkarte **Parameter**, und ändern Sie die Werte nach Bedarf.
    1. Klicken Sie auf **Überprüfen + speichern**.
    1. Wählen Sie **Speichern** aus.


1. Um eine der Empfehlungen zu erzwingen, gehen Sie wie folgt vor: 

    1. Öffnen Sie die Seite mit den Details zur Empfehlung, und wählen Sie **Ablehnen** aus:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Option „Ablehnen“ für Azure Policy-Parameter":::

        Dadurch wird das Fenster geöffnet, in dem Sie den Bereich festlegen. 

    1. Wenn Sie den Bereich festgelegt haben, wählen Sie **In „Ablehnen“ ändern** aus.

1. So zeigen Sie die für Ihre Cluster geltenden Empfehlungen an

    1. Öffnen Sie die Seite [Ressourcenbestand](asset-inventory.md) von Security Center, und verwenden Sie den Ressourcentypfilter für **Kubernetes-Dienste**.

    1. Wählen Sie einen Cluster aus, um die für diesen verfügbaren Empfehlungen zu untersuchen und zu überprüfen. 

1. Wenn Sie eine Empfehlung für den Workloadschutz anzeigen, sehen Sie neben dem Cluster auch die Anzahl der betroffenen Pods („Kubernetes-Komponenten“). Wählen Sie den Cluster und dann **Aktion ausführen** aus, um eine Liste mit den jeweiligen Pods auszuwählen.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Anzeigen der betroffenen Pods für eine K8s-Empfehlung"::: 

1. Um die Erzwingung zu testen, verwenden Sie die folgenden beiden Kubernetes-Bereitstellungen:

    - Eine ist eine fehlerfreie Bereitstellung, die mit den Empfehlungen zum Workloadschutz konform ist.
    - Die andere stellt eine fehlerhafte Bereitstellung dar, die mit *keiner* der Empfehlungen konform ist.

    Stellen Sie die YAML-Beispieldateien unverändert bereit, oder verwenden Sie sie als Referenz zum Korrigieren Ihrer eigenen Workload (Schritt VIII).  


## <a name="healthy-deployment-example-yaml-file"></a>YAML-Dateibeispiel einer fehlerfreien Bereitstellung

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>YAML-Dateibeispiel einer fehlerhaften Bereitstellung

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Kubernetes-Workloadschutz konfigurieren. 

Weitere verwandte Informationen finden Sie auf den folgenden Seiten: 

- [Security Center-Empfehlungen für Compute](recommendations-reference.md#recs-compute)
- [Warnungen auf AKS-Clusterebene](alerts-reference.md#alerts-akscluster)
- [Warnungen auf Containerhostebene](alerts-reference.md#alerts-containerhost)