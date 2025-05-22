# java-workflows

These actions provide an automated way to build and publish Java projects using Maven.
Packages are deployed on Github Packages.


## Release workflow: 
This action provides an automated way to build and publish Java releases of the project using Maven, to github packages.

Usage: 
```yaml
jobs:
  release:
    uses: easymountain-mhikes/java-workflows/.github/workflows/release.yml@v2
    with:
      java-version: 21
      release-branch: main
    secrets: inherit
```

## Snapshots workflow:
This action provides an automated way to build and publish Java snapshots of the project using Maven, to github packages.

Usage: 
```yaml
jobs:
  snapshot:
    uses: easymountain-mhikes/java-workflows/.github/workflows/snapshot.yml@v2
    with:
      java-version: 21
    secrets: inherit
```

## Pull Request workflow:

This action provides an automated way to build and test Java projects using Maven, ie. when creating a pull request.
The build jars are uploaded as artifacts.

Usage: 
```yaml
jobs:
  build:
    uses: easymountain-mhikes/java-workflows/.github/workflows/pull_request.yml@v2
    with:
      java-version: 21
    secrets: inherit
```

## Configuration of the projects to properly handle GitHub packages

For more information on how to configure your project to use GitHub packages, please refer to the [GitHub documentation](https://docs.github.com/fr/packages/working-with-a-github-packages-registry/working-with-the-apache-maven-registry).


To download dependencies from GitHub packages, you can either add the following configuration to your `pom.xml` file, or to your `.m2/settings.xml`:

```xml
    <repositories>
        <repository>
            <id>github</id>
            <url>https://maven.pkg.github.com/easymountain-mhikes/*</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </repository>
    </repositories>
```

In order to publish your project to GitHub packages, you need to add the following configuration to your `pom.xml` file, changing the REPOSITORY placeholder with the name of your repository:

```xml
    <distributionManagement>
        <repository>
            <id>github</id>
            <url>https://maven.pkg.github.com/easymountain-mhikes/REPOSITORY</url>
        </repository>
    </distributionManagement>
```