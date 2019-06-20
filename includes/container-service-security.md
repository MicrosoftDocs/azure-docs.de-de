---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 39bb75a6f834789f91cb590ffebb72f45624eb25
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178644"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(VERALTET) Schützen von Docker-Containern in Azure Container Service

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Dieser Artikel enthält Überlegungen und Empfehlungen zum Sichern von Docker-Containern, die in Azure Container Service bereitgestellt wurden. Viele dieser Überlegungen gelten allgemein für Docker-Container, die in Azure oder anderen Umgebungen bereitgestellt wurden. 

## <a name="image-security"></a>Sicherheit von Images

Container werden aus Images erstellt, die in einem oder mehreren Repositorys gespeichert sind. Diese Repositorys können zu öffentlichen oder privaten Containerregistrierungen gehören. [Docker Hub](https://hub.docker.com/) ist ein Beispiel für eine öffentliche Registrierung. Ein Beispiel für eine private Registrierung ist die [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), die lokal oder in einer virtuellen privaten Cloud installiert werden kann. Es gibt auch cloudbasierte private Containerregistrierungsdienste, wie z.B. [Azure Container Registry](../articles/container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Öffentliche und private Images
Im Allgemeinen gilt für ein öffentlich verfügbares Containerimage dasselbe wie für jedes andere öffentlich veröffentlichte Softwarepaket: Für die Sicherheit kann nicht garantiert werden. Containerimages bestehen aus mehreren Softwareschichten, und in jeder Softwareschicht können Sicherheitsrisiken vorliegen. Es ist von größter Bedeutung, den Ursprung des Containerimages zu kennen, einschließlich des Imagebesitzers (um zu ermitteln, ob es sich um eine zuverlässige Quelle handelt oder nicht). Zudem müssen Sie wissen, aus welchen Softwareschichten das Image besteht und welche Softwareversionen vorhanden sind. 

Wenn Sie z.B. das offizielle [Nginx-Repository](https://hub.docker.com/_/nginx/) in Docker Hub öffnen und zur Registerkarte **Tags** navigieren, sehen Sie, dass für jedes Image farbcodierte Sicherheitsrisiken angezeigt werden. Jede Farbe weist auf eine Sicherheitslücke einer Softwareschicht des Images hin. Weitere Informationen zum Suchen nach Sicherheitsrisiken in Docker Hub finden Sie unter [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Grundlegendes zu offiziellen Repositorys in Docker Hub).

![Nginx-Images in Docker Hub](./media/container-service-security/docker-hub-nginx.png)

Die Sicherheit spielt in Unternehmen eine sehr große Rolle, daher sollten sie Images in einer privaten Registrierung wie z.B. Azure Container Registry oder Docker Trusted Registry speichern und von dort abrufen, um sich vor Sicherheitsangriffen zu schützen. Azure Container Registry stellt nicht nur eine verwaltete private Registrierung bereit, sondern unterstützt auch die [dienstprinzipalbasierte Authentifizierung](../articles/container-registry/container-registry-authentication.md) über Azure Active Directory für grundlegende Authentifizierungsabläufe wie z.B. rollenbasierten Zugriff für Lese-, Schreib- und Besitzerberechtigungen.

### <a name="image-security-scanning"></a>Sicherheitsüberprüfung von Images

Auch wenn Sie eine private Registrierung verwenden, ist es ratsam, Lösungen für die Imageüberprüfung zu verwenden, um die Sicherheit zu erhöhen. Jede Softwareschicht in einem Containerimage ist potenziell anfällig für Sicherheitsrisiken, unabhängig von den anderen Schichten im Containerimage. Da Unternehmen ihre Produktionsworkloads in zunehmendem Maß mithilfe von Containertechnologien bereitstellen, wird die Überprüfung von Images immer wichtiger, um Sicherheitsbedrohungen von ihren Organisationen abzuwenden. 

Lösungen für die Überwachung und Überprüfung der Sicherheit, wie z.B. [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) und [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), können verwendet werden, um Containerimages in einer privaten Registrierung zu überprüfen und potenzielle Sicherheitsrisiken zu identifizieren. Sie müssen wissen, wie tief greifend die Überprüfung ist, die von den verschiedenen Lösungen vorgenommen wird. Eine Lösungen überprüfen die Imageschichten möglicherweise nur oberflächlich auf bekannte Schwachstellen. Diese Lösungen können möglicherweise keine Schichten einer Software im Image überprüfen, die mit einer bestimmten Paketmanagersoftware erstellt wurde. Andere Lösungen dagegen bieten einer tiefer greifende Integration und können Sicherheitslücken in jeder gepackten Software finden.

### <a name="production-deployment-rules-and-audit"></a>Regeln und Überwachung der Produktionsbereitstellung
Sobald eine Anwendung in der Produktion bereitgestellt wurde, müssen einige Regeln festgelegt werden, um sicherzustellen, dass in Produktionsumgebungen eingesetzte Images sicher sind und keine Sicherheitsrisiken bergen.

* Generell gilt: Images mit Sicherheitslücken, auch wenn sie noch so klein sind, sollten in Produktionsumgebungen nicht ausgeführt werden dürfen. Darüber hinaus sollten alle in der Produktion bereitgestellten Images idealerweise in einer privaten Registrierung gespeichert werden, auf die nur wenige ausgewählte Personen Zugriff haben. Es ist auch wichtig, nur eine geringe Anzahl von Produktionsimages bereitzustellen, um sicherzustellen, dass die Images effektiv verwaltet werden können.

* Da es schwierig ist, die Herkunft einer Software aus einem öffentlich verfügbaren Containerimage festzustellen, empfiehlt es sich, Images aus der Quelle zu erstellen, um sicherzustellen, dass Sie den Ursprung der Schicht kennen. Wenn in einem selbst erstellten Containerimage eine Schwachstelle zutage tritt, können Kunden schneller eine Lösung finden. Bei einem öffentlichen Image müssen Kunden herausfinden, woher das Image stammt, um das Problem zu beheben, oder ein anderes, sicheres Image vom Herausgeber abrufen.

* Auch bei einem sorgfältig überprüften Image, das in der Produktion bereitgestellt wurde, gibt es keine Garantie, dass es während der gesamten Lebensdauer der Anwendung aktuell ist. Möglicherweise werden Sicherheitslücken für Imageschichten gemeldet, die zuvor nicht bekannt waren oder erst nach Produktionsbereitstellung entstanden sind. Es ist notwendig, in der Produktion bereitgestellte Images regelmäßig zu überwachen, um Images zu ermitteln, die veraltet sind oder längere Zeit nicht aktualisiert wurden. Sie können Methoden für eine so genannte Blaugrünbereitstellung sowie parallele Bereitstellungsmechanismen verwenden, um Containerimages ohne Ausfallzeiten zu aktualisieren. Die Überprüfung der Images kann mit den Tools erfolgen, die im vorherigen Abschnitt beschrieben wurden. 

* Eine CI-Pipeline (Continuous Integration) zum Erstellen von Images und eine integrierte Sicherheitsüberprüfung können beim Verwalten von sicheren privaten Registrierungen mit sicheren Containerimages helfen. Die in die CI-Lösung integrierte Schwachstellenprüfung stellt sicher, dass Images, die alle Tests bestehen, per Push in die private Registrierung übertragen werden, aus der Produktionsworkloads bereitgestellt werden. Wenn in der CI-Pipeline ein Fehler festgestellt wird, bedeutet dies, dass anfällige Images nicht in die private Registrierung übertragen werden, die für die Bereitstellung von Produktionsworkloads verwendet wird. Auf diese Weise lässt sich die Sicherheitsüberprüfung von Images auch automatisieren, wenn sehr viele Images vorhanden sind. Andernfalls kann die manuelle Überprüfung von Images auf Sicherheitsrisiken sehr langwierig und zudem fehleranfällig sein.

## <a name="host-level-container-isolation"></a>Isolierung von Containern auf Hostebene
Wenn ein Kunde Containeranwendungen in Azure-Ressourcen bereitstellt, erfolgt die Bereitstellung auf Ebene des Abonnements in Ressourcengruppen, nicht in mehreren Mandanten. Wenn also ein Kunde ein Abonnement gemeinsam mit anderen nutzt, können zwischen zwei Bereitstellungen im gleichen Abonnement keine Grenzen erstellt werden. Aus diesem Grund lässt sich die Sicherheit auf Containerebene nicht garantieren. 

Es ist auch wichtig zu wissen, dass Container den Kernel und die Ressourcen des Hosts gemeinsam nutzen (in Azure Container Service ist der Host ein virtueller Azure-Computer in einem Cluster). Daher müssen Container in der Produktion in einem Benutzermodus ohne Berechtigungen ausgeführt werden. Die Ausführung eines Containers mit Berechtigungen auf Stammebene kann die Sicherheit der ganzen Umgebung gefährden. Wenn ein Hacker auf Stammebene auf einen Container zugreifen kann, erhält er damit Zugriff auf die vollständigen Berechtigungen auf dem Host. Darüber hinaus ist es wichtig, Container mit schreibgeschützten Dateisystemen auszuführen. Dies verhindert, dass eine Person, die Zugriff auf den gefährdeten Container hat, bösartige Skripts in das Dateisystem schreiben und so Zugriff auf andere Dateien erhalten kann. Ebenso ist es wichtig, die Ressourcen (z.B. Arbeitsspeicher, CPU oder Netzwerkbandbreite) zu begrenzen, die einem Container zugeordnet werden. So lässt sich verhindern, dass Hacker sich Ressourcen aneignen und illegalen Aktivitäten nachgehen, wie z.B. Kreditkartenbetrug oder Bitcoinmining, was dazu führen kann, dass andere Container nicht mehr auf dem Host oder im Cluster ausgeführt werden können.

## <a name="orchestrator-considerations"></a>Überlegungen zum Orchestrator

Für jeden in Azure Container Service verfügbaren Orchestrator gelten eigene Sicherheitsüberlegungen. Sie sollten beispielsweise den direkten SSH-Zugriff auf Orchestratorknoten in Container Service beschränken. Stattdessen sollten Sie für jeden Orchestrator die entsprechenden Schnittstellen- oder Befehlszeilenprogramme (z.B. `kubectl` für Kubernetes) verwenden, um die Containerumgebung zu verwalten, ohne auf die Hosts zuzugreifen. Weitere Informationen finden Sie unter [Herstellen einer Remoteverbindung mit einem Kubernetes-, DC/OS- oder Docker Swarm-Cluster](../articles/container-service/kubernetes/container-service-connect.md).

Weitere orchestratorspezifische Informationen zur Sicherheit finden Sie in den folgenden Ressourcen:

* **Kubernetes**: [Bewährte Methoden für die Sicherheit einer Kubernetes-Bereitstellung](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [Schützen Ihres Clusters](http://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Docker-Sicherheit](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Docker-Architektur und zur Containersicherheit finden Sie unter [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Einführung in die Containersicherheit).

* Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure).
