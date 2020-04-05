# 第 4 章 身份管理模式
CHAPTER 4
Patterns for Identity Management
He allowed himself to be swayed by his conviction that human beings are not born once and for all on the day their mothers give birth to them, but that life obliges them over and over again to give birth to themselves.

Gabriel García Márquez, ‘Love in the Time of Cholera’

‘Who are you?’ said the Caterpillar. Alice replied, rather shyly, ‘I – I hardly know, Sir, just at present – at least I know who I was when I got up this morning, but I think I must have been changed several times since then’.

Lewis Carroll, ‘Alice in Wonderland’

4.1 Introduction
The development of software has recently changed significantly. Applications are typically distributed and built from a variety of components, which are themselves developed ad hoc, bought or outsourced. The context for which these applications are intended has also evolved: users have become mobile and access applications from diverse devices that are more vulnerable to theft, eavesdropping or other attacks. In addition, with the ubiquity of computing, users may need to access a wider range of applications, which may not be known to them in advance. The increasing importance of web or cloud services is another important factor. So in many cases there is a need for dynamic trust establishment and identity exchange protocols, and whatever security model is used must support these aspects.

A user may not be known in advance by the resource manager at the time of the request, and consequently their identity may need to be transmitted to the resource’s domain. Traditional models don’t address the dynamic aspects of identity management. Furthermore, the channels of communication between the participating entities are much more vulnerable than for example in operating system design, or within the boundaries of an organization’s computer network.

A decision to grant access must be based on the service’s access control rules and the user’s degree of trustworthiness. So far there is no accepted way to evaluate this degree of trustworthiness. One solution is to leverage the existing trust relationships between a user and the services that ‘know’ the user, and propagate identity information about the user to other services [Jos05]. A large amount of work has been done on the propagation of identity information [Bha05] [Mad05] [Rod]. In particular, web services standards have been published that deal with identity management and trust. However, currently web services standards tend to overlap, and how we can integrate them with other components to produce secure applications is not clear. Our patterns may contribute to making the implementation for trust simpler.

Figure 4.1 describes the relationships of the patterns described in this chapter, and associated patterns, using a pattern diagram. We use UML generalization to indicate patterns that are specializations of other patterns; for example, the SAML ASSERTION is a specialized type of CREDENTIAL. A security domain is a set of resources in which the administration of security is performed by a unique entity, which typically stores identity information about the subjects1 of the domain:

Figure 4.1: Pattern diagram for identity management


 The IDENTITY PROVIDER pattern centralizes the administration of a security domain’s users, creating and managing identities for their credentials.
 The CIRCLE OF TRUST pattern represents a federation of service providers that share trust relationships.
 The IDENTITY FEDERATION pattern allows the federation of multiple identities across multiple organizations under a common identity. In web services environments, this pattern relies on the SAML ASSERTION pattern, which provides a unifying format for communicating identity information between different security domains [Fer06d].
 CREDENTIALs carry subjects’ identities, and may also carry authorization and descriptive information for that subject.
 The LIBERTY ALLIANCE IDENTITY FEDERATION pattern describes a specific architecture for an identity federation.
 The Liberty Alliance PAOS Identity Service pattern further specializes that standard to apply it to wireless systems.
All these patterns are described in this chapter with the exception of CREDENTIAL (Chapter 5), SAML ASSERTION (Chapter 11) and Liberty Alliance PAOS Identity Service. The patterns in this chapter were published in [Del06] and [Fer06d]; Nelly Delessy and Maria M. Larrondo-Petrie were coauthors.

We consider CIRCLE OF TRUST, IDENTITY PROVIDER, IDENTITY FEDERATION and CREDENTIAL as abstract patterns, independent of any specific platform-oriented implementation (see Chapter 2 and [Fer08a]). LIBERTY ALLIANCE IDENTITY FEDERATION, Liberty Alliance PAOS Identity Service and SAML ASSERTION are concrete patterns that apply to web services. The CREDENTIAL pattern relates this diagram to the patterns in Chapter 5 (Authentication), where we describe how we use identities to validate the user access to a system.

4.2 Circle of Trust
The CIRCLE OF TRUST pattern allows the formation of trust relationships among service providers to allow their subjects to access an integrated and more secure environment.

EXAMPLE
Our university had different ways to access different services. For each service, one needed a different protocol and to remember a different password. This was cumbersome and prone to error; it was also insecure, because people would write their multiple passwords on their office bulletin boards.

CONTEXT
Service providers that provide services to consumers (subjects) over large systems such as the Internet.

PROBLEM
In such large open environments, subjects are typically registered (have accounts) with unrelated services. Subjects may have no relationships with many other services available in the open environment. It may be cumbersome for the subject to deal with multiple accounts, and it may not be secure to build new relationships with other services, since identity theft or violation of privacy can be performed by rogue services. How can we take advantage of relationships between service providers to avoid the inconvenience of multiple log-ins and to select trusted services?

The solution to this problem must resolve the following forces:

 Service providers are numerous on public networks. It can be cumbersome, indeed impossible, for each service provider to define relations with every other provider.
 The service providers’ infrastructures for their subjects’ login may be implemented using different technologies.
SOLUTION
Each service provider establishes business relationships with a set of other service providers. These relationships are materialized by the existence of operational and possibly business agreements between services. Such relationships are trust relationships, since each service provider expects the other to behave according to the operating agreements. Therefore, a circle of trust is a set of service providers that have business and operational relationships with each other – that is, that trust each other. Operational agreements could include information about whether they can exchange information about their subjects, for example, and how and what type of information can be exchanged. Business agreements could describe how to offer cross-promotions to their subjects. Service providers need to agree on operational processes before their users can benefit from the seamless environment.

STRUCTURE
Figure 4.2 shows a UML class diagram with an OCL constraint describing the structure of the solution. A CircleOfTrust includes several ServiceProviders, who trust each other, as described by the OCL expression.

Figure 4.2: Class diagram for the CIRCLE OF TRUST pattern


IMPLEMENTATION
Operational agreements should include a means to concretely enable trust, such as the sharing of a secret key or the secure distribution of certificates. The providers have to exchange credentials through some kind of external channel to trust and recognize each other.

EXAMPLE RESOLVED
Our university can now trust the identity providers who are members of our federation, so we do not have to worry that they will misuse our information.

CONSEQUENCES
The CIRCLE OF TRUST pattern offers the following benefits:

 The subjects can interact more securely with (potentially unknown) trusted services from the circle of trust.
 The services can provide a seamless environment to the subjects, and can exchange information about their users by using operating agreements that describe what common technologies to use.
Liabilities include keeping the participants synchronized in the presence of changes.

KNOWN USES
 Identity federation systems and models such as the LIBERTY ALLIANCE IDENTITY FEDERATION standard (page 44).
 PayPal has a variety of partners that trust their payment system, including Verifone and Equinox [Pay].
SEE ALSO
 IDENTITY PROVIDER (below) and IDENTITY FEDERATION (page 38).
 LIBERTY ALLIANCE IDENTITY FEDERATION (page 44).
 [Niza 10] formally discusses the CIRCLE OF TRUST.
 CREDENTIAL (page 62).
4.3 Identity Provider
The IDENTITY PROVIDER pattern allows the centralization of the administration of subjects’ identity information for a security domain.

EXAMPLE
Having applied the CIRCLE OF TRUST pattern, we can trust the identity providers who are members of our federation, but we still have a variety of identities.

CONTEXT
One or several resources, such as web services, CORBA services, applications and so on that are accessed by a predetermined set of subjects. The subjects and resources are typically from the same organization.

PROBLEM
Each application or service may implement its own code for managing subjects’ identity information, leading to an overloading of implementation and maintenance costs that may lead to inconsistencies across the organization’s units.

SOLUTION
The management of the subjects’ information for an organization is centralized in an IDENTITY PROVIDER, which is responsible for storing and propagating parts of the subjects’ information (that form their identity) to the applications and services that need it.

We define a security domain as the set of resources whose subjects’ identities are managed by the IDENTITY PROVIDER. Typically, the IDENTITY PROVIDER issues a set of credentials to each subject that will be verified by the accessed resources. Notice that the security domain is a special kind of CIRCLE OF TRUST within an organization.

STRUCTURE
Figure 4.3 shows a UML class diagram describing the structure of the solution. This pattern combines the CIRCLE OF TRUST, making explicit its Resources and specializing ServiceProvider to the IdentityProvider. Subjects are identified using CREDENTIALs given by a specific identity provider.

Figure 4.3: Class diagram for the IDENTITY PROVIDER pattern


EXAMPLE RESOLVED
Now that we can trust the identity providers who are members of our federation and have a centralized identity managed by a specific identity provider, we do not need multiple identities.

CONSEQUENCES
The IDENTITY PROVIDER pattern offers the following benefits:

 Maintenance costs are reduced
 The system is consistent in terms of its users
It also suffers from the following liability:
 The IDENTITY PROVIDER can be a bottleneck in the organization’s network.
KNOWN USES
 Identity management products, such as IBM Tivoli [IBMc], Sun One Identity Server [SunC], Netegrity’s SiteMinder and WSO2 [WSO].
 SAP NetWeaver Identity Management [SAP] manages user access to applications by providing a central mechanism for provisioning users in accordance with their current business roles. It also supports related processes, such as password management, self-service and approvals workflow.
 The Empower Identity Manager is oriented to cloud computing [emp]. These products use SAML 2.0 (Chapter 11).
SEE ALSO
IDENTITY FEDERATION (below) uses this pattern, as well as the CREDENTIAL (page 62) and CIRCLE OF TRUST (page 34) patterns.

4.4 Identity Federation
The IDENTITY FEDERATION pattern allows the formation of a dynamically created identity within an identity federation consisting of several service providers. Therefore, identity and security information about a subject can be transmitted in a transparent way for the user among service providers from different security domains.

EXAMPLE
Having a centralized identity is not much good unless we can use it in many places. We need some structure to allow this behavior.

CONTEXT
We have several security domains in a distributed environment that trust each other. A security domain is a set of resources (web services, applications, CORBA services and so on) in which administration of security is performed by a unique entity, which typically stores identity information about the subjects known to the domain. Subjects can perform actions in one or more security domains.

PROBLEM
There may be no relationship between some of the security domains accessed by a subject. Thus, subjects may have multiple unrelated identities within each security domain. Consequently, they may experience multiple and cumbersome registrations, authentications and other identity-related tasks prior to accessing the services they need.

How can we avoid the inconvenience of multiple registrations and authorizations across security domains? The solution to this problem must resolve the following forces:

 The identity of a user can be represented in a variety of ways in different domains.
 Parts of a subject’s identity within a security domain may include sensitive information that should not be disclosed to other security domains.
 The identity and security-related information in transit between two security domains should be kept confidential, so that eavesdropping, tampering or identity theft cannot be realized.
 A subject may want to access a security domain’s resources in an anonymous way.
SOLUTION
Service providers, which are normally part of a security domain in which the local identity of subjects is managed by an identity provider, form identity federations by developing offline operating agreements with other service providers from other security domains. In particular, they can agree about their privacy policies.

In a security domain, the local identity associated with a user consists of a set of attributes. Some of those attributes can be marked as confidential and should not be passed to other security domains. A federated identity is created gradually and transparently by gathering some of a subject’s attributes from its local identities within an identity federation. Therefore, identity and security information about a subject can be transmitted between service providers from the same identity federation transparently to the user. In particular, its authentication status can be propagated to perform single sign-on within the identity federation.

Figure 4.4 illustrates an example of how security domains and identity federations can coexist: a security domain is typically a circle of trust within an organization, whereas an identity federation is a circle of trust whose members can come from different organizations.

Figure 4.4: Federation and domains


STRUCTURE
Figure 4.5 shows a UML class diagram with an OCL constraint describing the structure of the solution. An IdentityFederation consists of a set of ServiceProviders which provide services to Subjects. A Subject has multiple LocalIdentities with some ServiceProviders. A LocalIdentity can be described as a set of Attributes of the Subject.

Figure 4.5: Class diagram for the IDENTITY FEDERATION pattern


A Subject can have several FederatedIdentities. This FederatedIdentity is composed of a union of attributes from the LocalIdentities of the IdentityFederation. An IdentityProvider is responsible for managing the LocalIdentities within a SecurityDomain, and can authenticate any Subjects on behalf of any ServiceProvider of the IdentityFederation. A Subject has been issued a set of Credentials that collect information about its authentication status and its identity within a SecurityDomain.

DYNAMICS
We illustrate the dynamic aspects of the IDENTITY FEDERATION pattern by showing sequence diagrams for two use cases: ‘Federate two local identities’ (Figure 4.6) and ‘Single sign on’ (Figure 4.7). The first use case describes how a local entity invites another to join, and their mutual authentication. The second use case shows how a subject, after receiving a credential from an identity provider, uses it to access a new domain.

Figure 4.6: Sequence diagram for the use case ‘Federate two local identities’


Figure 4.7: Sequence diagram for the use case ‘Single sign on’


IMPLEMENTATION
The identity federation can be structured hierarchically or in a peer-to-peer manner. The most basic federation could be based on bilateral agreements between two service providers. In the LIBERTY ALLIANCE IDENTITY FEDERATION pattern (page 44), an IdentityFederation has an IdentityProvider responsible for managing the federated identity, whereas Shibboleth [Shi] defines a club as a set of service providers that has reached some operating agreements.

In the LIBERTY ALLIANCE IDENTITY FEDERATION model, the identity provider proposes incentives for other service providers to affiliate with them and federate their local identities. Furthermore, no attribute can be classified as private: privacy is achieved by letting the user provide their consent each time its identity is federated.

EXAMPLE RESOLVED
Having implemented an IDENTITY FEDERATION, we can visit different services using the same identity for all of them.

CONSEQUENCES
The IDENTITY FEDERATION pattern offers the following benefits:

 Subjects can access resources within the identity federation in a seamless and secure way without reauthenticating in each new domain.
 Many different representations of the identity of a user can be consolidated under the same federated identity.
 Subjects can classify some of their attributes as private. Therefore, an identity provider can identify which attributes it should transmit to other parties and which it should not.
 Parts of the security credentials issued about a subject can be encrypted, so that the subject’s privacy can be protected.
 The security credential can be signed, so that its integrity and authenticity is protected and attackers cannot forge security tokens or change some of the subject’s attributes.
 A subject can access a security domain’s resources in an anonymous way, since not all attributes from a local identity (such as a name) are required to be federated.
The pattern also has some potential liabilities:

 Service providers need to have some kind of agreement before their identities can be federated. They have to exchange credentials through some external channel to trust and recognize each other.
 Even when a subject’s sensitive information is classified as private, a security domain can still disclose the subject’s private information secretly to other parties, thus violating the subject’s privacy.
 A security token can be stolen and presented by an attacker, resulting in identity theft. This is alleviated by the use of expiration dates and unique IDs for credentials.
 In spite of an expiration date and the unique ID feature of a credential, which guarantees its freshness, the unconditional revocation of a credential is not addressed in this solution.
KNOWN USES
 WS-Federation [Aja 13] is a proposed standard allowing web services to federate their identities.
 Liberty Alliance is a standard that allows services to federate into Identity Federations [Liba].
 Microsoft Account (previously Microsoft Wallet, Microsoft Passport, .NET Passport, Microsoft Passport Network and Windows Live ID) is an identity federation that lets users log in to a variety of web sites using only one account [MAJ].
 Shibboleth is an open solution for realizing identity federation among enterprises [Shi].
SEE ALSO
 LIBERTY ALLIANCE IDENTITY FEDERATION (below) is a specialization of this pattern.
 CREDENTIAL (page 62).
 A Single Sign On pattern is shown in [Ste05].
 A formal description of identity management patterns is given in [Niza 10].
4.5 Liberty Alliance Identity Federation
The LIBERTY ALLIANCE IDENTITY FEDERATION pattern allows merging of identities across multiple organizations under a federated identity and following a common set of rules.

EXAMPLE
Now that we have centralized identities, we can use them in different places. However, our federation uses a variety of providers, following different standards, and sometimes it is complex to visit some places. We would like to simplify this sharing of resources.

CONTEXT
Service providers, such as financial institutions, entertainment companies, Internet portals and so on, that offer services to users. A user typically has accounts with several service providers. Those providers manage a set of attributes of the user, such as a name, date of birth, social security number, preferences and others, that constitute the user’s identity. Web services have identities that can be used to access other services.

PROBLEM
There may not be any relationship between service providers, thus users may have multiple unrelated accounts. Consequently, they may experience multiple and cumbersome registrations, authentications and other identity-related tasks prior to accessing the services they need on the Internet.

How can we leverage a federated identity of a user on the Internet? The solution to this problem is affected by the forces of IDENTITY FEDERATION (page 39), and by the following additional forces:

 The identity of a user can be represented in a variety of ways by different services.
 Parts of the user’s information may need to be kept confidential and not shown to some service providers.
SOLUTION
Service providers manage local accounts for their clients. They form identity federations by developing offline operating agreements. Among those service providers comprising an identity federation, at least one acts as an identity provider, which is responsible for managing a federated identity. A federated identity is the composite of several local identities. Therefore, identity information about a user or service can be transmitted between service providers in a way that is transparent to the subjects. In particular, their authentication status can be propagated to perform single sign-on within the identity federation. The subject has to provide their consent so that each of their local identities can be federated.

STRUCTURE
Figure 4.8 illustrates the solution. An IdentityFederation consists of a set of ServiceProviders which provide services to Subjects. At least one of the ServiceProviders acts as the IdentityProvider, with which other ServiceProviders affiliate. A Subject has multiple LocalIdentities with some ServiceProviders. A LocalIdentity can be described as a set of Attributes of the Subject.

Figure 4.8: Class diagram for the LIBERTY ALLIANCE IDENTITY FEDERATION pattern


This Subject can have several FederatedIdentities. Each FederatedIdentity is composed of several LocalIdentities. The IdentityProvider is responsible for managing the FederatedIdentities, and can authenticate any Subject on behalf of any ServiceProvider of the IdentityFederation. A Subject has a set of SAMLAuthenticationAssertions (Chapter 11) that collect information about its authentication status with a ServiceProvider.

DYNAMICS
We illustrate the dynamic aspects of the LIBERTY ALLIANCE IDENTITY FEDERATION pattern with sequence diagrams for two use cases: ‘Federate two local identities’ and ‘Single sign on’.

Use Case: Federate Two Local Identities – Figure 4.9
Figure 4.9: Sequence diagram for the use case ‘Federate two local identities’


Summary	A Subject accesses a ServiceProvider and is asked to allow the federation of its local identities between this ServiceProvider and one of the IdentityProvider.
Actors	Subject, ServiceProvider, IdentityProvider.
Preconditions	The IdentityProvider and the ServiceProvider are parts of a common identity federation. The Subject has previously agreed to let the IdentityProvider manage its federated identity. The Subject has been authenticated with the IdentityProvider, which has issued a SAMLAuthenticationAssertion.
Description	1 A Subject sends a request to a ServiceProvider.
 	2 The ServiceProvider accesses the SAMLAuthenticationAssertion previously created by the IdentityProvider, detects that the Subject has been authenticated with the IdentityProvider, and retrieves its IdentityProviderLocalID.
 	3 The ServiceProvider requests its consent for federating the two local identities of the Subject.
 	4 The Subject consents to federate these local identities, and is requested to authenticate with the ServiceProvider using its local identity.
 	5 The ServiceProvider is now able to retrieve the Subject’s local identity.
 	6 The ServiceProvider transmits this identity to the IdentityProvider along with the IdentityProviderLocalID for the Subject.
 	7 The IdentityProvider retrieves the identity corresponding to the IdentityProviderLocalID.
 	8 The IdentityProvider creates a FederatedIdentity and inserts the attributes from both the retrieved LocalIdentity and the Identity received from the ServiceProvider.
 	9 The ServiceProvider transmits its response to the Subject.
Alternate flows	If the Subject does not consent to federate the two identities, the user authenticates with the ServiceProvider as usual and no identity information is exchanged.
Postcondition	A FederatedIdentity, that is the union of the attributes from the two identities, has been created.
Use Case: Single Sign On – Figure 4.10
Figure 4.10: Sequence diagram for the use case ‘Single sign on’


Summary	A Subject requests access to a ServiceProvider. If the Subject has not been authenticated by an IdentityProvider that is trusted by the ServiceProvider, the ServiceProvider redirects the request to an IdentityProvider that can authenticate the Subject and redirect it to the ServiceProvider.
Actors	Subject, ServiceProvider, IdentityProvider.
Precondition	The Subject has previously established identity federation between the IdentityProvider and the ServiceProvider.
Description	1 A Subject sends a request to a ServiceProvider.
 	2 The ServiceProvider checks if any SAMLAuthenticationAssertions have been previously created by an IdentityProvider that it trusts.
 	3 If the Subject has not been authenticated yet, the ServiceProvider redirects the Subject towards IdentityProviders that it trusts, so that the Subject can authenticate itself.
 	4 The Subject chooses an IdentityProvider and authenticates itself.
 	5 The IdentityProvider creates and transfers the Subject’s SAMLAuthenticationAssertion in an authentication response.
 	6 The Subject transparently resends a request to the ServiceProvider, this time including its SAMLAuthenticationAssertion.
 	7 The ServiceProvider can verify that the Subject has been authenticated and sends its response back to the Subject.
Postcondition	The access to the service offered by the ServiceProvider has been controlled based on the identity provided by the IdentityProvider. If the Subject has already been authenticated by the IdentityProvider, it does not need to reauthenticate.
EXAMPLE RESOLVED
We decided to join the Liberty Alliance Federation. Now all our services are uniform and can be accessed with the same credentials.

CONSEQUENCES
The LIBERTY ALLIANCE IDENTITY FEDERATION pattern offers the following benefits:

 Subjects can perform transactions within the identity federation in a seamless and secure way.
 Many different representations of the identity of a user can be consolidated under the same federated identity.
 There is no need to reauthenticate in each new domain.
 Subjects can control whether or not a local identity can be federated. Thus parts of the user’s information may be kept confidential.
The pattern also has the following liabilities:

 Service providers need to have some kind of agreement before their accounts can be federated. They have to exchange credentials through some external channel to be able to trust and recognize each other.
 Both the subjects and the service providers from the identity federation need to trust the identity provider.
KNOWN USES
 Nokia provides a framework for wireless web services based on the LIBERTY ALLIANCE IDENTITY FEDERATION pattern (Nokia web services framework).
 Several vendors use the LIBERTY ALLIANCE IDENTITY FEDERATION pattern in their access management products: IBM Tivoli Federated Identity Manager [IBMc], Sun Java System Access Manager [SunC].
SEE ALSO
 SAML ASSERTION (page 279) is the fundamental building block for implementing the LIBERTY ALLIANCE IDENTITY FEDERATION pattern [Liba].
 The AUTHENTICATOR pattern (page 52) is used between the subject and the Identity Provider [Sch06b].
1A subject is an active system component that is able to request resources.