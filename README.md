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


## Access to the maven packages

You'll need to create a [personal access](https://github.com/settings/tokens) token with the `read:packages` scope and add it to your maven settings in `~/.m2/settings.xml` as following:

If you need to publish to the repository manually, you can also add the `write:packages` scope to your token.

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>github</id>
      <username>GITHUB-USERNAME</username>
      <password>GITHUB-TOKEN</password>
    </server>
  </servers>
</settings>

## Configuration of the projects to properly handle GitHub packages

For more information on how to configure your project to use GitHub packages, please refer to the [GitHub documentation](https://docs.github.com/fr/packages/working-with-a-github-packages-registry/working-with-the-apache-maven-registry).


To download dependencies from GitHub packages, you can either add the following configuration to your `pom.xml` file, or to your `~/.m2/settings.xml`:

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

## Releasing a new version of the workflows

Workflows should be referenced with their version, e.g. `@v2` or `@2.0`, to ensure that the workflow is not broken by changes in the main branch.

Unfortunately, these were referenced early in the projects as 
```yaml
  uses: easymountain-mhikes/java-workflows/.github/workflows/pull_request.yml@main
```
This should be changed to `@v2` or `@2.0` in the future, but for now, we needed to release a new version of the workflow to ensure that the workflow is not broken by changes in the main branch.
These changed are on the branch `github-repository`, which is the branch that is used to release new versions of the workflow. This branch should be merge to the main branch whenever it's possible.

Once the main branch is stable, we can release a new version of the workflow by creating a new tag.

```bash
# If the v2 tag already exists, delete it
git tag -d v2

# Create the tags
git tag -a v2 -m "Release v2.0"
git tag -a v2.0 -m "Release v2.0"

# Push the tags to the remote repository
git push -f origin v2
git push origin v2.0
```

## Manual release

```bash
./mvnw --batch-mode release:clean release:prepare release:perform
```

This command will perform the following operations:

- Check that there are no SNAPSHOT dependencies
- Build the project and verify that the tests pass
- Change the version from x.y.z-SNAPSHOT to x.y.z
- Create a commit for this change (with the message [maven-release-plugin] prepare release ...)
- Push this commit to GitHub
- Create a Git tag in the format artifactId-x.y.z
- Push this tag to GitHub
- Change the version from x.y.z to x.y.z+1-SNAPSHOT
- Create a commit for this change (with the message [maven-release-plugin] prepare for next development iteration)
- Push this commit to GitHub
- Push the packages to GitHub Packages

You may need to delete any tag / release / package that was created by the previous command, if it was already released on the CI.