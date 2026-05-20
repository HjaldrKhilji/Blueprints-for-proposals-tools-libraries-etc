# **TITLE: Extended Built-System-Library Interface for C++**

## **Abstract**

This informal document was initially draft at 5/20/2026 and proposes a ***built system interface in C++*** that is to provide a standardized interface to a built system. The proposal first describes the necessity of built systems to be provided to C++ developers as a library and then tries to expand such a built system to incorporate facilities for the creation of a Built, Compilation, and runtime environment for an application.

## **Introduction (1)**

### The essence of a built system (1.1)

A built system is one that:

1.(usually deterministically) automates the process of compiling many source files into a

type of compiled library or into an executable.

2.May have several facilities to make such processes efficient, such facilities may contain but are not limited to A form of Non volatile memory usage to track files such that files that need not to be recompiled aren't recompiled, Compiling multiple source files at the same time, requiring the underlying compiler (if not built in to the built system) to confirm to certain invariants.

3.Should provide commonly used programming constructs to make automation of such processes easier.

### How current built tools fail each point respectively (1.2)

#### 1 (1.2.1)

1. Current built systems complicate simple automations at a large scale by over abstracting sub builds and by not keeping simple things simple. A great example of this is CMake, where each parameter to a simple build operation containing a few files require you to set certain symbols to certain values, including values that describe the pathnames of the source files in the project. This is an obvious issues since Such issues complicate automations and makes the every CMake config file be very fragile to mistakes and misconfiguration. To make it worse, These facilities don't follow a class hierarchy, hence its harder to relate them or to use them together. CMake also lacks features that help define invariants for these facilities in ways that programmers already understand through experience in languages like C++. One might even speculate that some complexities as a conspiracy to lure large companies into paid CMake training sessions.

#### 2 (1.2.2)

1. Current built facilities are efficient but they fail to provide the ability to manage some efficiencies such that you could built upon the built system by using external utilities to track and provide more abstract resource from the system. For example, For threads, I may allocate extra threads if only my system has less load on the processor. This naturally begets the idea of a build time environment.
2. Any required invariants on the underlying compiler can be substituted by providing abstract standard interfaces to get such information so that the user can then feed information into programmed predicates to confirm any invariants for his build. Such tasks can of course be automated, if the user provides libraries to verify such invariants.
3. Tracking changes in files can also be extended by allowing external tools to advance the sub system doing such tracking. For example, instead of just deciding weather to compile a file based on if it was modified, an external tool can also input to the sub system doing such tracking weather the modification warrants a recompilation or not. Such external tool could be but is not limited to: A tool that tracks the change logs of version control systems.

#### 3 (1.2.3)

1. Current built tools providing languages that are inefficient by design since they are interpreted. Making any programming construct in such languages inferior to the ones in the languages whose source code they are trying to compile in the first place.
2. Such built tools lack support of constructs supporting commonly used programming language paradigms. Absence of such constructs make any utilization of such a built system become hard to scale.

## **The notion of a proposed Extended Built-System-Library Interface (2)**

### Fixes to all the issues in current built systems (2.1)

#### For issues in described in *1.2.1* (2.1.2)

##### Issue 1 (2.1.2.1):

1. The fix to not over abstracting and to keep simple things simple is to provide a standard minimal C++ interface where the availability of any advanced features are indicated by macros.  Such an interface would provide functions with well defined pre and post conditions with invariants of the individual of parameter types making such conditions easier to understand and implement. This would effectively minimize fragility by simply exploiting existing C++ features. Such an approach will also allow for the user to create Built, Compilation, and runtime environments for his application, make it easier for any software quality inspection to inspect the quality of such environments provided for the user's application (more on that later).

##### Issue 2 (2.1.2.2):

1. By providing a standard interface that is both minimal in that it allows for manual fine tuning while also providing defaults too  that are to be used if such manual fine tuning is not desired, the built system can be easily extended with support of external tools providing as C++ libraries or as executables invoked by the built config file. Furthermore such minimal interfaces will beget the idea of a built time environment, such built time environment will be the runtime environment provided for the compiled config C++ built file. The built time environment would be provided by linking the functions whose availability by the macros provided by the compile time environment. Certain functions must exist in hosted implementations, or if the proposal were to  be ready for some other standard's committee, say some future Posix standard supporting C++ then a Posix compliant implementation that indicates support C++.
2. Such an interface can enforce the implementation to support probing the compiler for information, bypassing the need of doing so using the terminal through unportable means. Such probing must be implemented behind standard interfaces and the availability of such functions can also be indicated by macros, though those functions that are mandated, must have a macro specifying them as such.
3. The fix to this problem is the same as the first, that is to provide the ability to do manual fine tuning while providing defaults too that are to be used if such fine tuning is not desired.

##### Issue 1 (2.1.2.3):

1. A compiled-built file in the C++ programming language would be faster to execute on many instances than an interpreted one.
2. 

















## **Definitions**

1. Source files: Files that the built system is to compile.
2. sub builds: Sub systems that a build relies on the presence and availability of.

3\. built config file: config files that determine how the project is to be built. The proposal in the document requires such config files to be C++ code files.

