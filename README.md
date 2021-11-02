# Fix the Sun.security.validation.ValidationException: PKIX path building failed

Build tools like Maven / Gradle refer to the HTTPS repository for dependency management. 
Usually, we use key tool import .cer by downloading .cer from browser for any public repository like [atlassian Public](https://packages.atlassian.com/mvn/maven-atlassian-external/). Most of the organizations will not allow you to update the certs files (/jre/lib/security/cacerts) so to fix that we can create a new truststore and pass the truststore as an environment variable on the build.

## Copy cacerts from JRE to any other folder like c:\dev\keystore
```
Copy cacerts from \jre\lib\security\cacerts to c:\dev\keystore\cacerts
```

## Commands to generate truststore

```
keytool -genkey -alias jiratruststore -keyalg RSA -keystore "C:\dev\keystore\cacerts"
keytool -import -alias jira -file "C:\Downloads\jira.cer" -keystore "C:\dev\keystore\cacertss" -storepass changeit
keytool -list -keystore "C:\dev\keystore\cacerts"
```
## Passing truststore on maven / gradle / shell run

```
mvn clean install -Djavax.net.ssl.trustStore="C:\dev\keystore\cacerts" -Djavax.net.ssl.trustStorePassword=changeit -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true

or

java -Djavax.net.ssl.trustStore="C:\dev\keystore\cacerts" -Djavax.net.ssl.trustStorePassword=changeit -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true -jar run.jar

```

## Check the properties

Check the compiler source and target
```
<properties>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.source>1.8</maven.compiler.source>
</properties>
```
