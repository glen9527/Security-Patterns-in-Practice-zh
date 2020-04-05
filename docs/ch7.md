# 第 7 章 用于安全流程管理的模式
CHAPTER 7
Patterns for Secure Process Management
Music is given to us with the sole purpose of establishing an order in things, including, and particularly, the coordination between man and time.

Igor Stravinsky

7.1 Introduction
Operating systems are fundamental to the provision of security to computing systems. The operating system supports the execution of applications, and any security constraints defined by applications must be enforced by the operating system. The operating system must also protect itself, because compromise would give access to all the user accounts and all the data in their files. A weak operating system would allow hackers access not only to data in the operating system files, but data in database systems that use the services of the operating system. The operating system enables this protection by protecting processes from each other and protecting the permanent data stored in its files [Sil05]. For this purpose, the operating system controls access to resources such as memory address spaces and I/O devices. Most operating systems use an access matrix or the ROLEBASED ACCESS CONTROL pattern (page 78) as a security model. For example, an access matrix defines which processes (subjects in general) have what types of access to specific resources (resources are represented as objects in modern operating systems).

In a computer system processes typically collaborate to perform some activity or call each other to request services. Process invocations occur through local or remote procedure calls; these operations are supported at the kernel level through send/receive operations, which may be direct or indirect (using mailboxes) [Sil05]. The operation name used for invocation, plus the number, type, and length of the parameters in the call is called the procedure signature. The controlled interaction of processes in a computing environment is fundamental to its security and reliability. Processes can be attacked by other processes or by external clients; errors in one process can propagate to others. Executing processes in a computing system need to be protected from attacks from other processes. Many of those attacks come from the invocation of unprotected (no access control) or wrong entry points, or using the wrong type or size of parameters in these calls.

Computer system functionality can be divided between the kernel (or operating system proper) components and user-oriented utilities such as browsers, media players and so on. Typically, an operating system includes the following functional components:

 Process management: handles creation and deletion of processes, communication and scheduling.
 Memory management: keeps track of which parts of memory are used by which processes; allocates and deallocates memory.
 File management: handles creation and deletion of files and directories, file searches, and mapping files to secondary storage.
 I/O management: provides interfaces to hardware device drivers, as well as handling mass memory management components including buffering, caching and spooling.
 Networking: controls communication paths between two or more systems.
 Protection system: includes authentication of users and file and memory protection.
 User interface: communicates between user and operating system, including command interpreters.
Operating systems authenticate users when they first log in. A user then executes an application composed of several concurrent processes. Processes are usually created through system calls to the operating system. A process that needs to create a new process gets the operating system to create a child process that is given access to some resources.

Executing applications need to create objects for their work. Some objects are created at program initialization, while others are created dynamically during execution. The access rights of processes with respect to objects must be defined when these processes are created. Applications also need resources such as I/O devices and others that may come from resource pools; when these resources are allocated, the application must be given rights for them. These rights are defined by authorization rules or policies that must be enforced when a process attempts to access an object. This means that we need to intercept every access request; this is done by the REIFIED REFERENCE MONITOR (page 100).

In this chapter we present patterns for secure process management. Figure 7.1 shows how these patterns work together. They include:

Figure 7.1: Patterns for secure process management


 SECURE PROCESS/THREAD. How can we make sure that a process does not interfere with other processes or misuse shared resources? A process is a program in execution; a secure process is also a unit of execution isolation as well as a holder of rights to access resources, and has a separate virtual address space. A thread is a lightweight process. A variant, called Secure Thread, is a lightweight process with controlled access to resources.
 CONTROLLED-PROCESS CREATOR. How can we define the rights to be given to new processes? Define the rights of a new process as part of their creation.
 CONTROLLED-OBJECT FACTORY. How can we specify the rights of processes with respect to a new object? When a process creates a new object through a Factory, the request includes the features of the new object. These features include a list of rights to access the object.
 CONTROLLED-OBJECT MONITOR. How can we control access by a subject to an object? A specialized Reference Monitor can intercept access requests from processes. The Reference Monitor checks whether the process has the requested type of access to the object.
We also included in this chapter two patterns that are useful for process isolation:

 PROTECTED ENTRY POINTS. This pattern forces a call from one process to another to go through only pre-specified entry points where the correctness of the call is checked and other access restrictions can be applied.
 PROTECTION RINGS. This pattern assigns processes to a set of hierarchical rings that control how processes call each other and how they access data. Crossing of rings is done through gates that check the rights of the crossing process. A process calling another process or accessing data in a higher ring must go through a gate.
Patterns for process scheduling [wik1] and resource management [Kir04] also exist, but are not relevant to security and so are not discussed here.

The Controlled-Process Creator, the Controlled-Object Factory and the Controlled-Object Monitor patterns were published in [Fer03b], coauthored by John C. Sinibaldi. The Secure Process/Thread pattern was published in [Fer06f], coauthored with Tami Sorgente and Maria M. Larrondo-Petrie. The Protected Entry Points pattern appeared in [Fer08c], coauthored with David LaRed. Some of these patterns are updated versions of the ones in Chapter 8 of [Sch06b].

7.2 Secure Process/Thread
The SECURE PROCESS/THREAD pattern describes how to make sure that a process does not interfere with other processes or misuse shared resources. A process is a program in execution; a secure process is also a unit of execution isolation as well as a holder of rights to access resources, and has a separate virtual address space. A thread is a lightweight process. A variant, called Secure Thread, is a thread with controlled access to resources.

EXAMPLE
A group of designers in Company X built an operating system and did not include any mechanisms to control the actions of processes. This resulted in processes being able to access the address space and resources of other processes. In this environment we cannot protect the shared information, nor assure the correct execution of any process – their code and stack sections may be corrupted by other processes. While its performance was good, nobody wanted to use this operating system once its poor security was known.

CONTEXT
Typically, operating systems support a multiprogramming environment, with many user-defined and system processes active at a given time. During execution it is essential to maintain all information regarding a process, including its current status (the value of the program counter), the contents of the processor’s registers, and the process stack containing temporary data (subroutine parameters, return addresses, temporary variables and unresolved recursive calls). All this information is called the process context. When a process needs to wait, the operating system must save the context of the first process and load the next process for execution; this is a context switch. The saved process context is brought back when a suspended process resumes execution.

PROBLEM
We need to control the resources accessed by a process during its execution and protect its context from other processes. The resources that can be accessed by a process define its execution domain, and the process should not break the boundaries of this domain. The integrity of a process’ context is essential, not only for context switching, but also for security, so that it cannot be controlled by another process, and for reliability, to prevent a rogue process from interfering with other processes.

The solution to this problem must resolve the following forces:

 If processes have unrestricted access to resources, they can interfere with the execution of other processes and misuse shared resources. We need to control what resources they can access.
 Processes should be given only the rights they need to perform their functions (need to know or least privilege principle ([Gol06], Chapter 3).
 The rights assigned to a process should be fine-grained, otherwise we cannot apply the least privilege principle.
 Each process requires some data, a stack, and space for temporary variables to store the status of its devices and other information. All this information resides in its address space and needs to be protected.
SOLUTION
Assign to each process a set of authorization rights to access the resources they need. Assign to the process a unique address space to store its context and execution-time data. This protects processes from interference from other processes, assuring confidentiality and integrity of the shared data and proper use of shared resources. In the process descriptor, a data structure containing all the information a process needs for its execution, add rights to make access to any resource explicitly authorized. Ensure that every access to a resource is intercepted and checked for authorization.

It may also be possible to add resource quotas, to avoid denial of service problems, but this requires some global resource usage policies.

STRUCTURE
Figure 7.2 shows the class diagram for the SECURE PROCESS/THREAD pattern. In the figure, each ProcessDescriptor has ProcessRights for specific Resources. Additional security information indicates the owner of the process. The ProcessRights are defined by the AUTHORIZATION pattern (page 74; the ProcessDescriptor acts as subject in this pattern) and are enforced by the REIFIED REFERENCE MONITOR pattern (page 100), which intercepts request for resources and checks them for authorization. More than one ProcessDescriptor can be created, describing different processes and corresponding to multiple executions of ProgramCode. A separate VirtualAddressSpace is associated with each process (defined by the VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern (page 146). The process context is stored in the VirtualAddressSpace of the process, while the ProgramCode can be shared by several processes.

Figure 7.2: Class diagram for the SECURE PROCESS pattern


DYNAMICS
Figure 7.3 shows a sequence diagram for the use case ‘Access a resource’. A requestResource operation from a process includes the process ID and the intended type of access. The request is intercepted by the ReferenceMonitor, which determines whether it is authorized (the checkAccess operation in the Right class). If so, the access proceeds.

Other related use cases (not shown) include ‘Assign a right to a process’ and ‘Remove a right from a process’.

Figure 7.3: Sequence diagram for the use case ‘Access a resource’


IMPLEMENTATION
The process descriptor is typically called a process control block (PCB), or task control block (TCB), and includes references (pointers) to its code section, its stack and other required information. There are different ways of implementing data structures: records (structs in C) are typically used for the process descriptor. The process descriptors of the processes in the same state are usually linked in a double-linked list. The hardware may include registers for some of the attributes of the process descriptor; for example, the Intel X86 Series includes registers for typical attributes. There are various ways of associating a virtual address space to a process, described in Chapter 9. There are also various ways to associate rights with a new process; see the CONTROLLED-PROCESS CREATOR pattern (page 126). The hardware architecture normally implements the virtual address space, and restricts access to the sections (segments) allocated to each process using appropriate mechanisms.

The patterns as shown describe models where subjects have rights described by an access matrix or according to ROLE-BASED ACCESS CONTROL (page 78). Some operating systems use multilevel (typically mandatory) models in which the access of a process is determined by its level with respect to the resource being accessed [Sch06b]. In the latter case, the process, instead of being given a right, has a tag or label that indicates its level. Resources have similar tags and the reference monitor compares both tags.

EXAMPLE RESOLVED
Company X solved its problem by adding rights to a process representation. Now each process is constrained to access only those resources for which it has rights. This protects processes from each other, as well as the confidentiality and integrity of shared data and other resources. While other security problems may still persist, the general security of the operating system increased significantly.

CONSEQUENCES
The SECURE PROCESS/THREAD pattern offers the following benefits:

 It is possible to give specific rights for resources to each process which restricts them to access only authorized resources.
 It is possible to apply the least privilege principle for execution.
 The process’ contexts can be protected from other processes, because they are restricted to access only authorized resources.
 The virtual address space of a process can be protected by the hardware and its memory manager.
The pattern has the following potential liabilities:

 There is some overhead in using a Reference Monitor to enforce accesses.
 It may not be clear what rights to assign to each process.
 Having a separate address space implies a slow context switch, which affects performance. Because of this, kernel processes usually share an address space.
 There are other security problems not controlled by this pattern, such as denial of service, users taking control in administrator mode, or virus propagation. Those problems require complementary security mechanisms, some of which are described by other patterns [Sch06b].
KNOWN USES
 Linux uses records for process descriptors. One of the entries defines the process credentials (rights) that define its access to resources [Nut03] [Sil05]. Other entries describe its owner (subject) and process ID. A more elaborated approach using execution domains is used in Selinux, a secure version of Linux [Sel].
 Windows NT and 2000. Resources are defined as objects (actually, as classes). The process ID is used to determine access to objects [Sil05]. Each file object has a security descriptor that indicates the owner of the file, and an access control list that describes the access rights for the processes to access the file.
 Solaris threads have controlled access to resources defined in the application, for example when using the POSIX standard [Sil05].
 Operating systems running on Intel architectures can protect thread stacks, data and code by placing them in special segments of the shared address space, with hardware-controlled access.
VARIANT
 Secure Thread. Because of the slow context switching of processes, most operating systems use threads, which have a smaller context. How can we make the execution of a thread secure? A secure thread is a thread with controlled access to resources. Figure 7.4 represents the addition of the ThreadDescriptor to the secure process. One process may have multiple threads of execution. Each thread is represented by a ThreadDescriptor. A unique VirtualAddressSpace is associated with a process and shared by peer threads. ThreadRights define access rights to the VirtualAddressSpace.
Figure 7.4: Class diagram for the SECURE THREAD pattern


Thread status typically includes a stack, a program counter and some status bits. There are various ways of associating threads with a process [Sil05]; typically, several threads are collected into a process. Threads can be created with special packages, for example PTHREADS in UNIX, or through the language, as in Java or Ada. Rights can be added explicitly, or we can use the hardware architecture’s enforcement of the proper use of the process areas (see Known Uses).

SEE ALSO
 CONTROLLED-PROCESS CREATOR (below): at process creation time, rights are assigned to the process.
 VIRTUAL ADDRESS SPACE ACCESS CONTROL (page 146). A virtual address space is assigned to each process that can be accessed according to the rights of the process.
 AUTHORIZATION (page 74), which defines the rights to access resources.
 The REIFIED REFERENCE MONITOR pattern (page 100), used to enforced the defined rights.
 Processes run at rebooting are critical for security [Loh10].
7.3 Controlled-Process Creator
The CONTROLLED-PROCESS CREATOR pattern describes how to define the rights to be given to new processes, by defining the rights as part of the process’ creation.

EXAMPLE
The UNIX operating system creates a process with the same rights as its parent. If a hacker can trick UNIX into creating a child of the supervisor process, this runs with all the rights of the supervisor.

CONTEXT
An operating system in which processes or threads need to be created according to application needs. Users execute applications composed of several concurrent processes. Processes are normally created through system calls to the operating system.

PROBLEM
A computing system uses many processes or threads. Processes need to be created according to applications’ needs, and the operating system itself is composed of processes. If processes are not controlled, they can interfere with each other and access data illegally. Their rights to resources should be carefully defined according to appropriate policies, for example need to know.

The solution to this problem must resolve the following forces:

 There should be a convenient way to select a policy to define process’ rights. Defining rights without a policy brings contradictory and unsystematic access restrictions that can be easily circumvented.
 The child process may need to run with its parent process’ rights for specific actions, but this should be carefully controlled, otherwise a compromised child could leak information or destroy data.
 The number of children created by a process must be restricted, or process spawning could be used to perform denial-of-service attacks.
 There are situations in which a process needs to act with more than its normal rights, for example to get data from a file to which it doesn’t normally have access.
SOLUTION
Because new processes are created through system calls or messages to the operating system, we have a chance to control the rights given to the new process. Typically, operating systems create a new process as a child process. There are several policies for granting rights to a child process. For example:

 The child process can inherit all the rights of its parent, or a subset of them.
 Allow the parent assign a specific set of rights to its children (more secure).
STRUCTURE
Figure 7.5 shows the class diagram for this pattern. The ControlledProcessCreator is the part of the operating system in charge of creating processes. The CreationRequest contains the access rights that the parent defines for the created child. These access rights must be a subset of the parent’s access rights.

Figure 7.5: Class diagram for the CONTROLLED-PROCESS CREATOR pattern


DYNAMICS
Figure 7.6 shows the dynamics of process creation. A Process requests the creation of a new Process. The access rights passed in the creation request are used to create the AccessRights for the new process.

Figure 7.6: Process creation dynamics


IMPLEMENTATION
For each required application of kernel processes or threads, define their rights according to their intended function.

EXAMPLE RESOLVED
There is now no automatic inheritance of rights in the creation of child processes, so creating a child process confers no advantage for a hacker.

CONSEQUENCES
The CONTROLLED-PROCESS CREATOR pattern offers the following benefits:

 It is possible to define rights to use an object according to its sensitivity.
 Objects allocated from a resource pool can have rights attached dynamically.
 The operating system can apply ownership policies: for example, the creator of an object may receive all possible rights for the objects it creates.
 The created process can receive rights according to predefined security policies.
 The number of children produced by a process can be controlled. This is useful to control denial of service attacks.
 The rights may include the parent’s ID, allowing the child to run with the rights of its parent.
The following potential liability may arise from applying this pattern:

 Explicit rights transfer takes more time than using a default transfer.
KNOWN USES
In many operating systems, for example UNIX, rights are inherited as a full set from the parent. Some hardened operating systems, such as Hewlett Packard’s Virtual Vault, do not allow inheritance, and a new set of rights must be defined for each child [HP].

SEE ALSO
The CONTROLLED EXECUTION DOMAIN pattern (page 151) could use this pattern to define the execution domain of new processes.

7.4 Controlled-Object Factory
The CONTROLLED-OBJECT FACTORY pattern describes how to specify the rights of processes with respect to a new object. When a process creates a new object through a Factory, the request includes the features of the new object. Among these features it includes a list of rights to access the object.

EXAMPLE
In many operating systems the creator of an object gets all possible rights to the object. Other operating systems apply predefined sets of rights: for example, in UNIX all the members of a file owner’s group may receive equal rights for a new file. These approaches may result in unnecessary rights being given to some users, violating the principle of least privilege (see Chapter 6 and [Fer11d]).

CONTEXT
A computing system that needs to control access to the objects it creates because of their different degrees of sensitivity. Rights for these objects are defined by authorization rules or policies that should be enforced when a process attempts to access an object.

PROBLEM
In a computing environment, executing applications need to create objects for their work. Some objects are created at program initialization, while others are created dynamically during execution. The access rights of processes with respect to objects must be defined when these processes are created, or there may be opportunities for the processes to misuse them. Applications also need resources such as I/O devices and others that may come from resource pools: when these resources are allocated, the application must be given rights to them.

The solution to this problem must resolve the following forces:

 Applications create objects of many different types, but we need to handle them uniformly with respect to their access rights, otherwise it would be difficult to apply standard security policies.
 We need to allow objects in a resource pool to be allocated and have their rights set dynamically; not doing so would be too rigid.
 There may be specific policies that define who can access a new object, and we need to apply them when creating the rights for an object. This is a basic aspect of security.
SOLUTION
Whenever a new object is created, define a list of subjects that can access it, and in what way.

STRUCTURE
The class diagram for this pattern is shown in Figure 7.7. When a Process creates a new object through a Factory, the CreationRequest includes the features of the new object. Among these features is a list of rights defining rights for a Subject to access the created Object. This implies that we need to intercept every access request: this is done by an implementation of the CONTROLLED-OBJECT MONITOR pattern (page 132).

Figure 7.7: Class diagram for the CONTROLLED-OBJECT FACTORY pattern


DYNAMICS
Figure 7.8 shows the dynamics of object creation. A Process creating an Object through a Factory defines the rights for other subjects with respect to this object.

Figure 7.8: Object creation dynamics


IMPLEMENTATION
Each object may have an associated access control list (ACL). This will list the rights each subject (represented by a process) has for the associated object. Each entry specifies the rights that any other object within the system can have. In general, each right can be an ‘allow’ or a ‘deny’. These are known as access control entries (ACE) in the Windows environment [Har01] [Mic00]. The set of access rules is also known as the access control list (ACL) in Windows and most other operating systems.

Capabilities are an alternative to an ACL. A capability corresponds to a row in an access matrix. This is in contrast to the ACL, which is associated with the object. The capability indicates to the secure object that the subject does indeed have the right to perform the operation. The capability may carry some authentication features in order to show that the object can trust the provided capability information. A global table can contain rows that represent capabilities for each authenticated user [And08], or the capability may be implemented as a list for each user which indicates to which object they have access.

EXAMPLE RESOLVED
Our users can now be given only the rights to the created objects that they need. This prevents them from having too many (possibly unnecessary) object rights: many misuses occur through processes having too many rights.

CONSEQUENCES
The CONTROLLED-OBJECT FACTORY pattern offers the following benefits:

 There can be no objects that have default access rights because somebody forgot to define access rights for them.
 It is possible to define access rights for an object based on its sensitivity.
 Objects allocated from a resource pool can have rights attached to them dynamically.
 The operating system can apply ownership policies: for example, the creator of an object may receive all possible rights to the objects it creates.
The following potential liabilities may arise from applying this pattern:

 There is a process creation overhead.
 It may not be clear what initial rights to define.
KNOWN USES
The Win32 API allows a process to create objects with various create() system calls using a structure containing access control information (DACL) passed as a reference. When the object is created the access control information is associated with the object by the kernel: the kernel returns a handle to the caller to be used for access to the object. Other operating systems apply predefined sets of rights: for example, all the members of the owner’s group in UNIX may receive equal rights for a new file.

SEE ALSO
Builder and other creation patterns [Gam94].

7.5 Controlled-Object Monitor
The CONTROLLED-OBJECT MONITOR pattern allows control of access by a subject to an object, using a specialized Reference Monitor to intercept access requests from processes. The Reference Monitor checks whether the process has the requested type of access to the object.

EXAMPLE
Our operating system does not check all user requests for access to resources such as files or areas of memory. A hacker discovered that some accesses are not checked, and was able to steal customer information from our files. He also left a program that randomly overwrites memory areas and creates serious disruption for other users.

CONTEXT
A computing system that needs to control access to its created objects because of their different degrees of sensitivity. Rights for these objects are defined by authorization rules or policies that should be enforced when a process attempts to access an object.

PROBLEM
When objects are created we define the rights of processes over them. These authorization rules or policies must be enforced when a process attempts to access an object.

The solution to this problem must resolve the following forces:

 There may be many objects with different access restrictions defined by authorization rules; we need to enforce these restrictions when a process attempts to access an object.
 We need to control different types of access, or the object may be misused.
SOLUTION
Use a specialized reference monitor to intercept access requests from processes. The reference monitor checks whether the process has the requested type of access to the object according to some access rule.

STRUCTURE
Figure 7.9 shows the class diagram for this pattern. This is a more specific implementation of the Reference Monitor pattern ([Sch06b]). The modification shows how the system associates the rule to the secure object in question.

Figure 7.9: Class diagram for the CONTROLLED-OBJECT MONITOR pattern


DYNAMICS
Figure 7.10 shows the dynamics of secure subject access to a secure Object. Here the request is sent to the ReferenceMonitor, where it checks the AccessRules. If the access is allowed, it is performed and the result returned to the subject. Note that here, a handle or ticket is returned to the subject, so that future access to the secure Object can be performed directly, without additional checking.

Figure 7.10: Sequence diagram for validating an access request


IMPLEMENTATION
A possible implementation would be as follows:

1 A user is authenticated when they log on.
2 Created objects inherit the original user’s ID that is contained within a token. This token associates with the user process to be used by the operating system to resolve access rights. Only those authorized may have the desired access to the secure object.
3 Each object that a user wishes to access may have an associated access control list (ACL). This will list what right each user has for the associated object.
4 Each entry specifies what right any other object within the system can have. In general, each right can be an ‘allow’ or a ‘deny’. These are also known as access control entries (ACE) in the Windows environment [Har01] [Mic00]. The set of access rules is also known as the access control list (ACL) in Windows and most operating systems (see Chapter 6).
Capabilities are an alternative to the ACL. A capability corresponds to a row in an access matrix. This is in contrast to the ACL, which is associated with the object. The capability indicates to the secure object that the subject does indeed have the right to perform the operation. The capability may carry some authentication features in order to show that the object can trust the provided capability information. A global table can contain rows that represent capabilities for each authenticated user [And08], or the capability may be implemented as a list corresponding to each user, indicating what objects the user can access.

EXAMPLE RESOLVED
A reference monitor mediates all requests. There are now no unchecked requests, so a hacker cannot get access to unauthorized files or memory areas.

CONSEQUENCES
The CONTROLLED-OBJECT MONITOR pattern offers the following benefits:

 The access rules can implement an access matrix defining different types of access for each subject.
 Each access request can be intercepted and accepted or rejected depending on the authorization rules.
Potential liabilities include:

 The need to protect the authorization rules. However, the same mechanism that protects resources can also protect the rules.
 The overhead of controlling each access. Some accesses may be compiled for efficiency.
KNOWN USES
Windows NT. The Windows NT security subsystem provides security using the patterns described here. It has the following three components (see [Har01] [Kel97] [Mic00]):

 Local Security Authority
 Security Account Manager
 Security Reference Monitor
This implementation is described in detail on page 153.

Java 1.2 Security. The Java security subsystem provides security using the patterns described here. The Java access controller builds access permissions based on permission and policy. It has a checkPermission method that determines the codesource object of each calling method and uses the current policy object to determine the permission objects associated with it. Note that the checkPermission method will traverse the call stack to determine the access of all calling methods in the stack. The java.policy file is used by the security manager and contains the grant statements for each codesource.

7.6 Protected Entry Points
The PROTECTED ENTRY POINTS pattern describes how to force a call from one process to another to go through only prespecified entry points where the correctness of the call is checked and other access restrictions may be applied.

EXAMPLE
ChronOS is a company building a new operating system, including a variety of plug-in services such as media players, browsers and others. In their design, processes can call each other in unrestricted ways. This makes process calls fast, which results in generally good performance, and everybody is satisfied. However, when they test the system, an error anywhere produces problems, because it propagates to other processes, corrupting their execution. Also, many security attacks are shown to be possible. It is clear that when their systems are in use they will acquire a bad reputation and ChronOS will have problems selling it. They need to have a system that provides resilient service in the presence of errors, and which is resistant to attacks.

CONTEXT
Executing processes in a computing system. Processes need to call other processes to ask for services or to collaborate in the computation of an algorithm, and usually share data and other resources. The environment can be centralized or distributed. Some processes may be malicious or contain errors.

PROBLEM
Process communication has an effect on security, because if a process calls another using entry points without appropriate checks, the calling process may read or modify data illegally, alter the code of the executing process, or take over its privilege level. If the checks are applied at specific entry points, some languages, such as C or C++, let the user manipulate pointers to bypass those entry points. Process communication also has a major effect on reliability, because an error in a process may propagate to others and disrupt their execution.

The solution to this problem must resolve the following forces:

 Executing processes need to call each other to perform their functions. For example, in operating systems user processes need to call kernel processes to perform I/O, communications and other system functions. In all environments, process may collaborate to solve a common problem, and this collaboration requires communication. All this means that we cannot use process isolation to solve this problem.
 A call must go to a specified entry point or checks could be bypassed. Some languages let users alter entry point addresses, allowing input checks to be bypassed.
 A process typically provides services to other processes, but not all services are available to all processes. A call to a service not authorized to a process can be a security threat or allow error propagation.
 In a computing environment we have a variety of processes with different levels of trust. Some are processes that we normally trust, such as kernel processes; others may include operating system utilities, user processes and processes of uncertain origin. Some of these processes may have errors or be malicious. All calls need to be checked.
 The number, type and size of the passed parameters in a call can be used to attack a process, for example by producing a buffer overflow. Incorrect parameters may produce or propagate an error.
SOLUTION
Systems that use explicit message passing have the possibility of checking each message to see if it complies with system policies. For example, one security feature that can be applied when calling another process is protected entry points. A process calling another process can only enter the called process at predesigned entry points, and only if the signature used is correct (name, number of parameters, type and size of parameters). This prevents bypassing entry checks and avoids attacks such a buffer overflows.

STRUCTURE
Figure 7.11 shows the class diagram of the solution. CallingProcess and CalledProcess are roles of processes in general. When a CallingProcess makes a request for a service to another process, the request is handled by an EntryPoint. This EntryPoint has a name and a list of parameters with predefined numbers, types, and size limits that can be used to check the correctness of the call signature. It can optionally add access control checks by using a Reference Monitor pattern or other input data tests.

Figure 7.11: Class diagram for the PROTECTED ENTRY POINTS pattern


DYNAMICS
Figure 7.12 shows a CallingProcess performing a service call. The call must use a proper signature: that is, if the name of the service (opName) or the names of the parameters are incorrect, and the type or length of the parameters is not correct, it is rejected (this is checked by operation checkParmList).

Figure 7.12 Sequence diagram for a process making a service call


IMPLEMENTATION
Kernels support calls as direct calls or through mailboxes. In the first case, the called process must check that the call is correct; in the second case, the mailbox must do the checking.

Entry points must be expressed as references as in Java, and not as pointers, as in C or C++ (as pointers allow arithmetic operations). In languages that use pointers, it is necessary to restrict their use in procedure calls; for example by disallowing pointer arithmetic.

EXAMPLE RESOLVED
If the parameters of all calls are validated through protected entry points, many security and reliability problems can be avoided. Additional checks, such as access control and data value checks, can also be applied.

CONSEQUENCES
The PROTECTED ENTRY POINTS pattern offers the following benefits:

 If we can check all the calls of one process to another, we can check that the calls are for appropriate services and apply checks for security or reliability purposes.
 Checking the number, type and length of the parameters passed in a call can prevent a variety of attacks and stop the propagation of some errors.
 If we know the level of trust of processes, we can adjust the number of checks; for example, we can apply more checks to suspicious processes.
KNOWN USES
 Multics.
 Systems that use ring architectures, for example the Intel Series 86 and Pentium.
 Systems that use capabilities, such as IBM S/6000.
 A specific use can be found in a patent for PC BIOS [Day91].
SEE ALSO
 This pattern can be seen as a specific realization of the abstract principle validate input parameters.
 The PROTECTION RINGS pattern (see below).
 Multilevel Secure Partitions; see [Fer08c].
 The CAPABILITY pattern (page 96).
 Access control and distributed access control (Chapter 6). These checks can be applied in specific entry points to control access to resources.
7.7 Protection Rings
The PROTECTION RINGS pattern allows control of how processes call other processes and how they access data. Crossing of rings is done through gates that check the rights of the crossing process. A process calling another process or accessing data in a higher ring must go through a gate.

EXAMPLE
The ChronOS designers found that for applications that use programs with a variety of origins, there is a high overhead in applying elaborate checks to all of them. It would be more efficient to apply the checks selectively, depending on how much they trust the programs making the calls, but this is not usually known at execution time. If they could find a way to classify processes according to trust, they could improve the application of checks. It is not enough to rely on program features to enforce entering the right entry points, because applications may come in a variety of languages, some of which may allow skipping entry points.

CONTEXT
Executing processes in a computing system. Processes need to call other processes to ask for services or to collaborate in the computation of an algorithm, and usually share data and other resources. Some processes may be malicious or contain errors that may affect process execution. This pattern applies only to centralized environments, as opposed to distributed systems.

PROBLEM
Defining a set of protected entry points is not enough if we cannot enforce their use. How can we prevent a process from calling another on an entry point that has no checks? We cannot rely on language features unless we only use a restricted set of languages, which is not practical in general. If all processes are alike we also need to apply the same checks to all of them, which may be overkill.

The solution to this problem must resolve the following forces:

 We want to be able to enforce the application of protected entry points, at least for some processes. In this way, requests from suspicious processes can always be controlled.
 We would like to separate processes according to their level of trust, and check only calls from a low-level to a higher-level process. This can reduce execution-time overhead considerably.
 In each higher level we can check signature validity, as well as control access or apply reliability tests. These actions should result in a more secure execution environment.
SOLUTION
Define a set of hierarchical protection domains, called protection rings (typically 4 to 32) with different levels of trust. Assign processes to rings based on their level of trust. Ring crossing is performed through gates that enforce protected entry points: a process calling a higher-level process or accessing data at a higher level can only call this process or access data at predesigned entry points with controlled parameters. Additional checks for security or reliability can be applied at the entry points.

STRUCTURE
Figure 7.13 shows a class diagram for this pattern. The CallingProcess requests services from a CalledProcess. To do so, it must enter a CallGate, which applies protected entry points that check the correct use of signatures. CallRules define the requirements for inter-level calls. The CallingProcess can access Data according to a set of DataAccess-Rules.

Figure 7.13: Class diagram for the PROTECTION RINGS pattern


DYNAMICS
Figure 7.14 shows a sequence diagram for a call to a higher-privilege ring. If the call fails an exception may be raised.

Figure 7.14: Sequence diagram for a successful call to a higher-privilege ring


IMPLEMENTATION
The call rules and the data access rules are usually implemented in the call instruction microcode [int99]. Figure 7.15 shows a typical use of rings. Processes are assigned to rings based on their level of trust; for example, we could assign four rings in decreasing order of privilege and trust, to supervisor, utilities, trusted user programs, untrusted user programs.

Figure 7.15: Assignment of protection rings


The program status word of the process indicates its current ring, and data descriptors also indicate their assigned rings. The values of the calling and called processes are compared to apply the transfer rules.

The Intel X86 architecture [int99] applies two rules:

 Calls are allowed only in a more privileged direction, with possible restriction of a minimum calling level.
 Data at level p can be accessed only by a program executing at a more privileged level (<= p).
Another possibility for improving security is to allow calls only within a range of rings: in other words, jumping many rings is considered suspicious. Multics defined a call bracket, where calls are allowed only within rings in the bracket. More precisely, for a call from procedure i to a procedure with bracket (n1, n2, n3) the following rules apply:

 If n2<i<=n3, the call is allowed to specific entry points.
 If <n3, the call is not allowed.
 If i < n1, any entry point is valid.
This extension only makes sense for systems that have many rings.

EXAMPLE RESOLVED
Now we can preassign processes to levels according to their trust. All calls to processes of higher privilege are checked. Processes of low trust get more checks.

CONSEQUENCES
The PROTECTION RINGS pattern offers the following benefits:

 We can separate processes according to their level of trust.
 Level transfers happen only through gates where we can apply the PROTECTED ENTRY POINTS pattern; that is, we have enforced protected entry points for upward calls.
 We can control procedure calls as well as data access across levels.
The pattern also has some potential liabilities:

 Crossing rings take time. Because of this delay, some operating systems use fewer rings. For example, Windows uses two rings, IBM’s OS/2 uses three rings [wik2]. Using fewer rings improves performance at the expense of security.
 Without hardware support the crossing ring overhead is unacceptable, which means that this approach is only practical for operating systems and for centralized environments.
VARIANTS
 Rings don’t need to be strictly hierarchical; partial orders are possible and convenient for some applications. For example, a system that includes a secure database could assign a level to the database equal to but separated from system utilities; the highest level is for the kernel, and the lowest level is for user programs. This was done in a design involving an IBM 370 [Fer78].
 In some systems, such as the MV8000, rings are associated with memory locations.
 Multics used the concept of the call bracket, where a call can be made within a range of rings.
KNOWN USES
 Multics introduced this concept and used 32 rings, as well as call brackets [Gra68].
 The Intel Series X86 and Pentium [int99].
 MV8000 [mv] [Wal81].
 Hitachi HITAC.
 ICL 2900, VAX 11 and MARA, described in [Fro85], which also describes Multics and the Intel series.
 [Shi00] shows a use of rings to protect against malicious mobile code.
 An IBM S/370 was modified to have non-hierarchical rings [Fer78].
 Rings have been used for fault-tolerant applications [Oza88].
SEE ALSO
 A combination (process, domain) corresponds to a row of the Access Matrix [Sch06b].
 Multilevel Secure Partitions [Fer08c] is an alternative for distributed environments, in which processes are assigned levels based on multilevel security models.
 PROTECTED ENTRY POINTS (page 136).