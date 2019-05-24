# Writing a module for MOM6
MOM6 developers follow a strict set of guidelines when it comes to the overall structure of the code. Some of these are stylistic in nature meant to make different parts of the code easier to read. The guidelines that affect the programming flow and namespace management of the code are detailed in this document. Overall they lead to a codebase where
1. External functions and subroutines are easily traceable
2. Any variables used by the model are explicitly within scope
3. Compilation is straightforward

# Inviolable rules
## 1. All modules must declare `implicit none ; private`
### `implicit none`
Fortran by default does not require that new variables need to be declared, but instead will implicitly declare a previously unused variable whose type is inferred by the first character of the variable name. This is especially dangerous because typos can lead to unexpected model behavior. For example without `implicit none`, mispelling `temp(i,j,k)` as `tmep(i,j,k)` is entirely valid Fortran code.

### `private`
The `private` keyword specifies that any variable, function, or subroutine is effectively invisible when the module is imported. Anything that should be modified or used outside of the module must be explicitly declared `public`.

## 2. `use ... only` when importing a module
This prevents accidental namespace classhes and clarifies where any subroutine or function comes from. For example, let's say that two modules `mod_a` and `mod_b` both have a function called `interp`. If a third module, `mod_c` imports both `mod_a` and `mod_b` wihtout a use only, a namespace clash can occur and it becomes ambiguous which `interp` is actually used. Some compilers do not detect these namespace clashes! Also, if a construct is not defined within a module it becomes very clear where it comes from. Further, when searching through the entire code, it becomes far easier to find which parts of the module are used.

## 3. Do not use compiler macros
Compiler macros like `#ifdef` or `#define` make it very unclear what is actually being compiled. There are only two exceptions to this rule in the codebase.

### `#include <MOM_memory.h>`
The macros contained here facillitate compiling the model with different 'memory management' modes in static mode, where the sizes of all the arrays are hardcoded at runtime, or with dynamic memory, where the size of the arrays are specified at runtime. Symmetric means that a velocity point includes a copy of every surrounding h-variable

### `_USE_GENERIC_TRACER`
This is only for use with GFDL's biogeochemical tracer package and is being deprecated.

## 4. No module level variables
Module level variables can interfere with the local namespace of a subroutine and make it harder to understand if two such named variables exist. If any information needs to be passed around constructs of a module, use a control structure

## 5. Use control structures to store module-level information
Almost every module contains a control structure designed to store runtime dependent information that ONLY affects the current module. This reduces the number of arguments needed to be passed into subroutines and makes it easier to debug the requested logic. All elements of these control structure types are `private` to prevent other modules from modifying/accessing the content. This keeps information localized to the module itself and makes it far less likely for variables to be indavertently
changed by a different module. If the type needs to be queried somewhere else, use an 'extractor' routine

## 6. Pass all necessary information via arguments
By not allowing module level variables, subroutines and functions by necessity have to have all relevant information passed in via commandline arguments. This ensures that the namespace is always explicitly within scope and makes debugging much easier and memory profiling of subroutines more accurate.
