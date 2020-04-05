# 第 2 章 模式和安全模式

> CHAPTER 2 Patterns and Security Patterns

Each problem that I solved became a rule which served afterwards to solve other problems.

René Descartes, Discourse on Method

## 2.1 What is a Security Pattern?

A security pattern describes a solution to the problem of controlling (stopping or mitigating) a set of specific threats through some security mechanism, defined in a given context [Sch06b]. This solution needs to resolve a set of forces, and can be expressed using UML class, sequence, state and activity diagrams. A set of consequences indicate how well the forces were satisfied; in particular, how well the attacks were handled. A security pattern is not directly related to a vulnerability, but is directly related to a threat. The specific threat may be the result of one or more vulnerabilities, but the pattern is not intended to repair the vulnerability, but to stop or mitigate the threat.

Figure 2.1 shows a generic diagram illustrating the effect of the use of security patterns as deployed in a specific architecture. The sequence diagrams on the left of the figure indicate possible attacks (threats) to a context defined by a deployment diagram. For example, a context may include distributed systems, distributed systems using web services, or operating systems. Typical objects in the deployment diagram (O1, O2, O3) are instantiated from classes in the application class diagram (Classes A, B and C respectively for this example). SP1 denotes the security pattern solution that is able to stop or mitigate these threats. There may be more than one SP1 that can handle the threats. Theoretical models of security patterns can be found in [Was09] and in Chapter 8 of [Sch03].

Figure 2.1: A security pattern controlling two attacks

## 2.2 The Nature of Security Patterns

There are several ways to look at security patterns:

- As an architectural pattern. Security patterns can be considered a type of architectural pattern because they usually describe global software architecture concepts; for example, do we need authentication between two distributed units? We prefer this interpretation because security is a global property.
- As a design pattern. The fact that security can be considered an aspect of a software subsystem has made some groups consider them design patterns [Bla04][dou09]. We think that design patterns are code-oriented and security is an architectural property, but this view can be useful to analyze the effect of code structure on security.
- As an analysis pattern. Security constraints should be defined at the highest possible level, that is, at the conceptual model of the application. For example, we can define which users have which roles and what rights they need to perform their tasks. A conceptual, implementation-free definition of a security mechanism is, in effect, an analysis pattern. We use this approach in our methodology to define system-independent requirements, as described in Chapter 3.
- As a special type of pattern. We can add new sections, remove some sections from the standard template, or we can use a different notation and model. The problem with using special notations is that the designer needs to learn a new language to use the patterns, which is time-consuming and prone to error. This type of pattern is useful for researchers and security experts, but not so much for the average developer.

Can we describe principles as patterns? We agree with [Hey07a] that security principles, for example separation of duty, are in general too broad to be considered patterns. We do not consider less broad concepts, such as confidentiality or integrity, as patterns, because there are many ways to obtain such properties: a pattern should describe a single solution.

Two other interesting points are:

- Are security patterns styles or architectural patterns?
- Are security patterns the same as tactics?
  Styles are more application-oriented and describe the basic structure of an architecture [Tay10]; should we also have a catalog of security styles? We are not elaborating these points further here: we discuss some of them in [Fer12a].

Other varieties of security patterns include:

- Security design patterns. The Open Group used the style of [Gam94] to build security patterns [Bla04]. They also included some availability patterns in their catalog.
- A group at CERT took a more literal approach and built secure design patterns [Dou09], in which they added security to several of the patterns described in [Gam94].
- Patterns for enterprise models to define global security concerns [Sch06b]. These patterns include Asset Valuation, Threat Assessment, Security Needs Identification and others. These patterns can be useful for security governance.
- Some authors, for example [Ray04], consider patterns as parameterized templates or types that are instantiated in applications. The additional precision this provides reduces flexibility in applying the pattern.
- Jackson’s Problem Frames have been used as the basis for patterns for security requirements [Hat07].
- Security and Dependability (S&D) patterns describe implementations of specific security mechanisms, supported by a complete runtime framework [Gal09][ser] [Spa09].
- Mouratidis uses Secure Tropos, an approach to support multiple views of security, including organizational and external aspects [Mou06][set].
- Security usability patterns, patterns oriented to build good user interfaces for security [Mun09].
- [IBMb] describes five security patterns. They are very high-level and do not follow any standard template. They also define a simple methodology for their application.
- Privacy patterns: patterns to help users express their privacy concerns [Lob09][sad05]. These can be a useful complement to the patterns presented in this book.
- Enterprise Security Patterns (ESPs) [Mor12]. Chapter 3 discusses these in more detail.
- [Bas06] presents an approach to building secure systems in which designers specify system models along with their security requirements, and use tools to generate system architectures from the models automatically, including complete, configured access-control infrastructures. Their approach includes a combination of UML-based modeling languages with a security modeling language for formalizing access control requirements. They do not really use patterns, but their models are related to pattern models.
- Two security antipatterns are proposed by Kis [Kis02]. An antipattern indicates practices that should be avoided.

Having such variety can be confusing to potential users. Not all of these pattern varieties are mutually exclusive; some can be combined. For example, ESPs include the standard type of security patterns as components.

## 2.3 Pattern Descriptions and Catalogs

Patterns are usually described using a pattern template with predefined sections. This type of description permits systematic use of the pattern and provides a guideline for the pattern writer. The selection of template is an important issue, because it defines the type and characteristics of the information about the pattern. Patterns using ad hoc templates or no templates are difficult to incorporate into existing catalogs, and make the job of comparing and applying patterns much harder. In our patterns we use a template based on that of [Bus96], known as the POSA (Pattern Oriented Software Architecture) template. We show its use in describing a specific pattern below. Other templates used for security patterns include the ‘Gang of Four’ (GOF) template [Gam94], as used by the Open Group and some authors. That template is intended for design patterns, which are more code-oriented, and does not describe architectural aspects well. Some enhancements of security pattern templates have been proposed, which extend the GOF template with security aspects [Mor12][ysk06].

We have tried to provide a reasonable amount of information in our pattern descriptions, aimed at the needs of an application designer, although another audience could be product designers, for example designers of firewalls: our descriptions are probably insufficient for them, although they could be used as initial guidelines. Each pattern is described in an abstract form [Fer08a], independent of implementation details. For some patterns we also show concrete versions, for example for web services. The idea is that one should decide first, in the conceptual model, what security mechanisms are needed, then map them to concrete versions depending on the software choices. In one of our papers we define precise models for the templates that we use for our patterns [Was09]. We used those models to classify security patterns in [Was09] and [Van09].

Some authors describe patterns showing only their main idea, taking about half to one page and using only words. While such patterns may be useful for experienced designers to remind them of possibilities, they are not detailed or precise enough to fulfill the function of communicating experience and knowledge. We think that the solution section of the pattern should be expressed with enough detail and precision to allow a designer to use it. We prefer using an informal description with a diagram, followed by a UML class diagram to describe the information static structure, and several sequence diagrams describing the dynamics of the main use cases. CRC cards can be used to introduce the needed classes, but we don’t see them as necessary. Other UML diagrams may be convenient in complex cases, such as state charts and activity diagrams.

Patterns solutions expressed in UML can be implemented readily in appropriate languages such as Java, Smalltalk, C++, XML or C#. Making a solution very precise or formal is against the spirit of patterns, which are suggestions rather than plug-in solutions: a pattern needs to be tailored to satisfy the requirements of the application. If a formal solution is used, it is hard to know if the tailoring has affected the model of the solution. When more formality is needed, we prefer to add OCL annotations to class diagrams [War03a].

## 2.4 The Anatomy of a Security Pattern

This section uses a Packet Filter Firewall [Sch06b] as an example; each pattern section is preceded by a description of its purpose.

Every pattern starts with a thumbnail – called an ‘intent’ by some authors – a description of the problem it solves, and optionally a brief description of how it solves the problem.

The Packet Filter Firewall filters incoming and outgoing network traffic in a computer system based on packet inspection at the IP level.

### EXAMPLE

We then give an example of a problem situation where use of this pattern may provide a solution.

Our system has been attacked recently by a variety of hackers, including somebody who penetrated our operating system and stole our clients’ credit card numbers. Our employees are wasting time at work by looking at inappropriate sites in the Internet. If we continue like this we will be out of business soon.

### CONTEXT

We define the context in which the pattern solution is applicable. We may explain relevant characteristics of this context.

Computer systems on a local network connected to the Internet and to other networks with different levels of trust. A host in a local network receives and sends traffic to other networks. This traffic has several layers or levels. The most basic level is the IP level, made up of packets consisting of headers and bodies (payloads). The headers include the source and destination addresses as well as other routing information; the bodies include the message payloads.

### PROBLEM

We follow the context with a generic description of what happens when we don’t have a good solution. We also indicate the forces that affect the possible solution. (Here we show only a few forces.)

Some of the hosts in other networks may try to attack the local network through their IP-level payloads. These payloads may include viruses or application-specific attacks. We need to identify and block those hosts.

The solution to this problem must resolve the following forces:

- We need to communicate with other networks, so isolating our network is not an option. However, we do not want to take on a high risk for doing so.
- Any protection mechanism should be transparent to the users. Users should not need to perform special actions to be secure.
- The cost and overhead of the protection mechanism should be relatively low or the system may become too expensive to run.
- The attacks are constantly changing, so it should be easy to make changes to the configuration of the protection mechanism.

### SOLUTION

The solution section describes the idea of the pattern. A descriptive figure may help to visualize the solution.

A Packet Filter Firewall intercepts all traffic coming/going from a port P and inspects its packets (Figure 2.2). Those coming from or going to untrusted addresses are rejected. The untrusted addresses are determined from a set of rules that implement the security policies of the institution. A client from another network can only access the local host if a rule exists authorizing traffic from its address. Specific rules may indicate an address or a range of addresses. Rules may be positive (allow traffic from some address) or negative (block traffic from some address). Most commercial products order these rules for efficiency in checking. Additionally, if a request is not satisfied by any of the explicit rules, then a default rule is applied.

Figure 2.2: The idea of the packet filter firewall

### STRUCTURE

We next describe the structure (static view) of the solution and some dynamic aspects in the form of sequence diagrams for a use case.

Figure 2.3 shows the class diagram for an external host requesting access to a local host (a server) through a Packet Filter Firewall (PFFirewall). The institution policies are embodied in the objects of class Rule collected by the rule base RuleBase. The rule base includes data structures and operations to manage rules in a convenient way. The rules in this set are ordered and can be explicit or default.

Figure 2.3: Class diagram for Packet Filter Firewall pattern

### DYNAMICS

We describe the dynamic aspects of the Packet Filter Firewall using a sequence diagram for one of its use cases (Figure 2.4). There is a symmetrical use case, ‘Filtering an outgoing request’, which we omit for briefness. We also omit use cases for adding, removing or reordering rules because they are straightforward. (Some authors describe the use case scenario more informally; we also do this at times in this book.)

Figure 2.4: Sequence diagram for filtering a client’s request

Use Case: Filtering a Client’s Request – Figure 2.4

|                |                                                                                                                                                                                                 |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Actors         | A host in an external network (client).                                                                                                                                                         |
| Precondition   | An existing set of rules to filter the request must be in place in the firewall.                                                                                                                |
| Description    | 1 An external host requests access to the local host.                                                                                                                                           |
|                | 2 A firewall filters the request according to a set of ordered rules. If none of the explicit rules in the rule set allows or denies the request, a default rule is used for making a decision. |
|                | 3 If the request is accepted, the firewall allows access to the local host.                                                                                                                     |
| Alternate flow | The request is denied.                                                                                                                                                                          |
| Postcondition  | The firewall has accepted the access of a trustworthy client to the local host.                                                                                                                 |

### IMPLEMENTATION

The objective of this section is to describe what one should consider when implementing the pattern. This can be a set of general recommendations, or a sequence of what to do to use the pattern. It may include some sample code, if appropriate. It is possible to add details of how some products implement this pattern, for example how a particular firewall is implemented by a specific company.

1. Define an institution policy about network access, classifying sites according to our trust in them.
2. Convert this policy into a set of access rules. This can be done manually, which may be complex for large systems. An alternative is using an appropriate commercial product.
3. Write the rules for each firewall. Products such as Solsoft and others automatically propagate the rules to each registered firewall.
4. Configure the corresponding firewalls according to standard architectures. A common deployment architecture is the Demilitarized Zone (DMZ) [Sch06b].

Now we can see what happens in the example after the pattern solution has been applied.

### EXAMPLE RESOLVED

We were able to trace the addresses of our attackers and we got a firewall to block requests from those addresses from reaching our system. We also made a list of addresses of inappropriate sites and blocked access to them from the hosts in our network. All this reduced the number of attacks and helped control the behavior of some of our employees.

### CONSEQUENCES

The Consequences section indicates the benefits and liabilities of the solution embodied in this pattern. The benefits should match the forces in the Problem section. Benefits that do not correspond to any force may appear. For our example pattern a truncated list might be as shown below.

The Packet Filter Firewall Pattern offers the following benefits:

- A firewall transparently filters all the traffic that passes through it, thus lowering the risk of communicating with potentially hostile networks.
- It is easy to update the rule set to counter new threats.
- It is low cost, and is included as part of many operating systems and simple network devices such as routers.
- It offers good performance: it only needs to look at the headers of IP packets, not at the complete packet.

The Packet Filter Firewall Pattern has the following (possible) liabilities:

- The firewall’s effectiveness and speed may be limited due to its rule set (order of precedence). Addition of new rules may interfere with existing rules in the rule set; so a careful approach should be taken to adding and updating access rules.
- The firewall can only enforce security policies on traffic that goes through the firewall. This means that one must make changes to the network to ensure that there are no other paths into its hosts.
- An IP-level firewall cannot stop attacks coming through the higher levels of the network. For example, a hacker could put malicious commands or data in header data not used for routing, and in the packet contents.

### KNOWN USES

To accept this solution as a pattern, we should find at least three examples of its use in real systems. We occasionally break this rule, for example when we see that the solution is clearly generic.

This model corresponds to an architecture that is seen in commercial firewall products, such as:

- ARGuE (Advanced Research Guard for Experimentation), which is based on Network Associates’ Gauntlet Firewall.
- OpenBSD Packet Filtering Firewall, which is the basic firewall architecture for the Berkeley Software Distribution system.
- The Linux Firewall, which is the basic firewall architecture used with the Linux operating system.
- The Packet Filter Firewall is also used as an underlying architecture for other types of firewalls that include more advanced features.

### SEE ALSO

Finally, we relate our pattern to other known patterns. Those may be complementary patterns, variations of our pattern or extensions of it.

The Authorization pattern [Fer01a] defines the standard security model for the Packet Filter Firewall pattern. This pattern is also a special case of the Single Access Point pattern [Sch06b], and it is the basis for other, more complex, types of firewalls. The DMZ pattern [Sch06b] defines a way to configure this pattern in a network. This pattern can also be combined with the Stateful Inspection Firewall [Sch06b].

We also include a Variants section if appropriate.

## 2.5 Pattern Diagrams

Figure 2.5 describes the relationships of the patterns described in Chapter 4 using a pattern diagram in which rounded rectangles represent patterns and arrows indicate the contribution of a pattern to another. For example, IDENTITY PROVIDER creates identities to be used by an IDENTITY FEDERATION1.

Figure 2.5: Pattern diagram for identity management patterns

This diagram is based on those used in [Bus96], but we have used UML generalization notation to indicate patterns that are specializations of other patterns; for example, SAML ASSERTION is a specialized type of CREDENTIAL. We believe that pattern diagrams offer very important help for designers to select which patterns to use at a given point [Fer06d]. This value was confirmed in the experiment described in [Ysk 12]. Their use can be improved by illustrating with a tool that can display them at different stages of design [Fer06d].

## 2.6 How Can We Classify Security Patterns?

According to [Hey07a], in 2007 about 220 security patterns had been described, but the paper’s authors considered only 55% of them to be core patterns. [Haf11] considered 96 patterns to be core patterns. It is not clear how many security patterns have been written, because several are the same pattern with different names, or different patterns with the same name. Patterns can also be defined at different architectural levels, which may lead to several variants of the same pattern. We think of a computer system as a hierarchy of layers, in which the application layer uses the services of the database and operating system layers, which in turn execute on a hardware layer. In fact, this structure (Layers) is a pattern in itself [Bus96], and was reinterpreted as a security pattern in [Fer01a] and [Yod97].

[Avg05] classifies architectural patterns using the type of concerns they address, for example layered structure, data flow, adaptation, user interaction, distribution. We classified security patterns based on architectural levels and concerns [Fer08b]. For example, access control can be defined in the application and propagated to the database and to the operating system. Architecture levels and security concerns are two possible dimensions, both used in this book. Other classifications are discussed in [Haf06].

We refined the basic classifications through the use of a multi-dimensional matrix of concerns [Van09]. Each dimension of the matrix is a distinct list of concerns along a single axis, with a simple concept and a set of distinctions that define the categories. The categories along an axis or dimension should be easily understood and represent widely-used and accepted classifications related to that concept. For example, one dimension would correspond to lifecycle stages, covering domain analysis, requirements, problem analysis, design, implementation, integration, deployment (including configuration), operation, maintenance and disposal. The list of component source types forms another dimension. Types of security response could form yet a third dimension, covering avoidance, deterrence, prevention, detection, mitigation, recovery and investigation (forensics). Cells at the intersections of two or more dimensions represent a concern that is more specific than would be expressed by the list of classifications in any one dimension. For example, with two dimensions we can target security patterns for requirements when using COTS for outsourced components. Similarly, we can target security patterns for analysis and design with web services, and of those, more specifically, patterns that address detection or recovery.

Figure 2.6 illustrates a mapping of design patterns in two lifecycle phases and at different levels of architecture. Only a small sample of patterns is shown. While all of the patterns in the figure are applicable to Service Oriented Architecture, some apply more generally to other domains as well. We grouped the patterns within Design along a secondary dimension with Filtering, Access Control and Authentication. In the figure, we show patterns from the domain analysis phase, where the developer would find patterns that explain the domain standards and technologies later used in the design phase. A developer might also navigate to adjoining analysis phase cells (not shown) to look for general patterns on Filtering, Access Control and Authentication. While the patterns are found in these locations in the matrix, understanding their role in a system, and how they relate to one another, still requires a pattern language diagram and other tools and methods for pattern application.

Figure 2.6: A matrix of patterns

The patterns presented in this book are grouped by concern, and within that, by the architectural level at which they would be used.

## 2.7 Pattern Mining

While applying security patterns in a design may not require a deep knowledge of security, mining security patterns does require such knowledge: you need to understand what mechanisms exist to prevent threats and need some abstraction capability to see the common aspects of such mechanisms. This is how we have found most of our patterns. Designers working on a project where security is an important issue, in addition to using security patterns from a catalog, should also be able to discover new patterns by abstracting recurrent security problems they may encounter.

Schumacher proposes digging into security standards, in particular in the Common Criteria [Sch01], to find patterns by observing the recommendations and approaches to evaluation of this standard. Another approach is to write patterns describing the main aspects of security standards. The justification for this is that standards define generic architectures and all implementations of the standard reflect it. We have produced a variety of standards of this type [Fer 12c].

## 2.8 Uses for Security Patterns

The most common use for security patterns is to help application developers who are not security experts to add security in their designs. With the help of a good catalog and a tool for guidance, developers can select security pattern for a complex application. Other than ours, there are few methodologies that guide designers in selection of security patterns [Bla04][mou06].

A set of abstract security patterns can be a good description of the security requirements of a system [Che03][fer06c]. Requirements should not include any implementation details, and abstract patterns define the conceptual security needed without deciding about specific implementations.

Another important use is as guidelines for designers of security mechanisms to define the objectives or intended features of their products. For example, a designer of a new XML firewall can find the basic functions for such a device in the corresponding pattern, and use patterns describing security standards to define its support for them in the product.

Another use is in the evaluation of existing systems. This may be the most frequent application of security patterns in practice, when we need to reinforce a legacy system or need to evaluate a system we are acquiring.

Making complex standards understandable is another valuable use. We have distilled the essence of security standards for web services to make them much easier to understand than by reading the corresponding documents [Fer06a][fer 12c] [Has09b]. We also showed how to use these patterns to simplify the standards for use in mobile devices [Del07c].

Finally, security patterns are a useful tool for teaching security concepts [Fer05a]. Security models and mechanisms must be described in a precise and systematic way. Our experience with formalizing complex access control models has shown that the resulting expressions are not intuitive, require mathematical sophistication, and make it difficult to describe structural properties of the system. On the other hand, UML models are quite intuitive and can conveniently describe structural properties. However, they are less precise than formal methods. We can therefore take a middle ground, integrating formal and informal techniques, describing our models using UML notation enhanced with constraints expressed in OCL.

In the same way that general architectural patterns can be used for recording architectural decisions, we can use security patterns to record decisions we have made about handling security requirements [Fer07e].

The use of patterns for traceability is just starting to be explored.

## 2.9 How to Evaluate Security Patterns and their Effect on Security

There has been little work on evaluating patterns. How can one define their quality? What makes a good pattern? Patterns are normally evaluated by submitting them to one of the pattern conferences, such as Pattern Languages of Programs (PLoP) or one of its variants, EuroPLoP, Latin American PLoP or Asian PLoP. In these conferences, a pattern paper is developed with the help of a ‘shepherd’ and then discussed in a workshop. The pattern is then published and exposed for criticism, the intention being to produce a better quality pattern. Of course, the ultimate evaluation comes when developers use such patterns in their designs and find they are useful in producing a robust system. But because security patterns have not been used extensively in practice, it is hard to use experience to evaluate them. [Ysk 12] evaluated their use with a student experiment; they added annotations to the patterns to help in their selection and found this useful.

Formal modeling of patterns, combined with model checking, can prove some of the properties of a pattern’s solution. Patterns are also evaluated for understandability, how well they fit the context of the problem, how easy it is to tailor them to the requirements and how reusable they are. In practice, the evaluation applies mostly to the solution, but other aspects are just as important. Any pattern that has gone through all these steps is believed to be of good quality: this conclusion applies to security patterns as well.

Halkidis et al. evaluated 13 patterns from the Open Group catalog [Bla04]. They used as criteria how well the patterns followed some principles and the threats they could handle [Hal06]. The quality of pattern documentation is an aspect considered by [Hey07a], which also uses coverage of concerns as a further quality factor. We think that factors such as how well a pattern implies security principles and its coverage of concerns are inherent aspects, while documentation is not related to quality of the pattern itself: a bad pattern cannot be improved, while a bad description can be redone easily [Uzu 12a].

The evaluation of how patterns can improve security may be more useful. Security is a quality of system architectures and we need ways to evaluate the effect of patterns on improving this quality. But security is a quality for which there are no numerical measures. It can only be defined in a relative way with respect to another system, or by showing that a system satisfies some predefined security properties. In particular, we are developing a methodology for building secure systems based on adding security patterns along the lifecycle and in all the architectural layers of the system (described in Chapter 3).

Can we show that a system built in this way is secure in some sense? We have explored some issues about evaluating the security of a system built using security patterns [Fer 10a]. As far as we know, none of the secure development methodologies [Uzu 12c] analyzed the security of a complete system built using their approaches. Later, he evaluated the security risk of systems that used specific security patterns by seeing how well they handled a set of threats (the STRIDE set) [Hal08a].

Yskout et al tried to evaluate the use of patterns by an inventory [Ysk08] and by experiment [Ysk 12]. Heyman tried to evaluate whether a pattern was instantiated properly during design and was working appropriately at run time [Hey07a]. [Bre08] used an enterprise metamodel to perform quantitative risk evaluation.

## 2.10 Threat Modeling and Misuse Patterns

To design a secure system, we first need to understand the possible threats to the system. Without this understanding we may produce a system that is more expensive than necessary and that has a large performance overhead. We have proposed a systematic approach to threat identification, starting from the analysis of the activities in the use cases of the system and postulating possible threats [Fer06c]. This method identifies high-level threats such as ‘the customer can be an imposter’, but once the system is designed. we need to see how the chosen components could be used by the attacker to reach their objectives.

The misuse pattern describes, from the point of view of the attacker, how a type of attack is performed (what units it uses and how), analyzes ways of stopping the attack by enumerating possible security patterns that can be applied for this purpose, and describes how to trace the attack once it has happened by appropriate collection and observation of forensic data. It also describes precisely the context in which the attack may occur. We have produced several misuse patterns, some of which are described in Chapter 14. Misuse patterns should not be confused with attack patterns [Hog04][whi01], which are specific actions to take advantage of a vulnerability, for example producing a buffer overflow. Okubo defined two other types of patterns with similar objectives [Oku11].

## 2.11 Fault Tolerance Patterns

Reliability and fault tolerance aim to control accidental errors, not intentional attacks. However, some of their aspects are common to security, and of course systems that need security very often also need reliability. We have produced a few patterns of this type [Buc09a] and we are writing a survey of them. We also combined some of them to produce Secure Reliability and Reliable Security patterns [Buc11]. We have not included any of these patterns in this book, but some catalogs of security patterns, such as [Bla04], include several of them.

1We use this uppercase notation, for example ‘IDENTITY PROVIDER’, to refer to patterns in this book.
