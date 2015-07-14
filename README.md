My Project
========

This a content package project generated using the multimodule-content-package-archetype.

Building
--------

This project uses Maven for building. Common commands:

From the root directory, run ``mvn -PautoInstallPackage clean install`` to build the bundle and content package and install to a CQ instance.

From the bundle directory, run ``mvn -PautoInstallBundle clean install`` to build *just* the bundle and install to a CQ instance.

Using with VLT
--------------

To use vlt with this project, first build and install the package to your local CQ instance as described above. Then cd to `content/src/main/content/jcr_root` and run

    vlt --credentials admin:admin checkout -f ../META-INF/vault/filter.xml --force http://localhost:4502/crx

Once the working copy is created, you can use the normal ``vlt up`` and ``vlt ci`` commands.

Specifying CRX Host/Port
------------------------

The CRX host and port can be specified on the command line with:
mvn -Dcrx.host=otherhost -Dcrx.port=5502 <goals>

Purpose & Demonstration
========================
It was really easy to set up a new cq5.x project with maven by following the adobe's article [How to Build AEM Projects using Apache Maven guideline](https://docs.adobe.com/docs/en/cq/5-6-1/developing/developmenttools/how-to-build-aem-projects-using-apache-maven.html#Getting started in 5 Minutes). Howerver, it took me a few hours to figure out how to make everything (POM restore,jsp intellisense etc) works under IntelliJ IDE. 

If you are a newbie like me to CQ/AEM development, I hope the following instructions will only take you a few minutes to make the first project fully running with maven and IntelliJ IDE

##Step 1 - Create a new project from maven

The project was setup using the following maven command

```
mvn archetype:generate -DarchetypeRepository=http://repo.adobe.com/nexus/content/groups/public/ -DarchetypeGroupId=com.day.jcr.vault -DarchetypeArtifactId=multimodule-content-package-archetype -DarchetypeVersion=1.0.2 -DgroupId=com.aem.community -DartifactId=echoproject -Dversion=1.0-SNAPSHOT -Dpackage=com.aem.community -DappsFolderName=myproject -DartifactName="My Project" -DcqVersion="5.6.1" -DpackageGroup="My Company"
```

##Step 2 - POM configuration

### Step 2.1 Parent POM file
Update the dependencies section by add the following dependency elements. They are the crucial dependencies to make the jsp compliation, auto complete work under the IDE.

For more details about **cq-quick-product-dependencies**, read [here](https://docs.adobe.com/docs/en/cq/5-6-1/developing/developmenttools/how-to-build-aem-projects-using-apache-maven.html#Importing AEM Product Dependencies)

The com.adobe.granite.xssprotection artifact is not included in the cq-quickstart-product-dependencies POM and requires full Maven coordinates as obtained from the Dependency Finder.

``` xml
            <dependency>
                <groupId>com.adobe.granite</groupId>
                <artifactId>com.adobe.granite.xssprotection</artifactId>
                <version>5.5.24</version>
                <scope>provided</scope>
                <!-- com.adobe.granite.xss -->
            </dependency>
            <dependency>
                <!-- use com.day.cq.wcm as groupId if you are using an AEM version previous to 5.5 -->
                <groupId>com.day.cq</groupId>
                <artifactId>cq-quickstart-product-dependencies</artifactId>
                <version>5.6.1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
```


### Step 2.2 Bundle POM file

Update the dependencies section by add the following dependency element
``` xml
<dependencies>
    <!-- ... -->
    <dependency>
        <groupId>com.day.cq.wcm</groupId>
        <artifactId>cq-msm-api</artifactId>
    </dependency>
    <!-- ... -->
</dependencies>
```

### Step 2.3 Content POM file
Update the dependencies section by add the following dependency elements
``` xml
<dependency>
    <groupId>org.apache.sling</groupId>
    <artifactId>org.apache.sling.jcr.jcr-wrapper</artifactId>
    <!-- javax.jcr -->
</dependency>
<dependency>
    <groupId>org.apache.sling</groupId>
    <artifactId>org.apache.sling.api</artifactId>
</dependency>
<dependency>
    <groupId>com.day.cq</groupId>
    <artifactId>cq-commons</artifactId>
    <!-- com.day.cq.commons -->
</dependency>
<dependency>
    <groupId>com.day.cq.wcm</groupId>
    <artifactId>cq-wcm-commons</artifactId>
    <!-- com.day.cq.wcm.commons -->
</dependency>
<dependency>
    <groupId>com.day.cq.wcm</groupId>
    <artifactId>cq-wcm-api</artifactId>
    <!-- com.day.cq.wcm.api -->
</dependency>
<dependency>
    <groupId>com.day.commons</groupId>
    <artifactId>day-commons-jstl</artifactId>
    <!-- javax.servlet.jsp.jstl.core -->
</dependency>
<dependency>
    <groupId>com.day.cq.wcm</groupId>
    <artifactId>cq-wcm-taglib</artifactId>
    <!-- com.day.cq.wcm.tags -->
</dependency>
<dependency>
    <groupId>org.apache.sling</groupId>
    <artifactId>org.apache.sling.scripting.jsp.taglib</artifactId>
    <!-- org.apache.sling.scripting.jsp.taglib -->
</dependency>
<dependency>
    <groupId>com.adobe.granite</groupId>
    <artifactId>com.adobe.granite.xssprotection</artifactId>
    <!-- com.adobe.granite.xss -->
</dependency>
<dependency>
    <groupId>com.day.cq.wcm</groupId>
    <artifactId>cq-wcm-core</artifactId>
    <!-- com.day.cq.wcm.core.components -->
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <!-- org.apache.commons.lang3 -->
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>
```
and update the plugins section by add the following plugin elements, read [here](https://docs.adobe.com/docs/en/cq/5-6-1/developing/developmenttools/how-to-build-aem-projects-using-apache-maven.html#Compiling JSPs as part of the Maven Compile Phase) for more details
```xml
<build>
  <!-- ... -->
  <plugins>
    <!-- ... -->
    <plugin>
      <artifactId>maven-resources-plugin</artifactId>
      <executions>
        <execution>
          <id>copy-resources</id>
          <phase>generate-sources</phase>
          <goals>
            <goal>copy-resources</goal>
          </goals>
          <configuration>
            <outputDirectory>${project.build.directory}/jsps-to-compile</outputDirectory>
            <resources>
              <resource>
                <directory>src/main/content/jcr_root</directory>
                <excludes>
                  <exclude>libs/**</exclude>
                </excludes>
              </resource>
            </resources>
          </configuration>
        </execution>
      </executions>
    </plugin>
    <plugin>
      <groupId>org.apache.sling</groupId>
      <artifactId>maven-jspc-plugin</artifactId>
      <version>2.0.6</version>
      <executions>
        <execution>
          <id>compile-jsp</id>
          <goals>
            <goal>jspc</goal>
          </goals>
          <configuration>
            <jasperClassDebugInfo>false</jasperClassDebugInfo>
            <sourceDirectory>${project.build.directory}/jsps-to-compile</sourceDirectory>
            <outputDirectory>${project.build.directory}/ignoredjspc</outputDirectory>
          </configuration>
        </execution>
      </executions>
    </plugin>
    <plugin>
      <artifactId>maven-clean-plugin</artifactId>
      <executions>
        <execution>
          <id>remove-compiled-jsps</id>
          <goals>
            <goal>clean</goal>
          </goals>
          <phase>process-classes</phase>
          <configuration>
            <excludeDefaultDirectories>true</excludeDefaultDirectories>
            <filesets>
              <fileset>
                <directory>${project.build.directory}/jsps-to-compile</directory>
                <directory>${project.build.directory}/ignoredjspc</directory>
              </fileset>
            </filesets>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

#References
####cq5.6.1 sample pom file
http://dev.cqblueprints.com/nexus/content/repositories/releases/com/cqblueprints/cqdependencies/5.6.1/cqdependencies-5.6.1.pom
####cq-quickstart-product-dependencies pom file
https://repo.adobe.com/nexus/content/groups/public/com/day/cq/cq-quickstart-product-dependencies/5.6.1/cq-quickstart-product-dependencies-5.6.1.pom
