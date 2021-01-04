---
title: 'Sicheres Verwalten von JAR-Abhängigkeiten: Azure HDInsight'
description: In diesem Artikel werden bewährte Methoden für die Verwaltung von JAR-Abhängigkeiten (Java-Archive) für HDInsight-Anwendungen erläutert.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 5834d3512c95e77e6ce45472ff8f2e1b4b2ed456
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545581"
---
# <a name="safely-manage-jar-dependencies"></a>Sicheres Verwalten von JAR-Abhängigkeiten

In HDInsight-Clustern installierte Komponenten weisen Abhängigkeiten von Drittanbieterbibliotheken auf. Normalerweise verweisen diese integrierten Komponenten auf eine bestimmte Version verbreiteter Module wie Guava. Wenn Sie eine Anwendung mit ihren Abhängigkeiten übermitteln, kann dies einen Konflikt zwischen verschiedenen Versionen desselben Moduls verursachen. Wenn Sie in „classpath“ zuerst auf eine Komponentenversion verweisen, lösen integrierte Komponenten möglicherweise Ausnahmen aufgrund der Versionsinkompatibilität aus. Wenn hingegen die integrierten Komponenten ihre Abhängigkeiten zuerst in „classpath“ einfügen, löst Ihre Anwendung möglicherweise Fehler wie `NoSuchMethod` aus.

Um Versionskonflikte zu vermeiden, sollten Sie ein Shading Ihrer Anwendungsabhängigkeiten in Erwägung ziehen.

## <a name="what-does-package-shading-mean"></a>Was bedeutet Paketshading?
Shading bietet eine Möglichkeit zum Einschließen und Umbenennen von Abhängigkeiten. Dabei werden die Klassen neu angeordnet, und der betroffene Bytecode und die Ressourcen werden umgeschrieben, sodass eine private Kopie Ihrer Abhängigkeiten erstellt wird.

## <a name="how-to-shade-a-package"></a>Vorgehensweise zum Shading eines Pakets

### <a name="use-uber-jar"></a>Verwenden einer Uber-JAR-Datei
Eine Uber-JAR-Datei ist eine einzelne JAR-Datei, die sowohl die Anwendungs-JAR-Datei als auch ihre Abhängigkeiten enthält. Für die Abhängigkeiten in der Uber-JAR-Datei wird standardmäßig kein Shading vorgenommen. In einigen Fällen können dadurch Versionskonflikte verursacht werden, wenn andere Komponenten oder Anwendungen auf eine andere Version dieser Bibliotheken verweisen. Um dies zu vermeiden, können Sie eine Uber-JAR-Datei mit einigen (oder allen) Abhängigkeiten inklusive Shading erstellen.

### <a name="shade-package-using-maven"></a>Shading von Paketen mithilfe von Maven
Maven kann Anwendungen erstellen, die sowohl in Java als auch in Scala geschrieben sind. Das maven-shade-plugin hilft dabei, eine Uber-JAR-Datei mit Shading einfach zu erstellen.

Das folgende Beispiel zeigt die Datei `pom.xml`, die so aktualisiert wurde, dass ein Shading für ein Paket mithilfe von maven-shade-plugin vorgenommen wurde.  Der XML-Abschnitt `<relocation>…</relocation>` verschiebt Klassen aus dem Paket `com.google.guava` in das Paket `com.google.shaded.guava`, indem die entsprechenden JAR-Dateieinträge verschoben und der betroffene Bytecode umgeschrieben wird.

Nachdem Sie `pom.xml` geändert haben, können Sie `mvn package` ausführen, um die Shading-Uber-JAR-Datei zu erstellen.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Shading von Paketen mithilfe von SBT
SBT ist ebenfalls ein Buildtool für Scala und Java. SBT verfügt nicht über ein Shading-Plug-In wie maven-shade-plugin. Sie können die Datei `build.sbt` ändern, um ein Shading von Paketen vorzunehmen. 

Wenn Sie z. B.ein Shading von `com.google.guava` erreichen möchten, können Sie der Datei `build.sbt` den folgenden Befehl hinzufügen:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Anschließend können Sie `sbt clean` und `sbt assembly` ausführen, um die Shading-JAR-Datei zu erstellen. 

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von IntelliJ-Tools für HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Erstellen einer Scala-Maven-Anwendung für Spark in IntelliJ](./apache-spark-create-standalone-application.md)