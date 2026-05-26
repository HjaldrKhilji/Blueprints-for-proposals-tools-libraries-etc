# **TITLE: Extended Build-System-Library Interface for C/C++**

## **Abstract**

This informal document was initially draft at 5/20/2026 and proposes a ***build system interface in C/C++*** that is to provide a standardized interface to a build system. The proposal first describes the necessity of build systems to be provided to C/C++ developers as a library and then tries to expand such a build system to incorporate facilities for the creation of a Build, Compilation, and runtime environment for an application. It also tries to justify and specify the use of preprocessor facilities in the specification, utilization, and implementation of such an interface. It shall be noted that the goal of the document is to be general and not to rely on the current functionality of C/C++, the reason for such is (excluding the fact its a informative informal document) that the document is meant to be the first mention of this potentially useful idea and tries to  be future proof in many ways.

## **Introduction (1)**

### The essence of a build system (1.1)

A build system is one that:

1.(usually deterministically) automates the process of compiling many source files into a

type of compiled library or into an executable.

2.May have several facilities to make such processes efficient, such facilities may contain but are not limited to A form of Non volatile memory usage to track files such that files that need not to be recompiled aren't recompiled, Compiling multiple source files at the same time, requiring the underlying compiler (if not build in to the build system) to confirm to certain invariants.

3.Should provide commonly used programming constructs to make automation of such processes easier.

### How current build tools fail each point respectively (1.2)

#### 1 (1.2.1)

1. Current build systems complicate simple automations at a large scale by over abstracting sub builds and by not keeping simple things simple. A great example of this is CMake, where each parameter to a simple build operation containing a few files require you to set certain symbols to certain values, including values that describe the pathnames of the source files in the  target system. This is an obvious issues since Such issues complicate automations and makes the every CMake config file be very fragile to mistakes and misconfiguration. To make it worse, These facilities don't follow a class hierarchy, hence its harder to relate them or to use them together. CMake also lacks features that help define invariants for these facilities in ways that programmers already understand through experience in languages like C/C++. One might even speculate that some complexities as a conspiracy to lure large companies into paid CMake training sessions.

#### 2 (1.2.2)

1. Current build facilities are efficient but they fail to provide the ability to manage some efficiencies such that you could build upon the build system by using external utilities to track and provide more abstract resource from the system. For example, For threads, I may allocate extra threads if only my system has less load on the processor. This naturally begets the idea of a build time environment.
2. Any required invariants on the underlying compiler can be substituted by providing abstract standard interfaces to get such information so that the user can then feed information into programmed predicates to confirm any invariants for his build. Such tasks can of course be automated, if the user provides libraries to verify such invariants.
3. Tracking changes in files can also be extended by allowing external tools to advance the sub system doing such tracking. For example, instead of just deciding weather to compile a file based on if it was modified, an external tool can also input to the sub system doing such tracking weather the modification warrants a recompilation or not. Such external tool could be but is not limited to: A tool that tracks the change logs of version control systems.

#### 3 (1.2.3)

1. Current build tools providing languages that are inefficient by design since they are interpreted. Making any programming construct in such languages inferior to the ones in the languages whose source code they are trying to compile in the first place.
2. Such build tools lack support of constructs supporting commonly used programming language paradigms. Absence of such constructs make any utilization of such a build system become hard to scale.

## **The notion of a proposed Extended build-System-Library Interface (2)**

### Fixes to all the issues in current build systems (2.1)

#### For issues in described in *1.2.1* (2.1.1)

##### Issue 1 (2.1.1.1)

The fix to not over abstracting and to keep simple things simple is to provide a standard minimal C/C++ interface where the availability of any advanced features are indicated by macros.  Such an interface would provide functions with well defined pre and post conditions with invariants of the individual of parameter types making such conditions easier to understand and implement. This would effectively minimize fragility by simply exploiting existing C/C++ features. Such an approach will also allow for the user to create build, Compilation, and runtime environments for his application, make it easier for any software quality inspection to inspect the quality of such environments provided for the user's application (more on that later).

#### For issues in described in *1.2.2* (2.1.2)

##### Issue 1 (2.1.2.1):

By providing a standard interface that is both minimal in that it allows for manual fine tuning while also providing defaults too  that are to be used if such manual fine tuning is not desired, the build system can be easily extended with support of external tools providing as C/C++ libraries or as executables invoked by the build config file. Furthermore such minimal interfaces will beget the idea of a build time environment, such build time environment will be the runtime environment provided for the compiled config C/C++ build file. The build time environment would be provided by linking the functions whose availability by the macros provided by the compile time environment.

##### Issue 2 (2.1.2.2):

Such an interface can enforce the implementation to support probing the compiler for information, bypassing the need of doing so using the terminal through unportable means. Such probing must be implemented behind standard interfaces and the availability of such functions can also be indicated by macros, though those functions that are mandated, must have a macro specifying them as such.

##### Issue 3 (2.1.2.3):

The fix to this problem is the same as the first, that is to provide the ability to do manual fine tuning while providing defaults too that are to be used if such fine tuning is not desired.

#### For issues in described in *1.2.3* (2.1.3)

##### Issue 1 (2.1.3.1):

A compiled-build file written in the C/C++ programming language would be faster to execute in many instances than an interpreted one would be to be to execute on multiple instances. This matter, since this means CMake scales poorer for performance, since each CMake file for a subsystem needs to be reinterpreted again and again for every build.

##### Issue 2 (2.1.3.2):

C/C++ supports multiple programming paradigms, allowing developers from different backgrounds to be able to scale projects easily with the flexibility of the level provided by C/C++.

### Extensions to the current notion of build systems (2.2)

#### build time environment (2.2.1)

The build time environment is the runtime environment of the compiled config file. It provides functions and types whose availability and properties are defined by macro values in the compile time environment of itself (headers usable by the build config file). The goal of build time environment is simple:

1. To provide functions to build the target system.
2. To provide the compile time and runtime environment of the target system.
3. To allow for dependencies between multiple external symbols to be tracked.

The target system can be another build time system that utilizes the same environment which makes the approach of providing build systems as C/C++ libraries more scalable. Such hierarchal management of the compile and runtime environments can lead to ease in maintaining system requirements specified by software quality management organizations. For example, they may provide macros indicating the weather exceptions, function pointers, naked new/free are allowed, such requirements may change, with some features being restricted and the other allowed, and/or the alternative interface's implementation may as well. The third point isn't one that is commonly done except for a tools like CMake, but it is important to complement quality management with facilities that help avoid issues like circular dependencies which in turn would allow fix issues like deadlocks and livelocks. All three keystones forming a build environment has to be implemented in the manner described by *2.1.2.1*.



The whole notion of a build time environment basically simplifies the management of libraries into hierarchies, making quality management simplified. This as mentioned previously has enourmiss advantages in quality management and scalability.

#### Compile time environment (2.2.2)

Compile time environment consists of Macros defined in headers. The justification of Macros is that to have code that the compiler only optionally needs, you will need macros. A fix to the possibility that a macro can be mistakenly used to substitute a part of your code is to either adhere to a proper naming convention or for the build system to supply headers that undefine macros, such headers are to be included after all such use cases of the respective macros have been done, such headers will be have the same name as the header containing the respect macro, except with an additional prefix or suffix. Both the build config file and the system will have different copies of such header files. The build system config will have it supplied by the implementation, while for the target system, the user must specify where the header files are to exist. Furthermore, the build time environment will provide a safe and extensible way of extending both compile time and runtime environment of the target system. Macros specified in the header supplied by the C/C++ implementation will be used by the build config file to check absence or presence of facilities for the build time environment. Whereas, Macros specified by the headers supplied by the build environment facilities will by the build environment.



An non-recommended alternative to using macros and preprocessor facilities is to completely rely on stable function able to do many tasks that previously may have required many functions and Constexpr functions displaying what options are supporting by such a function. For example, new/delete, could be replaced by simply another interface and the usage of which is to be indicated by the constexpr function. However, if the change in usage require changes in parameter types then somewhere along the line header files are unavoidable. which means that compile time environment wont be able to indicate change in code-enforced invariants unless preprocessor facilities are used.

#### Runtime environment (2.2.3)

The runtime environment is the runtime environment of the target project, specified by the build time environment.







#### Point to wrap the notion up (2.3)



To manage libraries into hierarchies (among other things) , the build environment will include a function to allow for the inclusion of both header files and object files provided by the implementation to it to the target project, unless if some of any file require appropriate privileges that the target project doesn't have.



To start The whole process of building, one will have to invoke a compiler with an implementation defined name from the shell for the starting build file of the whole target, but it should be noted that such an action should only be done per the compilation of a target.





## **Proposed interface (3)**



### Single file build Interface (3.1)



The first thing one would want to define an interface for is a function to build a single file and return the handle to what it builded it into. Such a function's name would be:

build\_file\_handle build\_file(build\_file\_config)

build\_file\_config is defined as:

struct build\_file\_config{

/\*implementation defined representation\*/

build\_file\_config(std::string path, enum\_type\_of\_compilation, unsigned long enum\_optimization\_level, std::initializer\_list<enum\_debug> debug\_build\_types,  std::initializer\_list<enum\_show\_dependencies> dependency\_tracking\_flags, 

std::initializer\_list<Append\_compile\_runtime\_environment> append\_flags,

std::initializer\_list<implementation\_defined\_compiler\_flags> impl\_defined\_flags);



void set(enum\_type\_of\_compilation, unsigned long enum\_optimization\_level, std::initializer\_list<enum\_debug> debug\_build\_types,  std::initializer\_list<enum\_show\_dependencies> dependency\_tracking\_flags,

std::initializer\_list<Append\_compile\_runtime\_environment> append\_flags,

std::initializer\_list<implementation\_defined\_compiler\_flags> impl\_defined\_flags);

};

*Path* defines an absolute path if the object std::Base\_path/Base\_path has no elements, but if it has, it will check all of those paths to find the path defined by *Path* and choose the first one found.

***C implementations can define a "factory function" to compensate for the lack of constructors. something similar can be done to set() member function. C can also use an pointer rather than std::initializer list.***

The availability of the function "build\_file", the build\_multiple\_file function (described in ***3.3***), along with all the types required by the definition of build\_file\_config above, and build\_file\_config itself are defined by the macro **BUILD\_SYSTEM\_AVALABILITY**. **BUILD\_SYSTEM\_AVALABILITY** must be defined with a value greater than 0 for compliance to this proposal. 

&#x20;

The availability of optional/mandatory facility that I will describe, define it as -1 or undefined to indicate no compile time or runtime support, define it as 0 to indicate that compile time support and that it may be supported at runtime (you have to do runtime checks using standard Posix/C++ functions, and greater than 0 to define that the facility has both compile time and runtime support. If the value of a macro is 0 then all the compile time support promised is for the application to just compile when contains code that uses the application, but how the interface of such facilities act is implementation defined. For mandatory features, it is justified to skip checking, where as in all the other cases, you must do otherwise. For C++, there must be a proposal for standard functions to check for the availability of facilities at runtime since C++ lacks them.



### Expanding on Single file build Interface(3.2)



#### enum\_type\_of\_compilation (3.2.1)



enum class **enum\_type\_of\_compilation**{

**preprocessed,** //compile into preprocessed file, if this is specified then all the other flags will be ignored.

**object\_file,** //compile into object file

**executable\_file,** //compile into executable file 

//all the facilities above are mandatory under the mandatory flag **BUILD\_SYSTEM\_AVALABILITY.**

**/\*Other implementation defined flags whose availability is specified by a macro with the name that is the capitalized name of the corresponding enumerator\*/**

};



#### enum\_optimization\_level (3.2.2)

enum\_optimization\_level is an argument that as it increases, the build system tries to optimize for performance at the expense of space as the value goes up.

The limit for enum\_optimization\_levefl is defined by the mandatory variable/macro enum\_optimization\_level\_limit/enum\_OPTIMIZATION\_LEVEL\_LIMIT.



#### The Debug\_build\_types (3.2.3)

The Debug\_build\_types is of type std::initializer\_list<enum\_debug>.

enum\_debug is defined as:

enum class **enum\_debug**{

**show\_function\_where\_leak,**//mandatory for confirming implementations. Will provide a compile time error if it suspects a chance of leak in a function (unconditional freeing of the pointer does not occur). Such a chance will be found through checking if a pointer that is assigned a value through new/delete/any C dynamic memory function or any function that returns "return (new/delete/other code)". Such a compile time error must provide the function name and the absolute file path of the file that it occurred.

**show\_file\_line\_and\_file\_where\_needed,** // provides a function to print the file and line where that function is invoked:

print\_line\_file();

//the format is required to be: *filename line\_number newline*

//The implementation of such a functionality would be either:

/\*

1. In C++ to use std::source\_location (but that might make the whole function obsolete).
2. In C for there to be a new file creating on the fly with a function:

&#x09;print\_line\_file(){

&#x09;static unsigned int instance\_of\_call=0;

&#x09;std::println/printf("*filename\_of\_the\_file\_to\_be\_linked\_to*");

&#x09;/\*

&#x09;Code that reads file until it encounters *instance\_of\_call* number of calls to

&#x09;print\_line\_file() and then prints the current line number.

&#x09;\*/

&#x09;//this of course is an implementation that is very inefficient but it is to just give a conceptual view.

&#x09;}

\*/

//all the facilities above are mandatory under the mandatory flag **BUILD\_SYSTEM\_AVALABILITY.**

**is\_mem\_freed,**//optional for confirming implementations. Will provide a function:

bool is\_mem\_free(void \*ptr);

//that check weather a memory pointed by a pointer is freed or not. Such a facility will require that the user allocates dynamic momory through new/delete/and other such C standard functions only, and that the implementation overrides such functions to also register the pointer, and remove such registries for pointers if the pointer is freed.



***/\*Other implementation defined facilities indicated by additional enumerators\*/***

//The implementation of such an option is not needed in C++ since it would only act as synonym to std::source\_location.

//The availability of each facility is confirm by the macro that has the capitalized name of the enumerator representing the facility.

};





#### The dependency\_tracking\_flags (3.2.4)



dependency\_tracking\_flags of type std::initializer\_list<enum\_show\_dependencies>. enum\_show\_dependencies is defined as:

enum class **enum\_show\_dependencies**{



**print\_dependency\_file\_names,** // in the case of C++, if the file is a module (not a header file), then the filename would be printed as *filename*/*module\_name* , any potential "/" in either filename or module\_name would be turned into "//". For header files its just *filename. Filename* is to be the absolute path of the file in question. After each dependency getting printed, there has to be a newline.



**print\_individual\_dependency\_mappings,** // The mapping of each symbol used to every *filename*. After print\_dependency\_file\_names prints a dependency, print\_individual\_dependency\_mappings prints the symbols used that are found in that dependency in a comma-separated list that is terminated by a newline. function dependencies have an escape character appended to there end and any possible "/" is escaped into "//". Such escaping will never happen unless a future standard allows for "/" to be in symbol names.

**throw\_circular\_dependency\_mappings\_for\_headers,** //only in effect if throw\_circular\_dependency\_mappings is defined.

**throw\_circular\_dependency\_mappings,** // The mapping of each symbol is checked for circular dependencies, it is defined as:

an operation A for a symbol B depends on the operation B of a symbol C while the same operation B depends on operation A, and vice verse. Such dependency can be anything but namely: C++ initialization/assignment operation that gets messy due to poorly-designed user defined constructors/assignment operations. Such a flag will not check header files to see if such issues exist between header file included and the source file it is included in, unless throw\_circular\_dependency\_mappings\_for\_headers is defined.

//all the facilities above are mandatory under the mandatory flag **BUILD\_SYSTEM\_AVALABILITY.**

***/\*Other implementation defined facilities indicated by additional enumerators\*/***

};

#### 

#### The append\_flags(3.2.5)

The append\_flags is a parameter of type std::initializer\_list<Append\_compile\_runtime\_environment>. 

Append\_compile\_runtime\_environment is defined as:

enum class **Append\_compile\_runtime\_environment** {

**Append\_build\_time,** //Appends build environment and the compile time preprocessor data specifying the availability of build time facilities to the target, making it available for use by the target.



};



#### The impl\_defined\_flags(3.2.6)

The impl\_defined\_flags is a parameter of type std::initializer\_list<implementation\_defined\_compiler\_flags>. implementation\_defined\_compiler\_flags type enumerate implementation defined flags that a confirming implementation must document if provided.





### multi-file build Interface (3.3)







## **Sample implementation Conceptual (4)**







## **Sample implementation Practical (4)**











## **Definitions**

1. Source files: Files that the build system is to compile.
2. Sub builds: Sub systems that a build relies on the presence and availability of.
3. Build config file: config files that determine how the  target system is to be build. The proposal in the document requires such config files to be C/C++ files.
4. target: Code to be build by code specified in the Build config file.

