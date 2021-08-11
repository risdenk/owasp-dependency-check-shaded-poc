# OWASP Dependency Check Shaded POC

## Fix
This is fixed by using:

 `./mvnw clean verify -Danalyzer.dependencybundling.enabled=false`

This was idenfied in https://github.com/jeremylong/DependencyCheck/issues/3577

## Overview
When two Maven dependencies both shade the same library, 
OWASP dependency check ends up only reporting one instance
of the shaded dependency.

Both `hbase-shaded-client` and `htrace-core4` shade 
the same version of `jackson-databind`, however when OWASP 
dependency check runs it only reports `hbase-shaded-client` 
as vulnerable. If you remove `hbase-shaded-client`, then 
`htrace-core4` is reported as vulnerable even though it 
was in the project the whole time.

This isn't a problem for non shaded dependencies,
since in a Maven project there will only be one version
of a non shaded library chosen to include in the final
jar.

## Reproduction
* `./mvnw clean verify`
* `open target/dependency-check-report.html`
* Verify that `jackson-databind` is only listed for `hbase-shaded-client` with CVEs.
* Modify `pom.xml` to comment out `hbase-shaded-client` dependency.
* `./mvnw clean verify`
* `open target/dependency-check-report.html`
* Verify that `jackson-databind` is now listed under `htrace-core4` with CVEs.
