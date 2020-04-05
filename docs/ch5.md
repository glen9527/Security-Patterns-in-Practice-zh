# 第 5 章 身份验证模式
> CHAPTER 5 Patterns for Authentication

This above all; to thine own self be true, and it must follow, as the night the day, thou canst not then be false to any man.

William Shakespeare, ‘Hamlet’

5.1 Introduction
The previous chapter discussed how users are identified in a system. Before they can perform any activities, both users and other systems must identify themselves and be authenticated – that is, prove to the system that they are who they say they are.

Identification and authentication (I&A) uses some kind of protocol to establish identity. I&A is the basis for authorization and for logging: it provides accountability. Once identity is verified, the system may provide a proof of authentication to avoid further authentications.

Figure 5.1 shows how the patterns described in this chapter are interrelated. Once a subject (a user or a system) has identified themselves to the system, we need to verify that their identity is correct. This is the function of the authentication function. AUTHENTICATOR is an abstract pattern, and we show here two concrete versions: REMOTE AUTHENTICATOR/AUTHORIZER and CREDENTIAL. CREDENTIALs may have also authorization properties, discussed later. In distributed systems where users may have access to several systems a Single Sign On service is very convenient1. REMOTE AUTHENTICATOR/AUTHORIZER and CREDENTIAL have dual purposes; they can also be used for authorization if they include user rights. Chapter 6 describes how to let users access specific resources once they have been authenticated.

Figure 5.1: Relationships between the patterns in this chapter


AUTHENTICATOR was first published in [Fer03b] and is joint work with John Sinibaldi. Remote Authenticator was first described in [War03a], and was written with Reghu Warrier. CREDENTIAL is joint work with Patrick Morrison [Mor06a].

5.2 Authenticator
When a subject identifies itself to the system, the AUTHENTICATOR pattern allows verification that the subject intending to access the system is who or what it claims to be.

EXAMPLE
The computer system at Melmac State University has legitimate users who use it to host their files. However, there is no way to ensure that a user who is logged in is a legitimate user. When Melmac was a small school and everybody knew everybody, this was acceptable and convenient. Now the school is bigger and there are many students, faculty members and employees who use the system. Some incidents have occurred in which users impersonated others and gained illegal access to their files.

CONTEXT
Computer systems that contain resources that may be valuable because they include information about business plans, user medical records and so on. We only want subjects that have some reason to gain access to our system to be able to do so.

PROBLEM
How can we prevent imposters from accessing our system? A malicious attacker could try to impersonate a legitimate user to gain access to their resources. This could be particularly serious if the user impersonated has a high level of privilege. How do we verify that a user intending to access the system is legitimate?

The solution to this problem must resolve the following forces:

 Flexibility. A variety of users require access to the system and a variety of system units exist with differently sensitive assets. We need to be able to handle all this variety appropriately, or we risk security exposures.
 Dependability. We need to authenticate users in a reliable and secure way. This means using a robust protocol and a way to protect the results of authentication. Otherwise, users may skip authentication, or illegally modify its results, exposing the system to security violations.
 Cost. There are trade-offs between security and cost: more secure systems are usually more expensive.
 Performance. If authentication needs to be performed frequently, performance may become an issue.
 Frequency. We should not make subjects authenticate frequently.
SOLUTION
Use a single point of access to receive the interactions of a subject with the system and apply a protocol to verify the identity of the subject. The protocol used may be simple or complex, depending on the needs of the application.

STRUCTURE
Figure 5.2 shows the class diagram for this pattern. A Subject requests access to the system. The Authenticator receives this request and applies a protocol using some AuthenticationInformation. If the authentication is successful, the Authenticator creates a ProofOfIdentity, which is assigned to the subject to indicate that they are legitimate.

Figure 5.2: Class diagram for the AUTHENTICATOR pattern


DYNAMICS
Figure 5.3 shows the dynamics of the authentication process. A subject User requests access to the system through the Authenticator. The Authenticator applies some authentication protocol, for example by verifying some information presented by the subject, and as a result a ProofOfIdentity is created and assigned to the subject.

Figure 5.3: Sequence diagram for the use case ‘Authenticate subject’


IMPLEMENTATION
In centralized systems, the operating system controls the creation of a session in response to the request by a subject, typically a user. The authenticated user (represented by processes running on their behalf) is then allowed to access resources according to their rights.

Some database systems have their own authentication systems, even when running on top of an operating system. There are many ways to implement authentication protocols, described in specific authentication patterns, for example in REMOTE AUTHENTICATOR/AUTHORIZER (page 56). Sensitive resource access, for example data in financial systems, requires more elaborate process authentication than systems that handle lower-value assets. Distributed systems may implement this function in specialized servers.

Patterns for selecting authentication approaches are described in [Sch06b].

EXAMPLE RESOLVED
Melmac State University implemented an authentication system and now only legitimate users can access their system. Illegal access to files has stopped.

CONSEQUENCES
The AUTHENTICATOR pattern offers the following benefits:

 Flexibility. Depending on the protocol and the authentication information used, we can handle any types of users and we can authenticate them in diverse ways.
 Dependability. Since the authentication information is separated, we can store it in a protected area, to which all subjects may have at most read-only access.
 Cost. We can use a variety of algorithms and protocols of different strength for authentication. The selection depends on the security and cost trade-offs. Three varieties include something the user knows (passwords), something the user has (ID cards), or something the user is (biometrics).
 Authentication can be performed in centralized or distributed environments.
 Performance. We can produce a proof of identity to be used in lieu of further authentication. This improves performance.
 Frequency. Using a token means that we do not need to authenticate users.
The pattern also has the following potential liabilities:

 The authentication process takes some time: the overhead depends on the protocol used.
 The general complexity and cost of the system increase with the level of security.
 If the protocol is complex, users waste time and get annoyed.
VARIANTS
Single Sign On. Single Sign On (SSO) is a process whereby a subject verifies their identity and the results of this verification can be used across several domains and for a given amount of time [Kin01]. The result of the authentication is an authentication token, used to qualify all future accesses by the user.

KNOWN USES
 Commercial operating systems use some form of authentication, typically passwords, to authenticate their users.
 RADIUS (Remote Authentication Dial-In User Service) provides a centralized authentication service for network and distributed systems [Gar02] [Has02] – see REMOTE AUTHENTICATOR/AUTHORIZER below.
 The SSL authentication protocol uses a PKI arrangement for authentication (Chapter 10).
 SAML, a web services standard for security, defines one of its main uses as a way to implement authentication in web services (Chapter 11).
 E-commerce sites such as eBay and Amazon.
SEE ALSO
 Distributed Authenticator [Bro99] discusses an approach to authentication in distributed systems.
 The Distributed Filtering and Access Control framework includes authentication [Hay00].
 REMOTE AUTHENTICATOR/AUTHORIZER (see below) provides facilities for authentication and authorization when accessing shared resources in a loosely-coupled distributed system.
 CREDENTIAL (page 62) provides a secure means of recording authentication and authorization information for use in distributed systems.
 Single Access Point and Check Point. See [Sch06b] [Yod97].
5.3 Remote Authenticator/Authorizer
The REMOTE AUTHENTICATOR/AUTHORIZER pattern provides facilities for authentication and authorization when accessing shared resources in a loosely-coupled distributed system.

EXAMPLE
A multinational corporation may have employees in more than one country, say in the US and Brazil. The user authentication and authorization information necessary to support an employee in the US is stored in the US servers and the information to support that of a Brazilian employee is stored in the Brazil servers. Now assume that an employee from the US is traveling to Brazil and has the need to access some data from the Brazilian database servers.

There are two possible ways to achieve this:

 Replicate the user information of the employee in the Brazilian server and give them the proper authorizations to access the data.
 Borrow the user name of an employee in Brazil who has similar rights and use that to access the required information.
Neither of these solutions are satisfactory. The system administrators will be faced with creating and managing coordinated access to user accounts within each of the multiple systems, to maintain the consistency of the security policy enforcement. If the username of another employee is borrowed, accountability is compromised.

CONTEXT
Loosely-coupled distributed systems such as the Internet that consist of a variety of computational nodes, and in which some nodes need to share resources. For example, a company with several divisions in different countries.

PROBLEM
A system with centralized authentication is easier to manage and potentially more secure, but it is not flexible enough for distributed systems. How can we provide authentication and authorization in a distributed environment without the need for redundant storage of rights?

The solution to this problem must resolve the following forces:

 Non-redundancy. Storing user authentication and authorization information at multiple locations makes it redundant, difficult to administer and prone to inconsistencies.
 Rights transparency. Although authentication information may be stored anywhere, this location should be transparent to users.
 Rights consistency. Users typically work in the context of some role, and these roles should be standard across a variety of domains, at least within a company or institution.
 Accountability. We need a way to keep users accountable when they are accessing remote resources.
SOLUTION
Set up a single entry point that can transparently redirect the user to the correct server where their user login and access information can be validated.

We can achieve this redirection by using a specialized authentication/authorization server. This server is used for embedded network devices such as routers, modem servers, switches and so on. The authentication servers are responsible for receiving user connection requests, authenticating the user, then returning all configuration information necessary for the client to deliver service to the user.

STRUCTURE
Figure 5.4 shows this approach. The Client makes a request for a service through a ProxyAuthenticator/Authorizer that represents the actual server that contains the user login information. The request is routed to the Authenticator/Authorizer, which validates it, based on the role of the subject of the request and the rights of this role with respect to the protection object1.

Figure 5.4: Class diagram for the REMOTE AUTHENTICATOR/AUTHORIZER pattern


DYNAMICS
Typical systems use the following types of messages:

 Access-request. Sent by a client to request authentication and authorization for a network access connection attempt.
 Access-accept. Sent by a server in response to an access-request message. This message informs the client that the connection attempt is authenticated and authorized.
 Access-reject. Sent by a server in response to an access-request message. This message informs the client that the connection attempt is rejected. A server sends this message if either the credentials are not authentic or the connection attempt is not authorized.
 Access-challenge. Sent by a server in response to an access-request message. This message is a challenge to the client that requires a response.
 Accounting-request. Sent by a client to specify accounting information for a connection that was accepted.
 Accounting-response. Sent by the server in response to the accounting-request message. This message acknowledges the successful receipt and processing of the accounting-request message.
A message consists of a header and attributes. Each attribute specifies a piece of information about the connection attempt. The scenario of Figure 5.5 illustrates a proxy-based communication between a client and the forwarding and remote servers:

Figure 5.5: Sequence diagram for client authentication


1 A client sends its access-request to the forwarding server.
2 The forwarding server forwards the access-request to the remote server.
3 The remote server sends an access-challenge back to the forwarding server.
4 The forwarding server sends the access-challenge to the client.
5 The client calculates a response for the challenge and forwards it to the forwarding server via a second access-request.
6 The forwarding server forwards the access-request to the remote server.
7 If the response matches the expected response, the remote server replies with an access-accept, otherwise an access-reject.
8 The forwarding server sends the access-accept to the client.
IMPLEMENTATION
For this approach to work:

 Roles and access rights have to be standard across locations.
 Both servers and clients should support the base protocol.
Authorization functions are discussed in Chapter 6: we consider here only authentication aspects. An authentication server can function as both a forwarding server and a remote server, serving as a forwarding server for some realms and a remote server for others. One forwarding server can act as a forwarder for any number of remote servers. A remote server can have any number of servers forwarding to it and can provide authentication for any number of realms. One forwarding server can forward to another forwarding server to create a chain of proxies. A lookup service is necessary to find the remote server.

Figure 5.6 shows an example of a remote authentication dial-in user service (RADIUS) system using a challenge-response approach. (More details of the RADIUS system are shown in the next section.)

Figure 5.6: RADIUS challenge/response authentication


EXAMPLE RESOLVED
When the US employee travels to Brazil he logs in a remote authenticator/authorizer, which reroutes his request to the US server that stores their login information.

CONSEQUENCES
The REMOTE AUTHENTICATOR/AUTHORIZER pattern offers the following benefits:

 Roaming permits two or more administrative entities to allow each other’s users to dial in to either entity’s network for service.
 Storing the user login and access rights at a single location makes it more secure and easy to maintain.
 The user’s login ID, password and other details are stored in the internal RADIUS database, or can be accessed from an SQL database.
 The location where the user information is stored is transparent to the user.
 Devices such as active cards [ACS] allow complex request/challenge interactions.
The pattern also has some potential liabilities:

 The additional messages needed increase overhead, thus reducing performance for simple requests. This is not a problem if remote accesses are not very frequent.
 The system is more complex than a system that directly validates clients.
KNOWN USES
RADIUS is a widely deployed IETF protocol enabling centralized authentication, authorization and accounting for network access [Has02] [Rig00]. Originally developed for dial-up remote access, RADIUS is now supported by virtual private network (VPN) servers, wireless access points, authenticating Ethernet switches, digital subscriber line (DSL) access and other network access types [Hil].

With proxy RADIUS, one RADIUS server receives an authentication (or accounting) request from a RADIUS client such as a network-attached storage (NAS) system, forwards the request to a remote RADIUS server, receives the reply from the remote server, and sends that reply to the client. A common use for proxy RADIUS is roaming. Roaming permits two or more administrative entities to allow each other’s users to dial in to either entity’s network for service.

There are many commercially available RADIUS servers in use today. These include:

 FreeRADIUS. FreeRADIUS Server [Frea] is a daemon for UNIX operating systems that allows one to set up a RADIUS protocol server, which is usually used for authentication and accounting of dial-up users. FreeRADIUS is an open source product, and has all the benefits open source provides.
 Steel-Belted Radius. Steel-Belted Radius is a complete implementation of RADIUS. It provides full user authentication, authorization and accounting capabilities.
Steel-Belted Radius fully supports proxy RADIUS; it can:
 Forward proxy RADIUS requests to other RADIUS servers.
 Act as a target server that processes requests from other RADIUS servers.
 Pass accounting information to a target server, either to the one performing the authentication or a different one.
 NavisRadius. NavisRadius is an implementation of the RFC standard RADIUS protocol that provides authentication, authorization and accounting (AAA) services. NavisRadius provides an integrated network-wide remote access security solution for service providers and carriers. NavisRadius supports the RADIUS standard as defined by the IETF RADIUS RFC 2865 (RADIUS authentication) and 2866 (RADIUS accounting), and is used to provision a wide range of network services.
Earlier authentication servers were used in products from CKS, MyNet and Security Dynamics [CTR96].

SEE ALSO
 The whole architecture is an application of the Check Point pattern [Sch06b] [Yod97]. It uses the Proxy pattern [Gam94] as a fundamental component.
 User rights may be defined using a Role-Based Access Control model [Fer01a] [Yod97].
 A pattern for authenticating distributed objects is given in [Bro99].
 The AUTHENTICATOR pattern (page 52) defines its abstract properties.
5.4 Credential
The CREDENTIAL pattern provides a secure means of recording authentication and authorization information for use in distributed systems.

EXAMPLE
Suppose we are building an instant messaging service to be used by members of a university community. Students, teachers and staff of the university may communicate with each other, while outside parties are excluded. Members of the community may use computers on school grounds, or their own systems, so the client software is made available to the community and is installed on the computers of their choice. Any community member may use any computer with the client software installed.

The client software communicates with servers run by the university in order to locate active participants and to exchange messages with them. In this environment, it is important to establish that the user of the client software is a member of the community, so that communications are kept private to the community. Further, when a student graduates, or an employee leaves the university, it must be possible to revoke their communications rights. Each member needs to be uniquely and correctly identified, and a member’s identity should not be forgeable.

CONTEXT
Systems in which the users of one system may wish to access the resources of another system, based on a notion of trust shared between the systems.

PROBLEM
In centralized computer systems, the authentication and authorization of a principal can be handled by that system’s operating system, middleware and/or application software; all attributes of the principal’s identity and authorization are created by and are available to the system. With distributed systems this is no longer the case. A principal’s identity, authentication and authorization on one system does not carry over to another system. If a principal is to gain appropriate access to another system, some means of conveying this information must be introduced.

More broadly, this is a problem of exchanging data between trust boundaries. Within a given trust boundary, a single authority is in control, and can authenticate and make access decisions on its own. If the system is to accept requests from outside its own authority/trust boundary, the system has no inherent way of validating the identity or authorization of the entity making that request. How then do we allow external users to access some of our resources?

The solution to this problem must resolve the following forces:

 Privacy. The user must provide enough information to grant authorization, without being exposed to intrusive data mining.
 Persistence. The information must be packaged and stored in a way that survives travel between systems, while allowing the data to be kept private.
 Authentication. The data available must be sufficient for identifying the principal to the satisfaction of the accepting system’s requirements, while disallowing others from accessing the system.
 Authorization. The data available must be sufficient for determining what actions the presenting principal is permitted to take within the accepting system, while also disallowing actions the principal is not permitted to take.
 Trust. The system accepting the credential must trust the system issuing the credential.
 Generation. There must be entities that produce the credentials such that other domains recognize them.
 Tamper freedom. It should be very difficult to falsify the credential.
 Validity. The credential should have an explicit temporal validity.
 Additional documents. It might be necessary to use the credential together with other documents.
 Revocation. It should be possible to revoke the credential conveniently.
SOLUTION
Store authentication and authorization data in a data structure external to the systems in which the data is created and used. When presented to a system, the data (credential) can be used to grant access and authorization rights to the requester. For this to be a meaningful security arrangement, there must be an agreement between the systems which create the credential (credential authority) and the systems which allow their use, dictating the terms and limitations of system access.

STRUCTURE
In Figure 5.7 the Principal is an active entity such as a person or a process1. The Principal possesses a Credential, representing its identity and its authorization rights. A Credential is a composite describing facts about the rights available to the principal. The Attribute may flag whether it is presently enabled, allowing principal control over whether to exercise the right implied by the Credential. An expiration date allows control over the duration of the rights implied by the attribute.

Figure 5.7: Class diagram for the CREDENTIAL pattern


A Credential is issued by an Authority, and is checked by an Authenticator or an Authorizer. Specialization of a Credential is achieved through setting Attribute names and values.

Some specializations of Attributes are worth mentioning. Identity, created by setting an attribute name to, say, ‘username’ and the value to the appropriate username instance, shows that the subject has been authenticated and identified as a user known to the Authenticator. Privilege, named after the intended privilege, implies some specific ability granted to the subject. Group and Role can be indicated in a similar fashion to Identity.

DYNAMICS
There are four primary use cases:

 Issue credential, by which a credential is granted to the principal by an authority.
 Principal authentication, where an authenticator accepts a credential provided to it by a principal, and makes an access decision based on the credential.
 Principal authorization, in which the principal is allowed access to specific items.
 Revoke credential, in which a principal’s credential are invalidated.
Use Case: Issue Credential – Figure 5.8

Figure 5.8: Sequence diagram for the use case ‘Issue credential’


The Principal presents itself and any required documentation of its identity to an Authority. Based upon its rules and what it ascertains about the Principal, the Authority creates and returns a Credential. The returned data may include an identity credential, group and role membership credential attribute, and privilege credential attributes. As a special case, the Authority may generate a defined ‘public’ Credential for Principals not previously known to the system. This Credential is made available to Authenticators which reference this Authority.

Use Case: Principal Authentication – Figure 5.9

Figure 5.9: Sequence diagram for the use case ‘Principal authentication’


The Principal requests authentication at an Authenticator, supplying its name and authentication Credential. The Authenticator checks the Credential and makes an access decision. There are different phases and strengths of check that may be appropriate for this step, discussed in the Implementation section. It is necessary for the Authenticator to be established in conjunction with the original authority. Not shown in the sequence diagram, but it is also optionally possible to forward the authentication request and credentials to the authority for verification.

Use Case: Revoke Credential

If it is determined that a given principal should no longer have access to the system, or that a principal’s credentials have been stolen or forged, the authority can issue a revocation message to each authenticator and authorizer. Once this message has been received, the authentication and authorization subsystems reject future requests from the affected credentials. If the principal is still authorized to use the system, new credentials must be issued.

IMPLEMENTATION
The most significant factor in implementing the CREDENTIAL pattern is to determine the nature of the agreement between the participating systems. This begins with consideration of the functions to be provided by the system to which credentials will give access, the potential users of those functions, and the set of rights that are required for each user to fulfill its role. Once these are understood, a clear representation of the subjects, objects and rights can be developed. This representation forms the basis for storing credentials in some persistent medium and sets the terms of authentication and authorization. It also forms the basis for portability, as persisted data may be placed on portable media for transmission to the location(s) of its use1.

The problem with a clear representation of security rights is that potential attackers can read them as well as valid participants in the systems in question. In the physical world, anti-forgery devices for credentials use techniques such as embedding the credential data in media that is too expensive to be worth forging for the benefit received: drivers’ licenses and other ID cards, passports and currency all are based on the idea that it is too complex and costly for the majority of users to create realistic fakes.

In the digital world, however, copies are cheap. There are two common means of addressing this. One is to require that credentials be established and used within a closed context, and encrypting the communications channels used in that context. The other is to encrypt the credentials when they are issued, and to set up matching decryption on the authenticating system. This further subdivides into ‘shared secret’ systems, in which the issuing and accepting systems share the cryptographic keys necessary to encrypt and decrypt credentials, and ‘public key’ systems, in which participating systems can establish means for mutual encryption/decryption without prior sharing. These design choices are part of the terms set by the authority agreement under which the credentials apply. The authenticator must use the same scheme as the authority. Kerberos tokens and X.509 certificates are examples of this that require more specific approaches [Lop04].

As a simple example of ‘shared secret’ systems, consider a typical online banking authority and authentication setup; at sign-up, the customer verifies their identity to the bank, the authority. As part of the bank’s processing, it creates customer data on its website, and allows the customer to create a username and password to gain access to the account. This data is stored on the bank’s web server, which serves as the authenticator. The customer later presents their credentials through a browser to the web server, which authenticates under the authority of the bank.

In implementing the ‘principal authentication’ use case, there are different phases and strengths of check that may be appropriate. For example, when entering my local warehouse club1, I need only show a card that looks like a membership card to the authenticator standing at the door. When it comes time to make a purchase, however, the membership card is checked for validity, expiration date and ownership of the person presenting it. In general, the authenticator is responsible for checking the authenticity of the credentials themselves (anti-forgery), whether they belong to their bearer, and whether they constitute valid access to the requested object(s).

EXAMPLE RESOLVED
The university created a credential authority, ‘IM Registration’. It gave it the responsibility of verifying identity and granting a username and password, in the form of an ID card, to university community members when they join the university community. This login embodies the authority of the granting agency and that of the identity of the subject as verified by the agency. The university defined policies such that members were encouraged to keep their login information private.

The client software is coded to implement an authenticator when someone wants to start a session. Access is granted or denied based on the results of the authentication. Checks on the servers ensure that the member’s credential is not expired.

CONSEQUENCES
The CREDENTIAL pattern offers the following benefits:

 Fine-grained authentication and authorization information can be recorded in a uniform and persistent way.
 A credential from a trusted authority can be considered proof of identity and of authorization.
 It is possible to protect credentials using encryption or other means.
The pattern has the following potential liabilities:

 It might be difficult to find an authority that can be trusted. This can be resolved with chains (trees) of credentials, by which an authority certifies another authority.
 Making credentials tamper-resistant incurs extra time and complexity.
 Storing credentials outside of the systems that use them leaves system authentication and authorization mechanisms open to offline attack.
KNOWN USES
This pattern is a generalization of the concepts embodied in X.509 Certificates, CORBA Security Service’s Credentials [And01], Windows security tokens [Bro05], SAML assertions ([Hug05] and Chapter 11) and the Credential Tokenizer pattern [Ste05]. Capabilities, as used in operating systems, are another implementation of the idea (Chapter 6).

Passports are a non-technical example of the problem and its solution. Countries must be able to distinguish between their citizens, citizens of nations friendly and unfriendly to them, trading partners, guests and unwanted people. There may be different rules for how long visitors may stay, and for what they may engage in while they are in the country. Computer systems share some of these traits: they must be able to distinguish between members and non-members of their user community; non-members may be eligible or ineligible to gain system access or participate in transactions.

SEE ALSO
 Metadata-Based Access Control [Pri04] describes a model in which credentials can be used to represent subjects.
 The CREDENTIAL pattern complements Security Session [Sch06b] by giving an explicit definition of that pattern’s ‘session object’, as extracted from several existing platforms.
 The Authenticator pattern [Bro99] and the REMOTE AUTHENTICATOR/AUTHORIZER pattern ([War03b], also page 56) describe types of authenticator.
 An Authorizer is a concrete version of the abstract concept of Reference Monitor ([Sch06b] and Chapter 6).
 Delegation of credentials is discussed in [Wei06a].
 [Ste05] describes a Session Object pattern that ‘abstracts encapsulation of authentication and authorization credentials that can be passed across boundaries’. They seems to be talking about access rights rather than credentials: credentials abstract authentication and authorization rights.
 The CIRCLE OF TRUST pattern (page 34) allows the formation of trust relationships among service providers in order for their subjects to access an integrated and more secure environment. CREDENTIALs can be used for identification in a circle of trust.
1We show this pattern as a variant of AUTHENTICATOR.

1Protection object refers to the object (data item or other resource) being requested by the user.

1The principal is the responsible subject.

1It is important to note that ‘portability’ is used in a restricted sense here, meaning only that the credential data can be read by a node of the system not directly connected at the time of credential creation, and not necessarily meaning that the data can be transferred for use in other systems.

1In the US there are ‘warehouse clubs’, such as Costco and Sam’s, whose members can buy goods at a discount.