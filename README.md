# sunkernal-starter-parent



```
You can easily create the Git repository on GitHub and do the following steps.

First of all tell maven to deploy artifacts to a temporary staging location inside your target directory. Add this to your project pom.xml

<distributionManagement>
    <repository>
        <id>internal.repo</id>
        <name>Temporary Staging Repository</name>
        <url>file://${project.build.directory}/mvn-repo</url>
    </repository>
</distributionManagement>
 
<build>
 <plugins>
    <plugin>
        <artifactId>maven-deploy-plugin</artifactId>
        <version>2.8.1</version>
        <configuration>
               <altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
        </configuration>
    </plugin>
 </plugins>
</build>
Run mvn clean deploy. You’ll see maven artifact is deployed your maven repository located in target/mvn-repo.

Secondly we have to upload the created artifact to github. In order to do that add your authentication information to ~/.m2/settings.xml so then the github site-maven-plugin can push to github

<settings>
  <servers>
    <server>
      <id>github</id>
      <username>YOUR-GITHUB-USERNAME</username>
      <password>YOUR-GITHUB-PASSWORD</password>
    </server>
  </servers>
</settings>
Please make sure this file has proper security because some one can take your credentials.Then the next step is give the server details to your project by referring the server id created above.

Go to your project pom file and place the following property

<properties>
    <!-- github server corresponds to entry in ~/.m2/settings.xml -->
    <github.global.server>github</github.global.server>
</properties>
The last step is going to upload your locally created artifacts in to the github. In my point of view you better create new branch on your project as mvn-repo and place the following configuration on your project.

<plugin>
         <groupId>com.github.github</groupId>
         <artifactId>site-maven-plugin</artifactId>
         <version>0.8</version>
         <configuration>
              <!-- git commit message -->
              <message>Maven artifacts for ${project.version}</message>
              <!-- disable webpage processing -->
              <noJekyll>true</noJekyll>
              <!-- matches distribution management repository url above -->
              <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory>
              <!-- remote branch name -->
              <branch>refs/heads/mvn-repo</branch>
              <!-- If you remove this then the old artifact will be removed and new 
               one will replace. But with the merge tag you can just release by changing 
                                                the version -->
              <merge>true</merge>
              <includes>
                <include>**/*</include>
                </includes>
                <!-- github repo name -->
                <repositoryName>sw-protection-client</repositoryName>
                <!-- github username -->
                <repositoryOwner>YOUR-GITHUB-USERNAME</repositoryOwner>
          </configuration>
          <executions>
              <execution>
                    <goals>
                         <goal>site</goal>
                    </goals>
                    <phase>deploy</phase>
              </execution>
          </executions>
</plugin>
Run mvn clean deploy again. You should see maven-deploy-plugin create the files to your local staging repository in the target directory, then site-maven-plugin committing those files and pushing them to the server. Now the artifact is available to public use.

When you need to use your uploaded artifact you have to do the following step. You need to add your repository as follows

<repositories>
    <repository>
        <id>YOUR-PROJECT-NAME-mvn-repo</id>
        <url>https://raw.github.comYOUR-GITHUB-USERNAME/YOUR-PROJECT-NAME/mvn-repo/</url>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
</repositories>
Now you can refer your maven artifacts easily. I hope now you can expose your simple artifacts as you wish.
```

