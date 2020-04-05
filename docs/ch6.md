# 第 6 章 访问控制模式
CHAPTER 6
Patterns for Access Control
With the Berlin (defense) I was able to set up a fortress that he could come near but not breach.

Vladimir Kramnik (ex-world chess champion)

6.1 Introduction
Once a subject has been granted access to a system, we need to control their access to specific resources. The rights of the subjects of the system are defined using some model of access control and expressed in the form of authorization rules. Security models are a more precise and detailed expression of policies and are used as guidelines to build and evaluate systems, usually are described in a formal or semi-formal way.

Models can be discretionary or mandatory. In a discretionary access control (DAC) model, users can be owners of data and can transfer their rights at their discretion: that is, in a DAC model, there is no clear separation of use and administration; users can be owners of the data they create and act as their administrators. In a mandatory access control (MAC) model, only designated users are allowed to grant rights, and users cannot transfer them. Users and data are classified by administrators, and the system applies a set of built-in rules that users cannot circumvent.

Orthogonal to this classification, there are several models for information access control that differ in how they define and enforce their policies [Gol06], [Sum97]. The most common are:

 An Access Matrix describes access by subjects (actors, entities) to protected objects (data/resources) in specific ways (access types) [Gol06] [Har76] [Sum97]. It is more flexible than the multilevel model and it can be made even more flexible and precise using predicates and other extensions. However, it is intrinsically a discretionary model in which users own the data objects and may grant access to other subjects. It is not clear who owns the information in companies and institutions, and the discretionary property reduces security. This model is usually implemented using access control lists (lists of the subjects that can access a given object) or capabilities (tickets that allow a process to access some objects).
 Role-Based Access Control (RBAC) collects users into roles based on their tasks or functions and assigns rights to each role [San96]. Some of these models [San96], [Tho98] have their roles structured as hierarchies, which may simplify administration. RBAC has been extended and combined in many ways.
 Attribute-Based Access Control (ABAC). This model controls access based on properties (attributes) of subjects or objects. It is used in environments where subjects may not be pre-registered [Pri04].
 The Multilevel Model organizes data using security levels. This model is usually implemented as a mandatory model in which its entities are labeled indicating their levels. The multilevel model is able to achieve a high degree of security, although it can be too rigid for some applications. Usually it is not possible to structure the variety of entities involved in complex applications into strictly hierarchical structures.
While these basic models may be useful for specific domains or applications, they are not flexible enough for the full range of policies present in some of these applications [DeC02] [DeC05]. This is manifested in the large variety of ad hoc RBAC variations that have been proposed, most of which add specialized policies to a basic RBAC model. For example, some models have added content or context-dependent access [Cha01], delegation [Zha02], task-based access [Tho97] and relationships between role entities [Bar99]. All these models effectively incorporate a set of built-in access control policies and cannot handle situations not considered by these policies, which means that a complex system may need such models for specific users or divisions. The variety of access control models makes it difficult for software developers to select an appropriate model for the application they build.

The contents of the rules are defined by institution policies. Policies are high-level guidelines defining the way an institution conducts its activities in its business, professional, economic, social and legal environment. The institution security policy includes laws, rules and practices that regulate how an institution uses, manages and protects resources.

We present here several patterns for access control that correspond to the models described on page 72 and some of their extensions or generalizations. Figure 6.1 starts from the basic components of access control to provide a more general approach to developing access control models. This diagram can be the starting point that allows a designer to select the type of access control they need in their application. Once this abstract level is clear, we need to go to a software-oriented level, where we can choose more specific approaches. The center of this diagram is POLICY-BASED ACCESS CONTROL (PBAC, page 84) which indicates that the rules represent access policies, which are in turn defined by a Policy pattern (see Figure 6.1). The POLICY-BASED ACCESS CONTROL pattern decides whether a subject is authorized to access an object according to policies defined in a central policy repository. The enforcement of these policies is defined by a Reference Monitor pattern. Depending on its administration, PBAC can be mandatory or discretionary. XACML is a type of PBAC-oriented Service-Oriented Architectures (SOA) [Del07a], shown here as two patterns to separate its aspects of rule definition and evaluation. Policies can be implemented as access control lists (ACLs) or capabilities. The Reference Monitor may use a policy enforcement point (PEP), a policy decision point (PDP), and other patterns to describe the administrative structure of enforcement.

Figure 6.1: A classification of access control patterns


Authorization, RBAC, and multilevel patterns appeared in [Sch06b]; we have revised and updated them. Policy-Based Authorization, Access Control List and Capability are from [Del07a]. The Reified Reference Monitor pattern is from [Fer09c], while the Controlled Access Session pattern is from [Fer06e]. The Security Logger/Auditor pattern comes from [Fer11d].

6.2 Authorization
ALSO KNOWN AS ACCESS MATRIX
The AUTHORIZATION pattern describes who is authorized to access specific resources in a system, in an environment in which we have resources whose access needs to be controlled. The model indicates, for each active subject, which resources the subject can access and what it can do with them.

EXAMPLE
In a medical information system we keep sensitive information about patients. Unrestricted disclosure of this data would violate the privacy of the patients, while unrestricted modification could jeopardize their health.

CONTEXT
A computing environment that has resources that have value for its users or their institution.

PROBLEM
We need a way to control access to resources, otherwise any active entity (user, process) could access any resource and we could have confidentiality and integrity problems, as well as misuse of network bandwidth or peripheral devices.

How can we describe who is authorized to access specific resources in a system? The solution to this problem must resolve the following forces:

 Independence. The resource control structure must be independent of the type of resources and must apply to all of them.
 Flexibility. The resource control structure should be flexible enough to accommodate different types of subjects and resources.
 Modifiability. It should be easy to modify the rights of active entities in response to changes in their duties or responsibilities.
 Security. The resource control structure should be protected against tampering.
SOLUTION
Indicate, for each active subject that can access resources – objects or protection objects (see page 58) – which resources it can access and how (access type).

STRUCTURE
Figure 6.2 shows a class diagram of the entities involved. The Subject class describes an active entity that attempts to access a resource (ProtectionObject) in some way. The association between the Subject and the ProtectionObject defines an authorization, from which the pattern gets its name. The association class Right describes the access type (for example read, write) the subject has to the corresponding object. Through this class one can check the rights that a subject has on some object, or who is allowed to access a given object.

Figure 6.2: Class diagram for the AUTHORIZATION pattern


DYNAMICS
Use cases include ‘Add a rule’, ‘Delete a rule’, ‘Modify a rule’, ‘Create user’ and so on. We do not show their sequence diagrams here because they are very simple.

IMPLEMENTATION
An organization, according to its policies, should define all the required accesses to resources. The most common policy is need-to-know, in which active entities receive access rights according to their needs (see the Least Privilege pattern [Fer11d]). The AUTHORIZATION pattern is abstract and many implementations of it are possible: the two most common approaches are access control lists and capabilities. Access control lists (ACLs) are kept with the protected objects to indicate who is authorized to access them, while capabilities are assigned to processes to define their execution rights. Access types should be application-oriented.

EXAMPLE RESOLVED
A hospital using an authorization system can define rules that allow only doctors or nurses to modify patient records, and only medical personnel to read patient records. We can also define specific types of access for finer control, for example readMedicine for an assistant nurse.

CONSEQUENCES
The AUTHORIZATION pattern offers the following benefits:

 Independence. The pattern applies to any type of resource. Subjects can be executing processes, users, roles, user groups. Protection objects can be transactions, data, memory areas, I/O devices, files or other resources. Access types are individually definable and can be application-specific in addition to the usual read and write. It is easy to add or remove authorizations.
 Security. Some systems separate administrative authorizations from user authorizations for further security, on the principle of separation of duties [Sum97]. Authorization rules can be protected in the same way as other data structures such as relations.
 Flexibility. The rules apply to any type of subject or object.
 Modifiability. It is easy to add new rules to reflect policy changes.
 An access request may not need to specify the exact object in the rule: the object may be implied by an existing protected object [Fer75]. Subjects and access types may also be implied. This reduces the number of rules, at the cost of some extra processing time to deduce the specific rule needed.
The pattern also has the following potential liabilities:

 If there are many users or many objects, a large number of rules must be written. This makes administration difficult and error-prone.
 It may be hard for the security administrator to realize why a given subject needs a right, or the implications of a new rule. There is no semantic relation between subjects and objects.
 Defining authorization rules is not enough, we also need an enforcement mechanism.
VARIANTS
AUTHORIZATION is usually represented by an access matrix model, which is the model usually described in textbooks, and may also include:

 Predicates or guards, which may restrict the use of the authorization according to specific conditions, or provide content-dependent authorization.
 Delegation of some of the authorizations by their holders to other subjects through the use of a Boolean ‘copy’ flag [Sum97].
 Packet Filter Firewall [Sch06b] implements a variety of this pattern in which the subjects and objects are defined by Internet addresses.
Figure 6.3 extends AUTHORIZATION to include those aspects. A Right now includes not only the type of access allowed, but also a predicate that must be true for the authorization to hold, and a copy flag that can be true or false, indicating whether or not the right can be transferred. CheckRights is an operation to determine the rights of a subject or to find who has the rights to access a given object.

Figure 6.3: Access matrix with predicates and copy flag


KNOWN USES
This pattern defines the most basic type of authorization rule, from which more complex access-control models can be built. It is based on the concept of the access matrix, a fundamental security model [Gol06] [Sum97]. Its first object-oriented form appeared in [Fer93b]. Subsequently, it has appeared in several other papers and products. It is the basis for the access control systems of most commercial operating system and database products, such as UNIX, Windows, Oracle and many others.

SEE ALSO
 ROLE-BASED ACCESS CONTROL (below) is a specialization of this pattern.
 The Reference Monitor pattern complements the AUTHORIZATION pattern by defining how to enforce the defined rights.
 There is a discussion of authorization in [Fer81].
6.3 Role-Based Access Control
The ROLE-BASED ACCESS CONTROL pattern describes how to assign rights based on the functions or tasks of users in an environment in which control of access to computing resources is required.

CONTEXT
Any environment in which we need to control access to computing resources and in which there is a large number of users and information types, or a large variety of resources.

PROBLEM
For convenient administration of authorization rights, we need to have a means of factoring out rights. Otherwise, the number of individual rights is just too large; granting rights to individual users would require storing many authorization rules, and it would be hard for administrators to keep track of these rules. It is also hard to associate semantic meanings to the rules. How can we reduce the number of rules and make their semantics clearer?

The solution to this problem must resolve the following forces:

 Complexity. We would like to make the work of the security administrator as simple as possible.
 Semantics. In most organizations people are assigned specific functions or tasks. Their rights should correspond to those tasks.
 Policy. We need to define rights according to organizational policies.
 Commonality. People performing the same tasks should have the same rights.
 Policy enforcement. We want to help the organization to define precise access rights for its members according to a need-to-know policy.
 Flexibility. People joining, leaving and changing functions should not require complex rights manipulation.
SOLUTION
Most organizations have a variety of job functions that require different skills and responsibilities. Users should be assigned rights based on their job functions or their designated tasks. This corresponds to the application of the need-to-know principle, a fundamental security policy [Sum97]. Job functions can be interpreted as roles that people play in performing their duties. In particular, web-based systems have a variety of users: company employees, customers, partners, search engines and so on.

STRUCTURE
Figure 6.4 shows a class diagram for ROLE-BASED ACCESS CONTROL. The User and Role classes describe registered users and their predefined roles respectively. Users are assigned to roles, roles are given rights according to their functions. The association class Right defines the access types that a user within a role is authorized to apply to the protection object. The combination Role, ProtectionObject and Right is an instance of the AUTHORIZATION pattern.

Figure 6.4: Class diagram for the ROLE-BASED ACCESS CONTROL pattern


DYNAMICS
Use cases include ‘Add a rule’, ‘Delete a rule’, ‘Modify a rule’, ‘Assign user to role’, ‘Assign rights to role’ and so on. We do not show their sequence diagrams here because they are very simple.

IMPLEMENTATION
Roles may correspond to job titles, for example ‘manager’, ‘secretary’. A finer-grained approach is to make them correspond to tasks. For example, a professor has the roles of ‘thesis advisor’, ‘teacher’, ‘committee member’, ‘researcher’ and so on. An approach to defining role rights is described in [Ful07].

There are many possible ways to implement roles in a software system. [Kod01] considers the implementation of the data structures needed to apply an RBAC model. Concrete implementations can be found in operating systems, database systems and web application servers.

EXAMPLE RESOLVED
The hospital now assigns rights to the roles of doctors, nurses and so on. The number of authorization rules has decreased dramatically as a result.

CONSEQUENCES
The ROLE-BASED ACCESS CONTROL pattern offers the following benefits:

 Semantics. We can make the rights given to a role correspond to tasks.
 Complexity. It allows administrators to reduce the complexity of security. Because there are many more users than roles, the number of roles becomes much smaller.
 Policy. Organization policies about job functions can be reflected directly in the definition of roles and the assignment of users to roles.
 Commonality. People performing the same tasks can be given the same rights.
 Flexibility. It is very simple to accommodate users joining, leaving or being reassigned. All these use cases require only manipulation of the associations between users and roles.
 Structure. Roles can be structured into hierarchies for further flexibility and reduction of rules.
 Role separation. Users can activate more than one session at a time for functional flexibility: some tasks may require multiple views or different types of actions. Role separation is also important to avoid conflicts of interest: we can add UML constraints to indicate that some roles cannot be used in the same session or given to the same user (separation of duties).
The following potential liability may arise from applying this pattern:

 Some institutions may not have clearly defined roles in their organization, and some work must therefore be done to define these roles.
VARIANTS
The model shown in Figure 6.5 additionally considers composite roles and objects: it is an application of the Composite pattern [Gam94]. The figure also includes the concept of a session, which defines a context for the use of a role, may restrict the number of roles used together at execution time, and can be used to enforce role exclusion at execution time.

Figure 6.5: Class diagram for the Extended RBAC model


KNOWN USES
The RBAC pattern represents in object-oriented form a model described in terms of sets in [San96]. That model has been the basis of most research papers and implementations of this concept. RBAC is implemented in a variety of commercial systems, including Sun’s J2EE, Microsoft’s Windows 2000 and later, Microsoft’s .NET [Fen06], IBM’s WebSphere, and Oracle, among others. The basic security facilities of Java’s JDK 1.2 have been shown to be able to support a rich variety of RBAC policies. The NIST has developed a standard for RBAC [Fer01b]. We have used RBAC to describe access to physical structures [Fer07f].

SEE ALSO
Earlier versions of this pattern appeared in [Fer93b] and [Yod97], and a pattern language for its software implementation appears in [Kod01], although this paper does not consider composite roles, groups and sessions.

The pattern whose class diagram is shown in Figure 6.5 includes AUTHORIZATION and Composite. A session object is used to provide execution context (see Variants).

6.4 Multilevel Security
In some environments data and documents may have critical value and their disclosure could bring serious problems. The MULTILEVEL SECURITY pattern describes how to categorize sensitive information and prevent its disclosure. It describes how to assign classifications (clearances) to users and classifications (sensitivity levels) to data, and how to separate different organizational units into categories. Access of users to data is based on policies, while changes to the classifications are performed by trusted processes that are allowed to violate the policies.

EXAMPLE
The general command of an army has decided on a plan of attack in a war. It is extremely important that this information is not known outside a small group of people, or the attack may be a failure.

CONTEXT
In some environments data and documents may have critical value and their disclosure could bring serious problems.

PROBLEM
How can you control access in an environment with sensitive documents so as to prevent leakage of information?

The solution to this problem must resolve the following forces:

 We need to protect the confidentiality and integrity of data based on its sensitivity.
 Users have to be allowed to read documents based on their position in the organization.
 There should be a way to increase or decrease the ability of users to read documents and the sensitivity of the documents. Otherwise, people promoted to higher positions, for example, will not be able to read sensitive documents, and we will end up with a proliferation of sensitive and obsolete documents.
SOLUTION
Assign classifications (as clearances) to users and classifications (as sensitivity levels) to data. Separate different organizational units into categories. For example, classifications may include levels such as ‘top secret’, ‘secret’ and so on, and categories may include units such as engDept, marketingDept and so on. For confidentiality purposes, access of users to data is based on policies defined by the Bell-LaPadula model [Gol06], while for integrity the policies are defined by Biba’s model [Sum97]. Changes to the classifications are performed by trusted processes that are allowed to violate the policies of these models.

STRUCTURE
Figure 6.6 shows the class diagram of the MULTILEVEL SECURITY pattern. The UserClassification and DataClassification classes define the active entities and the objects of access respectively. Both classifications may include categories and levels. Trusted processes are allowed to assign users and data to classifications, as defined by the Assignment class.

Figure 6.6: Class diagram for the MULTILEVEL SECURITY pattern


IMPLEMENTATION
Data classification is a tedious task, because every piece of information or document must be examined and assigned a classification tag. New documents may get automatic tags based on their links to other documents. User classifications are based on users’ rank and units of work and are only changed when they change jobs. It is hard to classify users in commercial environments in this way: for example, in a medical system it makes no sense to assign a doctor a higher classification than a patient, because a patient has the right to see their record.

EXAMPLE RESOLVED
The group involved in planning attacks, as well as all the related documents it produces, are given a classification of ‘top secret’. This will prevent leakage towards lower-level army staff.

CONSEQUENCES
The MULTILEVEL SECURITY pattern offers the following benefits:

 The classification of users and data is relatively simple and can follow organization policies.
 This model of the pattern can be proved to be secure under certain assumptions [Sum97].
 The pattern is useful to isolate processes and execution domains.
The following potential liabilities may arise from applying this pattern:

 Implementations should use labels in data to indicate their classification. This assures security: if not done, the general degree of security is reduced.
 We need trusted programs to assign users and data to classifications.
 Data must be able to be structured into hierarchical sensitivity levels and users should be able to be structured into clearances. This is usually hard, or even impossible, in commercial environments.
 This model can handle only secrecy and prevention of leakage of information. A dual model is needed to also handle integrity.
 Covert channels may break the assumed security.
VARIANTS
It is possible to define a similar pattern to control integrity in multilevel models according to the Biba rules [Gol06].

KNOWN USES
This pattern has been used by several military-sponsored projects and in a few commercial products, including DBMSs (Informix, Oracle) and operating systems (Pitbull [Arg] and HP’s Virtual Vault [HP]).

SEE ALSO
The concept of roles can also be applied when implementing this pattern, role classifications replacing user classifications.

6.5 Policy-Based Access Control
The POLICY-BASED ACCESS CONTROL pattern describes how to decide whether a subject is authorized to access an object according to policies defined in a central policy repository.

EXAMPLE
Consider a financial company that provides services to its clients. Their computer systems can be accessed by clients, who send orders to the company for buying or selling commodities (stocks, bonds, real estate, art and so on) via e-mail or through their website. Brokers employed by the company can carry out the clients’ orders by sending requests to the systems of various financial markets, or by consulting information from financial news websites. A government auditor visits periodically to check for compliance with laws and regulations.

All of these activities are regulated by policies with various granularities within the company. For example, the billing department can have the rule ‘Only registered clients whose account status is in good standing may send orders’, the technical department can decide that ‘E-mails with attachments bigger than x Mb won’t be delivered’, the company security policy can state that ‘Only employees with a broker role can access the financial market’s web services’ and that ‘Only the broker or custodian of a client can access its transaction information’, whereas the legal department can issue the rule that ‘Auditors can access all transaction information’, and so on.

All of these policies are enforced by different components of the company’s computer system (e-mail server, file system, web service access control component, and financial application). This approach has several problems: the policies may be described in different syntaxes, and it is difficult to have a global view of which policies apply to a specific case. Moreover, two policies can be conflicting, and there is no way to combine them in a clear way. In summary, this approach could be error-prone and complex to manage.

CONTEXT
Consider centralized or distributed systems with a large number of resources (objects). A large number of subjects may access those objects. Rules are defined to control access to objects. The rules defined by the organization are typically designed by different actors (technical, organizational, legal and so on), and each set of rules designed by a specific policy designer can concern overlapping sets of objects and/or subjects.

PROBLEM
Enforcing these rules for a particular access request may be complex, and thus error-prone, because there is no clear view of which rules to apply to a request.

How can we enforce access control according to the predefined rules in a consistent way? The solution to this problem must resolve the following forces:

 Objects may be frequently added or removed.
 The solution should be able to implement a wide variety of access control models, such as access matrix, RBAC.
 Malicious users can attempt unauthorized access to objects.
 There should be no direct access to objects: every request must be mediated.
SOLUTION
Most access control systems are based on the AUTHORIZATION pattern (page 74), in which the access of a subject to an object depends only on the existence of a positive applicable rule. If no such rule exists, the access is denied.

In our case, the situation is more complicated: the existence of a positive applicable rule should not necessarily imply that access should be granted. All the rules must be taken into account, and a final decision must be made from the set of applicable rules and some meta-information about the way they should be combined. Part of that meta-information is located in a policy object. This policy object aggregates a set of rules, and specifies how those rules must be combined. For more flexibility about the combination of rules, a composite object regroups the rules into policies and policy sets. Policy sets aggregate policies, and include information about how to combine rules from different policies. To be able to select all applicable rules easily, they should be stored in a unique repository for the organization and administered in a centralized way.

At access time, all requests are intercepted by policy enforcement points (PEPs), a specific type of Reference Monitor [Sch06b]. The repository is accessed by a unique policy decision point (PDP), which is responsible for computing the access decision by cooperating with a policy information point (PIP), which may provide information about the subject or the resource accessed. The rules and policies are administered through a unique policy administration point (PAP).

Finally, because rules and policies are designed by different teams, possibly for the same objects and subjects, this scheme does not guarantee that a conflict between rules in different policy components would never occur. In that case, the PDP may have a dynamic policy conflict resolver to resolve the conflict, which would need to use meta-rules. A complementary static policy conflict resolver may be a part of the PAP, and should detect conflicts between rules at the time they are entered into the repository.

STRUCTURE
Figure 6.7 illustrates the solution. A Subject’s access requests to particular objects are intercepted by PEPs, which are a part of the security infrastructure that is responsible for enforcing the organization Policy about this access. PEPs query another part of the security infrastructure, the PDP, which is responsible for computing an access decision. To compute the decision, the PDP uses information from a PIP, and retrieves the applicable Policy from the unique PolicyRepository, which stores all of the PolicyRules for the organization.

Figure 6.7: Class diagram for the POLICY-BASED ACCESS CONTROL pattern


The PolicyRepository is also responsible for retrieving the applicable rules by selecting those rules whose subjectDescriptor, resourceDescriptor and environmentDescriptor match the information about the subject, the resource and the environment obtained from the PIP, and whose accessType matches the required accessType from the request. The PAP is a unique point for administering the rules. In case the evaluation of the Policy leads to a conflict between the decisions of the applicable Rules, a part of the PDP, the DynamicPolicyConflictResolver, is responsible for producing a uniquely determined access decision. Similarly, a StaticPolicyConflictResolver is a part of the PAP and is responsible for identifying conflicting rules within the PolicyRepository.

DYNAMICS
Figure 6.8 shows a sequence diagram describing the most commonly used case of ‘Request access to an object’. The Subject’s request for accessing an Object is intercepted by a PEP, which forwards the request to the PDP. The PDP can retrieve information about the Subject, the Object and the current environment from the PIP. This information is used to retrieve the applicable Rules from the PolicyRepository.

Figure 6.8: Sequence diagram for the use case ‘Request access to an object’

 
The PDP can then compute the access decision by combining the decisions from the Rules forming the applicable policy and can finally send this decision back to the PEP. If the access has been granted by the PDP, the PEP forwards the request to the Object.

EXAMPLE RESOLVED
Use of the POLICY-BASED ACCESS CONTROL pattern allows the company to centralize its rules. Now the billing department, as well as the technical department, the legal department and the corporate management department can insert their rules in the same repository, using the same format. The different components of the computer system that used to enforce policies directly (that is, e-mail server, file system, web service access control component and financial application) just need to intercept the requests and redirect them to the central policy decision point. To do that, each of them runs a policy enforcement point, which interfaces with the main policy decision point.

The rules could be grouped in the following way: a unique company policy set might include all other policies and express the fact that all policies coming from the corporate management should dominate all other policies. Each department would have their own policy, composed of rules from that department, and combined according to each department’s policy.

Finally, a simple dynamic conflict resolver could be configured to enforce a closed policy in case of conflict. The rules can be managed easily, since they are written to the same repository, conflicts can be resolved, and there is a clearer view of the company’s security policy.

CONSEQUENCES
The POLICY-BASED ACCESS CONTROL pattern offers the following benefits:

 Since the access decisions are requested in a standard format, an access decision becomes independent of its enforcement. A wide variety of enforcement mechanisms can be supported and can evolve separately from the policy decision point.
 The pattern can support the access matrix, RBAC or multilevel models for access control.
 Since every access is mediated, illegal accesses are less likely to be performed.
The pattern also has some potential liabilities:

 It could affect the performance of the protected system, since the central PDP/PolicyRepository/PIP subsystem may be a bottleneck in the system.
 Complexity.
 We need to protect the access control information.
KNOWN USES
 XACML (eXtensible Access Control Markup Language), defined by OASIS, uses XML for expressing authorization rules and for access decision following this pattern.
 Symlabs’ Federated Identity Access Manager Federation is an identity management that implements identity federation. Its components include a PDP and PEPs.
 Components Framework for Policy-Based Admission Control, a part of the Internet 2 project, is a framework for the authentication of network components. It is based on five major components: Access Requester (AR), Policy Enforcement Point (PEP), Policy Decision Point (PDP), Policy Repository (PR) and the Network Detection Point (NDP).
 XML and Application firewalls [Del05] also use policies.
 SAML (Security Assertion Markup Language) is an XML standard defined by OASIS for exchanging authentication and authorization data between security domains. It can be used to transmit the authorization decision.
SEE ALSO
 XACML patterns [Del05] is an implementation of this pattern (Chapter 11).
 The ACCESS CONTROL LIST (below) and CAPABILITY (page 96) patterns are specific implementations of this pattern.
 The PEP is just a Reference Monitor [Fer01a].
 This pattern can implement the Access Matrix and ROLE-BASED ACCESS CONTROL patterns.
A general discussion of security policies, including some IETF models that resemble patterns, is given in [Slo02].

6.6 Access Control List
The ACCESS CONTROL LIST (ACL) pattern allows controlled access to objects by indicating which subjects can access an object and in what way. There is usually an ACL associated with each object.

EXAMPLE
We are designing a system in which documents should be accessible only to specific registered users, who can either retrieve them for reading or submit modified versions. We need to verify that a specific user can access the document requested in a rapid manner.

CONTEXT
Centralized or distributed systems in which access to resources must be controlled. The systems comprise a policy decision point and policy enforcement points that enforce the access policy. A system has subjects that need to access resources to perform tasks. In the system, not every subject can access any object: access rights are defined and can be modeled as an access matrix, in which each row represents a subject and each column represents an object. An entry in the matrix is indexed by a specific subject and a specific object, and lists the types of actions that this subject can execute on this object.

PROBLEM
In some systems the number of subjects and/or objects can be large. In this case, the direct implementation of an access matrix can use significant amounts of storage, and the time used for searching this large matrix can be significant.

In practice, the matrix is sparse. Subjects have rights on few objects and thus most of the entries are empty.

How can we implement the access matrix in a space- and time-efficient way? The solution to this problem must resolve the following forces:

 The matrix may have many subjects and objects. Finding the rule that authorizes a specific request to an object may take a lot of time, as entries are unordered.
 The matrix can be very sparse: storing it as a matrix would require storing many empty entries, thus wasting space.
 Subjects and objects may be frequently added or removed. Making changes in a matrix representation is inefficient.
 The time spent for accessing a centralized access matrix may result in an additional overhead.
 A request received by a policy enforcement point indicates the requester identity, the requested object and the type of access requested. The requester identity, in particular, is controlled by the requester, and so may be forged by a malicious user.
SOLUTION
Implement the access matrix by associating each object with an access control list (ACL) that specifies which actions are allowed on the object, by which authenticated users. Each entry in the list comprises a subject’s identifier and a set of rights. Policy enforcement points enforce the access policy by requesting the policy decision point to search the object’s ACL for the requesting subject identifier and access type. For the system to be secure, the subject’s identity must be authenticated prior to its access to any objects. Since the ACLs may be distributed, like the objects they are associated with, several policy administration points may be responsible for creating and modifying the ACLs.

STRUCTURE
Figure 6.9 illustrates the solution. To be protected, an Object must have an associated ACL. This ACL is made up of ACLEntries, each of which contains a set of Rights permitted for a specific authenticated Subject. An authenticated Subject accesses an Object only if a corresponding Right exists in the Object’s ACL. For security reasons, only the PDP can create and modify ACLs. At execution time, the PDP is responsible for searching an Object’s ACL for a Right in order to make an access decision.

Figure 6.9: Class diagram for the ACCESS CONTROL LIST pattern


DYNAMICS
Figure 6.10 shows a sequence diagram describing the typical use case for ‘Request object access’. The authenticated Subject’s request for access to an Object is intercepted by a PEP, which forwards the request to the PDP. It can then check that the ACL corresponding to the Object contains an ACLEntry which corresponds to the Subject and which holds the accessType requested by the Subject.

Figure 6.10: Sequence diagram for use case ‘Request object access’


IMPLEMENTATION
A decision must be made regarding the granularity of the ACLs. For example, it is possible to regroup users, such as the minimal access control lists in UNIX. It is also possible to have a finer-grained access control system. For example, the extended access control lists in UNIX allow specified access not only for the file’s owner and owner’s group, but also for additional users or groups.

The choice of access types can also contribute to a finer-grained access control system. For example, Windows defines over ten different permissions, whereas UNIX-like systems usually define three.

A creation/inheritance policy must also be defined: what should the ACL look like at the creation of an object? From what objects should it inherit its permissions?

ACLs are pieces of information of variable length. A strategy for storing ACLs must be chosen. For example, in the Solaris UFS file system, each inode has a field called i_shadow. If an inode has an ACL, this field points to a shadow inode. On the file system, shadow inodes are used like regular files. Each shadow inode stores an ACL in its data blocks. Linux and most other UNIX-like operating systems implement a more general mechanism called extended attributes (EAs). Extended attributes are name/value pairs associated permanently with file system objects, similar to the environment variables for a process [Gru03].

EXAMPLE RESOLVED
To enforce access control, we create a policy decision point and its corresponding policy enforcement points, which are responsible for intercepting and controlling accesses to the documents. For each document, we provide the policy decision point with a list of the users authorized to access the document and how (read or write). At access time, the policy decision point is able to search the list for the user. If the user is on the list with the proper access type, it can grant access to the document, otherwise it will refuse access.

In our distributed system, we make sure that only authenticated users — that is, users who provided a valid credential — can make requests.

CONSEQUENCES
The ACCESS CONTROL LIST pattern offers the following benefits:

 Because all authorizations for a given object are kept together, we can go to the requested object and find out if a subject is there. This is much quicker than searching the whole matrix.
 The time spent accessing an ACL is less than the time that would have been spent accessing a centralized matrix.
 Access to unauthorized objects using forged requests on behalf of legitimate subjects is not possible, because we make sure that the requests are from only authenticated subjects.
The pattern also has the following potential liabilities:

 The administration of the subjects is rendered more difficult: the deletion of a subject may imply a scan of all ACLs, although this can be done automatically.
 When the environment is heterogeneous, it needs to be adapted to each type of PEPs. PDPs and PAPs must be implemented in a different way, adding an additional development cost.
KNOWN USES
 Operating systems such as Microsoft Windows (from NT/2000 on), Novell’s NetWare, Digital’s OpenVMS and UNIX-based systems use ACLs to control access to their resources.
 In Solaris 2.5, file ACLs allow a finer control over access to files and directories than the control that was possible with the standard UNIX file permissions. It is possible to specify specific users in an ACLEntry. It is also possible to modify ACLs for a file testfile by using the setfacl command in a similar way to the chmod command used for changing standard UNIX permissions:
setfacl -s u::rwx,g::—,o::—,m:rwx,u:user1:rwx,u:user2:rwx testfile
 IBM Tivoli Access Manager for e-businesses uses ACLs to control access to the web and application resources [IBMc].
 Cisco IOS, Cisco’s network infrastructure software, provides basic traffic filtering capabilities with ACLs [Cisa].
SEE ALSO
 The PEP and PDP come from the previous pattern in this chapter. The CAPABILITY pattern (below) is another way to implement the Access Matrix.
 Access Matrix and RBAC [Fer01a] are models that can be implemented using ACLs.
 PEP is just a Reference Monitor [Fer01a].
 A variant exists oriented to centralized systems, Policy Enforcement, which leverages ad hoc data structures to enhance efficiency [Zho02].
 Acegi is a security framework for Java, used to build ACLs [Sid07].
6.7 Capability
The CAPABILITY pattern allows controlled access to objects by providing a credential or ticket to a subject to allow it to access an object in a specific way. Capabilities are given to the principal.

EXAMPLE
We are designing a system that allows registered users to read or modify confidential documents. We need to verify that a specific user can access a confidential document in an efficient and secure manner. In particular, we worry that if the parts of our system that deal with access control are too large and/or distributed, they may be compromised by attackers.

CONTEXT
Distributed systems in which access to resources must be controlled. The systems have a policy decision point and its corresponding policy enforcement points that enforce the access policy. A system is composed of subjects that need to access resources to perform their tasks. In the system, not every subject can access any object: access rights are defined and can be modeled as an access matrix, in which each row represents a subject and each column represents an object. An entry of the matrix is indexed by a specific subject and a specific object, and lists the types of actions that this subject can execute on this object. The system’s implementation is vulnerable to threats from attackers that may compromise its components.

PROBLEM
In some of these systems the number of subjects and/or objects can be large. In this case, the direct implementation of the access matrix can use significant amounts of storage, and the time to search a large matrix can be significant.

In practice, the matrix is sparse. Subjects have rights on few objects and thus most of the entries are empty. How can we implement the access matrix in a space- and time-efficient way?

The solution to this problem must resolve the following forces:

 The matrix may have many subjects and objects. Finding the rule that authorizes a specific request to an object may take a lot of time (unordered entries).
 The matrix can be very sparse, and storing it as a matrix would require storing many empty entries, thus wasting space.
 Subjects and objects may be frequently added or removed. Making changes in a matrix representation is inefficient.
 The time spent for accessing a centralized access matrix may result in an additional overhead.
 A request received by a policy enforcement point indicates the requester identity, the requested object and the type of access requested. The requester identity, in particular, is controlled by the requester, and so may be forged by a malicious user.
 The size of the units that can create and/or modify the policies (such as policy administration points) has an impact on the security of the system. Minimizing their size will reduce their chance of being compromised by attackers.
SOLUTION
Implement the access matrix by issuing a set of capabilities to each subject. A capability specifies that the subject possessing the capability has a right on a specific object. Policy enforcement points and the policy decision point of the system enforce the access policy by checking that the capability presented by the subject at the access time is authentic, and by searching the capability for the requested object and access type. Trust a minimum part of the system – create a unique capability issuer that is responsible for issuing the capabilities. The capabilities must be implemented in a way that allows the policy decision point to verify their authenticity, so that a malicious user cannot forge one.

STRUCTURE
Figure 6.11 illustrates the solution. In order to protect the Objects, a CapabilityProvider, the minimum trusted part of our system, issues a set of Capabilities to each Subject by using a secure channel. A Capability contains a set of Rights that the Subject can perform on a specific Object. A Subject accesses an Object only if a corresponding Right exists in one of the Subject’s Capabilities. At execution time, the PDP is responsible for checking the Capability’s authenticity and searching the Capability for both the requested Object and the requested accessType in order to make an access decision.

Figure 6.11: Class diagram for the CAPABILITY pattern


DYNAMICS
Figure 6.12 shows a sequence diagram describing the typical use case of ‘Request object access’. The Subject requests access to an Object by including a corresponding Capability. The request is intercepted by a PEP, which forwards the request to the PDP. It can then check that the Capability holds the accessType requested by the Subject.

Figure 6.12: Sequence diagram for the use case ‘Request object access’


IMPLEMENTATION
Since a capability must be unforgeable and unmodifiable, it can be implemented as hardware or software:

 As hardware:
 Tags. Tagging allows for the categorization of each word as data or a capability. Then no copying should be allowed from capability to data or vice versa, no arithmetic operation should be allowed on capabilities. A disadvantage of this method is the memory waste by using tags.
 Segmentation. Whole segments of memory are used exclusively for capabilities or for data. No operation should be allowed between partitions of different types. A disadvantage of this is that many processes may need two segments.
 As software:
 Cryptography. Usually used, the capabilities may be encrypted by the capability issuer’s key.
EXAMPLE RESOLVED
To enforce access control, we create a policy decision point and its corresponding policy enforcement points that are responsible for intercepting and controlling accesses to confidential documents. When a user logs on to the system, a robust token issuer provides a set of tokens that indicate which confidential documents are authorized. Tokens are digitally signed so that they can’t be created or modified by users. At request time, a user wishing to access a confidential document presents its token to the policy enforcement point, and then to the policy decision point, which grants them access to the document. If a user does not present a token corresponding to the document and the access mode, access is refused.

CONSEQUENCES
The CAPABILITY pattern offers the following benefits:

 Because the capability is sent together with the request, the time spent for accessing an authorization is much less than the time that would have been spent searching a whole matrix, or searching an access control list (ACL).
 The time spent accessing a capability at request time is less than the time that would have been spent accessing a centralized matrix.
 The part of the system that we need to trust is minimal. The capability provider is only responsible for issuing capabilities to the right users at an initial time.
 It is harder for malicious users to forge or modify capabilities, since a capability provides a way to verify its authenticity.
The pattern also has some potential liabilities:

 The administration of the objects is more difficult: The addition of an object implies the issuing of capabilities to every authorized user.
 When the environment is heterogeneous, the administration of the rights is more complex. There is no straightforward way to revoke a right, since users are in control of the capabilities they have acquired. A solution could be to add a validity time to each capability, or through indirection, or by using virtual addresses [And08].
 The right is transferable: that is, a capability can be stolen and replayed by (or given to) a malicious user. (This is not the case in OSs in which accesses to the capabilities are also controlled by the TCB, but those need the support of special hardware.)
KNOWN USES
 Most of the capability-based systems are operating systems. Usually hardware assistance is needed; for example, capabilities are placed in special registers and manipulated with special instructions (Plessey P250), or they are stored in tagged areas of memory (IBM 6000).
 Many distributed capability-based systems have been researched and described [Joh85] [Don76] [San96] [Amo96]. Among those, Amoeba [Amo96] is a distributed operating system in which multiple machines can be connected together. It has a microkernel architecture. All objects in the system are protected using a simple scheme. When an object (representing a resource) is created, the server doing the creation constructs a capability in the form of an 128-bit value and returns it to the caller. Subsequent operations on the object require the user to send its capability to the server to both specify the object and to prove the user has permission to manipulate the object. Capabilities are protected cryptographically to prevent tampering. The Symbian operating system uses capabilities [Hea06].
SEE ALSO
 The PEP and PDP are from the ACCESS CONTROL LIST pattern (page 91). The ACCESS CONTROL LIST pattern is another way to implement the access matrix.
 Capabilities can be implemented into the VAS (virtual address space) using segmentation.
 The PEP is just a Reference Monitor [Fer01a].
 Access Matrix, RBAC [Fer01a] are models that can be implemented using ACLs. Credentials [Mor06a] are a type of capability.
6.8 Reified Reference Monitor
ALSO KNOWN AS INTERCEPTING FILTER, APPLICATION CONTROLLER
The REIFIED REFERENCE MONITOR pattern describes how to force authorizations when a subject requests a protection object and provide the subject with a decision.

In a computational environment in which users or processes make requests for data or resources, this pattern describes how to define an abstract process that intercepts all requests for resources from subjects and checks them for compliance with authorizations.

CONTEXT
A multiprocessing environment in which subjects request protection objects to perform their functions and access resources based on a decision made by a reference monitor.

PROBLEM
Not enforcing the defined authorizations is the same as not having them: subjects can perform all types of illegal actions. Any user could read any file, for example. How can we control the subjects’ actions?

Also, an access decision can be sometimes more complex than a Boolean response; for example, when a user wants to access a database type: they may be authorized to access a subset of the data requested as per the rules. In this case, the reference monitor communicates with the subject and the decision is not Boolean – ‘yes’ or ‘no’: it can be either a display on the screen, some statement, or maybe negation. In many cases there is also the need to keep decisions in memory. If the same subject requests the same object again, the system should not spend time in re-deciding, as it affects performance.

The solution to this problem must resolve the following forces:

 Defining authorization rules is not enough: they must be enforced whenever a subject makes a request for a protection object.
 There are many possible implementations: we need an abstract model of enforcement.
 Decisions should be sent to the subject, as they can be more complex than mere Boolean decisions. By defining set of attributes for decisions, we can make the reference monitor more flexible.
SOLUTION
Define an abstract process that intercepts all requests for resources, checks them for compliance with authorizations, makes decisions based on these authorization rules, and stores the decisions, including their attributes (Figure 6.13).

Figure 6.13: The concept of the Reference Monitor


Figure 6.14 shows the class diagram for a reified Reference Monitor. In this figure SetofAuthorizationRules denotes a collection of authorization rules organized in some convenient way. Figure 6.15 shows a sequence diagram illustrating how checking is performed. An executing subject (ActualSubject) requests some type of access to a ProtectionObject. The ReferenceMonitor intercepts the request and searches in the set of authorization rules for a matching Authorization (rule). After the search a Decision is created. If positive, the request proceeds to access the ProtectionObject.

Figure 6.14: Class diagram for the REIFIED REFERENCE MONITOR pattern


Figure 6.15: Sequence diagram for enforcing security of requests


CONSEQUENCES
The REIFIED REFERENCE MONITOR pattern offers the following benefits:

 If all requests are intercepted we can make sure that they comply with the rules.
 The subject has better understanding of the decision made by the reference monitor to grant or deny its request.
 Implementation has not been constrained by using this abstract process.
The pattern also has the following potential liabilities:

 Specific implementations (concrete Reference Monitors) are needed for each type of resource. For example, a file manager controls requests for files.
 Checking each request and making decision may result in unacceptable performance loss. We may need to perform some checks at compile time, for example, and not repeat them at execution time. Another possibility is to factor out checks, for example when opening a file, or having trusted processes that are not checked.
 We may have to keep decisions in memory, so that when the same subject requests the same protection object, we already know the decision and do not compromise performance in making it again.
KNOWN USES
Most modern operating systems implement this concept, for example Solaris 9, Windows 2000, AIX and others. The Java Security Manager is another example.

SEE ALSO
 This pattern is a generalization of the Reference Monitor pattern we described in [Sch06b]. Some patterns combine it with the AUTHORIZATION, ROLE-BASED ACCESS CONTROL or DAC patterns [Kim06], but we feel that it is better to separate it so that it can be combined with other models for the authorization rules.
 The Java community has rediscovered this pattern and call it Intercepting Filter [Rad04] or Application Controller [OWAb].
6.9 Controlled Access Session
The CONTROLLED ACCESS SESSION pattern describes how to provide a context in which a subject (user, system) can access resources with different rights without need to reauthenticate every time they access a new resource.

EXAMPLE
Lisa is a secretary in a medical organization who sometimes helps with patient tests in the laboratory. As a secretary she has access to patients’ information such as name, address, social security number and so on. This is necessary so that she can bill them and their insurance companies. In the lab she has access to anonymized patient test results. Combining the accesses provided by her two jobs in one window allows her to associate test results and patients’ names, which violates patient privacy.

CONTEXT
Any environment in which we need to control access to computing resources and in which users can be classified according to their jobs, groups, departments, assignments or tasks.

PROBLEM
A given user may be authorized to access a system because they need to perform several functional activities. However, for a particular access, only those privileges should be active that are necessary to perform the intended task. This is an application of the principle of least privilege, and is necessary to prevent the user from misusing the system, either intentionally, accidentally by performing an error, or without knowledge and tricked to do so, for example through a Trojan Horse attack. Additionally, it potentially restricts damage in the case of session hijacking: a successful attack process would not have all the privileges of a user available, only the active subset.

The solution to this problem must resolve the following forces:

 Subjects may have many rights directly or indirectly through the execution contexts they need for their tasks. Using all of them at one time may result in conflicts of interest and security violations. We need to restrict the use of those rights depending on the application or task the subject is performing.
 In the context of an interaction we can make access to some functions implicit, thus facilitating the use of the system and preventing errors that may result in vulnerabilities. For example, some editors or other tools could be implicitly available in some sessions.
 It is not convenient to make subjects reauthenticate every time they request a new resource. Once the subject is authenticated, this condition should remain valid during the whole session.
SOLUTION
Define a unit of interaction, a session, which has a limited lifetime, for example between login and logoff of a user, or between the beginning and the end of a transaction. When a user logs on and after authentication, the session activates some execution contexts with only a subset of the authorizations they possess. This should be the minimum subset that is needed for the user or transaction to perform the intended task. Only those rights are available within the session. A subject can be in several sessions at the same time; however, in every session only the necessary rights are active.

STRUCTURE
Figure 6.16 shows the class model of the CONTROLLED ACCESS SESSION pattern. The classes Subject and Session have obvious meanings. The class ExecutionContext contains the set of active rights that the subject may use within the session.

Figure 6.16: Class model for the CONTROLLED ACCESS SESSION pattern


DYNAMICS
Figure 6.17 shows the use case ‘Open (activate) a session’. A subject logs on and the logon interface authenticates it. The box with a double arrow indicates some authentication dialog or protocol. After the subject is authenticated, the interface creates a session object and returns a handle to the subject.

Figure 6.17: Sequence diagram for the use case ‘Open a session’


IMPLEMENTATION
Based on institution and application policies, define which contexts (implying specific rights) should be used in each task and grant them to the corresponding subject. The rights should be selected using the least privilege principle, and there should be no contexts with excessive rights; for example, the administrator rights should be divided into smaller sets.

EXAMPLE RESOLVED
Lisa can log on a secretary or as a lab assistant, but she cannot combine these activities in one session. Now she cannot relate test results to patients’ names.

CONSEQUENCES
The CONTROLLED ACCESS SESSION pattern offers the following benefits:

 We can give only the necessary rights to each execution context, according to its function, and we can invoke in a session only those contexts that are needed for a given task.
 We can exclude combinations of contexts that might result in possible access violations or conflicts of interest.
 Any functions can be made implicit in a session.
 Once a subject starts a session it doesn’t have to be reauthenticated: its status is kept by the session.
The pattern also has the following potential liabilities:

 If we need to apply fine-grained access, it might be inefficient to include many contexts in order to perform complex activities.
 Using sessions may be confusing to the users.
KNOWN USES
 Session Access is part of the RBAC standard proposal by NIST, which has been adopted by the American National Standards Institute, International Committee for Information Technology Standards (ANSI/INCITS) as ANSI INCITS 359-2004 [Fer01b].
 Multics [Sum97] used execution contexts (based on projects) to limit access rights.
 Session Access is implemented in the security module CSAP [Dri03] of the Webocrat system in conjunction with an RBAC policy.
 Views in relational databases can be used to define sets of rights. Controlling the use of views by users can control their use of rights in sessions. This is done for example in Oracle and DB2, where SQL can be used to define restricted views [Elm03].
SEE ALSO
 The Access Session pattern is used in the SESSION-BASED ROLE-BASED ACCESS CONTROL pattern (page 107) and Attribute-Based Access Control [Pri04] patterns.
 The Session pattern of [Yod97] created a session object that defined a namespace to hold all the variables that need to be referenced by many objects.
 Peter Sommerlad remade this pattern as a Security Session [Sch06b], intended to prevent a user having to be reauthenticated every time they access a new object.
 Abstract Session [Pry00] is a pattern with a similar objective to Security Session: when an object’s services are invoked by clients, the server object may have to maintain state for each client. The server creates a session object that encapsulates state information for the client, and returns a pointer to the session object.
However, none of these patterns considers limitation of rights. Our pattern is an extension of these patterns, concentrating all its security functions and emphasizing the function of a session as a limiter of rights.

6.10 Session-Based Role-Based Access Control
The SESSION-BASED ROLE-BASED ACCESS CONTROL pattern allows access to resources based on the role of the subject, and limits the rights that can be applied at a given time based on the roles defined by the access session.

EXAMPLE
John is a developer on a project. He is also a project leader for another project. As a project leader he can evaluate the performance of the members of his project. He combines his two roles and adds several flattering evaluations about himself in the project where he is a developer. Later, his manager, thinking that the comments came from the project leader of the project on which John is a developer, gives John a big bonus.

CONTEXT
Any environment in which we need to control access to computing resources, in which users can be classified according to their jobs or their tasks, and in which we assign rights to the roles needed to perform those tasks.

We assume the existence of a Session pattern that can be used for the solution.

PROBLEM
In an organization a user may play several roles. However, for each access the user must act only within the authorizations of a single role (that is, within the context of the role) or combinations of roles that do not violate institution policies. How can we force subjects to follow the policies of the institution when using their roles?

In addition to the forces defined for the CONTROLLED ACCESS SESSION pattern, the solution to this problem must resolve the following forces:

 People in institutions have different needs for access to information, according to their functions. They may have several roles associated with specific functions or tasks.
 We want to help the institution to define precise access rights for its members so that the least privilege policy can be applied when they perform specific tasks.
 Users may have more than one role and we may want to enforce policies such as separation of duty, where a user cannot be in two or more specific roles in the same session.
SOLUTION
A subject may have several roles. Each role collects the rights that a user can activate at a given moment (execution context), while a session controls the way in which roles are used, and can enforce role exclusion at execution time.

STRUCTURE
The structure of the SESSION-BASED ROLE-BASED ACCESS CONTROL pattern is shown in the class diagram in Figure 6.18. The class Role is an intermediary between Subject and Object, holding all authorizations a user possesses while performing the role, and acts here as an execution context. Within a Session, only a subset of the roles assigned to a Subject may be activated, just those necessary to perform the intended task. Roles may be composed according to a Composite pattern [Gam94], in which higher-level roles acquire (inherit) rights from lower-level roles.

Figure 6.18: Class diagram for the SESSION-BASED ROLE-BASED ACCESS CONTROL pattern


DYNAMICS
Figure 6.19 shows a sequence diagram for the use case ‘Request access to an object’. A Subject has already opened a Session (see Figure 6.17 on page 106) and requests access to an object in a specific way (accessType). The session uses the corresponding ReferenceMonitor, which in turn checks whether the rights of the Session roles allow the access. If so, the access is permitted.

Figure 6.19: Sequence diagram for the use case ‘Request access to an object’


IMPLEMENTATION
1 Determine the roles the system should contain (role catalog), according to the user functions or tasks.
2 Collect lists of incompatible roles and use these lists when a session is started (static constraints). These constraints can be defined using OCL or some other formal language as additions to the class diagram of the pattern.
3 Determine the number of roles which may be active within a session (dynamic constraints).
4 When a user opens a session, they must declare what roles they intend to use, and the system will open the corresponding session, or refuse to do so in the case of conflicts.
See [Fer06e] for an example of a real implementation.

EXAMPLE RESOLVED
When John logs on in the project where he is a developer, he only gets the rights for a developer and cannot add evaluations. When he logs on in the project where he is a project leader he can only evaluate the members of his group. He cannot combine the rights of his role in the same session, and now he only gets legitimate evaluations.

CONSEQUENCES
In addition to the benefits mentioned for CONTROLLED ACCESS SESSION (page 104), additional benefits of the SESSION-BASED ROLE-BASED ACCESS CONTROL pattern are:

 Sessions may include all needed roles for those subjects authorized for some task.
 Users can activate more than one session at a time for functional flexibility (some tasks may require multiple roles).
 Fine-grained rights can be assigned to roles to enforce a need-to-know policy.
 When a session is open, we can exclude roles that violate institution policies.
The pattern has the following potential liabilities

 Additional conceptual complexity is required to define which roles can be used together and which should be mutually exclusive.
 User confusion if they have to use several roles to perform their work.
KNOWN USES
 The structure and dynamics of a Session-Based RBAC are implemented in the security module CSAP [Dri03] of the Webocrat system. Webocrat is a portal supporting E-Democracy which was developed within the European Webocracy project (FP5IST-1999-20364) between 2000-2003.
 Views in relational databases can be used to define sets of rights. Controlling the use of views by roles can control the use of rights in sessions. In both Oracle and DB2 SQL can be used to define restricted views based on roles [Elm03].
SEE ALSO
This pattern is a combination of the CONTROLLED ACCESS SESSION pattern (page 104) and the RBAC pattern [Sch06b]. As indicated earlier, structuring of roles can be represented by a Composite pattern. A Reference Monitor pattern is needed to enforce the use of rights during execution.

6.11 Security Logger and Auditor
ALSO KNOWN AS AUDIT TRAIL
The SECURITY LOGGER AND AUDITOR pattern describes how to keep track of users’ actions in order to determine who did what and when. It logs all security-sensitive actions performed by users and provides controlled access to records for audit purposes.

EXAMPLE
A hospital uses RBAC to define the rights of its employees. For example, doctors and nurses can read and write medical records and related patient information (lab tests and medicines). When a famous patient came to the hospital, one of the doctors, who was not treating him, read his medical record and leaked this information to the press. When the leak was discovered there was no way to find out which doctor had accessed the patient’s records.

CONTEXT
Any system that handles sensitive data, in which it is necessary to keep a record of access to data.

PROBLEM
How can we keep track of users’ actions in order to determine who did what and when? The solution to this problem must resolve the following forces:

 Accuracy. We should faithfully record what a user or process has done with respect to the use of system resources.
 Security. Any information we use to keep track of what the users have done must be protected. Unauthorized reading may reveal sensitive information. Tampering may erase past actions.
 Forensics. When a misuse of data occurs, it may be necessary to audit the access operations performed by users to determine possible unauthorized actions, and maybe trace the attacker or understand how the attack occurred.
 System improvement. The same misuses may keep occurring; we need to learn from past attacks.
 Compliance. We need a way to verify and to prove to third parties that we have complied with institution policies and external regulations.
 Performance. We need to minimize the overhead of logging.
SOLUTION
Each time a user accesses some object, we record this access, indicating the user identifier, the type of access, the object accessed and the time when the access happened.

The database of access entries must have authentication and authorization systems, and possibly an encryption capability.

STRUCTURE
In Figure 6.20 User operations are logged by the LoggerAuditor. The LoggerAuditor keeps the Log of user accesses, in which each access is described by a LogEntry. The security administrator (SecAdmin) activates or deactivates the Log. The Auditor can read the Log to detect possible unauthorized actions.

Figure 6.20: Class diagram of the SECURITY LOGGER AND AUDITOR pattern


DYNAMICS
Possible use cases include ‘Log user access’, ‘Audit log’, ‘Query log database’.

A sequence diagram for the use case ‘Log user access’ is shown in Figure 6.21. The User performs an operation to apply an access type on some object: operation (accessType, object). The LoggerAuditor adds an entry with this information, and the name of the user, to the Log. The Log creates a LogEntry, adding the time of the operation.

Figure 6.21: Sequence diagram for the use case ‘Log user access’


IMPLEMENTATION
The class diagram shown in Figure 6.20 provides a clear guideline for implementation, since its classes can be directly implemented in any object-oriented language. We need to define commands to activate or deactivate logging, apply filters, indicate devices to be used, allocate amount of storage and select security mechanisms. One can filter some logging by selecting users, events, importance of events, times and objects in the filters. Administrative security actions, for example account creation/deletion, assignment of rights and others, must also be logged.

Logging is performed by calling methods on the LoggerAuditor class. Every non-filtered user operation should be logged. Logged messages can have levels of importance associated with them.

Audit is performed by an auditor reading the log. This can be complemented with manual assessments that include interviewing staff, performing security vulnerability scans, reviewing application and operating system access controls and analyzing physical access to the systems [sau]. The Model-View-Controller pattern can be used to visualize the data using different views during complex statistical analysis of the log data.

EXAMPLE RESOLVED
After the incident, the hospital installed a SECURITY LOGGER AND AUDITOR, so in the future such violations can be discovered.

CONSEQUENCES
The SECURITY LOGGER AND AUDITOR pattern offers the following benefits:

 Security. It is possible to add appropriate security mechanisms to protect recorded data, for example access control and/or encryption.
 Forensics. The pattern enables forensic auditing of misused data objects. Records of access can be used to determine whether someone has maliciously gained access to data. This pattern can also be used to log access to data objects by system processes. For example, malicious code planted in the system can be tracked by finding processes that have misused objects.
 System improvement. By studying how past attacks happened, we can improve the system security.
 Compliance. Auditing a log can be used to verify and prove that institutional and regulatory policies have been followed.
 Performance. We can reduce overhead by parallel or background logging. We can also not log some events not considered significant. Finally, we can merge this log with the recovery log, needed for possible rollback.
The pattern has the following potential liabilities:

 It can incur significant overhead, since each object access has to be logged.
 A decision must be made by software designers as to the granularity at which objects are logged. There is a trade-off between security and performance.
 It is not easy to perform forensic analysis, and specialists are required.
 Protecting the log adds some overhead and cost.
VARIANTS
Most systems have a system logger, used to undo/rollback actions after a system crash. That type of logger has different requirements, but sometimes is merged with the security logger [SAP09]. System logs are of interest to system and database administrators, while security logs are used by security administrators, auditors and system designers.

Another variant could include the automatic raising of alarms by periodic examination of the log, searching records that match a number of rules that characterize known violations. For example, intrusion detection systems use this variant.

We can also add logging for reliability, to detect accidental errors.

KNOWN USES
 Most modern operating systems, including Microsoft Windows [Smi04], AIX [aix10], Solaris and others have security loggers.
 SAP uses both a security audit log and a system log [SAP09].
SEE ALSO
 The Secure Logger is a pattern for J2EE [Ste06]. It defines how to capture application-specific events and exceptions to support security auditing. This pattern is mostly implementation-oriented and does not consider the conceptual aspects discussed in our pattern. It should have been called a ‘security logger’, because it does not include any mechanisms to protect the logged information.
 Martin Fowler has an Audit Log analysis pattern [Fow] for tracking temporal information. The idea is that whenever something significant happens, you write some record indicating what happened and when it happened.
 Patterns for authentication (Chapter 5): how can we make sure that a subject is who they say they are?
 AUTHORIZATION (page 74) describes how we can control who can access to which resources, and how, in a computer system.