::: {#header}
# JSSE Utility
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The JSSE Utility allows you to easily configure aspects of the [Java
Secure Socket
Extension](https://docs.oracle.com/en/java/javase/11/security/java-secure-socket-extension-jsse-reference-guide.html)
(JSSE) API in order to greatly simplify the use of custom transport
layer security (TLS) settings on Camel components.
:::
::::
:::::

:::::: sect1
## Supported JSSE Components {#_supported_jsse_components}

::::: sectionbody
::: paragraph
A number of Camel components support this (such as but not limited to):
:::

::: ulist
- [Cometd](components::cometd-component.html)

- [HTTP](components::http-component.html)

- [IRC](components::irc-component.html)

- [Jetty](components::jetty-component.html)

- [Netty](components::netty-component.html)

- [Mail](components::mail-component.html)
:::
:::::
::::::

::::::::::::::::::::::::::::::::::::: sect1
## Configuring JSSE with Camel {#_configuring_jsse_with_camel}

:::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The key component in configuring TLS through the JSSE API is the
`SSLContext`.
:::

::: paragraph
The `SSLContext` provides socket factories for both client side and
server side sockets as well as another component called an `SSLEngine`
that is used by non-blocking IO to support TLS.
:::

::: paragraph
The JSSE configuration utility provides an easy-to-use builder for
configuring these JSSE components, among others, in a manner that allows
you to provide all configuration options up front during the
initialization of your application such that you don't have to customize
library code or dig though the inner workings of a third-party library
in order to inject hooks for the configuration of each component in the
JSSE API. The central builder in the JSSE configuration utility is the
SSLContextParameters. This class serves as the entry point for most
configuration in the JSSE utility.
:::

::::::::::: sect2
### SSLContextParameters {#_sslcontextparameters}

::: paragraph
`SSLContextParameters` (`<sslContextParameters>` XML tag) contain the
following elements:
:::

::: ulist
- provider (attribute)

- secureSocketProtocol (attribute)

- certAlias (attribute)

- sessionTimeout (attribute)

- cipherSuites (element)

- cipherSuitesFilter (element)

- secureSocketProtocols (element)

- secureSocketProtocolsFilter (element)

- keyManagers (element)

- trustManagers (element)

- secureRandom (element)

- clientParameters (element)

- serverParameters (element)

  ::: dlist

  provider

  :   The optional provider identifier for the JSSE implementation to
      use when constructing the SSLContext. If omitted, the standard
      provider look-up mechanism is used to resolve the provider.

  secureSocketProtocol

  :   The optional secure socket protocol. See [Java Security Standard
      Algorithm
      Names](https://docs.oracle.com/en/java/javase/11/docs/specs/security/standard-names.html)
      for information about standard protocol names. If omitted, TLS is
      used by default. Note that this property is related to but
      distinctly different from the secureSocketProtocols and
      secureSocketProtocolsFilter properties.

  certAlias

  :   An optional certificate alias to use. This is useful when the
      keystore has multiple certificates.

  sessionTimeout

  :   This optional property defines the timeout period, in seconds, for
      sessions on both the client and server side as well as in the
      SSLEngine.

  cipherSuites

  :   This optional property represents a collection of explicitly named
      cipher suites to enable on both the client and server side as well
      as in the SSLEngine. These values take precedence over filters
      supplied in cipherSuitesFilter. The utility attempts to enable the
      listed cipher suites regardless of whether the JSSE provider
      actually supports them or not. This behavior guarantees that
      listed cipher suites are always enabled when listed. For a more
      lenient option, use cipherSuitesFilter.

  cipherSuitesFilter

  :   This optional property represents a collection of include and
      exclude patterns for cipher suites to enable on both the client
      and server side as well as in the SSLEngine. The patterns are
      applied over only the available cipher suites. The excludes
      patterns have precedence over the includes patterns. If no
      cipherSuites and no cipherSuitesFilter are present, the default
      patterns applied are:
  :::
:::

:::: listingblock
::: content
``` highlight
Includes .\*;
Excludes .*_NULL_.*, .\*_anon_.*, .\*DES.*, .\*EXPORT.*, .\*MD5, .*RC4.*
```
:::
::::

::: dlist

secureSocketProtocols

:   This optional property represents a collection of explicitly named
    secure socket protocols, such as SSLv3/TLS/etc., to enable on both
    the client and server side as well as in the SSLEngine. These values
    take precedence over filters supplied in
    secureSocketProtocolsFilter. The utility attempts to enable the
    listed protocols regardless of whether the JSSE provider actually
    supports them or not. This behavior guarantees that listed protocols
    are always enabled when listed. For a more lenient option, use
    secureSocketProtocolsFilter.

secureSocketProtocolsFilter

:   This optional property represents a collection of include and
    exclude patterns for secure socket protocols to enable on both the
    client and server side as well as in the SSLEngine. The patterns are
    applied over only the available protocols. The excludes patterns
    have precedence over the includes patterns. If no
    secureSocketProtocols and no secureSocketProtocolsFilter are
    present, the default patterns applied are:
:::

:::: listingblock
::: content
``` highlight
Includes .*
```
:::
::::

::: dlist

keyManagers

:   This optional property configures the source of key material for
    providing identity of client and server side connections as well as
    in the SSLEngine. If omitted, no source of key material is provided
    and the SSLContext is suitable only for client-side usage when
    mutual authentication is not in use. You typically configure this
    property with a key store containing a client or server private key.

trustManagers

:   This optional property configures the source of material for
    verifying trust of key material used in the handshake process. If
    omitted, the default trust manager is automatically used. See the
    [JSSE
    documentation](https://docs.oracle.com/en/java/javase/11/security/java-secure-socket-extension-jsse-reference-guide.html#GUID-7D9F43B8-AABF-4C5B-93E6-3AFB18B66150)
    for more information on how the default trust manager is configured.
    You typically configure this property with a key store containing
    trusted CA certificates.

secureRandom

:   This optional property configures the secure random number generator
    used by the client and server side as well as in the SSLEngine. If
    omitted, the default secure random number generator is used.

clientParameters

:   This optional property configures additional settings that apply
    only to the client side aspects of the SSLContext. If present, these
    settings override the settings specified at the SSLContextParameters
    level.

serverParameters

:   This optional property configures additional settings that apply
    only to the server side aspects of the SSLContext. If present, these
    settings override the settings specified at the SSLContextParameters
    level.
:::
:::::::::::

::::: sect2
### KeyManagersParameters {#_keymanagersparameters}

::: paragraph
KeyManagersParameters contain the following elements:
:::

::: ulist
- keyPassword (attribute)

- provider (attribute)

- algorithm (attribute)

- keyStore (element)

  ::: dlist

  keyPassword

  :   The optional password for recovering/accessing the private key in
      the key store. This is typically the password for the private key
      in the configured key store; however, in some cases, such as when
      using PKCS#11, the key password may be provided through other
      means and is omitted entirely in this configuration.

  provider

  :   The optional provider identifier for the KeyManagerFactory used to
      create the KeyManagers represented by this object's configuration.
      If omitted, the default look-up behavior is used.

  algorithm

  :   The optional algorithm name for the KeyManagerFactory used to
      create the KeyManager represented by this object's configuration.
      See the [Java Secure Socket Extension Reference
      Guide](https://docs.oracle.com/en/java/javase/11/security/java-secure-socket-extension-jsse-reference-guide.html)
      for information about standard algorithm names.

  keyStore

  :   This optional property represents the key store that provides key
      material to the key manager. This is typically configured with a
      key store containing a user or server private key. In some cases,
      such as when using PKCS#11, the key store is omitted entirely.
  :::
:::
:::::

::::: sect2
### TrustManagersParameters {#_trustmanagersparameters}

::: paragraph
TrustManagersParameters contain the following elements:
:::

::: ulist
- provider (attribute)

- algorithm (attribute)

- trustManager (attribute)

- keyStore (element)

  ::: dlist

  provider

  :   The optional provider identifier for the TrustManagerFactory used
      to create the TrustManagers represented by this object's
      configuration. If omitted, the default look-up behavior is used.

  algorithm

  :   The optional algorithm name for the TrustManagerFactory used to
      create the TrustManager represented by this object's
      configuration. See the [Java Secure Socket Extension Reference
      Guide](https://docs.oracle.com/en/java/javase/11/security/java-secure-socket-extension-jsse-reference-guide.html)
      for information about standard algorithm names.

  trustManager

  :   To use an existing configured trust manager instead of using
      TrustManagerFactory to get the TrustManager.

  keyStore

  :   See
      [KeyStoreParameters](#CamelConfigurationUtilities-KeyStoreParameters).
      This optional property represents the key store that provides key
      material to the trust manager. This is typically configured with a
      key store containing trusted CA certificates / public keys. In
      some cases, such as when using PKCS#11, the key store is omitted
      entirely.
  :::
:::
:::::

::::: sect2
### KeyStoreParameters {#CamelConfigurationUtilities-KeyStoreParameters}

::: paragraph
KeyStoreParameters contain the following elements:
:::

::: ulist
- type (attribute)

- password (attribute)

- provider (attribute)

- resource (attribute)

  ::: dlist

  type

  :   The optional type of the key store. See the [Java Security
      Standard Algorithm
      Names](https://docs.oracle.com/en/java/javase/11/docs/specs/security/standard-names.html)
      for more information on standard names. If omitted, defaults to
      the default lookup mechanism as defined by
      `KeyStore.getDefaultType()`

  password

  :   The optional password for reading/opening/verifying the key store.

  provider

  :   The optional provider identifier for the provider used to create
      the KeyStores represented by this object's configuration. If
      omitted, the default look-up behavior is used.

  resource

  :   Optional property to load the key store resource from a uri. By
      default the class path is used, otherwise the uri needs to be
      specified. For example to use file path as a resource, the prefix
      file is needed (file:/path/to/keystore). In some cases, the
      resource is omitted as the key store is provided by other means.
  :::
:::
:::::

::::: sect2
### FilterParameters {#_filterparameters}

::: paragraph
FilterParameters contain the following elements:
:::

::: ulist
- include (element, multiple string)

- exclude (element, multiple string)

  ::: dlist

  include

  :   This optional property represents zero or more regular expression
      patterns for which matching values should be included. The list of
      excludes takes precedence over the includes patterns.

  exclude

  :   This optional property represents zero or more regular expression
      patterns for which matching values should be included. The list of
      excludes takes precedence over the includes patterns.
  :::
:::
:::::

::::: sect2
### SecureRandomParameters {#_securerandomparameters}

::: paragraph
SecureRandomParameters contain the following elements:
:::

::: ulist
- algorithm (attribute)

- provider (attribute)

  ::: dlist

  algorithm

  :   This optional property represents the Random Number Generator
      (RNG) algorithm identifier for the SecureRandom factory method
      used to create the SecureRandom represented by this object's
      configuration. See [Java Security Standard Algorithm
      Names](https://docs.oracle.com/en/java/javase/11/docs/specs/security/standard-names.html)
      for information about standard RNG algorithm names.

  provider

  :   The optional provider identifier for the SecureRandom factory
      method used to create the SecureRandom represented by this
      object's configuration. If omitted, the default look-up behavior
      is used.
  :::
:::
:::::

::::: sect2
### SSLContextServerParameters {#_sslcontextserverparameters}

::: paragraph
SSLContextServerParameters contain the following elements:
:::

::: ulist
- clientAuthentication (attribute)

- sessionTimeout (attribute)

- cipherSuites (element)

- cipherSuitesFilter (element)

- secureSocketProtocols (element)

- secureSocketProtocolsFilter (element)

  ::: dlist

  clientAuthentication

  :   This optional property indicates if the server side does not
      request, requests, or requires clients to provide authentication
      credentials during the handshake process. This is commonly
      referred to as mutual authentication, two direction SSL/TLS, or
      two-legged SSL/TLS. Valid values are: NONE, WANT, REQUIRE

  sessionTimeout

  :   This optional property defines the timeout period, in seconds, for
      sessions on the server side. This setting affects both the
      SSLServerSocketFactory/SSLServerSocket and the server side of the
      SSLEngine.

  cipherSuites

  :   This optional property overrides the value of this setting in the
      SSLContextParameters. This option has no effect on the SSLEngine
      configuration.

  cipherSuitesFilter

  :   This optional property overrides the value of this setting in the
      SSLContextParameters. This option has no effect on the SSLEngine
      configuration.

  secureSocketProtocols

  :   This optional property overrides the value of this setting in the
      SSLContextParameters. This option has no effect on the SSLEngine
      configuration.

  secureSocketProtocolsFilter

  :   This optional property overrides the value of this setting in the
      SSLContextParameters. This option has no effect on the SSLEngine
      configuration.
  :::
:::
:::::

::::: sect2
### SSLContextClientParameters {#_sslcontextclientparameters}

::: paragraph
SSLContextClientParameters contains the following elements:
:::

::: ulist
- sniHostNames (elements)

- sessionTimeout (attribute)

- cipherSuites (element)

- cipherSuitesFilter (element)

- secureSocketProtocols (element)

- secureSocketProtocolsFilter (element)

  ::: dlist

  sniHostNames

  :   Contains a list of sniHostName elements which provides a list of
      SNIHostNames to be used for SSL.

  sessionTimeout

  :   See above

  cipherSuites

  :   See above

  cipherSuitesFilter

  :   See above

  secureSocketProtocols

  :   See above

  secureSocketProtocolsFilter

  :   See above
  :::
:::
:::::
::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::

:::::::: sect1
## Examples {#_examples}

::::::: sectionbody
:::::: sect2
### Setting Client Authentication on the Server Side {#_setting_client_authentication_on_the_server_side}

::: paragraph
This configuration sets the server side aspects of the TLS configuration
to require client authentication during the handshake process. This
configuration uses the default trust store and a custom key store to
provide key material for both the server and client sides of the
SSLContext.
:::

:::: listingblock
::: content
``` highlight
KeyStoreParameters ksp = new KeyStoreParameters();
ksp.setResource("file:/users/home/server/keystore.jks");
ksp.setPassword("keystorePassword");

KeyManagersParameters kmp = new KeyManagersParameters();
kmp.setKeyStore(ksp);
kmp.setKeyPassword("keyPassword");

SSLContextServerParameters scsp = new SSLContextServerParameters();
scsp.setClientAuthentication(ClientAuthentication.REQUIRE);
SSLContextParameters scp = new SSLContextParameters();
scp.setServerParameters(scsp);
scp.setKeyManagers(kmp);

SSLContext context = scp.createSSLContext();
SSLEngine engine = scp.createSSLEngine();
```
:::
::::
::::::
:::::::
::::::::

::::::::: sect1
## Configuring Different Options on the Client and Server Side {#_configuring_different_options_on_the_client_and_server_side}

:::::::: sectionbody
::: paragraph
In this example, both the client and server sides share the same custom
key store; however, the client side allows any supported cipher suite
while the server side will use the default cipher suite filter and
exclude any cipher suites that match the patterns:
:::

:::: listingblock
::: content
``` highlight
.*_NULL_.*
.*_anon_.*
```
:::
::::

:::: listingblock
::: content
``` highlight
KeyStoreParameters ksp = new KeyStoreParameters();
ksp.setResource("file:/users/home/server/keystore.jks");
ksp.setPassword("keystorePassword");

KeyManagersParameters kmp = new KeyManagersParameters();
kmp.setKeyStore(ksp);
kmp.setKeyPassword("keyPassword");

FilterParameters filter = new FilterParameters();
filter.getInclude().add(".*");

SSLContextClientParameters sccp = new SSLContextClientParameters();
sccp.setCipherSuitesFilter(filter);

SSLContextParameters scp = new SSLContextParameters();
scp.setClientParameters(sccp);
scp.setKeyManagers(kmp);

SSLContext context = scp.createSSLContext();
SSLEngine engine = scp.createSSLEngine();
```
:::
::::
::::::::
:::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
