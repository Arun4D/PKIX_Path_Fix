# Fix the Sun.security.validation.ValidationException: PKIX path building failed

Build tools like Maven / Gradle refer to the HTTPS repository for dependency management. 
Usually, we use key tool import .cer by downloading .cer from browser for any public repository like [atlassian Public](https://packages.atlassian.com/mvn/maven-atlassian-external/). Most of the organizations will not allow you to update the certs files (/jre/lib/security/cacerts) so to fix that we can create a new truststore and pass the truststore as an environment variable on the build.

## Commands to generate truststore

```
keytool -genkey -alias jiratruststore -keyalg RSA -keystore "C:\dev\keystore\jiratruststore.jks"
keytool -import -alias jira -file "C:\Downloads\jira.cer" -keystore "C:\dev\keystore\jiratruststore.jks" -storepass changeit
keytool -list -keystore "C:\dev\keystore\jiratruststore.jks"

```
## Passing truststore on build

```
mvn clean install -Djavax.net.ssl.trustStore="C:\dev\keystore\jiratruststore.jks" -Djavax.net.ssl.trustStorePassword=changeit

or

java -Djavax.net.ssl.trustStore="C:\dev\keystore\jiratruststore.jks" -Djavax.net.ssl.trustStorePassword=changeit -jar run.jar

```

