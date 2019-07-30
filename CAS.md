# CAS (Control Access Service)

## @CAS

The purpose of CAS Validations is to ensure that the client have access only to his own data.

CAS there have been considered 3 new annotations that will ensure that customers and contracts have the right attributes to operate, or in case of not handling sensitive information they can be annotated as IGNORE. These annotations are:

@CasCustomer - validates the client
@CasContract - validates contracts associated to the client previously signed
@Ignore - is used when enforcement is applied to 100% of services

For more details, please refer to [Service Architecture CAS Annotations][service-developer-manual-doc] guide.

### How to

The code example for these annotations for these examples can be found in the `cas_annotations` branch of [code labs repository][code-labs-repo].

#### @CasCustomer Example
Annotation to verify that it's a field with data that identifies the client
```java

@CasCustomer 
String userId 

```

#### @CasContract Example
Annotation to verify that it's a field that correspondes to a contract of the client
```java

@CasContract
String cardId

```

#### @CasIgnore Example
This annotation is used to avoid inspect the current service,  when enforcement is applied to 100% of services(in construction)
```java

@CasIgnore
public DtoCampaigns listCampaigns(){
}

```
These are the basic cases to apply CAS validations, for more details:
[dev-doc]: https://docs.google.com/document/d/18oF--nRNFU2YufhDnr9kPHsz3dl01BJ_IQ3yTwNtDsU/edit?usp=sharing
[cas-def]: https://docs.google.com/document/d/1Uh6Uptvd3-6D-vQsoQ6iZ_ZNwJmcGpTJ6xUlNxMZERk/edit?usp=sharing