# 第 3 章 安全的系统开发方法
CHAPTER 3
A Secure Systems Development Methodology
By three methods we may learn wisdom: first, by reflection, which is noblest; second, by imitation, which is easiest; and third by experience, which is the bitterest.

Confucius

3.1 Adding Information to Patterns
A big problem for designers is to know where to apply the patterns. For an expert on security this aspect should not be a problem, but for a designer with little experience of security it can be a daunting task. Guiding the designer in the selection of patterns along the development lifecycle is very important in getting patterns accepted and used by developers.

As a possible approach to simplifying the use of patterns by designers, we can define extended patterns that include more information about their use:

 Secure semantic analysis patterns (SSAPs). In this approach a SAP is made secure by adding security patterns after analyzing its use cases and its possible threats. A SAP is a pattern combining a set of basic use cases [Fer00]. For example, we produced a set of secure functions for law firms [Fer07c]. The work described in [Rod07] is also related to this topic.
 Enterprise security patterns (ESPs) [Mor12]. An enterprise security pattern combines a wide range of items describing generic enterprise security architectures that protect a set of information assets in a specific context. They are a more comprehensive type of pattern that can handle more threats, to facilitate the selection and tailoring of security policies, patterns, mechanisms and technologies for designers when building enterprise security architectures.
 Tags [Ysk 12]. Tags include the security objective, the pattern applicability, trade-off labels (impact on other concerns) and relationships between patterns.
Another approach is to define a complete methodology that guides the designer at each step. [Uzu 12c] surveys the methodologies that have been proposed up to now. Any methodology should start from some basic principles, of which the two most important are:

 Security constraints should be defined at the highest layer, where their semantics are clear, and propagated to the lower levels, which enforce them.
 All the layers of the architecture must be secure.
These principles fit well with the ‘security context’, defined in [Sch03], a set of lifecycle phases and hierarchical layers.

3.2 A Lifecyle-Based Methodology
There is already consensus that security must be applied throughout the complete lifecycle: adding security at the end of the development lifecycle has been shown to be insufficient. This means that every methodology for building secure systems, using patterns or not, must consider all stages of the lifecycle. We understand the lifecycle to encompass the use of the platform, not just the application levels. Security depends on all levels and must be considered from the beginning. Our use of patterns is guided by these principles. We can define patterns at all levels, which allows a designer to ensure that all levels are secured, and also makes propagating high-level constraints to lower levels easier.

A better approach is extending a development process to incorporate security in all the stages of the lifecycle: this makes it more acceptable to practitioners. The most common lifecycle process approaches are the Rational Unified Process (RUP) and Agile methodologies [Bra 10]. Both have several variants. We use the standard RUP as the basis for our approach.

A fundamental idea in our proposed methodology is that security principles must be applied at every development stage, and that each stage can be tested for compliance with those principles. Some approaches to object-oriented development already emphasize tests at every stage.

We first sketch a secure software development cycle that we consider effective for building secure systems, then we discuss each stage in detail. Figure 3.1 shows a secure software lifecycle. The white arrows show where security can be applied, while the black arrows show where we can audit compliance with security policies:

Figure 3.1: Secure software lifecycle


 From the requirements stage we generate secure use cases.
 From the analysis stage we generate authorization rules that apply to the conceptual model.
 From the design stage we enforce rules through the architecture.
 In the implementation, deployment and maintenance stages, language enforcement of the countermeasures and rules is required.
Security verification and testing occurs at every stage of development. We describe the details of each stage below.

 Domain analysis stage. Conceptual models to cover areas relevant to the type of applications we are building are defined. Legacy systems are identified and their security implications analyzed. General security or reliability constraints can be applied at this stage.
 Requirements stage. Use cases define the required interactions with the system. Applying the principle that security must start from the highest levels, it makes sense to relate attacks to use cases and develop what we call secure use cases. We study each action within a use case and see which attacks are possible [Fer06c], then determine which policies would stop these attacks. From the use cases we can also determine the required rights for each actor, and thus apply a need-to-know policy. Note that the set of all use cases defines all the uses of the system; from all the use cases we can determine all the rights for each actor. The security test cases for the complete system can also be defined at this stage. Risk analysis should also be applied at this stage.
 Analysis stage. Analysis patterns, and in particular semantic analysis patterns, can be used to build the conceptual model in a more reliable and efficient way [Fer00]. Security patterns describe security models or mechanisms. We can build a conceptual model in which repeated applications of a security pattern realize the rights determined from use cases. In fact, analysis patterns can be built with predefined authorizations according to the roles in their use cases. Then we only need to additionally specify the rights for those parts not covered by patterns. We can then start to define mechanisms (countermeasures) to prevent attacks.
 Design stage. We express the abstract security patterns identified in the analysis stage in the design artifacts; for example interfaces, components, distribution and networking. Figure 3.2 shows some possible attacks to a system. Design mechanisms are selected to stop these attacks. User interfaces should correspond to use cases, and may be used to enforce the authorizations defined in the analysis stage. Secure interfaces enforce authorizations when users interact with the system. Components can be secured by using authorization rules for Java or .NET components.
Figure 3.2: Typical attacks to the layers of a system


  Distribution provides another dimension to which security restrictions can be applied. Deployment diagrams can define secure configurations to be used by security administrators. A multilayer architecture is needed to enforce the security constraints defined at the application level. In each level we use patterns to represent appropriate security mechanisms. Security constraints must be mapped between levels.
 Implementation stage. This stage requires reflecting in the code the security rules defined in the design stage. Because these rules are expressed as classes, associations and constraints, they can be implemented as classes in object-oriented languages. In this stage we can also select specific security packages or COTS components, for example a firewall product or a cryptographic package.
 Deployment and maintenance stages. Our methodology does not yet address issues in these stages. When the software is in use other security problems may be discovered by users. These problems can be handled by patching, although the amount of patching after applying our approach should be significantly smaller compared to current systems.
If necessary security constraints can be made more precise by using Object Constraint Language (OCL) [War03a] in place of textual constraints. Patterns for security models define the highest level of the architecture. At each lower level we apply the model patterns to specific mechanisms that enforce these models. In this way we can define patterns for file systems, web documents, J2EE components and so on. We can also evaluate new or existing systems using patterns. If a system doesn’t contain an embodiment of a correct pattern, it cannot support the corresponding secure model or mechanism.

3.3 Using Model-Driven Engineering
Metamodels describe sets of related concepts that are instantiated together (maybe partially) as part of a methodology or procedure to design a system. The UML class diagrams that describe the solutions of patterns are metamodels that are instantiated to apply security, reliability or some other property to the functional aspects of an application. Metamodels are useful for understanding the security design process and in the implementation of model-driven engineering (MDE) approaches. Alternatives or complements could be the use of ontologies: an ontology is a logical theory making precise the intended meaning of a formal vocabulary. Ontologies have been used to organize repositories for security patterns [Dri05].

Figure 3.3 shows a metamodel connecting threats and failures to patterns [Fer11c]. In the diagram, a threat can be neutralized by a security policy. Similarly, a failure can be neutralized by a reliability policy. Policies may also include regulations and institution policies. Security and reliability policies are realized by security and reliability patterns, respectively. A policy realization pattern is a pattern that realizes any type of policy and consists of a few classes and associations. Security and reliability patterns are special cases of policy realization patterns.

Figure 3.3: Metamodel for requirements and patterns


We have done some work on MDE [Del08], but we need to use it more to make this methodology easier to use, by automating parts of it and adding appropriate tools. [Del08] proposed a metamodel to go from the analysis to a design model. [Ysk08] presented a set of transformations for specific security requirements: delegation of execution, authorization and auditing, using a metamodel tailored for these requirements. A sub-product of MDE is traceability; it now becomes easier to trace back the effect of changes in the code.

It is clearly fundamental to have some methodology for applying the patterns. We considered three approaches here:

 Adding more information to each pattern.
 A stage-by-stage approach applied to an existing lifecycle process.
 Model-driven engineering, in which we transform models from stage to stage following metamodels and rules.