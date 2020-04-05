# 第 9 章 安全操作系统体系结构和管理的模式
CHAPTER 9
Patterns for Secure OS Architecture and Administration
A great building must begin with the immeasurable, must go through measurable means when it is being designed, and in the end must be unmeasured.

Louis Kahn

9.1 Introduction
Operating systems act as an intermediary between the user of a computer and its hardware. The purpose of an operating system is to provide an environment in which users can execute programs in convenient and efficient manner [Sil08]. They control and coordinate the available resources to present an abstract machine with convenient features to the user. The architecture of the operating system organizes components to structure its functional and non-functional aspects. The security of operating systems is very critical, since they support the execution of all applications. Most of the reported attacks occur through the operating system. The security of individual execution-time actions such as process creation and memory protection is very important, and we presented patterns for these functions in Chapter 7 and Chapter 8. However, the general architecture of the operating system is also very important to the system’s ability to provide a secure execution environment.

Most operating systems use five basic architectures [Sil08] [Tan08]. One, the monolithic architecture, has little value for security and it is only mentioned as a possible variant of the modular architecture. We present here patterns representing these four architectures (Figure 9.1):

Figure 9.1: Pattern diagram of OS architectures, administration and file systems


 MODULAR OPERATING SYSTEM ARCHITECTURE. Separate the operating system’s services into modules, each representing a basic function or component. The basic core kernel only has the required components to start itself and the ability to load modules. The core is the one module always in memory. Whenever the services of any additional modules are required, the module loader loads the appropriate module. Each module performs a function and may take parameters.
 LAYERED OPERATING SYSTEM ARCHITECTURE. The overall features and functionality of the operating system are decomposed and assigned to hierarchical layers. This provides clearly defined interfaces between each section of the operating system and between user applications and the system functions. Layer i uses services of a lower layer i-1 and does not know of the existence of a higher layer, i+1.
 MICROKERNEL OPERATING SYSTEM ARCHITECTURE. Move as much of the operating system functionality as possible from the kernel into specialized servers, coordinated by a microkernel. The microkernel itself has a very basic set of functions. Operating system components and services are implemented as external and internal servers.
 VIRTUAL MACHINE OPERATING SYSTEM ARCHITECTURE. Provide a set of replicas of the hardware architecture (virtual machines) that can be used to execute multiple and possibly different operating systems with strong isolation between them.
We also include two other related patterns in this chapter:

 ADMINISTRATOR HIERARCHY. Many attacks come from the unlimited power of administrators. How can we limit the power of administrators? Define a hierarchy of system administrators with rights controlled using a ROLE-BASED ACCESS CONTROL (RBAC, page 78) pattern and assign rights according to their functions.
 FILE ACCESS CONTROL. How can we control access to files in an operating system? Apply the AUTHORIZATION pattern (page 74) to describe access to files by subjects. The protection object is now a file component that may be a directory or a file.
The four secure architectures appeared as patterns in [Fer05c], coauthored with Tami Sorgente. The Administrator Hierarchy pattern appeared in [Fer06f], coauthored with Tami Sorgente and Maria M. Larrondo-Petrie. Finally, the File Access Control pattern appeared in [Fer03b], coauthored with John Sinibaldi.

9.2 Modular Operating System Architecture
The MODULAR OPERATING SYSTEM ARCHITECTURE pattern describes how to separate operating system services into modules, each representing a basic function or component. The basic core kernel only has the required components to start itself and the ability to load modules. The core is the one module always in memory. Whenever the services of any additional modules are required, the module loader loads the appropriate module. Each module performs a function and may take parameters.

EXAMPLE
Our group is building a new operating system that should support various types of devices requiring dynamic services with a wide variety of security requirements. We want to dynamically add operating system components, functions and services, as well as tailor their security aspects according to the type of application. For example, a media player may require support to prevent copying of its contents, or a module for which a vulnerability alert has been issued could be removed.

CONTEXT
A variety of applications with diverse requirements that need to execute together, sharing hardware resources.

PROBLEM
We need to be able to add or remove functions easily so that we can accommodate applications with a number of security requirements. How can we structure the operating system functions for this purpose?

The solution to this problem must resolve the following forces:

 Operating systems for PCs and other types of uses require a large variety of plugins. New plugins appear frequently, and we need the ability to add and remove them without disrupting normal operation.
 Some of the plugins may contain malware; we need to isolate their execution so they do not affect other processes.
 We would like to hide security-critical modules from other modules to avoid possible attacks.
 Modules can call each other, which is a possible source of attacks.
SOLUTION
Define a core module that can load and link modules dynamically as needed.

STRUCTURE
Figure 9.2 shows a class diagram for this pattern. The KernelCore is the core of the modular operating system. A set of LoadableModules is associated with the KernelCore, indicating the modules that can be loaded according to their applications and the functions required. Any LoadableModule can call any other LoadableModule.

Figure 9.2: Class diagram for the MODULAR OPERATING SYSTEM ARCHITECTURE pattern


IMPLEMENTATION
1 Separate the functions of the operating system into independent modules according to whether:
 They are complete functional units.
 They are critical with respect to security.
 They should execute in their own process for security reasons, or their own thread for performance reasons.
 They should be isolated during execution because they may contain malware.
2 Define a set of loadable modules. New modules can be added later, according to the needs of specific applications.
3 Define a communication structure for the resultant modules. Operations should have well-defined call signatures and all calls should be checked.
4 Define a preferred order for loading some basic modules. Modules that are critical for security should be loaded only when needed to reduce exposure to attacks.
EXAMPLE RESOLVED
We structured the functions of our system following the MODULAR OPERATING SYSTEM ARCHITECTURE pattern. Because each module could have its own address space, we can isolate its execution. Because each module can be designed independently, they can have different security constraints in their structure. This structure gives us flexibility with a good degree of security.

CONSEQUENCES
The MODULAR OPERATING SYSTEM ARCHITECTURE pattern offers the following benefits:

 Flexibility to add and remove functions contributes to security, in that we can add new versions of modules with better security.
 Each module is separate and communicates with other modules over known interfaces. We can introduce controls in these interfaces.
 It is possible to partially hide critical modules by loading them only when needed and removing them after use.
 By giving each executing module its own address space we can isolate the effects of a rogue module.
This pattern also has the following potential liabilities:

 Any module can ‘see’ all the others and potentially interfere with their execution.
 Uniformity of call interfaces between modules makes it difficult to apply stronger security restrictions to critical modules.
VARIANTS
Monolithic Kernel. The operating system is a collection of procedures. Each procedure has a well-defined interface in terms of parameters and results and each one is free to call any other [Tan08]. There is no organization relating the operating system, components, services and user applications: all the modules are at the same level. The difference between monolithic and modular operating system architectures is that in the monolithic approach, all the modules are loaded together at installation time, instead of on demand. This approach is not very attractive for secure systems.

KNOWN USES
 The Solaris 10 operating system (Figure 9.3) is designed following this pattern. Its kernel is dynamic and composed of a core system that is always resident in memory [Sun04a]. The various types of Solaris 10 loadable modules are shown in Figure 9.3 as loaded by the kernel core: the diagram does not represent the communication links between individual modules.
Figure 9.3: The modular design of the Solaris 10 operating system [Si|08]


 Extreme Ware from Extreme Networks [Ext].
 Some versions of Linux use a combination of modular and monolithic architectures.
SEE ALSO
The CONTROLLED EXECUTION DOMAIN pattern (page 151) can be used to isolate executing modules.

9.3 Layered Operating System Architecture
The LAYERED OPERATING SYSTEM ARCHITECTURE pattern allows the overall features and functionality of the operating system to be decomposed and assigned to hierarchical layers. This provides clearly defined interfaces between each section of the operating system and between user applications and the operating system functions. Layer i uses the services of a lower layer i-1 and does not know of the existence of a higher layer, i+1.

EXAMPLE
Our operating system is very complex and we would like to separate different aspects in order to handle them in a more systematic way. Complexity brings vulnerability. We also want to control the calls between operating system components and services to improve security and reliability. Finally, we would like to hide critical modules. We tried a modular architecture, but it did not have enough structure to do all this systematically.

CONTEXT
A variety of applications with diverse requirements that need to execute together sharing hardware resources.

PROBLEM
Unstructured modules, as in the MODULAR OPERATING SYSTEM ARCHITECTURE pattern (page 165), have the problem that all modules can reach all other modules, which facilitates attacks. We need to conceal the existence of some critical modules.

The solution to this problem must resolve the following forces:

 Interfaces should be stable and well-defined. Going through any interface could imply authorization checks.
 Parts of the system should be exchangeable or removable without affecting the rest of the system. For example, we could have modules that perform more security checks than others.
 Similar responsibilities should be grouped, to help understandability and maintainability. This contributes indirectly to improved security.
 We should control module visibility to avoid possible attacks from other modules.
 Complex components need further decomposition. This makes the design simpler and clearer and also improves security.
SOLUTION
Define a hierarchical set of layers and assign components to each layer. Each layer presents an abstract machine (a set of operations) to the layer above it, hiding the implementation details of the lower layers.

STRUCTURE
Figure 9.4 shows a class diagram for the LAYERED OPERATING SYSTEM ARCHITECTURE pattern. LayerN represents the highest level of abstraction, and Layer1 is the lowest level of abstraction. The main structural characteristic is that the services of LayerN are used only by LayerN+1. Each layer may contain complex entities consisting of different components.

Figure 9.4: Class diagram for LAYERED OPERATING SYSTEM ARCHITECTURE pattern


DYNAMICS
Figure 9.5 shows the sequence diagram for the use case ‘Open and read a disk file’:

Figure 9.5: Sequence diagram for the use case ‘Open and read a disk file’


 A user sends an openFile() request to the OSInterface.
 The OSInterface interprets the openFile() request.
 The openFile() request is sent from the OSInterface to the FileManager.
 The FileManager sends a readDisk() request to the DiskDriver.
IMPLEMENTATION
1 List all units in the system and define their dependencies.
2 Assign units to levels such that units in higher levels depend only on units of lower levels.
3 Once the modules in a given level are assigned, define a language (set of commands) for the level. This language includes the operations that we want to make visible to the next level above. Add well-defined operation signatures and security checks in these operations to assure the proper use of the level.
4 Hide those modules that control critical security functions in lower levels.
EXAMPLE RESOLVED
We structured the functions of our system as in shown in Figure 9.6, and now we have a way to control interactions and enforce abstraction. For example, the file system can use the operations of the disk drivers and enforce similar restrictions in the storage of data.

Figure 9.6: An example of the use of a layered OS architecture


The user of a file cannot take advantage of the implementation details of the disk driver to attack the system.

CONSEQUENCES
The LAYERED OPERATING SYSTEM ARCHITECTURE pattern offers the following benefits:

 Lower levels can be changed without affecting higher layers. We can add or remove security functions as needed.
 There are clearly defined interfaces between each operating system layer and the user applications, which improves security.
 Control of information is possible using layer hierarchical rules and enforcement of security policies between layers.
 The fact that layers hide implementation aspects is useful for security, in that possible attackers cannot exploit lower-level details.
The pattern also has the following potential liabilities:

 It may not be clear what to put in each layer. In particular, related modules may be hard to allocate. There may be conflicts between functional and security needs when allocating modules.
 Performance may decrease due to the indirection of calls through several layers. If we try to improve performance, we may sacrifice security.
KNOWN USES
 The Symbian operating system (Figure 9.7) uses a variation of the layered approach [Sym01].
Figure 9.7: Symbian operating system layered architecture [Sym01]


 The UNIX operating system (Figure 9.8) is separated into four layers, with clear interfaces between the system calls to the kernel and between the kernel and the hardware.
Figure 9.8: UNIX layered architecture [Sil08]


 IBM’s OS/2 also uses this approach [OS2].
VARIANT
Layer skipping. In this architecture there are special applications that are able to skip layers for added performance. This structure requires a trade-off between performance and security. By deviating from the strict hierarchy of the layered system, there may not be enforcement of security policies between layers for such applications.

SEE ALSO
This pattern is a specialization of the Layers architectural pattern [Bus96]. Security versions of the Layers pattern have appeared in [Fer02] [Sch06b] [Yod97].

9.4 Microkernel Operating System Architecture
The MICROKERNEL OPERATING SYSTEM ARCHITECTURE pattern describes how to move as much of the operating system functionality as possible from the kernel into specialized servers, coordinated by a microkernel. The microkernel itself has a very basic set of functions. Operating system components and services are implemented as external and internal servers.

EXAMPLE
We are building an operating system to support a range of applications with different reliability and security requirements and a variety of plugins. We would like to provide operating system versions with different types of modules: some more secure, some less so.

CONTEXT
A variety of applications with diverse requirements that need to execute together sharing hardware resources.

PROBLEM
In general-purpose environments we need to be able to add new functionality with variation in security and other requirements, as well as provide alternative implementations of services to accommodate different application requirements.

The solution to this problem must resolve the following forces:

 The application platform must be able to cope with continuous hardware and software evolution: these additions may have very different security or reliability requirements.
 Strong security or reliability requirements indicate the need for modules with well-defined interfaces.
 We may want to perform different types of security checks in different modules, depending on their security criticality.
 We would like a minimum of functionality in the kernel, so that we have a minimum of processes running in supervisor mode. A simple kernel can be checked for possible vulnerabilities, which is good for security.
SOLUTION
Separate all functionality into specialized services with well-defined interfaces, and provide an efficient way to route requests to the appropriate servers. Each server can be built with different security constraints. The kernel mainly routes requests to servers and has minimal functionality.

STRUCTURE
The Microkernel is the central communication for the operating system. There is one Microkernel and several InternalServers and ExternalServers, each providing a set of specialized services (Figure 9.9). In addition to the servers, an Adapter is used between the Client and the Microkernel or an external server. The Microkernel controls the internal servers.

Figure 9.9: Class diagram for MICROKERNEL OPERATING SYSTEM ARCHITECTURE pattern


DYNAMICS
A client requests a service from an external server using the following sequence (Figure 9.10):

Figure 9.10: Sequence diagram for an operating system call through the microkernel


1 The Adapter receives the request from the Client and asks the Microkernel for a communication link with the ExternalServer.
2 The Microkernel checks for authorization to use the server, determines the physical address of the ExternalServer and returns it to the Adapter.
3 The Adapter establishes a direct communication link with the ExternalServer.
4 The Adapter sends the request to the ExternalServer using a procedure call or a remote procedure call (RPC). The RPC can be checked for well-formed commands, correct size and type of parameters (that is, we can check signatures).
5 The ExternalServer receives the request, unpacks the message and delegates the task to one of its own methods. All results are sent back to the Adapter.
6 The Adapter returns to the Client, which in turn continues with its control flow.
IMPLEMENTATION
1 Identify the core functionality necessary for implementing external servers and their security constraints. Typically, basic functions of the operating system should be internal servers; utilities, or user-defined services should go into external servers. Each server can use the patterns from [Fer02] and [Fer03b] for their secure construction.
2 Define policies to restrict access to external and internal servers. Clients may be allowed to call only specific servers.
3 Find a complete set of operations and abstractions for every category of server identified.
4 Determine strategies for request transmission and retrieval.
5 Structure the microkernel component. The microkernel should be simple enough to ensure its security properties; for example, it should be impossible to infect it with malware.
6 Design and implement the internal servers as separate processes or shared libraries. Add security checks in each server using the PROTECTED ENTRY POINTS pattern (page 136).
7 Implement the external servers. Add security checks in each service provided by the servers using AUTHORIZATION (page 74) and AUTHENTICATOR (page 52).
EXAMPLE RESOLVED
By implementing our system using a microkernel, we can have several versions of each service, each with different degrees of security and reliability. We can replace servers dynamically if needed. We can also control access to specific servers and ensure that they are called in the proper way.

CONSEQUENCES
The MICROKERNEL OPERATING SYSTEM ARCHITECTURE pattern offers the following benefits:

 Flexibility and extensibility: if you need an additional function or an existing function with different security requirements, you only need to add an external server. Extending the system capabilities or requirements also only requires addition or extension of internal servers.
 The microkernel mediates all calls for services and can apply authorization checks. In fact, the microkernel is in effect a concrete realization of a reference monitor (page 100).
 The well-defined interfaces between servers allow each server to check every request for their services.
 It is possible to add even more security by putting fundamental functions in internal servers.
 Servers usually run in user mode, which further increases security.
 The microkernel is very small and can be verified or checked for security. The pattern also has the following potential liability:
 Communication overhead, since all messages must go through the Microkernel.
KNOWN USES
 The PalmOS Cobalt (Figure 9.11) operating system has a preemptive multitasking kernel that provides basic task management. Many applications in PalmOS do not use the microkernel services; they are handled automatically by the system. The microkernel functionality is provided for internal use by system software or for certain special-purpose applications [Pal].
Figure 9.11: PalmOS Microkernel combined with layered OS architecture [Pal]


 The QNX Microkernel (Figure 9.12) is intended mostly for communication and process scheduling in real-time systems [QNX]. It will be used in the new RIM systems, adopting a layered architecture [Qwi].
Figure 9.12: QNX microkernel architecture [QNX]


 Mach and Windows NT also use some form of microkernels [Sil08].
VARIANT
Layered Microkernel. The MICROKERNEL OPERATING SYSTEM ARCHITECTURE pattern can be combined with the LAYERED OPERATING SYSTEM ARCHITECTURE pattern. In this case, servers can be assigned to levels and a call is accepted only if it comes from a level above the server level.

SEE ALSO
This pattern is a specialization of the Microkernel pattern [Bus96]. As indicated, the microkernel itself can be considered as a concrete version of the REIFIED REFERENCE MONITOR pattern (page 100).

9.5 Virtual Machine Operating System Architecture
The VIRTUAL MACHINE OPERATING SYSTEM ARCHITECTURE pattern describes how to provide a set of replicas of the hardware architecture (virtual machines) that can be used to execute multiple and possibly different operating systems with strong isolation between them.

EXAMPLE
A web server is hosting applications for two competing companies. These companies use different operating systems. We want to ensure that neither of them can access the other company’s files or launch attacks against the other system.

CONTEXT
Mutually suspicious sets of applications that need to execute in the same hardware. Each set requires isolation from the other sets.

PROBLEM
Sometimes we need to execute different operating systems on the same hardware. How can we keep those operating systems isolated in such a way that their executions don’t interfere with each other?

The solution to this problem must resolve the following forces:

 Each operating system needs to have access to a complete set of hardware features to support its execution.
 Each operating system has its own set of machine-dependent features, such as interrupt handlers. In other words, each operating system uses the hardware in different ways.
 When an operating system crashes or it is penetrated by a hacker, the effects of this situation should not propagate to other operating systems running on the same hardware.
 There should be no way for a malicious user in one virtual machine to get access to the data or functions of another virtual machine.
SOLUTION
Define an architectural layer that is in control of the hardware and supervises and coordinates the execution of each operating system environment. This extra layer, usually called a virtual machine monitor (VMM) or hypervisor, presents to each operating system a replica of the hardware. The VMM intercepts all system calls and interprets them according to the operating system from which they came.

STRUCTURE
Figure 9.13 shows a class diagram for the VIRTUAL MACHINE OPERATING SYSTEM ARCHITECTURE (VMOS) pattern. The VMOS contains one VirtualMachineMonitor (VMM) and multiple virtual machines (VM). Each VM can run a local operating system (LocalOS). The VirtualMachineMonitor supports each LocalOS and is able to interpret its system calls. As a LocalProcess runs on a LocalOS, the VM passes the operating system calls to the VMM, which executes them in the hardware.

Figure 9.13: Class diagram for the VIRTUAL MACHINE OPERATING SYSTEM ARCHITECTURE pattern


DYNAMICS
Figure 9.14 shows the sequence diagram for the use case ‘Perform an OS call on a virtual machine’. A local process wishing to perform a system operation uses the following sequence:

Figure 9.14: Sequence diagram for the use case ‘Perform an OS call on virtual machine’


1 A LocalProcess makes an operating system call to the LocalOS.
2 The LocalOS maps the operating system call to the VMM (by executing a privileged operation).
3 The VMM interprets the call according to the local operating system from which it came, and it executes the operation in hardware.
4 The VMM sends return codes to the LocalOS to indicate successful instruction execution, as well as the results of the instruction execution.
5 The LocalOS sends the return code and data to the LocalProcess.
IMPLEMENTATION
1 Select the hardware that will be virtualized. All of its privileged instructions must trap when executed in user mode (this is the usual way to intercept system calls).
2 Define a representation (data structure) for describing operating system features that map to hardware aspects, such as meaning of interrupts, disk space distribution, and so on, and build tables for each operating system to be supported.
3 Enumerate the system calls for each supported operating system and associate them with specific hardware instructions.
EXAMPLE RESOLVED
In the example shown in Figure 9.15, two companies using UNIX and Linux can execute their applications in different virtual machines. The VMM provides strong isolation between these two execution environments.

Figure 9.15: Virtual Machine operating system example


CONSEQUENCES
The VIRTUAL MACHINE OPERATING SYSTEM ARCHITECTURE pattern offers the following benefits:

 The VMM intercepts and checks all system calls. The VMM is in effect a reference monitor and provides total mediation for the use of the hardware. This can provide strong isolation between virtual machines [Ros05].
 Each environment (virtual machine) does not know about the other virtual machine(s), this helps prevent cross-VM attacks.
 There is a well-defined interface between the VMM and the virtual machines.
 The VMM is small and simple and can be checked for security.
 The architecture defined by this pattern is orthogonal to the other three architectures discussed earlier, and can execute any of them as local operating systems.
The pattern also has the following potential liabilities:

 All the virtual machines are treated equally. If virtual machines with different security categories are required, it is necessary to build specialized versions. This approach is followed in KVM/370 (see Variants).
 Extra overhead in the use of privileged instructions.
 It is complex to let virtual machines communicate with each other, if this is needed.
VARIANTS
 The architecture defined by this pattern is orthogonal to the other three architectures discussed earlier, and can execute any of them as local operating systems.
 KVM/370 was a secure extension of VM/370 [Gol79]. This system included a formally verified security kernel, and its virtual machines executed in different security levels, for example top secret, confidential, and so on. In addition to the isolation provided by the VMM, this system also applied the multilevel model described in Chapter 6.
KNOWN USES
 IBM VM/370 [Cre81]. This was the first VMOS, and provided virtual machines for an IBM 370 mainframe.
 VMware [Nie00]. This is a current range of products that provide virtual machines for Intel X86 hardware.
 Solaris 10 [Sun04a] calls the virtual machines ‘containers’, and one or more applications execute in each container.
 Connectix [Cona] produces virtual PCs to run Windows and other operating systems.
 Xen is a VMM for the Intel x86 developed as a project at the University of Cambridge, UK [Bar00].
 Some smart phone operating systems use virtual machines to separate users’ private system from their work environment. These include the L4 Microvisor and RIM’s BlackBerry 10 OS [Qwi].
SEE ALSO
 REIFIED REFERENCE MONITOR (page 100). The VMM is a concrete version of a reference monitor.
 The operating system patterns in [Fer02] and [Fer03b] can be used to implement the structure of a VMOS architecture.
9.6 Administrator Hierarchy
Many attacks come from the unlimited power of administrators. The ADMINISTRATOR HIERARCHY pattern allows the power of administrators to be limited, by defining a hierarchy of system administrators with rights controlled using a ROLE-BASED ACCESS CONTROL (RBAC) model, and assigns them rights according to their functions.

EXAMPLE
UNIX defines a superuser who has all possible rights. This is expedient: for example, when somebody forgets a password, but allows hackers to totally control the system through a variety of implementation flaws. Through gaining access to the administrator rights, an individual can create new administrator and user accounts, restrict their privileges and quotas, access their protected areas, or remove their accounts.

CONTEXT
An operating system with a variety of users, connected to the Internet. There are some commands and data that are used for system administration, and access to them needs to be protected. This control is usually applied through special interfaces. There are at least two roles required to properly manage privileges, Administrator and User.

PROBLEM
Usually, the administrator has rights such as creating accounts and passwords, installing programs and so on. This creates a series of security problems. For example, a rogue administrator can perform all the usual functions, and even erase the log to hide their tracks. A hacker that takes over administrative power can do similar things. How can we curtail the excessive power of administrators to control rogue administrators or hackers?

The solution to this problem must resolve the following forces:

 Administrators need to use commands that permit management of the system, for example define passwords for files, define quotas for files, and create user accounts. We cannot eliminate these functions.
 Administrators need to be able to delegate some responsibilities and privileges to manage large domains. They also need the right to take back these delegations, otherwise the system is too rigid.
 Administrators should have no control of system logs, or no valid auditing would be possible.
 Administrators should have no access to the operational data in the users’ applications. Or, if they do, their accesses should be logged.
SOLUTION
Separate the different administrative rights into several hierarchical roles. The rights for these roles allow the administrators to perform their administrative functions and no more. Critical functions may require more than one administrative role to participate. Use the principle of separation of duty (Chapter 6), where a user cannot perform critical functions unless in conjunction with other users.

STRUCTURE
Figure 9.16 shows a hierarchy for administration roles. This follows the Composite pattern [Gam94]; that is, a role can be simple or composed of other roles, defining a tree hierarchy. The top-level Administrator can add or remove administrators of any type and initialize the system, but should have no other functions. Administrators in the second level control different aspects, for example security, or use of resources. Administrators can further delegate their functions to lower-level administrators. Some functions may require two administrators to collaborate.

Figure 9.16: Class diagram for the ADMINISTRATOR HIERARCHY pattern


IMPLEMENTATION
1 Define a top-level administrative role with only the functions of setting up and initializing the system. This includes definition of administrative roles, assignment of rights to roles and assignment of users to roles.
2 Separate the main administrative functions of the system and define an administrative role for each one of them. These define the second level of the hierarchy.
3 Define further levels to accommodate administrative units in large systems, or for breaking down rights into functional sets.
Figure 9.17 shows a class diagram describing a typical administrator hierarchy. Here the SystemAdministrator starts the system and does not perform further actions. The second-level administrators can perform set up and other functions; the SecurityAdministrator defines security rights. SecurityDomainAdministrators define security in their domains.

Figure 9.17: A typical administration hierarchy


EXAMPLE RESOLVED
Now the superuser only starts the system. During normal operation the administrators have restricted powers. If a hacker takes over their functions they can do only limited damage.

CONSEQUENCES
The ADMINISTRATOR HIERARCHY pattern offers the following benefits:

 If an administrative role is compromised, the attacker gets only limited privileges, so the potential damage is limited.
 The reduced rights also reduce the possibility of misuse by the administrators.
 The hierarchical structure allows taking back control of a compromised administrative function.
 The advantages of the RBAC model apply: simpler and fewer authorization rules, flexibility for changes, and so on [Sch06b].
 This structure is useful not only for operating systems, but also for servers, databases systems or any systems that require administration.
The pattern also has the following potential liabilities:

 Extra complexity for the administrative structure.
 Less expediency: performing some functions may involve more than one administrator.
 Many attacks are still possible: if someone misuses an administrative right, this pattern only limits the damage. Logging can help misuse detection.
KNOWN USES
 AIX [Cam90] reduces the privileges of the system administrator by defining five partially-ordered roles: superuser, security administrator, auditor, resource administrator and operator.
 Windows NT uses four roles for administrative privileges: standard, administrator, guest and operator. A user manager has procedures for managing user accounts, groups and authorization rules.
 Trusted Solaris [Sun04a]. This operating system is an extension of Solaris 8, using the concept of trusted roles with limited powers.
 Argus Pitbull [Arg]. In this operating system, least privilege is applied to all processes, including the superuser. The superuser is implemented using three roles: systems security officer, system administrator and system operator.
SEE ALSO
 This pattern applies the principles of least privilege and separation of duty, which some people consider also to be patterns. Each administrator role is given only the rights it needs to perform its duties and some functions may require collaboration.
 Administrative rights are usually organized according to a ROLE-BASED ACCESS CONTROL model (page 78).
9.7 File Access Control
The FILE ACCESS CONTROL pattern allows control of access to files in an operating system. Authorized users are the only ones that can use a file in specific ways.

EXAMPLE
In a laboratory researchers used to share all their files: they were working on common projects and they trusted each other. However, the laboratory grew and inexperienced or unknown colleagues started to work. Now it is not such a good idea to share everything.

CONTEXT
The users of operating systems need to use files to store permanent information. These files can be accessed by different users from different workstations, and access to the files must be restricted to authorized users who can use them in specific ways. Because of the needs of the institution, some (or all) of the files must be shared by these subjects.

Use cases for a file system include creation and deletion of files, opening and closing of files, reading and writing files, copying files and so on. A subject has a home directory for each authorized workstation, but the same home directory can be shared among several workstations or among several subjects. The home directory is used to search the files for which a subject has rights. Files are organized using directories, usually in a tree-like structure of directories and files. This facilitates the search for specific files.

PROBLEM
How can we control access to files in an operating system and ensure that only authorized users can use files in specific ways?

The solution to this problem must resolve the following forces:

 There may be different types of subjects, for example users, roles and groups. The rights for users in groups or roles are derived from the group or role’s rights (that is, they are implicit rights). Groups of groups are possible, which makes deducing access even harder. All these subjects must be handled uniformly.
 Subjects may be authorized to access files or directories, and to exercise their file access rights from specific workstations. To prevent illegal actions, we may need ways to apply these two types of authorization.
 Each operating system implements file systems in a different way. We need to abstract out implementation details.
 Not all operating systems use workstations, groups or roles. We need a modular system in which features not used can be removed easily from the model.
SOLUTION
We apply the AUTHORIZATION pattern (page 74) first to describe access to files by subjects. Typically, file systems use ACCESS CONTROL LISTs (page 91) consisting of sets of authorizations. The protection object is now a file component that may be a directory or a file. To reflect the fact that files may be accessed only from some workstations, we use the AUTHORIZATION pattern again, with the same subject and with workstations as protection objects. The tree structure of files and directories can be conveniently described by applying the Composite pattern [Gam94].

STRUCTURE
The class diagram in Figure 9.18 combines two versions of the AUTHORIZATION pattern with a Composite pattern. File access is an extension of that pattern by replacing ProtectionObject by FileComponent and Right by AccessControlListEntry (ACLE), and workstation access is defined by a similar application of the AUTHORIZATION pattern.

Figure 9.18: Class diagram for the FILE ACCESS CONTROL pattern


DYNAMICS
The sequence diagram in Figure 9.19 shows the use case ‘Open and write to a file’. A user actor opens the file, the directory locates it and when found, opens it. Opening results in the file access permission being set up for future reference1. When the user later tries to write to the file, their rights to write the file are checked and the write operation proceeds if authorized.

Figure 9.19: Sequence diagram for the use case ‘Open and write to a file’


EXAMPLE RESOLVED
A new operating system is installed that has authorization controls for its files. Now a need-to-know policy is set up, where only users that need access to specific files are given such access. This is the end of erroneous or unauthorized activities.

CONSEQUENCES
The FILE ACCESS CONTROL pattern offers the following benefits:

 The subjects can be users, roles and groups by proper specialization of the class Subject. Roles and groups can be structured recursively (see Chapter 6); for example, role and group hierarchies permit more flexibility in the assignment of rights.
 The protection objects can be single files, directories, or recursive structures of directories and files.
 Most operating systems use read/write/execute as access types, but higher-level types of access are possible. For example, a file representing students in a university could be accessed with commands such as list, order alphabetically, and so on.
 Implied authorization is possible; for example, access to a directory may imply similar type of access to all the files in the directory [Fer94b]. This approach allows an administrator to write fewer authorization rules, because some access rights can be deduced from others.
 Workstation access is also controlled – workstations can be homes for directories.
 This is a conceptual model that doesn’t restrict implementation approaches.
 Workstation authorization is separated from file authorization; systems that do not need workstation authorization can just ignore the relevant classes.
 In some operating systems, for example Inferno [Rau97], all resources are represented as files. Other systems represent resources by objects with access control lists. This means that this pattern could be used to control all the resources of such operating systems.
The pattern also has the following potential liabilities:

 Implementations of the pattern are not forced to follow the access matrix model. For example, UNIX uses a pseudo-access matrix that is not appropriate for applying the need-to-know policy1. However, constraints can be added to the pattern to force all the instances of the pattern to conform to an access matrix model.
 Typically, access permissions are implemented as access control lists (ACLs) [Gol06] [Sil08]. These are data structures associated with a file in which each entry defines a subject that can access the file and its permitted access modes. The pattern models the entries of the ACLs, but not the fact that they are associated with the file components.
Other aspects include:

 Some systems use the concept of owner, who has all rights on the files they create. The owner in this model corresponds to a special type of subject. When roles are used, there are no owners, and when groups are used, ownership is not inherited in subgroups.
 In some systems, files are mapped to the virtual memory address space. The FILE ACCESS CONTROL pattern still applies to this case, although a more uniform solution is then possible (see the VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern, page 146).
 In some systems, a directory is not strictly a tree, because it is possible to have links between files in different subtrees [Sil08]. Modeling this case would require adding some associations in the model of Figure 9.18.
KNOWN USES
This pattern can be found in most current operating systems, such as Windows, UNIX, Linux. Not all these systems use all the concepts of the pattern.

SEE ALSO
 This pattern uses the AUTHORIZATION pattern (page 74).
 If roles are used, then the ROLE-BASED ACCESS CONTROL pattern (page 78) is also relevant.
 The file structure uses a Composite pattern [Gam94]. It can use the CONTROLLED EXECUTION DOMAIN pattern (page 151) for its implementation.
1 In some systems, opening a file also requires a specific authorization. Figure 9.19 assumes that this is not the case, although the pattern does not preclude this possibility.

1 Some versions of UNIX, for example IBM AIX, extend this model to support a full access matrix.