::: {#header}
# Security
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
Camel offers several forms and levels of security capabilities that can
be used on Camel routes. These various forms of security may be used in
conjunction with each other or separately.
:::

::: paragraph
The broad categories offered are:
:::

::: ulist
- *Route Security* - Authentication and Authorization services to
  proceed on a route or route segment

- *Payload Security* - Data Formats that offer encryption/decryption
  services at the payload level

- *Endpoint Security* - Security offered by components that can be
  utilized by endpointUri associated with the component

- *Configuration Security* - Security offered by encrypting sensitive
  information from configuration files or external Secured Vault
  systems.
:::

::: paragraph
Camel offers the [JSSE Utility](camel-configuration-utilities.html) for
configuring SSL/TLS related aspects of a number of Camel components.
:::
:::::::
::::::::

:::::::: sect1
## Route Security {#_route_security}

::::::: sectionbody
::: paragraph
Authentication and Authorization Services
:::

::: paragraph
Camel offers [Route Policy](route-policy.html) driven security
capabilities that may be wired into routes or route segments. A route
policy in Camel utilizes a strategy pattern for applying interceptors on
Camel Processors. It's offering the ability to apply cross-cutting
concerns (for example. security, transactions etc) of a Camel route.
:::

::: paragraph
The components offering authentication and authorization services
utilizing [Route Policy](route-policy.html) are:
:::

::: ulist
- [Shiro Security](components:others:shiro.html)

- [Spring Security](components:others:spring-security.html)
:::
:::::::
::::::::

::::::: sect1
## Payload Security {#_payload_security}

:::::: sectionbody
::: paragraph
Camel offers encryption/decryption services to secure payloads or
selectively apply encryption/decryption capabilities on
portions/sections of a payload.
:::

::: paragraph
The dataformats offering encryption/decryption of payloads utilizing
[Marshal](components:eips:marshal-eip.html) are:
:::

::: ulist
- [Crypto](components:dataformats:crypto-dataformat.html)

- [PGP](components:dataformats:pgp-dataformat.html)

- [XML security](components:dataformats:xmlSecurity-dataformat.html)
:::
::::::
:::::::

::::: sect1
## Endpoint Security {#_endpoint_security}

:::: sectionbody
::: paragraph
Some components in Camel offer an ability to secure their endpoints
(using interceptors etc) and therefore ensure that they offer the
ability to secure payloads as well as provide
authentication/authorization capabilities at endpoints created using the
components.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Configuration Security {#_configuration_security}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel offers the [Properties](components::properties-component.html)
component to externalize configuration values to properties files. Those
values could contain sensitive information such as usernames and
passwords.
:::

::: paragraph
Those values can be encrypted and automatically decrypted by Camel
using:
:::

::: ulist
- [Jasypt](components:others:jasypt.html)
:::

::: paragraph
Camel also supports accessing the secured configuration from an external
vault systems.
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### Configuration Security using Vaults {#_configuration_security_using_vaults}

::: paragraph
The following *Vaults* are supported by Camel:
:::

::: ulist
- [AWS Secrets Manager](components::aws-secrets-manager-component.html)

- [Google Secret
  Manager](components::google-secret-manager-component.html)

- [Azure Key Vault](components::azure-key-vault-component.html)

- [Hashicorp Vault](components::hashicorp-vault-component.html)
:::

:::::::::::::::::::::::::::::::::::::::::: sect3
#### Using AWS Vault {#_using_aws_vault}

::: paragraph
To use AWS Secrets Manager, you need to provide *accessKey*, *secretKey*
and the *region*. This can be done using environmental variables before
starting the application:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AWS_ACCESS_KEY=accessKey
export $CAMEL_VAULT_AWS_SECRET_KEY=secretKey
export $CAMEL_VAULT_AWS_REGION=region
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.accessKey = accessKey
camel.vault.aws.secretKey = secretKey
camel.vault.aws.region = region
```
:::
::::

::: paragraph
If you want instead to use the [AWS default credentials
provider](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/credentials.html),
you'll need to provide the following env variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AWS_USE_DEFAULT_CREDENTIALS_PROVIDER=true
export $CAMEL_VAULT_AWS_REGION=region
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.defaultCredentialsProvider = true
camel.vault.aws.region = region
```
:::
::::

::: paragraph
It is also possible to specify a particular profile name for accessing
AWS Secrets Manager
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AWS_USE_PROFILE_CREDENTIALS_PROVIDER=true
export $CAMEL_VAULT_AWS_PROFILE_NAME=test-account
export $CAMEL_VAULT_AWS_REGION=region
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.profileCredentialsProvider = true
camel.vault.aws.profileName = test-account
camel.vault.aws.region = region
```
:::
::::

::: paragraph
At this point you'll be able to reference a property in the following
way by using `aws:` as prefix in the `{{ }}` syntax:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{aws:route}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Where `route` will be the name of the secret stored in the AWS Secrets
Manager Service.
:::

::: paragraph
You could specify a default value in case the secret is not present on
AWS Secret Manager:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{aws:route:default}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist, the property will fallback to
\"default\" as value.
:::

::: paragraph
Also, you are able to get a particular field of the secret, if you have,
for example, a secret named database of this form:
:::

:::: listingblock
::: content
``` highlight
{
  "username": "admin",
  "password": "password123",
  "engine": "postgres",
  "host": "127.0.0.1",
  "port": "3128",
  "dbname": "db"
}
```
:::
::::

::: paragraph
You're able to do get single secret value in your route, like for
example:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{aws:database#username}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Or re-use the property as part of an endpoint.
:::

::: paragraph
You could specify a default value in case the particular field of secret
is not present on AWS Secret Manager:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{aws:database#username:admin}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist or the secret exists, but the
username field is not part of the secret, the property will fall back to
\"admin\" as value.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For the moment we are not         |
| Note                              | considering the rotation function |
| :::                               | if any are applied, but it is in  |
|                                   | the work to be done.              |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The only requirement is adding `camel-aws-secrets-manager` JAR to your
Camel application.
:::
::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::: sect3
#### Using GCP Vault {#_using_gcp_vault}

::: paragraph
To use GCP Secret Manager, you need to provide *serviceAccountKey* file
and GCP *projectId*. This can be done using environmental variables
before starting the application:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_GCP_SERVICE_ACCOUNT_KEY=file:////path/to/service.accountkey
export $CAMEL_VAULT_GCP_PROJECT_ID=projectId
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.gcp.serviceAccountKey = accessKey
camel.vault.gcp.projectId = secretKey
```
:::
::::

::: paragraph
If you want instead to use the [GCP default client
instance](https://cloud.google.com/docs/authentication/production),
you'll need to provide the following env variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_GCP_USE_DEFAULT_INSTANCE=true
export $CAMEL_VAULT_GCP_PROJECT_ID=projectId
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.gcp.useDefaultInstance = true
camel.vault.aws.projectId = region
```
:::
::::

::: paragraph
At this point you'll be able to reference a property in the following
way by using `gcp:` as prefix in the `{{ }}` syntax:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{gcp:route}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Where `route` will be the name of the secret stored in the GCP Secret
Manager Service.
:::

::: paragraph
You could specify a default value in case the secret is not present on
GCP Secret Manager:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{gcp:route:default}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist, the property will fallback to
\"default\" as value.
:::

::: paragraph
Also, you are able to get a particular field of the secret, if you have,
for example, a secret named database of this form:
:::

:::: listingblock
::: content
``` highlight
{
  "username": "admin",
  "password": "password123",
  "engine": "postgres",
  "host": "127.0.0.1",
  "port": "3128",
  "dbname": "db"
}
```
:::
::::

::: paragraph
You're able to do get single secret value in your route, like for
example:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{gcp:database#username}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Or re-use the property as part of an endpoint.
:::

::: paragraph
You could specify a default value in case the particular field of secret
is not present on GCP Secret Manager:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{gcp:database#username:admin}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist or the secret exists, but the
username field is not part of the secret, the property will fallback to
\"admin\" as value.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For the moment we are not         |
| Note                              | considering the rotation function |
| :::                               | if any are applied, but it is in  |
|                                   | the work to be done.              |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
There are only two requirements: - Adding `camel-google-secret-manager`
JAR to your Camel application. - Give the service account used
permissions to do operation at secret management level (for example
accessing the secret payload, or being admin of secret manager service)
:::
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::: sect3
#### Using Azure Key Vault {#_using_azure_key_vault}

::: paragraph
To use this function, you'll need to provide credentials to Azure Key
Vault Service as environment variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AZURE_TENANT_ID=tenantId
export $CAMEL_VAULT_AZURE_CLIENT_ID=clientId
export $CAMEL_VAULT_AZURE_CLIENT_SECRET=clientSecret
export $CAMEL_VAULT_AZURE_VAULT_NAME=vaultName
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.azure.tenantId = accessKey
camel.vault.azure.clientId = clientId
camel.vault.azure.clientSecret = clientSecret
camel.vault.azure.vaultName = vaultName
```
:::
::::

::: paragraph
Or you can enable the usage of Azure Identity in the following way:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AZURE_IDENTITY_ENABLED=true
export $CAMEL_VAULT_AZURE_VAULT_NAME=vaultName
```
:::
::::

::: paragraph
You can also enable the usage of Azure Identity in the
`application.properties` file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.azure.azureIdentityEnabled = true
camel.vault.azure.vaultName = vaultName
```
:::
::::

::: paragraph
At this point, you'll be able to reference a property in the following
way:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{azure:route}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Where route will be the name of the secret stored in the Azure Key Vault
Service.
:::

::: paragraph
You could specify a default value in case the secret is not present on
Azure Key Vault Service:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{azure:route:default}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist, the property will fallback to
\"default\" as value.
:::

::: paragraph
Also you are able to get a particular field of the secret if you have,
for example, a secret named database of this form:
:::

:::: listingblock
::: content
``` highlight
{
  "username": "admin",
  "password": "password123",
  "engine": "postgres",
  "host": "127.0.0.1",
  "port": "3128",
  "dbname": "db"
}
```
:::
::::

::: paragraph
You're able to do get single secret value in your route, like for
example:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{azure:database#username}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Or re-use the property as part of an endpoint.
:::

::: paragraph
You could specify a default value in case the particular field of secret
is not present on Azure Key Vault:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{azure:database#username:admin}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist or the secret exists, but the
username field is not part of the secret, the property will fallback to
\"admin\" as value.
:::

::: paragraph
For the moment we are not considering the rotation function if any are
applied, but it is in the work to be done.
:::

::: paragraph
The only requirement is adding the camel-azure-key-vault jar to your
Camel application.
:::
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::: sect3
#### Using Hashicorp Vault {#_using_hashicorp_vault}

::: paragraph
To use this function, you'll need to provide credentials for Hashicorp
vault as environment variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_HASHICORP_TOKEN=token
export $CAMEL_VAULT_HASHICORP_HOST=host
export $CAMEL_VAULT_HASHICORP_PORT=port
export $CAMEL_VAULT_HASHICORP_SCHEME=http/https
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.hashicorp.token = token
camel.vault.hashicorp.host = host
camel.vault.hashicorp.port = port
camel.vault.hashicorp.scheme = scheme
```
:::
::::

::: paragraph
In case the running Hashicorp Vault instance you're pointing is running
on Hashicorp Cloud, the configuration will require two additional
parameters:
:::

:::: listingblock
::: content
``` highlight
export CAMEL_VAULT_HASHICORP_TOKEN=token
export CAMEL_VAULT_HASHICORP_HOST=host
export CAMEL_VAULT_HASHICORP_PORT=port
export CAMEL_VAULT_HASHICORP_SCHEME=http/https
export CAMEL_HASHICORP_VAULT_CLOUD=true
export CAMEL_HASHICORP_VAULT_NAMESPACE=namespace
```
:::
::::

::: paragraph
You can also set the same in the `application.properties` file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.hashicorp.token = token
camel.vault.hashicorp.host = host
camel.vault.hashicorp.port = port
camel.vault.hashicorp.scheme = scheme
camel.vault.hashicorp.cloud = true
camel.vault.hashicorp.namespace = namespace
```
:::
::::

::: paragraph
At this point, you'll be able to reference a property in the following
way:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{hashicorp:secret:route}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Where route will be the name of the secret stored in the Hashicorp Vault
instance, in the \'secret\' engine.
:::

::: paragraph
You could specify a default value in case the secret is not present on
Hashicorp Vault instance:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{hashicorp:secret:route:default}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist in the \'secret\' engine, the
property will fall back to \"default\" as value.
:::

::: paragraph
Also, you are able to get a particular field of the secret, if you have,
for example, a secret named database of this form:
:::

:::: listingblock
::: content
``` highlight
{
  "username": "admin",
  "password": "password123",
  "engine": "postgres",
  "host": "127.0.0.1",
  "port": "3128",
  "dbname": "db"
}
```
:::
::::

::: paragraph
You're able to do get single secret value in your route, in the
\'secret\' engine, like, for example:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{hashicorp:secret:database#username}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Or re-use the property as part of an endpoint.
:::

::: paragraph
You could specify a default value in case the particular field of secret
is not present on Hashicorp Vault instance, in the \'secret\' engine:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{hashicorp:secret:database#username:admin}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist or the secret exists (in the
\'secret\' engine) but the username field is not part of the secret, the
property will fall back to \"admin\" as value.
:::

::: paragraph
There is also the syntax to get a particular version of the secret for
both the approach, with field/default value specified or only with
secret:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{hashicorp:secret:route@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the RAW route secret with version \'2\', in
the \'secret\' engine.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{hashicorp:route:default@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the route secret value with version \'2\' or
default value in case the secret doesn't exist or the version doesn't
exist (in the \'secret\' engine).
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{hashicorp:secret:database#username:admin@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the username field of the database secret with
version \'2\' or admin in case the secret doesn't exist or the version
doesn't exist (in the \'secret\' engine).
:::
::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::: sect3
#### Using IBM Secrets Manager Vault {#_using_ibm_secrets_manager_vault}

::: paragraph
To use this function, you'll need to provide credentials for IBM Secrets
Manager vault as environment variables:
:::

:::: listingblock
::: content
``` highlight
export CAMEL_VAULT_IBM_TOKEN=token
export CAMEL_VAULT_IBM_SERVICE_URL=serviceUrl
```
:::
::::

::: paragraph
You can also configure the credentials in the `application.properties`
file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.ibm.token = token
camel.vault.ibm.serviceUrl = serviceUrl
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | if you're running the application |
| Note                              | on a Kubernetes based cloud       |
| :::                               | platform, you can initialize the  |
|                                   | environment variables from a      |
|                                   | Secret or Configmap to enhance    |
|                                   | security. You can also enhance    |
|                                   | security by [setting a Secret     |
|                                   | property                          |
|                                   | placeholder](manual::using-p      |
|                                   | ropertyplaceholder.html#_resolvin |
|                                   | g_property_placeholders_on_cloud) |
|                                   | which will be initialized at      |
|                                   | application runtime only.         |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | `camel.vault.ibm` configuration   |
| Note                              | only applies to the IBM Secrets   |
| :::                               | Manager Vault properties function |
|                                   | (E.g when resolving properties).  |
|                                   | When using the `operation` option |
|                                   | to create, get, list secrets      |
|                                   | etc., you should provide the      |
|                                   | `token` and `serviceUrl` options. |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
At this point, you'll be able to reference a property in the following
way:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{ibm:default:route}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Where route will be the name of the secret stored in the IBM Secrets
Manager Vault instance, in the \'default\' secret group.
:::

::: paragraph
You could specify a default value in case the secret is not present on
IBM Secrets Manager Vault instance:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{ibm:default:route:default}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist in the \'default\' secret
group, the property will fall back to \"default\" as value.
:::

::: paragraph
Also, you are able to get a particular field of the secret, if you have,
for example, a secret named database of this form:
:::

:::: listingblock
::: content
``` highlight
{
  "username": "admin",
  "password": "password123",
  "engine": "postgres",
  "host": "127.0.0.1",
  "port": "3128",
  "dbname": "db"
}
```
:::
::::

::: paragraph
You're able to do get single secret value in your route, in the
\'default\' secret group, like for example:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{ibm:default:database#username}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
Or re-use the property as part of an endpoint.
:::

::: paragraph
You could specify a default value in case the particular field of secret
is not present on IBM Secrets Manager Vault instance, in the \'secret\'
engine:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{ibm:default:database#username:admin}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this case, if the secret doesn't exist or the secret exists (in the
\'default\' secret group) but the username field is not part of the
secret, the property will fall back to \"admin\" as value.
:::

::: paragraph
There is also the syntax to get a particular version of the secret for
both the approaches, with field/default value specified or only with
secret:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{ibm:default:route@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the RAW route secret with version \'2\', in
the \'default\' secret group.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{ibm:default:route:default@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the route secret value with version \'2\' or
default value in case the secret doesn't exist or the version doesn't
exist (in the \'default\' secret group).
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <log message="Username is {{ibm:default:database#username:admin@2}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
This approach will return the username field of the database secret with
version \'2\' or admin in case the secret doesn't exist or the version
doesn't exist (in the \'default\' secret group).
:::

::: paragraph
The only requirement is adding the camel-ibm-secrets-manager jar to your
Camel application.
:::
:::::::::::::::::::::::::::::::::::::::::

::::::::::::::::: sect3
#### Automatic Camel context reloading on Secret Refresh while using AWS Secrets Manager {#_automatic_camel_context_reloading_on_secret_refresh_while_using_aws_secrets_manager}

::: paragraph
Being able to reload Camel context on a Secret Refresh, could be done by
specifying the usual credentials (the same used for AWS Secret Manager
Property Function).
:::

::: paragraph
With Environment variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AWS_USE_DEFAULT_CREDENTIALS_PROVIDER=accessKey
export $CAMEL_VAULT_AWS_REGION=region
```
:::
::::

::: paragraph
or as plain Camel main properties:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.useDefaultCredentialProvider = true
camel.vault.aws.region = region
```
:::
::::

::: paragraph
Or by specifying accessKey/SecretKey and region, instead of using the
default credentials provider chain.
:::

::: paragraph
To enable the automatic refresh, you'll need additional properties to
set:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.refreshEnabled=true
camel.vault.aws.refreshPeriod=60000
camel.vault.aws.secrets=Secret
camel.main.context-reload-enabled = true
```
:::
::::

::: paragraph
where `camel.vault.aws.refreshEnabled` will enable the automatic context
reload, `camel.vault.aws.refreshPeriod` is the interval of time between
two different checks for update events and `camel.vault.aws.secrets` is
a regex representing the secrets we want to track for updates.
:::

::: paragraph
Note that `camel.vault.aws.secrets` is not mandatory: if not specified
the task responsible for checking updates events will take into accounts
or the properties with an `aws:` prefix.
:::

::: paragraph
The only requirement is adding the camel-aws-secrets-manager jar to your
Camel application.
:::
:::::::::::::::::

::::::::::::::::::::: sect3
#### Automatic Camel context reloading on Secret Refresh while using AWS Secrets Manager with Eventbridge and AWS SQS Services {#_automatic_camel_context_reloading_on_secret_refresh_while_using_aws_secrets_manager_with_eventbridge_and_aws_sqs_services}

::: paragraph
Another option is to use AWS EventBridge in conjunction with the AWS SQS
service.
:::

::: paragraph
On the AWS side, the following resources need to be created:
:::

::: ulist
- an AWS CloudTrail trail

- an AWS SQS Queue

- an EventBridge rule of the following kind
:::

:::: listingblock
::: content
``` highlight
{
  "source": ["aws.secretsmanager"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["secretsmanager.amazonaws.com"]
  }
}
```
:::
::::

::: paragraph
This rule will make the event related to AWS Secrets Manager filtered
:::

::: ulist
- You need to set the a Rule target to the AWS SQS Queue for Eventbridge
  rule

- You need to give permission to the Eventbrige rule, to write on the
  above SQS Queue. For doing this you'll need to define a json file like
  this:
:::

:::: listingblock
::: content
``` highlight
{
    "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"<queue_arn>/SQSDefaultPolicy\",\"Statement\":[{\"Sid\": \"EventsToMyQueue\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"events.amazonaws.com\"}, \"Action\": \"sqs:SendMessage\", \"Resource\": \"<queue_arn>\", \"Condition\": {\"ArnEquals\": {\"aws:SourceArn\": \"<eventbridge_rule_arn>\"}}}]}"
}
```
:::
::::

::: paragraph
Change the values for queue_arn and eventbridge_rule_arn, save the file
with policy.json name and run the following command with AWS CLI
:::

:::: listingblock
::: content
``` highlight
aws sqs set-queue-attributes --queue-url <queue_url> --attributes file://policy.json
```
:::
::::

::: paragraph
where queue_url is the AWS SQS Queue URL of the just created Queue.
:::

::: paragraph
Now you should be able to set up the configuration on the Camel side. To
enable the SQS notification, add the following properties:
:::

:::: listingblock
::: content
``` highlight
camel.vault.aws.refreshEnabled=true
camel.vault.aws.refreshPeriod=60000
camel.vault.aws.secrets=Secret
camel.main.context-reload-enabled = true
camel.vault.aws.useSqsNotification=true
camel.vault.aws.sqsQueueUrl=<queue_url>
```
:::
::::

::: paragraph
where queue_url is the AWS SQS Queue URL of the just created Queue.
:::

::: paragraph
Whenever an event of PutSecretValue for the Secret named \'Secret\' will
happen, a message will be enqueued in the AWS SQS Queue and consumed on
the Camel side and a context reload will be triggered.
:::
:::::::::::::::::::::

::::::::::::::::::: sect3
#### Automatic Camel context reloading on Secret Refresh while using Google Secret Manager {#_automatic_camel_context_reloading_on_secret_refresh_while_using_google_secret_manager}

::: paragraph
Being able to reload Camel context on a Secret Refresh, could be done by
specifying the usual credentials (the same used for Google Secret
Manager Property Function).
:::

::: paragraph
With Environment variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_GCP_USE_DEFAULT_INSTANCE=true
export $CAMEL_VAULT_GCP_PROJECT_ID=projectId
```
:::
::::

::: paragraph
or as plain Camel main properties:
:::

:::: listingblock
::: content
``` highlight
camel.vault.gcp.useDefaultInstance = true
camel.vault.aws.projectId = projectId
```
:::
::::

::: paragraph
Or by specifying a path to a service account key file, instead of using
the default instance.
:::

::: paragraph
To enable the automatic refresh you'll need additional properties to
set:
:::

:::: listingblock
::: content
``` highlight
camel.vault.gcp.projectId= projectId
camel.vault.gcp.refreshEnabled=true
camel.vault.gcp.refreshPeriod=60000
camel.vault.gcp.secrets=hello*
camel.vault.gcp.subscriptionName=subscriptionName
camel.main.context-reload-enabled = true
```
:::
::::

::: paragraph
where `camel.vault.gcp.refreshEnabled` will enable the automatic context
reload, `camel.vault.gcp.refreshPeriod` is the interval of time between
two different checks for update events and `camel.vault.gcp.secrets` is
a regex representing the secrets we want to track for updates.
:::

::: paragraph
Note that `camel.vault.gcp.secrets` is not mandatory: if not specified
the task responsible for checking updates events will take into accounts
or the properties with an `gcp:` prefix.
:::

::: paragraph
The `camel.vault.gcp.subscriptionName` is the subscription name created
in relation to the Google PubSub topic associated with the tracked
secrets.
:::

::: paragraph
This mechanism makes use of the notification system related to Google
Secret Manager: through this feature, every secret could be associated
with one up to ten Google Pubsub Topics. These topics will receive
events related to the life cycle of the secret.
:::

::: paragraph
There are only two requirements: - Adding `camel-google-secret-manager`
JAR to your Camel application. - Give the service account used
permissions to do operation at secret management level (for example,
accessing the secret payload, or being admin of secret manager service
and also have permission over the Pubsub service)
:::
:::::::::::::::::::

::::::::::::::::::::::: sect3
#### Automatic Camel context reloading on Secret Refresh while using Azure Key Vault {#_automatic_camel_context_reloading_on_secret_refresh_while_using_azure_key_vault}

::: paragraph
Being able to reload Camel context on a Secret Refresh, could be done by
specifying the usual credentials (the same used for Azure Key Vault
Property Function).
:::

::: paragraph
With Environment variables:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AZURE_TENANT_ID=tenantId
export $CAMEL_VAULT_AZURE_CLIENT_ID=clientId
export $CAMEL_VAULT_AZURE_CLIENT_SECRET=clientSecret
export $CAMEL_VAULT_AZURE_VAULT_NAME=vaultName
```
:::
::::

::: paragraph
or as plain Camel main properties:
:::

:::: listingblock
::: content
``` highlight
camel.vault.azure.tenantId = accessKey
camel.vault.azure.clientId = clientId
camel.vault.azure.clientSecret = clientSecret
camel.vault.azure.vaultName = vaultName
```
:::
::::

::: paragraph
If you want to use Azure Identity with environment variables, you can do
in the following way:
:::

:::: listingblock
::: content
``` highlight
export $CAMEL_VAULT_AZURE_IDENTITY_ENABLED=true
export $CAMEL_VAULT_AZURE_VAULT_NAME=vaultName
```
:::
::::

::: paragraph
You can also enable the usage of Azure Identity in the
`application.properties` file such as:
:::

:::: listingblock
::: content
``` highlight
camel.vault.azure.azureIdentityEnabled = true
camel.vault.azure.vaultName = vaultName
```
:::
::::

::: paragraph
To enable the automatic refresh, you'll need additional properties to
set:
:::

:::: listingblock
::: content
``` highlight
camel.vault.azure.refreshEnabled=true
camel.vault.azure.refreshPeriod=60000
camel.vault.azure.secrets=Secret
camel.vault.azure.eventhubConnectionString=eventhub_conn_string
camel.vault.azure.blobAccountName=blob_account_name
camel.vault.azure.blobContainerName=blob_container_name
camel.vault.azure.blobAccessKey=blob_access_key
camel.main.context-reload-enabled = true
```
:::
::::

::: paragraph
where `camel.vault.azure.refreshEnabled` will enable the automatic
context reload, `camel.vault.azure.refreshPeriod` is the interval of
time between two different checks for update events and
`camel.vault.azure.secrets` is a regex representing the secrets we want
to track for updates.
:::

::: paragraph
where `camel.vault.azure.eventhubConnectionString` is the eventhub
connection string to get notification from,
`camel.vault.azure.blobAccountName`,
`camel.vault.azure.blobContainerName` and
`camel.vault.azure.blobAccessKey` are the Azure Storage Blob parameters
for the checkpoint store needed by Azure Eventhub.
:::

::: paragraph
Note that `camel.vault.azure.secrets` is not mandatory: if not specified
the task responsible for checking updates events will take into accounts
or the properties with an `azure:` prefix.
:::

::: paragraph
The only requirement is adding the camel-azure-key-vault jar to your
Camel application.
:::
:::::::::::::::::::::::

::::::::::::::::: sect3
#### Automatic Camel context reloading on Secret Refresh while using IBM Secrets Manager {#_automatic_camel_context_reloading_on_secret_refresh_while_using_ibm_secrets_manager}

::: paragraph
Being able to reload Camel context on a Secret Refresh could be done by
specifying the IBM Event Streams credentials combined with the IBM
Secrets Manager one (the same used for IBM Secrets Manager Property
Function).
:::

::: paragraph
With Environment variables:
:::

:::: listingblock
::: content
``` highlight
export CAMEL_VAULT_IBM_TOKEN=token
export CAMEL_VAULT_IBM_SERVICE_URL=serviceUrl
export CAMEL_VAULT_IBM_EVENTSTREAM_BOOTSTRAP_SERVERS=bootstrapServers
export CAMEL_VAULT_IBM_EVENTSTREAM_TOPIC=topic
export CAMEL_VAULT_IBM_EVENTSTREAM_USERNAME=token
export CAMEL_VAULT_IBM_EVENTSTREAM_PASSWORD=password
export CAMEL_VAULT_IBM_EVENTSTREAM_CONSUMER_GROUP_ID=groupId
export CAMEL_VAULT_IBM_EVENTSTREAM_CONSUMER_POLL_TIMEOUT=3000
```
:::
::::

::: paragraph
or as plain Camel main properties:
:::

:::: listingblock
::: content
``` highlight
camel.vault.ibm.token = token
camel.vault.ibm.serviceUrl = serviceUrl
camel.vault.ibm.eventStreamBootstrapServers = bootstrapServers
camel.vault.ibm.eventStreamTopic = topic
camel.vault.ibm.eventStreamUsername = token
camel.vault.ibm.eventStreamPassword = password
camel.vault.ibm.eventStreamGroupId = groupId
camel.vault.ibm.eventStreamConsumerPollTimeout=3000
```
:::
::::

::: paragraph
To enable the automatic refresh, you'll need additional properties to
set:
:::

:::: listingblock
::: content
``` highlight
camel.vault.ibm.refreshEnabled=true
camel.vault.ibm.secrets=Secret
camel.main.context-reload-enabled = true
```
:::
::::

::: paragraph
where `camel.vault.ibm.refreshEnabled` will enable the automatic context
reload and `camel.vault.ibm.secrets` is a regex representing the secrets
we want to track for updates.
:::

::: paragraph
where `camel.vault.ibm.eventStreamBootstrapServers` is the
comma-separated list of Bootstrap Servers for IBM Event Stream,
`camel.vault.ibm.eventStreamTopic`,
`camel.vault.ibm.eventStreamUsername`,
`camel.vault.ibm.eventStreamPassword`,
`camel.vault.ibm.eventStreamGroupId` and
`camel.vault.ibm.eventStreamConsumerPollTimeout` are the IBM Event
Stream parameters for connecting and consuming events related to
Secrets.
:::

::: paragraph
Note that `camel.vault.ibm.secrets` is not mandatory: if not specified
the task responsible for checking updates events will take into accounts
or the properties with an `ibm:` prefix.
:::

::: paragraph
The only requirement is adding the camel-ibm-secrets-manager jar to your
Camel application.
:::
:::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
