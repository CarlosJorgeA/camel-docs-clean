::: {#header}
# Release Guide
:::

::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This guide covers how to create and announce a Camel release.
Instructions on updating the website to include the new documentation
version are [here](release-guide-website.html).
:::
::::
:::::

:::::: sect1
## Prequisites {#ReleaseGuide-Prequisites}

::::: sectionbody
::: paragraph
To prepare or perform a release, you **must be** at least an Apache
Camel committer.
:::

::: ulist
- The artifacts for each and every release must be **signed**.

- Your public key must be added to the KEYS file.

- Your public key should also be cross-signed by other Apache committers
  (this can be done at key signing parties at ApacheCon, for instance).

- Make sure you have the correct maven configuration in
  `~/.m2/settings.xml`.

- [Maven Wrapper](https://github.com/takari/maven-wrapper) is used and
  bundled with Camel 2.21 onwards and should be used for building the
  release.

- You may want to get familiar with the release settings in the parent
  Apache POM.

- Make sure you are using Java 11 for Apache Camel 3 and Java 21 for
  Apache Camel 4.
:::
:::::
::::::

:::::::: sect1
## Maven Setup {#ReleaseGuide-MavenSetup}

::::::: sectionbody
::: paragraph
Before you deploy anything to the [Apache Nexus
repository](https://repository.apache.org) using Maven, you should
configure your `~/.m2/settings.xml` file so that the file permissions of
the deployed artifacts are group-writable. If you do not do this, other
developers will not be able to overwrite your SNAPSHOT releases with
newer versions.
:::

::: paragraph
The settings follow the guidelines used by the Maven project. Please pay
particular attention to the [password encryption
recommendations](http://maven.apache.org/guides/mini/guide-encryption.html).
:::

:::: listingblock
::: content
``` highlight
<settings>
  ...
  <servers>
    <!-- Per http://maven.apache.org/developers/committer-settings.html -->

    <!-- To publish a snapshot of some part of Maven -->
    <server>
      <id>apache.snapshots.https</id>
      <username> <!-- YOUR APACHE LDAP USERNAME --> </username>
      <password> <!-- YOUR APACHE LDAP PASSWORD --> </password>
    </server>
    <!-- To publish a website of some part of Maven -->
    <server>
      <id>apache.website</id>
      <username> <!-- YOUR APACHE LDAP USERNAME --> </username>
      <filePermissions>664</filePermissions>
      <directoryPermissions>775</directoryPermissions>
    </server>
    <!-- To stage a release of some part of Maven -->
    <server>
      <id>apache.releases.https</id>
      <username> <!-- YOUR APACHE LDAP USERNAME --> </username>
      <password> <!-- YOUR APACHE LDAP PASSWORD --> </password>
    </server>
    <!-- To stage a website of some part of Maven -->
    <server>
      <id>stagingSite</id> <!-- must match hard-coded repository identifier in site:stage-deploy -->
      <username> <!-- YOUR APACHE LDAP USERNAME --> </username>
      <filePermissions>664</filePermissions>
      <directoryPermissions>775</directoryPermissions>
    </server>

  </servers>
  ...
  <profiles>
    <profile>
      <id>release</id>
      <properties>
        <gpg.useagent>false</gpg.useagent>
        <gpg.passphrase><!-- YOUR GPG PASSPHRASE --></gpg.passphrase>
        <test>false</test>
      </properties>
    </profile>

  </profiles>
...
</settings>
```
:::
::::
:::::::
::::::::

:::::: sect1
## Creating the Release {#ReleaseGuide-CreatingTheRelease-Camel}

::::: sectionbody
::: paragraph
Complete the following steps to create a new Camel release:
:::

::: {.olist .arabic}
1.  Grab the latest source from Git, checkout the target branch
    (`BRANCH_NAME`) to build from, and create a release branch off of
    that branch:

    :::: literalblock
    ::: content
        $ git clone https://git-wip-us.apache.org/repos/asf/camel.git
        $ cd camel
        $ git checkout BRANCH_NAME
        $ git checkout -b release/NEW-VERSION
    :::
    ::::

2.  Perform a license check with [Apache
    Rat](http://creadur.apache.org/rat/apache-rat-plugin):

    :::: literalblock
    ::: content
        ./mvnw -e org.apache.rat:apache-rat-plugin:check
        grep -e ' !?????' target/rat.txt
    :::
    ::::

    ::: ulist
    - The latter command will provide a list of all files without valid
      license headers. Ideally this list is empty, otherwise fix the
      issues by adding valid license headers and rerun the above
      commands before proceeding with the next step.
    :::

3.  Do a release dry run to check for problems:

    :::: literalblock
    ::: content
        ./mvnw release:prepare -DdryRun -Prelease
    :::
    ::::

    ::: ulist
    - The release plugin will prompt for a release version, an SCM tag
      and the next release version.

    - Use a three digit release version of the form:
      `MAJOR.MINOR.PATCH`, e.g. `3.0.0`.

    - For the tag use a string of the form: `camel-MAJOR.MINOR.PATCH`,
      e.g. `camel-3.0.0`.

    - For the next version increase the patch version and append
      `-SNAPSHOT`, e.g. `3.0.1-SNAPSHOT`.

    - Make sure to check the generated signature files:

      :::: literalblock
      ::: content
          $ gpg camel-core/target/camel-core-3.0.0-SNAPSHOT.jar.asc
          gpg: assuming signed data in `camel-core/target/camel-core-3.0.0.jar'
          gpg: Signature made Sat 06 Apr 2019 03:58:01 AM PDT using RSA key ID 5942C049
          gpg: Good signature from "Gregor Zurowski <gzurowski@apache.org>"
      :::
      ::::
    :::

4.  Prepare the release:

    ::: ulist
    - First clean up the dry run results:

      :::: literalblock
      ::: content
          $ ./mvnw release:clean -Prelease
      :::
      ::::

    - Next prepare the release:

      :::: literalblock
      ::: content
          $ ./mvnw release:prepare -Prelease
      :::
      ::::

    - This command will create the tag and update all pom files with the
      given version number.
    :::

5.  Perform the release and publish to the Apache staging repository:

    :::: literalblock
    ::: content
        $ ./mvnw release:perform -Prelease
    :::
    ::::

6.  Close the Apache staging repository:

    ::: ulist
    - Login to
      [https://repository.apache.org](https://repository.apache.org){.bare}
      using your Apache LDAP credentials. Click on \"Staging
      Repositories\". Then select \"org.apache.camel-xxx\" in the list
      of repositories, where xxx represents your username and ip. Click
      \"Close\" on the toolbar above. This will close the repository
      from future deployments and make it available for others to view.
      If you are staging multiple releases together, skip this step
      until you have staged everything. Enter the name and version of
      the artifact being released in the \"Description\" field and then
      click \"Close\". This will make it easier to identify it later.
    :::

7.  Verify staged artifacts:

    ::: ulist
    - If you click on your repository, a tree view will appear below.
      You can then browse the contents to ensure the artifacts are as
      you expect them. Pay particular attention to the existence of
      \*.asc (signature) files. If you don't like the content of the
      repository, right-click your repository and choose \"Drop\". You
      can then roll back your release and repeat the process. Note the
      repository URL, you will need this in your vote email.
    :::
:::
:::::
::::::

:::::: sect1
## Creating the Release for camel-spring-boot {#ReleaseGuide-CreatingTheRelease-Camel-spring-boot}

::::: sectionbody
::: paragraph
Complete the following steps to create a new Camel-spring-boot release:
:::

::: {.olist .arabic}
1.  Grab the latest source from Git and checkout the target branch
    (`BRANCH_NAME`) to build from:

    :::: literalblock
    ::: content
        $ git clone https://git-wip-us.apache.org/repos/asf/camel-spring-boot.git
        $ cd camel
        $ git checkout BRANCH_NAME
    :::
    ::::

2.  From Camel 3.3.0 ahead, the camel-spring-boot project uses
    camel-dependencies as parent. You'll need to set the version here
    [https://github.com/apache/camel-spring-boot/blob/master/pom.xml#L26](https://github.com/apache/camel-spring-boot/blob/master/pom.xml#L26){.bare}
    To the version released from the main Camel repository as the first
    step.

3.  Perform a license check with [Apache
    Rat](http://creadur.apache.org/rat/apache-rat-plugin):

    :::: literalblock
    ::: content
        ./mvnw -e org.apache.rat:apache-rat-plugin:check
        grep -e ' !?????' target/rat.txt
    :::
    ::::

    ::: ulist
    - The latter command will provide a list of all files without valid
      license headers. Ideally this list is empty, otherwise fix the
      issues by adding valid license headers and rerun the above
      commands before proceeding with the next step.
    :::

4.  You already have built the main camel repo for releasing, so you
    already have a final version in your local repository. Change the
    camel-version property in
    [https://github.com/apache/camel-spring-boot/blob/master/pom.xml](https://github.com/apache/camel-spring-boot/blob/master/pom.xml){.bare}
    accordingly and commit.

5.  Do a release dry run to check for problems:

    :::: literalblock
    ::: content
        ./mvnw release:prepare -DdryRun -Prelease
    :::
    ::::

    ::: ulist
    - The release plugin will prompt for a release version, an SCM tag
      and the next release version.

    - Use a three digit release version of the form:
      `MAJOR.MINOR.PATCH`, e.g. `3.0.0`.

    - For the tag use a string of the form: `camel-MAJOR.MINOR.PATCH`,
      e.g. `camel-3.0.0`.

    - For the next version increase the patch version and append
      `-SNAPSHOT`, e.g. `3.0.1-SNAPSHOT`.

    - Make sure to check the generated signature files:

      :::: literalblock
      ::: content
          $ gpg core/camel-spring-boot/target/camel-spring-boot-3.0.0-SNAPSHOT.jar.asc
          gpg: assuming signed data in `core/camel-spring-boot/target/camel-spring-boot-3.0.0-SNAPSHOT.jar'
          gpg: Signature made Sat 06 Apr 2019 03:58:01 AM PDT using RSA key ID 5942C049
          gpg: Good signature from "Gregor Zurowski <gzurowski@apache.org>"
      :::
      ::::
    :::

6.  Prepare the release:

    ::: ulist
    - First clean up the dry run results:

      :::: literalblock
      ::: content
          $ ./mvnw release:clean -Prelease
      :::
      ::::

    - Next prepare the release:

      :::: literalblock
      ::: content
          $ ./mvnw release:prepare -Prelease
      :::
      ::::

    - This command will create the tag and update all pom files with the
      given version number.
    :::

7.  Perform the release and publish to the Apache staging repository:

    :::: literalblock
    ::: content
        $ ./mvnw release:perform -Prelease
    :::
    ::::

8.  Close the Apache staging repository:

    ::: ulist
    - Login to
      [https://repository.apache.org](https://repository.apache.org){.bare}
      using your Apache LDAP credentials. Click on \"Staging
      Repositories\". Then select \"org.apache.camel-xxx\" in the list
      of repositories, where xxx represents your username and ip. Click
      \"Close\" on the tool bar above. This will close the repository
      from future deployments and make it available for others to view.
      If you are staging multiple releases together, skip this step
      until you have staged everything. Enter the name and version of
      the artifact being released in the \"Description\" field and then
      click \"Close\". This will make it easier to identify it later.
    :::

9.  Verify staged artifacts:

    ::: ulist
    - If you click on your repository, a tree view will appear below.
      You can then browse the contents to ensure the artifacts are as
      you expect them. Pay particular attention to the existence of
      \*.asc (signature) files. If you don't like the content of the
      repository, right-click your repository and choose \"Drop\". You
      can then roll back your release and repeat the process. Note the
      repository URL, you will need this in your vote email.
    :::

10. Once the release has been voted

    ::: ulist
    - Login to
      [https://repository.apache.org](https://repository.apache.org){.bare}
      using your Apache LDAP credentials. Click on \"Staging
      Repositories\". Then select \"org.apache.camel-xxx\" in the list
      of repositories, where xxx represents your username and ip. Click
      \"Release\" on the tool bar above. This will release the
      artifacts.
    :::
:::
:::::
::::::

::::: sect1
## Publishing the Release {#ReleaseGuide-PublishingTheRelease-Camel}

:::: sectionbody
::: {.olist .arabic}
1.  Once the release has been voted:

    ::: ulist
    - Login to
      [https://repository.apache.org](https://repository.apache.org){.bare}
      using your Apache LDAP credentials. Click on \"Staging
      Repositories\". Then select \"org.apache.camel-xxx\" in the list
      of repositories, where xxx represents your username and IP. Click
      \"Release\" on the tool bar above. This will release the
      artifacts.
    :::

2.  Perform a release in JIRA:

    ::: ulist
    - Release the version in JIRA:
      [https://issues.apache.org/jira/plugins/servlet/project-config/CAMEL/versions](https://issues.apache.org/jira/plugins/servlet/project-config/CAMEL/versions){.bare}
    :::

3.  Copy distribution to Apache website:

    :::: literalblock
    ::: content
        cd ${CAMEL_ROOT_DIR}/etc/scripts
        ./release-distro.sh <Camel version>
    :::
    ::::

4.  Copy SBOMs to Apache website:

    :::: literalblock
    ::: content
        cd ${CAMEL_ROOT_DIR}/etc/scripts
        ./release-sbom.sh <Camel version>
    :::
    ::::

5.  Remove the old distribution version from the Apache website:

    :::: literalblock
    ::: content
        svn rm https://dist.apache.org/repos/dist/release/camel/apache-camel/OLD_CAMEL_VERSION -m "Removed the old release"
    :::
    ::::

6.  Upload the new schema files (and the manual):

    :::: literalblock
    ::: content
        cd ${CAMEL_ROOT_DIR}/etc/scripts
        ./release-website.sh <Camel version>
    :::
    ::::

7.  Merge the release branch back into the corresponding base branch
    (e.g., merge `release/3.2.0` into `camel-3.2.x`)

    :::: literalblock
    ::: content
        git checkout BASE_BRANCH
        git pull
        git merge --no-ff release/VERSION
        git push
    :::
    ::::

8.  Delete the local and remote release branch:

    :::: literalblock
    ::: content
        git branch -D release/VERSION
        git push origin --delete release/VERSION
    :::
    ::::
:::
::::
:::::

::::: sect1
## Publish xsd schemas {#Publish-xsd-schemas}

:::: sectionbody
::: ulist
- On
  [https://github.com/apache/camel-website/tree/main/static/schema](https://github.com/apache/camel-website/tree/main/static/schema){.bare}
  the xsd related to cxf,spring-security and spring must be pushed to
  make them available to end users.
:::
::::
:::::

::::::: sect1
## Tagging examples {#Tagging-examples}

:::::: sectionbody
::: paragraph
These steps are optional, and they could be done later too.
:::

::: paragraph
Once the release train (camel and camel-spring-boot) has been voted and
published, there are some additional steps needed for the camel
examples.
:::

::: {.olist .arabic}
1.  Camel-examples

    ::: ulist
    - On
      [https://github.com/apache/camel-examples](https://github.com/apache/camel-examples){.bare}
      in the examples/pom.xml file, the following steps are necessary:

    - Update the camel-dependencies version to the version coming from
      the release-train

    - Update the `camel.version` properties to the version coming from
      the release-train

    - To be sure everything is fine, run:

      :::: literalblock
      ::: content
          $ ./mvnw clean install
      :::
      ::::

    - Commit

      :::: literalblock
      ::: content
          $ git commit -a
          $ git push origin master (or the branch related to the release, eg. camel-3.4.x)
          $ git tag -a camel-examples-$version -m "$version"
          $ git push origin camel-examples-$version
      :::
      ::::

    - Now we pushed the tag, and we need to advance the version of the
      examples

    - Update the camel-dependencies version to the next version

    - Update the `camel.version` properties to the next version

    - Run the following command to advance the version in the examples

      :::: literalblock
      ::: content
          $ find . -type f -exec sed -i 's/$oldVersion/$newVersion/g' {} +
      :::
      ::::

    - To be sure everything is fine, run:

      :::: literalblock
      ::: content
          $ ./mvnw clean install
      :::
      ::::
    :::

2.  Camel-spring-boot-examples

    ::: ulist
    - On
      [https://github.com/apache/camel-spring-boot-examples](https://github.com/apache/camel-spring-boot-examples){.bare}
      in the examples/pom.xml file the following steps are necessary:

    - Update the camel-dependencies version to the version coming from
      the release-train

    - Update the `camel.version` properties to the version coming from
      the release-train

    - To be sure everything is fine, run:

      :::: literalblock
      ::: content
          $ ./mvnw clean install
      :::
      ::::

    - Commit

      :::: literalblock
      ::: content
          $ git commit -a
          $ git push origin master (or the branch related to the release, eg. camel-3.4.x)
          $ git tag -a camel-spring-boot-examples-$version -m "$version"
          $ git push origin camel-spring-boot-examples-$version
      :::
      ::::

    - Now we pushed the tag, and we need to advance the version of the
      examples

    - Update the camel-dependencies version to the next version

    - Update the `camel.version` properties to the next version

    - Run the following command to advance the version in the examples

      :::: literalblock
      ::: content
          $ find . -type f -exec sed -i 's/$oldVersion/$newVersion/g' {} +
      :::
      ::::

    - To be sure everything is fine, run:

      :::: literalblock
      ::: content
          $ ./mvnw clean install
      :::
      ::::
    :::
:::
::::::
:::::::
:::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
