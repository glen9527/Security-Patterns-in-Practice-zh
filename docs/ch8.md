# 第 8 章 用于安全执行和文件管理的模式
> CHAPTER 8 Patterns for Secure Execution and File Management

The severity of the laws prevents their execution.

Charles de Montesquieu

## 8.1 Introduction
In this chapter we present patterns for the secure execution of processes:

- VIRTUAL ADDRESS SPACE ACCESS CONTROL. How can we control access by processes to specific areas of their virtual address space (VAS) according to a set of predefined access types? Divide the VAS into segments that correspond to logical units in the programs. Use special words (descriptors) to represent access rights for these segments.
- EXECUTION DOMAIN. How can we define an execution environment for processes, indicating explicitly all the resources a process can use during its execution, as well as the type of access for the resources? Attach a set of descriptors to the process that represent the rights of the process.
- CONTROLLED EXECUTION DOMAIN. How can we define an execution environment for processes? Attach a set of descriptors to each process that represents the rights of the process. Use the Reference Monitor pattern to enforce access.
- VIRTUAL ADDRESS SPACE STRUCTURE SELECTION. How can we select the virtual address space for operating systems that have special security needs? Some systems emphasize isolation, others information sharing, yet others good performance. The organization of each process’ virtual address space (VAS) is defined by the hardware architecture and has an effect on performance and security. Consider all the hardware possibilities and select according to need.

Assume here that resources are represented as objects, as it is common in modern operating systems. Figure 8.1 shows how these patterns are organized into a pattern language. For example, authentication is needed for file access and for controlled object access, a subject must be authorized to access some object in a specific way, and we need to make sure that the requester is not an imposter. The other three patterns complete the definition of the controlled execution domain, where the creation and access to objects are now controlled.

Figure 8.1: Patterns for secure process execution


The first three patterns come from [Fer02], the last one from [Fer05c].

## 8.2 Virtual Address Space Access Control
The VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern allows control of access by processes to specific areas of their virtual address space (VAS) according to a set of predefined access types.

### CONTEXT
Multiprogramming systems with a variety of users. Processes executing on behalf of these users must be able to share memory areas in a controlled way. Each process runs in its own address space. The total virtual address space (VAS) at a given moment includes the union of the VASs of the individual processes, including user and system processes. Typical allowed accesses are read, write, and execute, although finer access typing is possible.

### PROBLEM
Processes must be controlled when accessing memory, otherwise they could overwrite each other’s memory areas or gain access to private information. While relatively small amounts of data can be directly compromised, illegal access to system areas could allow a process to get a higher execution privilege level and thus access files and other resources.

The solution to this problem must resolve the following forces:

- There is a need for a variety of access rights for each separate logical unit (segment) of the VAS. In this way security and controlled sharing are possible.
- There is a variety of virtual memory address space structures: some systems use a set of separate address spaces, others a single-level address space. Further, the VAS may be split between the users and the operating system. We would like to control access to all of these types of virtual memory in a uniform manner.
- For any approach to be efficient, hardware support is necessary. This implies that an implementation of the solution will require a specific hardware architecture. However, the solution must be hardware-independent.
### SOLUTION
Divide the VAS into segments that correspond to logical units in the programs. Use special words (descriptors) to indicate access rights as the starting address of the accessible segment, the limit of the accessible segment and the type of access permitted (read, write, execute).

Figure 8.2 shows a class diagram of the solution. A Process must have a Descriptor to access a segment in the VAS.

Figure 8.2: Class diagram for the VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern


### IMPLEMENTATION
Some implementation aspects include:

- The limit check when accessing an address must be done by the instruction microcode, or the overhead would be unacceptable. This check is part of an instance of the REIFIED REFERENCE MONITOR pattern (page 100).
- The same idea applies to purely paging systems, except that the limit in the descriptor is defined by the page size. In paged systems pages do not correspond to logical units and cannot perform a fine-granularity security control.
- There are two basic ways to implement this pattern:
    - Proper descriptor systems. The descriptors are loaded at process creation by the operating system, handled through special registers, and disappear at the end of execution.
    - Capability systems. A special trusted portion of the operating system distributes capabilities to programs. Programs own these capabilities: to use them, the operating system loads them into special registers or memory segments. In both cases, access to files is derived from their ACLs.
### CONSEQUENCES
The VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern offers the following benefits:

- The pattern provides the required segment protection, because a process cannot access a segment without a descriptor for it. Two processes with descriptors with the same base–limit pair can conveniently share a segment.
- The pattern applies to any type of virtual address space: single, segregated or split.
- If all resources are mapped to the virtual address space, the pattern can control access to any type of resource, including files.
- The solution can be implemented in different ways.

The pattern also has the following potential liabilities:

- Segmentation makes storage allocation inefficient because of external fragmentation [Sil03]. In most systems segments are paged for convenient allocation.
- Hardware support is needed, which makes the implementation of this solution hardware-dependent.
- In systems that use separate address spaces, it is necessary to add an extra identifier to the descriptor registers to indicate the address space number.
### KNOWN USES
The Plessey 250 [Ham73], Multics [Gra68], IBM S/38, IBM S/6000, Intel X86 [Chi84] and Intel Pentium use some type of descriptors for memory access control. The operating systems in these machines must use this approach for memory management.

### SEE ALSO
This pattern is a direct application of the AUTHORIZATION pattern (page 74) to the processes’ address space.

## 8.3 Execution Domain
The EXECUTION DOMAIN pattern describes how to define an execution environment for processes, indicating explicitly all the resources a process can use during its execution, as well as the type of access for the resources.

### CONTEXT
A process executes on behalf of a user, group or role (a subject). During execution a process must posses the access rights to resources that were defined for its subject. The set of access rights given to a process define its execution domain. At times the process may also need to enter other domains to perform its work; for example, to access data from a file in another domain. Frequently, users structure their domains as a tree of nested domains.

### PROBLEM
Restricting a process to a specific set of resources is a basic step to control malicious behavior. Otherwise, unauthorized processes could destroy or modify information in files or databases, with obvious results, or could interfere with the execution of other processes.

The solution to this problem must resolve the following forces:

- There is a need to restrict the actions of a process during its execution, otherwise it could perform illegal actions.
- Resources typically include memory and I/O devices, but can also be system data structures and special instructions. Although resources are heterogeneous, we want to treat them uniformly.
- A process needs the flexibility to create multiple domains and to enter inner domains for specific purposes.
- There should be no restrictions on how the domain is implemented.
### SOLUTION
Attach a set of descriptors to the process that represent the rights of the process. In Figure 8.3, the class Domain represents domains, and, in conjunction with the Composite pattern [Gam94], describes nested domains. Operation enter() in class Domain lets a Process enter a new Domain. A Domain includes a set of descriptors that define rights for resources.

Figure 8.3: Class diagram for the EXECUTION DOMAIN pattern


### CONSEQUENCES
The EXECUTION DOMAIN pattern offers the following benefits:

- It lets users apply the principle of least privilege to processes: they can be given only the rights they need to perform their functions.
- It can be applied to describe access to any type of resource if the resource is mapped to a specific memory address.
- Processes may have several execution domains, either peer or nested.
- The model does not restrict the implementation of domains. A domain could be represented in many ways. For example, the Plessey 250, IBM S/38 and IBM S/6000 use capabilities. The Intel X86 and Pentium series (and their corresponding operating systems) use descriptors for memory access control.
- Special domains with predefined rights or types of rights can be defined. For example, Multics and the Intel X86 series use protection rings, where each ring is assigned to a type of program: supervisor, utilities, user programs, and external programs. The rings are hierarchically structured, based on their level of trust. Descriptors are used to cross rings in program calls.
- The descriptors refer to VAS segments, which is the most usual implementation. However, they could indicate resources not mapped to memory.

This pattern also has the following potential liabilities:

- Extra complexity: special hardware may be needed to accelerate processing.
- Performance overhead in setting up domains and in entering and leaving domains. Because of this, some operating systems for Intel processors use only two rings, improving performance but reducing security.
- The way to set up the execution domain is implementation-dependent. In descriptor systems the operating system creates a descriptor segment with the required descriptors. In capability systems the descriptors are part of the process code and are enabled during execution.
### KNOWN USES
- The concept of domains comes from Multics [Gra68]. Segments or pages (as in EROS [Sha02]) are structured as tree directories.
- The Plessey 250 and the IBM S/6000 running AIX [Cam90] are good examples of the use of this pattern.
- The Java Virtual Machine defines restricted execution environments in a similar way [Oak01].
## 8.4 Controlled Execution Domain
The CONTROLLED EXECUTION DOMAIN pattern allows control of access to all operating system resources by processes, based on user, group or role authorizations.

### EXAMPLE
When Jim discovered that the customer files had authorizations and could not be accessed directly, he tried another approach. He realized that processes were not well controlled and could access memory and other resources belonging to other users. He systematically searched areas of memory and I/O devices being used by other processes until he could scavenge a few credit card numbers that he could use in his illicit activities.

### CONTEXT
A system in which a process executes on behalf of a user or role (a subject). A process must have access rights to use these resources during execution. The set of access rights given to a process define its execution domain. Processes must be able to share resources in a controlled way. The rights of the process are derived from the rights of its invoker.

### PROBLEM
Even if direct access to files is restricted, users can use ‘tunneling’ to attack them through a lower level. If the process execution environment is uncontrolled, processes can scavenge information by searching memory and accessing disk drives. They might also take control of the operating system itself, in which case they have access to everything.

The solution to this problem must resolve the following forces:

- We need to constrain the execution of processes and restrict them to use only resources that have been authorized based on the rights of the subject that activated the process.
- Subjects can be users, roles or groups. We want to deal with them uniformly.
- Resources typically include memory and I/O devices, but can also be files and special instructions. We want to consider them in a uniform way.
- A subject may need to activate several processes, and a process may need to create multiple domains. Execution domains may need to be nested. We want flexibility for our processes.
- Typically, only a subset of a subject’s rights needs to be used in a specific execution. We need to provide to a process only the rights it needs during its execution (using the principle of least privilege).
- The solution should put no constraints on implementation.
### SOLUTION
Figure 8.4 shows the class diagram of the CONTROLLED EXECUTION DOMAIN pattern. This model combines the AUTHORIZATION (page 74), EXECUTION DOMAIN (page 149) and REIFIED REFERENCE MONITOR (page 100) patterns to let processes operate in an environment with controlled actions based on the rights of their invoker.

Figure 8.4: Class diagram for the CONTROLLED EXECUTION DOMAIN pattern


Process execution follows the EXECUTION DOMAIN pattern (page 149): as a process executes it creates one or more Domains. Domains can be recursively composed. The descriptors used in the process’ domains are a subset of the Authorizations that the subject has for some ProtectionObjects (defined by an instance of the AUTHORIZATION pattern). ProtectionObject is a superclass of the abstract Resource class, and ConcreteResource defines a specific resource. Process’ requests go through a ReferenceMonitor that can check the domain descriptors for compliance.

Figure 8.5 (page 154) shows a sequence diagram showing the use of a right after entering a domain. Here x denotes a segment requested by the Process. An instance of the Reference Monitor pattern controls the process requests. This diagram assumes that the descriptors of the domain have been previously set up.

Figure 8.5: Sequence diagram for entering a domain and using a right in that domain


### IMPLEMENTATION
Windows NT. The Windows NT security subsystem provides security using this and others of the patterns described here, including REIFIED REFERENCE MONITOR (page 100), AUTHENTICATOR (page 52) and ACCESS CONTROL LIST (page 91). It has the following three components [Har01] [Kel97] [Mic00]:

- Local Security Authority (LSA)
- Security Account Manager (SAM)
- Security Reference Monitor (SRM)

The LSA and SAM work together to authenticate the user and create the user’s access token. The security reference monitor runs in kernel mode and is responsible for the enforcement of access validation. When access to an object is requested, a comparison is made between the file’s security descriptor and the secure ID (SID) information stored in the user’s access token. The security descriptor is made up of access control entries (ACE) included in the object’s access control list (ACL). When an object has an ACL, the SRM checks each ACE in the ACL to determine whether access is to be granted. After the SRM grants access to the object, further access checks are not needed, as a handle to the object that allows further access is returned the first time.

Types of object permissions are ‘no access’, ‘read’, ‘change’, ‘full control’ and ‘special access’. For directory access, the following are added: ‘list’, ‘add’ and ‘read’.

Windows use the concept of a handle for access to protected objects within the system. Each object has a security descriptor (SD) that contains a discretionary access control list (DACL) for the object. Each process has a security token that contains an SID that identifies the process. This is used by the kernel to determine whether access is allowed. The ACL contains access control entries (ACEs) that indicate what access is allowed for a particular process SID. The kernel scans the ACL for the rights corresponding to the requested access.

A process requests access to an object when it asks for a handle using, for example, a call to CreateFile(), which is used both to create a new file or open an existing file. When the file is created, a pointer to an SD is passed as a parameter. When an existing file is opened, the request parameters, in addition to the file handle, contain the desired access, such as GENERIC_READ. If the process has the desired rights for the access, the request succeeds and an access handle is returned; this allows different handles to the same object to have different accesses [Har01]. Once the handle is obtained, additional access to read a file will not require further authorization. The handle may also be passed to another trusted subject for further processing.

Java 1.2 Security. The Java security subsystem provides security using the patterns described here. The Java access controller builds access permissions based on permission and policy. It has a checkPermission method that determines the codesource object of each calling method and uses the current policy object to determine the permission objects associated with it. Note that the checkPermission method will traverse the call stack to determine the access of all calling methods in the stack. The java.policy file is used by the security manager and contains the grant statements for each codesource.

### EXAMPLE RESOLVED
A new operating system was installed, with mechanisms to make processes operate with the rights of their activator. Jim did not have access to customer files, which made his processes also unable to access these files. Now he could not scavenge in other users’ areas and his illicit actions were thwarted.

### CONSEQUENCES
The CONTROLLED EXECUTION DOMAIN pattern offers the following benefits:

- We can apply the least privilege principle to processes based on the rights of their activators. This also provides accountability.
- It can be applied with any type of resource.
- Subjects may activate any number of processes, and processes may have several execution domains.
- The model is abstract and does not restrict possible implementations.
- Execution domains are defined according to the EXECUTION DOMAIN pattern (page 149) and may include any subset of the subject’s rights.

This pattern also has the following potential liabilities:

- Some extra complexity and performance overhead.
- Dependence on the hardware architecture.
### KNOWN USES
The IBM S/38, the IBM S/6000 running AIX, the Plessey 250 [Ham73] and EROS [Sha02] have applied this pattern using capabilities. Proper descriptor systems such as the Intel architectures may use this approach, although their operating systems do not always do so. Recent uses of this pattern include Adobe Reader Protected Mode [Ark 10] and Chromium Sandbox [Chr].

### SEE ALSO
This pattern uses the AUTHORIZATION (page 74), EXECUTION DOMAIN (page 149) and REIFIED REFERENCE MONITOR (page 100) patterns. The VIRTUAL ADDRESS SPACE ACCESS CONTROL pattern (page 146) may be used indirectly by the EXECUTION DOMAIN pattern.

## 8.5 Virtual Address Space Structure Selection
The VIRTUAL ADDRESS SPACE STRUCTURE SELECTION pattern describes how to select the virtual address space for operating systems that have special security needs. Some systems emphasize isolation, others information sharing, others good performance. The organization of each process’ virtual address space (VAS) is defined by the hardware architecture and has an effect on performance and security. The pattern enables all the hardware possibilities to be considered and selected according to need.

### EXAMPLE
We have a system running applications using images that require large graphic files. The application also has stringent security requirements, because some of the images are sensitive and should be only accessed by authorized users. We need to decide on an appropriate VAS structure.

### CONTEXT
Virtual memory allows the total size of the memory used by processes to exceed the size of physical memory. Upon use, the virtual address is translated by the address translation unit (usually the memory management unit (MMU) in microprocessors) to obtain a physical address that is used to access physical memory. To execute a process, the kernel creates a per-process virtual address space. We have a multiprogramming system with a variety of users and applications. Processes execute on behalf of users and at times must be able to share memory areas, at other times must be isolated, and in all cases we need access control. Performance may also be an issue.

### PROBLEM
We need to select the virtual address space for processes depending on the majority of the applications we intend to execute, otherwise we can have mismatches that may result in poor security or performance.

The solution to this problem must resolve the following forces:

- Each process needs to be assigned a relatively large VAS to hold its data, stack, space for temporary variables, variables to keep the status of its devices, and other information.
- In multiprogramming environments processes have diverse requirements; some require isolation, others information sharing, others good performance.
- Data typing is useful to prevent errors and improve security. Several attacks occur by executing data and modifying code [Gol06].
- Sharing between address spaces should be convenient, otherwise performance may suffer.
### SOLUTION
Select from four basic approaches that differ in their security features:

- One address space per process (Figure 8.6). The supervisor (kernel plus utilities) and each user process get their own address spaces. Using one VAS per process has the following trade-offs:

Figure 8.6: One address space per process


- Good process isolation.
- Some protection against possible illegal actions by a compromised operating system.
- Simplicity.
- Sharing is complex (special instructions to cross spaces are needed).
- Two address spaces per process (Figure 8.7). Each process gets a data and a code (program) virtual address space. Use of two VASs per process has the following trade-offs:

Figure 8.7: Two address spaces per process


- Good process isolation.
- Some protection against possible illegal actions by a compromised operating system.
- Data and instructions can be separated for better protection (some attacks take advantage of execution of data or modification of code). Data typing is also good for reliability.
- A disadvantage is complex sharing, plus poor address space utilization.
- One address space per user process, all of them shared with one address space for the operating system (Figure 8.8). The operating system (supervisor) can be shared between all processes. This scheme has the following trade-offs:

Figure 8.8: One address space per user process, all of them shared with one address space for the operating system


- Good process isolation.
- Good sharing of resources and services.
- Suboptimal with respect to security (the supervisor has complete access to the user processes, and it must be trusted).
- The address space available to each user process has been halved.
- A single-level address space (Figure 8.9). Everything, including files, is mapped to one memory space. Use of a single-level address space has the following trade-offs:

Figure 8.9: A single-level address space


- Good process isolation.
- Logical simplicity.
- Uniform protection (all I/O is mapped to memory).
- Offers the most elegant solution (only one mechanism to protect memory and files), and is potentially the most secure if capabilities are also used.
- Hard to implement in hardware due to the large address space required.
### IMPLEMENTATION
The VAS is implemented by the hardware architecture. The operating system designer can choose one of the architectures based on the requirements of the applications, according to the trade-offs discussed above. In a particular case, the choice may be influenced by company policies, cost, performance and other factors, as well as security.

### CONSEQUENCES
In addition to the specific benefits described as part of the solution (trade-offs), the VIRTUAL ADDRESS SPACE STRUCTURE SELECTION pattern has the following general liabilities:

- Without hardware support it is not feasible to separate the virtual address spaces of the processes. Most processors use register pairs or descriptors that indicate the base (start) of a memory unit (segment) and its length or limit [Sil08].
- If the mix of applications is not well-defined, it is hard to select the best solution. Considerations other than security then become more important.
### KNOWN USES
- One address space per process. The NS32000, WE32100 and Clipper microprocessors [Fer85]. Several versions of UNIX were implemented in these processors.
- Two address spaces per process. Used in the Motorola 68000 series. The Minix 2 operating system uses this approach [Tan06].
- One address space per user process, all of them shared with one address space for the operating system. Used in the VAX series and in Intel processors. Windows runs in this type of address space.
- Single-level address space. Multics, IBM S/38, IBM S/6000 and HP PA-RISC use this approach. Multics had its own operating system. IBM AIX ran in S/6000 [Cam90]. The PA-RISC architectures ran a version of UNIX.
### SEE ALSO
- SECURE PROCESS/THREAD (page 120). The interaction between processes depends strongly on the virtual address space configuration, which can affect security, performance and sharing properties of the processes.
- VIRTUAL ADDRESS SPACE ACCESS CONTROL (page 146) [Fer02] [Sch06b]. A VAS is assigned to each process that can be accessed according to the rights of the process. The VIRTUAL ADDRESS SPACE STRUCTURE SELECTION pattern is applied first to select the appropriate structure. Once selected, the VAS is secured using the Controlled Virtual Address Space pattern.
- The Secure Storage pattern for rebooting is discussed by [Loh10].