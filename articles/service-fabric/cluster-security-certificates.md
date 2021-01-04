---
title: Zertifikatbasierte X. 509-Authentifizierung in einem Service Fabric-Cluster
description: Erfahren Sie mehr über zertifikatbasierte Authentifizierung in Service Fabric-Clustern und wie Sie zertifikatbezogene Probleme erkennen, verringern und beheben können.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4d81cb9d224bdc2e3002c621c86729df235e0d81
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574767"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Zertifikatbasierte X. 509-Authentifizierung in einem Service Fabric-Cluster

Dieser Artikel ergänzt die Einführung in [Sicherheit für Service Fabric-Cluster](service-fabric-cluster-security.md) und erläutert die Details von zertifikatbasierter Authentifizierung in Service Fabric-Clustern. Wir gehen davon aus, dass Leser dieses Artikels mit grundlegenden Sicherheitskonzepten und auch mit den Steuerungsmöglichkeiten vertraut sind, die Service Fabric zur Verfügung stellt, um die Sicherheit eines Clusters zu steuern.  

Themen, die in diesem Artikel behandelt werden:

* Grundlagen zertifikatbasierter Authentifizierung
* Identitäten und ihre jeweiligen Rollen
* Regeln für die Zertifikatkonfiguration
* Problembehandlung und häufig gestellte Fragen (FAQ)

## <a name="certificate-based-authentication-basics"></a>Grundlagen zertifikatbasierter Authentifizierung
Kurz zur Erinnerung: Im Sicherheitskontext ist ein Zertifikat ein Instrument, das dazu dient, Informationen zu einer Entität (den Antragsteller) an den Besitz eines Paares asymmetrischer kryptographischer Schlüssel zu binden. Es stellt somit ein Kernkonstrukt der Kryptographie mit öffentlichen Schlüsseln dar. Die durch ein Zertifikat dargestellten Schlüssel können zum Schutz von Daten oder zum Beweisen der Identität der Schlüsselinhaber verwendet werden. Bei Verwendung in Verbindung mit einem PKI-System (Public Key-Infrastruktur) kann ein Zertifikat zusätzliche Merkmale seines Antragstellers darstellen, z. B. den Besitz einer Internetdomäne oder bestimmte Berechtigungen, die ihm vom Aussteller des Zertifikats (als Zertifizierungsstelle oder CA (Certification Authority) bezeichnet) gewährt werden. Eine gängige Anwendung von Zertifikaten unterstützt das kryptografische TLS-Protokoll (Transport Layer Security), das sichere Kommunikation über ein Computernetzwerk ermöglicht. Insbesondere verwenden Client und Server Zertifikate, um Datenschutz und Integrität ihrer Kommunikation sicherzustellen und gegenseitige Authentifizierung durchzuführen.

In Service Fabric baut die grundlegende Ebene eines Clusters (Verbund) auch auf TLS (und anderen Protokollen) auf, um ein zuverlässiges und sicheres Netzwerk der beteiligten Knoten zu erreichen. Verbindungen mit dem Cluster über Service Fabric-Client-APIs verwenden ebenso TLS, um den Datenverkehr zu schützen, sowie zum Einrichten der Identitäten der Parteien. Insbesondere kann ein Zertifikat, wenn es zur Authentifizierung in Service Fabric verwendet wird, zum Nachweis folgender Ansprüche verwendet werden: a) der Aussteller des Zertifikats ist im Besitz des privaten Schlüssels des Zertifikats, b) der SHA-1-Hash („Fingerabdruck“) des Zertifikats stimmt mit einer in der Clusterdefinition enthaltenen Deklaration überein, oder c) der allgemeine Name des Antragstellers des Zertifikats stimmt mit einer in der Clusterdefinition enthaltenen Deklaration überein, und der Aussteller des Zertifikats ist bekannt oder vertrauenswürdig.

In der Liste oben wird „b“ umgangssprachlich als „Thumbprint Pinning“ (Anheften des Fingerabdrucks) bezeichnet. In diesem Fall bezieht sich die Deklaration auf ein bestimmtes Zertifikat, und die Stärke des Authentifizierungsschemas beruht auf der Prämisse, dass es rechnerisch nicht möglich ist, ein Zertifikat zu fälschen, das den gleichen Hashwert wie ein anderes generiert, während es trotzdem ein gültiges, wohlgeformtes Objekt bleibt. Die Angabe „c“ stellt eine alternative Form der Deklaration eines Zertifikats dar, bei dem die Stärke des Schemas auf der Kombination des Antragstellers des Zertifikats mit der ausstellenden Zertifizierungsstelle liegt. In diesem Fall bezieht sich die Deklaration auf eine Klasse von Zertifikaten: Zwei Zertifikate mit denselben Merkmalen werden als vollständig gleichwertig betrachtet. 

In den folgenden Abschnitten wird ausführlich erläutert, wie die Service Fabric-Runtime Zertifikate verwendet und überprüft, um die Clustersicherheit zu gewährleisten.

## <a name="identities-and-their-respective-roles"></a>Identitäten und ihre jeweiligen Rollen
Bevor Sie sich mit den Details der Authentifizierung oder Sicherung von Kommunikationskanälen befassen, ist es wichtig, die beteiligten Akteure und die entsprechenden Rollen aufzulisten, die sie in einem Cluster übernehmen:
- Service Fabric-Runtime, die als „System“ bezeichnet wird: die Gruppe von Diensten, die die Abstraktionen und Funktionen bereitstellen, die den Cluster darstellen. Beim Verweisen auf die clusterinterne Kommunikation zwischen Systeminstanzen wird der Begriff „Clusteridentität“ verwendet. Beim Verweisen auf den Cluster als Empfänger/Ziel für Datenverkehr von außerhalb des Clusters wird der Begriff „Serveridentität“ verwendet.
- Gehostete Anwendungen, die als „Anwendungen“ bezeichnet werden: Code, der vom Besitzer des Clusters bereitgestellt wird, der im Cluster orchestriert und ausgeführt wird.
- Clients: Entitäten, die eine Verbindung mit dem Cluster herstellen und entsprechend der Clusterkonfiguration Funktionen in einem Cluster ausführen dürfen. Wir unterscheiden zwischen zwei Ebenen von Berechtigungen: „Benutzer“ und „Administrator“. Ein „Benutzer“client ist hauptsächlich auf schreibgeschützte Vorgänge (aber nicht auf alle schreibgeschützten Funktionen) beschränkt, während ein „Administrator“client uneingeschränkten Zugriff auf die Clusterfunktionalität besitzt. (Weitere Informationen finden Sie unter [Sicherheitsrollen in einem Service Fabric-Cluster](service-fabric-cluster-security-roles.md).)
- (Nur Azure) Die Service Fabric Dienste, die Steuerungen für den Betrieb und die Verwaltung von Service Fabric-Clustern orchestrieren und verfügbar machen, die einfach als „Dienst“ bezeichnet werden. Abhängig von der Umgebung verweist der „Dienst“ möglicherweise auf den Azure Service Fabric-Ressourcenanbieter oder auf andere Ressourcenanbieter, die dem Service Fabric-Team gehören und von ihm betrieben werden.

In einem sicheren Cluster kann jede dieser Rollen mit eigener, unterschiedlicher Identität konfiguriert werden, die als Kopplung eines vordefinierten Rollennamens mit den entsprechenden Anmeldeinformationen deklariert ist. Service Fabric unterstützt das Deklarieren von Anmeldeinformationen als Zertifikate oder domänenbasierter Dienstprinzipal. (Windows-/Kerberos-basierte Identitäten werden ebenfalls unterstützt, sprengen aber den Rahmen dieses Artikels. Weitere Informationen dazu finden Sie unter [Windows-basierte Sicherheit in Service Fabric-Clustern](service-fabric-windows-cluster-windows-security.md).) In Azure-Clustern können Clientrollen auch als [auf Azure Active Directory basierende Identitäten](service-fabric-cluster-creation-setup-aad.md) deklariert werden.

Wie bereits oben erwähnt, definiert die Service Fabric-Runtime zwei Berechtigungsebenen in einem Cluster: „Administrator“ und „Benutzer“. Ein Administratorclient und eine „System“komponente würden beide mit „Administrator“berechtigungen arbeiten und können daher nicht voneinander unterschieden werden. Beim Herstellen einer Verbindung im/mit dem Cluster wird einem authentifizierten Aufrufer von der Service Fabric-Runtime eine der beiden Rollen als Grundlage für die nachfolgende Autorisierung erteilt. Authentifizierung wird in den folgenden Abschnitten ausführlich untersucht.

## <a name="certificate-configuration-rules"></a>Regeln für die Zertifikatkonfiguration
### <a name="validation-rules"></a>Validierungsregeln
Die Sicherheitseinstellungen eines Service Fabric-Clusters beschreiben im Prinzip die folgenden Aspekte:
- Den Authentifizierungstyp. Dabei handelt es sich um eine Erstellungszeit, ein unveränderliches Merkmal des Clusters. Beispiele für derartige Einstellungen sind „ClusterCredentialType“, „ServerCredentialType“, und zulässige Werte sind „none“, „x509“ oder „windows“. Der Schwerpunkt dieses Artikels liegt auf X509-Authentifizierung.
- Die Validierungsregeln (der Authentifizierung). Diese Einstellungen werden vom Clusterbesitzer festgelegt und beschreiben, welche Anmeldeinformationen für eine bestimmte Rolle akzeptiert werden sollen. Beispiele dafür werden direkt im Anschluss eingehend untersucht.
- Einstellungen, die verwendet werden, um das Ergebnis der Authentifizierung zu optimieren oder geringfügig zu ändern. Beispiele hierfür sind Flags, die die Erzwingung von Zertifikatsperrlisten einschränken bzw. lockern usw.

> [!NOTE]
> Die nachstehend aufgeführten Clusterkonfigurationsbeispiele sind Ausschnitte aus dem Clustermanifest im XML-Format. Dies ist das am häufigsten verwendete Format, das die in diesem Artikel beschriebenen Service Fabric-Funktionen direkt unterstützt. Die gleichen Einstellungen können direkt in den JSON-Darstellungen einer Clusterdefinition ausgedrückt werden, unabhängig davon, ob es sich um ein eigenständiges JSON-Clustermanifest oder eine Azure Resource Management-Vorlage handelt.

Eine Zertifikatvalidierungsregel besteht aus den folgenden Elementen:
- Der entsprechenden Rolle: Client, Administratorclient (privilegierte Rolle).
- Den für die Rolle akzeptierten Anmeldeinformationen, die entweder durch den Fingerabdruck oder den allgemeinen Namen des Antragstellers deklariert sind.

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Fingerabdruckbasierte Zertifikatvalidierungsdeklarationen
Im Fall von auf einem Fingerabdruck basierenden Validierungsregeln werden die Anmeldeinformationen eines Aufrufers, der eine Verbindung im/mit dem Cluster anfordert, wie folgt überprüft:
  - Die Anmelde Informationen sind ein gültiges, wohlgeformtes Zertifikat: Seine Kette kann erstellt werden, und die Signaturen stimmen überein.
  - Die Zeitangabe des Zertifikats ist gültig (NotBefore <= now < NotAfter).
  - Der SHA-1-Hash des Zertifikats stimmt als Zeichenfolgenvergleich ohne Berücksichtigung von Groß-/Kleinschreibung und ohne alle Leerzeichen mit der Deklaration überein.

Alle während der Kettenerstellung oder -validierung auftretenden Vertrauensstellungsfehler werden für fingerabdruckbasierte Deklarationen unterdrückt, mit Ausnahme abgelaufener Zertifikate, obwohl auch für diesen Fall Bereitstellungen vorhanden sind. Insbesondere gilt dies für folgende Fehler: Sperrstatus ist unbekannt oder offline, nicht vertrauenswürdiger Stamm, ungültige Schlüsselverwendung, partielle Kette werden als nicht schwerwiegende Fehler betrachtet. In diesem Fall besteht die Voraussetzung darin, dass es sich bei dem Zertifikat lediglich um einen Umschlag für ein Schlüsselpaar handelt. Die Sicherheit besteht darin, dass der Clusterbesitzer Maßnahmen festgelegt hat, um den privaten Schlüssel zu schützen.

Der folgende Auszug aus einem Clustermanifest veranschaulicht einen solchen Satz von fingerabdruckbasierten Validierungsregeln:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Jeder der oben aufgeführten Einträge verweist auf eine bestimmte Identität, wie zuvor beschrieben. Jeder Eintrag ermöglicht auch das Angeben mehrerer Werte als durch Trennzeichen getrennte Liste von Zeichenfolgen. In diesem Beispiel wird bei erfolgreicher Überprüfung der eingehenden Anmeldeinformationen beim Vorlegen eines Zertifikats mit dem SHA-1-Fingerabdruck „d5ec"...4264“ die Rolle „Administrator“ erteilt. Umgekehrt wird einem Aufrufer, der sich mit dem Zertifikat „7c8f...01b0“ authentifiziert, eine Benutzerrolle erteilt, die auf hauptsächlich schreibgeschützte Vorgänge beschränkt ist. Ein eingehender Aufrufer, der ein Zertifikat darstellt, dessen Fingerabdruck entweder „abcd...1234“ oder „ef01...5678“ lautet, wird als Peerknoten im Cluster akzeptiert. Schließlich erwartet ein Client, der eine Verbindung mit einem Verwaltungsendpunkt des Clusters herstellt, dass der Fingerabdruck des Serverzertifikats „ef01...5678“ lautet. 

Wie bereits erwähnt, sieht Service Fabric Maßnahmen für die Annahme abgelaufener Zertifikate vor. Der Grund hierfür ist, dass Zertifikate eine begrenzte Lebensdauer aufweisen und bei der Deklaration über Fingerabdruck (der sich auf eine bestimmte Zertifikatinstanz bezieht) zu einem Fehler bei der Verbindungsherstellung mit dem Cluster oder einem vollständigen Ausfall des Clusters führen, wenn der Ablauf eines Zertifikats zugelassen wird. Es wird allzu leicht vergessen oder vernachlässigt, ein mit einem Fingerabdruck versehenes Zertifikat zu rotieren, und leider ist die Wiederherstellung nach einem solchen Vorfall schwierig.

Aus diesem Grund kann der Clusterbesitzer explizit angeben, dass selbstsignierte Zertifikate, die über einen Fingerabdruck deklariert werden, wie folgt als gültig eingestuft werden:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Dieses Verhalten erstreckt sich nicht auf von der Zertifizierungsstelle ausgestellte Zertifikate. Wenn dies der Fall wäre, könnte ein gesperrtes, als gefährlich eingestuftes abgelaufenes Zertifikat „gültig“ werden, sobald es nicht mehr in der Zertifikatsperrliste der Zertifizierungsstelle enthalten wäre, und würde somit ein Sicherheitsrisiko darstellen. Bei selbstsignierten Zertifikaten wird der Clusterbesitzer als einzige Partei betrachtet, die für die Sicherung des privaten Schlüssels des Zertifikats zuständig ist. Dies ist nicht der Fall bei Zertifikaten, die von einer Zertifizierungsstelle ausgestellt wurden: Der Clusterbesitzer weiß möglicherweise nicht, wie oder wann das Zertifikat als kompromittiert deklariert wurde.

#### <a name="common-name-based-certificate-validation-declarations"></a>Auf dem allgemeinen Namen basierende Zertifikatvalidierungsdeklarationen
Auf dem allgemeinen Namen basierende Deklarationen nehmen eine der folgenden Formen an:
- Allgemeiner Name des Antragstellers (ausschließlich)
- Allgemeiner Name des Antragstellers mit Anheften des Ausstellers

Betrachten wir zunächst einen Auszug aus einem Clustermanifest, der beide Deklarationsstile veranschaulicht:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Die Deklarationen beziehen sich auf die Server- bzw. Clusteridentitäten. Beachten Sie, dass die CN-basierten Deklarationen eigene Abschnitte im Clustermanifest haben, die von der standardmäßigen „Sicherheit“ getrennt sind. In beiden Deklarationen stellt „Name“ den allgemeinen Distinguished Name des Antragstellers des Zertifikats dar, und das Feld „Value“ stellt den erwarteten Aussteller wie folgt dar:

- Im ersten Fall gibt die Deklaration an, dass der allgemeine Distinguished Name des Antragstellers des Serverzertifikats der Zeichenfolge „server.demo.system.servicefabric.azure-int“ entsprechen soll. Das leere Feld „Value“ weist darauf hin, dass der Stamm der Zertifikatkette auf dem Knoten/Computer, auf dem das Serverzertifikat überprüft wird, als vertrauenswürdig eingestuft wird. Unter Windows bedeutet dies, dass das Zertifikat mit einem der Zertifikate verkettet werden kann, die im Speicher der vertrauenswürdigen Stammzertifizierungsstelle installiert sind.
- Im zweiten Fall gibt die Deklaration an, dass die Vorlage eines Zertifikats als Peerknoten im Cluster akzeptiert wird, wenn der allgemeine Name des Zertifikats mit der Zeichenfolge „cluster.demo.system.servicefabric.azure-int“ übereinstimmt, *und* der Fingerabdruck des direkten Ausstellers des Zertifikats mit einem der durch Kommas getrennten Einträge im Feld „Value“ übereinstimmt. (Dieser Regeltyp wird umgangssprachlich als „allgemeiner Name mit angeheftetem Aussteller“ bezeichnet.)

In beiden Fällen wird die Zertifikatkette erstellt, und es wird erwartet, dass sie fehlerfrei ist. Das heißt, Sperrfehler, partielle Ketten- oder Vertrauensfehler wegen einer ungültigen Zeitangabe werden als schwerwiegend angesehen, und die Überprüfung des Zertifikats schlägt fehl. Das Anheften der Aussteller führt dazu, dass der Status „nicht vertrauenswürdiger Stamm“ als nicht schwerwiegender Fehler betrachtet wird. Obwohl es nicht den Anschein hat, handelt es sich hierbei um eine strengere Form der Überprüfung, da der Clusterbesitzer den Satz autorisierter/akzeptierter Aussteller auf seine eigene PKI beschränken kann.

Nachdem die Zertifikatkette erstellt wurde, wird sie anhand einer standardmäßigen TLS/SSL-Richtlinie mit dem deklarierten Antragsteller als Remotename überprüft. Ein Zertifikat wird als Übereinstimmung betrachtet, wenn der allgemeine Name des Antragstellers oder einer seiner alternativen Antragstellernamen mit der CN-Deklaration aus dem Clustermanifest übereinstimmt. Platzhalter werden in diesem Fall unterstützt, und beim Zeichenfolgenabgleich wird keine Groß-/Kleinschreibung beachtet.

(Wir sollten darauf hinweisen, dass die oben beschriebene Sequenz für jede Art von Schlüsselverwendung ausgeführt werden kann, die vom Zertifikat deklariert wird. Wenn das Zertifikat die Verwendung des Clientauthentifizierungsschlüssels angibt, wird die Kette zuerst für eine Clientrolle erstellt und ausgewertet. Im Erfolgsfall wird die Auswertung abgeschlossen, und die Überprüfung ist erfolgreich. Wenn für das Zertifikat keine Clientauthentifizierung verwendet wird oder die Überprüfung fehlgeschlagen ist, erstellt und wertet die Service Fabric-Runtime die Kette für die Serverauthentifizierung aus.)

Um das Beispiel abzuschließen, veranschaulicht der folgende Auszug das Deklarieren von Clientzertifikaten nach dem allgemeinen Namen:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Die oben aufgeführten Deklarationen entsprechen den Administrator- und Benutzeridentitäten. Die Überprüfung von Zertifikaten, die auf diese Weise deklariert werden, geschieht genau so wie für die vorherigen Beispiele von Cluster- und Serverzertifikaten beschrieben.

> [!NOTE]
> Deklarationen, die auf dem allgemeinen Namen basieren, dienen zur Vereinfachung der Rotation und im Allgemeinen zur Verwaltung von Clusterzertifikaten. Es wird jedoch empfohlen, die folgenden Empfehlungen einzuhalten, um die kontinuierliche Verfügbarkeit und Sicherheit des Clusters sicherzustellen:
  * Bevorzugen Sie Anheften des Ausstellers vor vertrauenswürdigen Stämmen.
  * Vermeiden Sie das Mischen von Ausstellern aus unterschiedlichen PKIs.
  * Stellen Sie sicher, dass alle erwarteten Aussteller in der Zertifikatdeklaration aufgeführt sind. Ein nicht übereinstimmender Aussteller führt zu einer fehlgeschlagenen Überprüfung.
  * Stellen Sie sicher, dass die Zertifikatrichtlinienendpunkte der PKI auffindbar, verfügbar und zugänglich sind. Dies bedeutet, dass die AIA-, CRL- oder OCSP-Endpunkte im Blattzertifikat deklariert sind und darauf zugegriffen werden kann, damit die Erstellung der Zertifikatkette vollständig ausgeführt werden kann.

Zusammengefasst gilt: Nach Erhalt einer Anforderung für eine Verbindung in einem mit X.509-Zertifikaten gesicherten Cluster verwendet die Service Fabric-Runtime die Sicherheitseinstellungen des Clusters, um die Anmeldeinformationen der Remotepartei wie oben beschrieben zu validieren. Bei Erfolg gilt der Aufrufer bzw. die Remotepartei als authentifiziert. Wenn die Anmeldeinformationen mit mehreren Validierungsregeln übereinstimmen, erteilt die Runtime dem Aufrufer die Rolle mit den höchsten Berechtigungen aller übereinstimmenden Regeln. 

### <a name="presentation-rules"></a>Präsentationsregeln
Im vorherigen Abschnitt wurde beschrieben, wie Authentifizierung in einem durch Zertifikate gesicherten Cluster funktioniert. In diesem Abschnitt wird erläutert, wie die Service Fabric-Runtime selbst die Zertifikate ermittelt und lädt, die für die Kommunikation innerhalb des Clusters verwendet werden. Diese Regeln werden als „Präsentationsregeln“ bezeichnet.

Wie bei den Validierungsregeln geben die Präsentationsregeln eine Rolle und die zugehörige Deklaration von Anmeldeinformationen an, ausgedrückt durch Fingerabdruck oder allgemeinen Name. Im Gegensatz zu den Validierungsregeln verfügen auf dem allgemeinen Namen basierende Deklarationen über keine Vorkehrungen für das Anheften von Ausstellern. Dies ermöglicht höhere Flexibilität sowie bessere Leistung. Die Präsentationsregeln werden in den „NodeType“-Abschnitten des Clustermanifests für jeden einzelnen Knotentyp deklariert. Die Einstellungen werden von den Sicherheitsabschnitten des Clusters getrennt, sodass jeder Knotentyp seine vollständige Konfiguration in einem einzelnen Abschnitt aufweisen kann. In Azure Service Fabric-Clustern stimmen die Knotentyp-Zertifikatdeklarationen standardmäßig mit den entsprechenden Einstellungen im Abschnitt „Security“ (Sicherheit) der Clusterdefinition überein.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Fingerabdruckbasierte Zertifikatpräsentationsdeklarationen
Wie bereits beschrieben, unterscheidet die Service Fabric-Runtime zwischen ihrer Rolle als Peer anderer Knoten im Cluster und als Server für Clusterverwaltungsvorgänge. Diese Einstellungen können im Prinzip getrennt voneinander konfiguriert werden. In der Praxis werden Sie jedoch tendenziell aneinander ausgerichtet. Im restlichen Teil dieses Artikels gehen wir aus Gründen der Einfachheit davon aus, dass die Einstellungen übereinstimmen.

Betrachten wir den folgenden Auszug aus einem Clustermanifest:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Das Element „ClusterCertificate“ veranschaulicht das vollständige Schema, einschließlich optionaler Parameter („X509FindValueSecondary“) oder Parametern mit entsprechenden Standardwerten („X509StoreName“). Die anderen Deklarationen zeigen eine abgekürzte Form an. Die Clusterzertifikatdeklaration oben besagt, dass die Sicherheitseinstellungen von Knoten des Typs „nt1vm“ mit dem Zertifikat „cc71..1984“ als primärer Wert und dem Zertifikat „49e2..19d6“ als sekundärer Wert initialisiert werden. Es wird erwartet, dass beide Zertifikate im Zertifikatspeicher „LocalMachine\'My“ (oder dem entsprechenden Linux-Pfad *var/lib/sfcerts*) gespeichert sind.

#### <a name="common-name-based-certificate-presentation-declarations"></a>Auf dem allgemeinen Namen basierende Zertifikatpräsentationsdeklarationen
Die Knotentypzertifikate können auch nach dem allgemeinen Namen des Antragstellers deklariert werden, wie unten gezeigt:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Bei beiden Deklarationen liest ein Service Fabric-Knoten die Konfiguration beim Start, sucht und lädt die angegebenen Zertifikate und sortiert sie in absteigender Reihenfolge ihres NotAfter-Attributs. Abgelaufene Zertifikate werden ignoriert, und das erste Element der Liste wird als Clientanmeldeinformationen für alle Service Fabric-Verbindungen ausgewählt, die von diesem Knoten versucht werden. (Tatsächlich bevorzugt Service Fabric das am spätesten ablaufende Zertifikat.)

Beachten Sie, dass ein Zertifikat für auf dem allgemeinen Namen basierende Präsentationsdeklarationen als Übereinstimmung betrachtet wird, wenn der allgemeine Name des Antragstellers dem Feld X509FindValue (oder X509FindValueSecondary) der Deklaration im genauen Zeichenfolgenvergleich unter Beachtung von Groß- und Kleinschreibung entspricht. Dies steht im Gegensatz zu den Validierungsregeln, die Platzhaltervergleiche unterstützen, sowie Zeichenfolgenvergleiche ohne Berücksichtigung von Groß- und Kleinschreibung.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Verschiedene Zertifikatkonfigurationseinstellungen
Es wurde bereits erwähnt, dass die Sicherheitseinstellungen eines Service Fabric-Clusters auch eine subtile Änderung des Verhaltens des Authentifizierungscodes ermöglichen. Während der Artikel zu [Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md) die umfassendste und aktuellste Liste von Einstellungen darstellt, werden wir hier die Bedeutung einiger ausgewählter Sicherheitseinstellungen erläutern, um die vollständigen Ausführungen zur zertifikatbasierten Authentifizierung zu ergänzen. Für jede Einstellung werden Absicht, Standardwert und Standardverhalten, Auswirkungen auf die Authentifizierung und zulässige Werte erläutert.

Wie bereits erwähnt, impliziert Zertifikatvalidierung immer das Erstellen und Auswerten der Zertifikatkette. Für von einer Zertifizierungsstelle ausgestellte Zertifikate umfasst dieser offenbar einfache Aufruf der Betriebssystem-API in der Regel mehrere ausgehende Aufrufe verschiedener Endpunkte der ausstellenden PKI, das Zwischenspeichern von Antworten usw. Angesichts der Häufigkeit von Zertifikatsvalidierungsaufrufen in einem Service Fabric-Cluster können Probleme an den Endpunkten der PKI zu einer reduzierten Verfügbarkeit des Clusters oder zu einem völligen Ausfall führen. Obwohl die ausgehenden Aufrufe nicht unterdrückt werden können (weitere Informationen dazu finden Sie weiter unten im Abschnitt mit häufig gestellten Fragen), können die folgenden Einstellungen verwendet werden, um Validierungsfehler, die durch fehlerhafte CRL-Aufrufe verursacht werden, zu maskieren.

  * CrlCheckingFlag: Im Abschnitt „Security“ wird die Zeichenfolge in UINT konvertiert. Der Wert dieser Einstellung wird von Service Fabric verwendet, um Fehler im Zertifikatskettenstatus durch Änderung des Verhaltens der Kettenerstellung zu maskieren. Er wird an den Win32 CryptoAPI-Aufruf [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) als Parameter „dwFlags“ übergeben und kann auf jede gültige Kombination von Flags festgelegt werden, die von der Funktion angenommen wird. Durch den Wert 0 wird die Service Fabric-Runtime gezwungen, alle Vertrauensstatusfehler zu ignorieren. Dies wird nicht empfohlen, da die Verwendung zu einem erheblichen Sicherheitsrisiko führen würde. Der Standardwert ist 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Verwendungsmöglichkeiten: für lokale Tests mit selbstsignierten Zertifikaten oder Entwicklerzertifikaten, die nicht vollständig ausgebildet sind bzw. nicht über eine geeignete Public Key-Infrastruktur zur Unterstützung der Zertifikate verfügen. Kann auch während des Übergangs zwischen PKIs als Risikominderung in Air Gap-Umgebungen verwendet werden.

  Verwendung: Wir zeigen ein Beispiel, das die Sperrungsüberprüfung für den Zugriff nur auf zwischengespeicherte URLs erzwingt. Annahmen:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  Anschließend gilt für die Deklaration im Clustermanifest:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError: Boolescher Wert mit dem Standardwert FALSE im Abschnitt „Security“. Stellt eine Verknüpfung zum Unterdrücken eines Kettenerstellungs-Fehlerstatus „Sperrung offline“ dar (oder einen nachfolgenden Richtlinienvalidierungs-Fehlerstatus der Kette).

  Verwendungszweck: lokale Tests oder mit Entwicklerzertifikaten, die nicht von einer ordnungsgemäßen PKI unterstützt werden. Verwendung als Risikominderung in Air Gap-Umgebungen oder wenn der Zugriff auf die PKI nicht möglich ist.

  Verwendung:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Weitere relevante Einstellungen (alle im Abschnitt „Security“):
  * AcceptExpiredPinnedClusterCertificate: Wird im Abschnitt zur fingerabdruckbasierten Zertifikatüberprüfung erläutert. Ermöglicht das Akzeptieren abgelaufener selbstsignierter Clusterzertifikate. 
  * CertificateExpirySafetyMargin: Intervall, ausgedrückt in Minuten vor dem NotAfter-Zeitstempel des Zertifikats, in dem für das Zertifikat ein Ablaufrisiko angenommen wird. Service Fabric überwacht Clusterzertifikate und gibt regelmäßig Integritätsberichte zur verbleibenden Verfügbarkeit aus. Innerhalb des Sicherheitsintervalls werden diese Integritätsberichte in den Status „Warning“ (Warnung) heraufgestuft. Der Standardwert ist 30 Tage.
  * CertificateHealthReportingInterval: Steuert die Häufigkeit von Integritätsberichten, die die verbleibende Gültigkeitsdauer von Clusterzertifikaten betreffen. Berichte werden nur ein Mal pro Intervall ausgegeben. Der Wert wird in Sekunden angegeben. Der Standardwert ist 8 Stunden.
  * EnforcePrevalidationOnSecurityChanges: Boolescher Wert, steuert das Verhalten des Clusterupgrades, wenn Änderungen an Sicherheitseinstellungen erkannt werden. Wenn der Wert auf TRUE festgelegt ist, wird bei einem Clusterupgrade versucht, sicherzustellen, dass mindestens eines der Zertifikate, die mit einer der Darstellungsregeln übereinstimmen, eine entsprechende Validierungsregel bestehen kann. Die Vorvalidierung wird ausgeführt, bevor die neuen Einstellungen auf einen Knoten angewandt werden. Sie wird jedoch nur auf dem Knoten ausgeführt, der zum Zeitpunkt der Initiierung des Upgrades das primäre Replikat des Cluster-Manager-Diensts hostet. Zum Zeitpunkt der Erstellung dieses Artikels besitzt die Einstellung den Standardwert FALSE und wird für neue Azure Service Fabric-Cluster mit einer Laufzeitversion ab 7.1 auf TRUE festgelegt.
 
### <a name="end-to-end-scenario-examples"></a>End-to-End-Szenario (Beispiele)
Wir haben uns mit Präsentationsregeln, Validierungsregeln und Optimierungsflags beschäftigt, aber wie funktioniert das alles zusammen? In diesem Abschnitt werden zwei End-to-End-Beispiele erläutert, die veranschaulichen, wie die Sicherheitseinstellungen für sichere Clusterupgrades genutzt werden können. Beachten Sie, dass dieser Artikel nicht als umfassende Abhandlung zur richtigen Zertifikatverwaltung in Service Fabric gedacht ist. Suchen Sie nach einem begleitenden Artikel zu diesem Thema.

Die Trennung von Präsentations- und Validierungsregeln wirft die offensichtliche Frage (oder Besorgnis) auf, ob sie voneinander abweichen können und was die Konsequenzen wären. Tatsächlich ist es möglich, dass die Auswahl eines Authentifizierungszertifikats durch einen Knoten den Validierungsregeln eines anderen Knotens nicht genügt. Diese Diskrepanz ist in der Tat die Hauptursache für Vorfälle im Zusammenhang mit der Authentifizierung. Gleichzeitig ermöglicht die Trennung dieser Regeln einem Cluster, den Betrieb während eines Upgrades fortzusetzen, durch das die Sicherheitseinstellungen des Clusters geändert werden. Bedenken Sie, dass durch die Erweiterung der Validierungsregeln in einem ersten Schritt zunächst alle Knoten des Clusters auf die neuen Einstellungen umgestellt werden, während sie weiterhin die aktuellen Anmeldeinformationen verwenden. 

Denken Sie daran, dass in einem Service Fabric-Cluster ein Upgrade (bis zu 5) „Upgradedomänen“ oder UDS durchläuft. Nur Knoten in der aktuellen UD werden zu einem bestimmten Zeitpunkt aktualisiert/geändert, und das Upgrade wird nur dann mit der nächsten UD fortgesetzt, wenn die Verfügbarkeit des Clusters dies zulässt. (Weitere Informationen finden Sie unter [Service Fabric-Clusterupgrades](service-fabric-cluster-upgrade.md) und in anderen Artikeln zu diesem Thema.) Zertifikat-/Sicherheitsänderungen sind besonders riskant, da sie Knoten aus dem Cluster isolieren oder den Cluster am Rand eines Quorumverlusts bringen können.

Die folgende Notation wird verwendet, um die Sicherheitseinstellungen eines Knotens zu beschreiben:

Nk: {P:{TP=A}, V:{TP=A}}, wobei gilt:
  - „NK“ stellt einen Knoten in der Upgradedomäne *k* dar.
  - „P“ stellt die aktuellen Präsentationsregeln des Knotens dar (vorausgesetzt, wir beziehen uns nur auf Clusterzertifikate). 
  - „V“ stellt die aktuellen Validierungsregeln des Knotens dar (nur Clusterzertifikat).
  - „TP=a“ stellt eine fingerabdruckbasierte Deklaration (TP) dar, wobei „A“ ein Zertifikafingerabdruck ist.
  - „CN=B“ stellt eine auf dem allgemeinen Namen basierende Deklaration (CN) dar, wobei „B“ der allgemeine Name des Antragstellers des Zertifikats ist. 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotieren eines durch einen Fingerabdruck deklarierten Clusterzertifikats
Die folgende Sequenz beschreibt, wie ein 2-stufiges Upgrade verwendet werden kann, um ein sekundäres Clusterzertifikat sicher einzuführen, das durch einen Fingerabdruck deklariert wird. In der ersten Phase wird die neue Zertifikatdeklaration in den Validierungsregeln eingeführt, und in der zweiten Phase wird sie in den Präsentationsregeln eingeführt:
  - Anfangszustand: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}}: Der Cluster befindet sich im Ruhezustand, alle Knoten haben eine gemeinsame Konfiguration.
  - Nach Durchlaufen von Upgradedomäne 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}}: Knoten in UD0 präsentieren Zertifikat A und akzeptieren die Zertifikate A oder B. Alle anderen Knoten präsentieren und akzeptieren nur Zertifikat A.
  - Nach Durchlaufen der letzten Upgradedomäne: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}}: Alle Knoten präsentieren Zertifikat A, alle Knoten akzeptieren entweder das Zertifikat A oder B.
      
An diesem Punkt befindet sich der Cluster wieder im Gleichgewicht, und die zweite Phase des Upgrades bzw. der Änderung der Sicherheitseinstellungen kann beginnen:
  - Nach Durchlaufen von Upgradedomäne 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}}: Knoten in UD0 beginnen mit der Präsentation von Zertifikat B, was von jedem anderen Knoten im Cluster akzeptiert wird.
  - Nach Durchlaufen der letzten Upgradedomäne: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}: Alle Knoten präsentieren nun Zertifikat B. Zertifikat A kann nun mit einem nachfolgenden Satz von Upgrades aus der Clusterdefinition zurückgezogen/entfernt werden.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Konvertieren eines Clusters aus fingerabdruckbasierter in auf dem allgemeinen Namen basierende Zertifikatdeklarationen
Entsprechend folgt das Ändern des Typs der Zertifikatdeklaration (aus Fingerabdruck in den allgemeinen Namen) dem gleichen Muster wie oben. Beachten Sie, dass Validierungsregeln das Deklarieren der Zertifikate einer bestimmten Rolle durch Fingerabdruck und den allgemeinen Namen in der gleichen Clusterdefinition ermöglichen. Im Gegensatz dazu lassen Präsentationsregeln nur eine Form der Deklaration zu. Übrigens besteht der sichere Ansatz bei der Konvertierung eines Clusterzertifikats aus Fingerabdruck in den allgemeinen Namen darin, das beabsichtigte Zielzertifikat zunächst über Fingerabdruck einzuführen und diese Deklaration dann in eine auf dem allgemeinen Namen basierende zu ändern. Im folgenden Beispiel wird davon ausgegangen, dass der Fingerabdruck „A“ und der allgemeine Name „B“ des Antragstellers auf dasselbe Zertifikat verweisen. 

  - Anfangszustand: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}}: Der Cluster befindet sich im Ruhezustand, alle Knoten verfügen über eine gemeinsame Konfiguration, wobei A der primäre Zertifikatfingerabdruck ist.
  - Nach Durchlaufen von Upgradedomäne 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}}: Knoten in UD0 präsentieren Zertifikat A und akzeptieren Zertifikate mit Fingerabdruck A oder dem allgemeinen Namen B. Alle anderen Knoten präsentieren und akzeptieren nur Zertifikat A.
  - Nach Durchlaufen der letzten Upgradedomäne: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}}: Alle Knoten präsentieren Zertifikat A, alle Knoten akzeptieren entweder das Zertifikat A (TP) oder B (CN).

An diesem Punkt können wir mit dem Ändern der Präsentationsregeln mit einem nachfolgenden Upgrade fortfahren:
  - Nach Durchlaufen von Upgradedomäne 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}}: Knoten in UD0 präsentieren Zertifikat B, das durch CN gefunden wird, und akzeptieren Zertifikate mit dem Fingerabdruck A oder dem allgemeinen Namen B. Alle anderen Knoten präsentieren und akzeptieren nur Zertifikat A, das über Fingerabdruck ausgewählt wird.
  - Nach Durchlaufen der letzten Upgradedomäne: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}}: Alle Knoten präsentieren das nach CN gefundene Zertifikat B, alle Knoten akzeptieren entweder das Zertifikat A (TP) oder B (CN).
    
Der Abschluss von Phase 2 markiert auch die Konvertierung des Clusters in auf dem allgemeinen Namen basierende Zertifikate. Die fingerabdruckbasierten Validierungsdeklarationen können bei einem nachfolgenden Clusterupgrade entfernt werden.

> [!NOTE]
> In Azure Service Fabric-Clustern werden die oben dargestellten Workflows vom Service Fabric-Ressourcenanbieter orchestriert. Der Clusterbesitzer ist nach wie vor für die Bereitstellung von Zertifikaten im Cluster gemäß den angegebenen Regeln (Präsentation oder Validierung) verantwortlich, und es wird empfohlen, Änderungen in mehreren Schritten auszuführen.

In einem separaten Artikel wird das Thema zum Verwalten und Bereitstellen von Zertifikaten in einem Service Fabric-Cluster behandelt.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Problembehandlung und häufig gestellte Fragen (FAQ)
Das Debuggen von authentifizierungsbezogenen Problemen in Service Fabric Clustern ist zwar nicht einfach, aber wir hoffen, dass Ihnen die folgenden Hinweise und Tipps dabei helfen können. Die einfachste Möglichkeit, um die Untersuchung zu starten, besteht darin, die Service Fabric-Ereignisprotokolle auf den Knoten des Clusters zu untersuchen. Dies gilt nicht notwendigerweise nur für die Knoten, die Symptome zeigen, sondern auch für Knoten, die zwar aktiv sind, aber keine Verbindung mit einem ihrer Nachbarn herstellen können. Unter Windows werden Ereignisse mit Bedeutung in der Regel unter den Kanälen „Anwendungs- und Dienstprotokolle\Microsoft-ServiceFabric\Admin“ bzw. „Operational“ protokolliert. Manchmal kann es hilfreich sein, [CAPI2-Protokollierung](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues) zu aktivieren, um weitere Details zur Zertifikatüberprüfung, zum Abrufen von CRL/CTL usw. zu erfassen. (Denken Sie daran, diese Protokollierung nach Abschluss der Untersuchung zu deaktivieren, sie kann recht ausführlich sein.)

Typische Symptome, die sich in einem Cluster mit Authentifizierungsproblemen manifestieren, sind folgende: 
  - Knoten sind ausgefallen/zyklisch. 
  - Verbindungsversuche werden zurückgewiesen.
  - Timeout bei Verbindungsversuchen.

Jedes der Symptome kann durch unterschiedliche Probleme verursacht werden, und die gleiche Grundursache kann zu verschiedenen Manifestationen führen. Daher werden wir nur ein kleines Beispiel für typische Probleme mit Empfehlungen für die Behebung auflisten. 

* Knoten können Nachrichten austauschen, aber keine Verbindung herstellen. Eine mögliche Ursache für die Beendigung von Verbindungsversuchen ist ein Fehler vom Typ „Nicht übereinstimmendes Zertifikat“. Eine der Parteien in einer Service Fabric-zu-Service Fabric-Verbindung legt ein Zertifikat vor, das die Validierungsregeln des Empfängers nicht erfüllt. Kann mit einem der folgenden Fehler einhergehen: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Zur weiteren Diagnose/Untersuchung: Ermitteln Sie auf jedem Knoten, der die Verbindung versucht, welches Zertifikat vorgelegt wird. Untersuchen Sie das Zertifikat, und testen und emulieren Sie die Validierungsregeln (überprüfen Sie die Gleichheit von Fingerabdruck oder allgemeinem Namen, überprüfen Sie Ausstellerfingerabdrücke, wenn angegeben).

  Ein weiterer häufig auftretender Fehlercode lautet wie folgt:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  In diesem Fall wird das Zertifikat durch den allgemeinen Namen deklariert, und es gilt eine der folgenden Aussagen:
    - Die Aussteller sind nicht angeheftet, und das Stammzertifikat ist nicht vertrauenswürdig.
    - Die Aussteller sind angeheftet, aber die Deklaration enthält nicht den Fingerabdruck des direkten Ausstellers dieses Zertifikats.

* Ein Knoten ist aktiv, kann aber keine Verbindung mit anderen Knoten herstellen. Andere Knoten empfangen keinen eingehenden Datenverkehr vom fehlerhaften Knoten. In diesem Fall ist es möglich, dass das Laden des Zertifikats auf dem lokalen Knoten fehlschlägt. Suchen Sie nach den folgenden Fehlern:
  - Das Zertifikat wurde nicht gefunden: Stellen Sie sicher, dass die in den Präsentationsregeln deklarierten Zertifikate durch den Inhalt des Zertifikatspeichers „LocalMachine\MY“ (oder wie angegeben) aufgelöst werden können. 
    Mögliche Ursachen für den Fehler: 
      - Ungültige Zeichen in der Fingerabdruckdeklaration.
      - Das Zertifikat ist nicht installiert.
      - Das Zertifikat ist abgelaufen.
      - Die Deklaration des allgemeinen Namens enthält das Präfix „CN=“.
      - Die Deklaration gibt einen Platzhalter an, und im Zertifikatspeicher ist keine genaue Übereinstimmung vorhanden (Deklaration: CN=*.mydomain.com, tatsächliches Zertifikat: CN=server.mydomain.com)

  - Unbekannte Anmeldeinformationen: Zeigt entweder einen fehlenden, dem Zertifikat entsprechenden privaten Schlüssel an, der in der Regel mit dem Fehlercode verknüpft ist: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Überprüfen Sie, ob der private Schlüssel vorhanden ist, um Abhilfe zu schaffen. Überprüfen Sie, ob SFAdmins Lese- und Ausführungszugriff („read|execute“) auf den privaten Schlüssel gewährt wurde.

  - Fehlerhafter Anbietertyp: Gibt ein CNG-Zertifikat (Crypto New Generation) an („Microsoft Software Key Storage Provider“). Zurzeit unterstützt Service Fabric nur CAPI1-Zertifikate. In der Regel mit diesem Fehlercode versehen:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Um das Problem zu beheben, erstellen Sie das Clusterzertifikat mithilfe des CAPI1-Anbieters neu (z. B. „Microsoft Enhanced RSA and AES Cryptographic Provider“). Weitere Informationen zu Kryptografieanbietern finden Sie unter [Grundlegendes zu Kryptografieanbietern](/windows/win32/seccertenroll/understanding-cryptographic-providers).
