# TsReflect

TsReflect is a CommonJS module for [Node](http://nodejs.org/) applications for working with JSON declaration files
generated by the [tsreflect-compiler](https://github.com/artifacthealth/tsreflect-compiler). The library is
essentially a wrapper for the [TypeScript](http://www.typescriptlang.org/) 1.4 compiler type system along with
methods for loading the JSON declaration file format.


## Examples

Example: load all declaration files in the cwd and it's subdirectories, then print the name of any exported interfaces.
```
/// <reference path="./lib/tsreflect.d.ts" />
import reflect = require("tsreflect");

// load all declaration files in the cwd and it's subdirectories.
reflect.load("**/*.d.json", (err, symbols) => {
    if(err) throw err;

    // print name of all exported interfaces
    for(var i = 0, l = symbols.length; i < l; i++) {
        var symbol = symbols[i];

        // if the symbol is an interface, then print it's name
        if(symbol.isInterface()) {
            process.stdout.write(symbol.getName() + '\n');
        }
        else if(symbol.isModule()) {

            // if the symbol is a module, then get all symbols exported by the module
            var moduleExports = symbol.getExports();
            for(var j = 0, k = moduleExports.length; j < k; j++) {

                if(moduleExports[j].isInterface()) {
                    process.stdout.write(moduleExports[j].getName() + '\n');
                }
            }
        }
    }
});
```


## Documentation


### require(moduleName)
Load type information for an external module. Analogous to Node's require function.

This method assumes that the .d.json file is in the same directory as the .js file that it contains type
information for. Just like Node's require function, if a relative path is specified, it is considered to be
relative to the source file that called require. Also like Node's require function, files are loaded
synchronously. If you would like to load type information asynchronously, see the load function.

Returns: `Symbol`

__Parameters__
* `moduleName` - The name of the module to load.


### reference(fileName)
Load type information for an internal module or global declarations. Analogous to TypeScript's reference tags.

This method assumes that the .d.json file is in the same directory as the .js file that it contains type
information for. Just like TypeScript's /// <reference path="... tags, the path is considered to be relative to
the source file that called reference. Files are loaded synchronously. If you would like to load type information
asynchronously, see the load function.

__Parameters__
* `fileName` - The name of the file to load.


### load(path, callback)
Asynchronously load type information for the given filename pattern(s).

This method assumes that the .d.json files are in the same directory as the .js file that they contain type
information for. The load method supports [glob](https://github.com/isaacs/node-glob) patterns for filename
matching. Relative paths are considered to be relative to the current working directory.

Once all declaration files have been loaded, the callback is called with the symbols for any external
modules. If no external modules were loaded an empty array is passed to the callback. The list of symbols
does not include any global symbols that were loaded.

__Parameters__
* `path` - A string containing the path to load or an array containing the paths to load. Glob patterns are
supported.
* `callback` __type  - Called when the load operation completes.


### load(paths, callback)

__Parameters__
* `paths` Array
* `callback` __type


### resolve(entityName)
Finds the symbol for the given entity name in the global scope. If a global symbol with the given name cannot
be found, undefined is returned.

Returns: `Symbol`

__Parameters__
* `entityName` - The global entity name to resolve.


### Symbol
Represents a named identifier.


#### getName()
Gets the name of the symbol.


#### getFullName()
Gets the qualified name of the symbol.


#### getDescription()
Gets the description of the symbol.


#### getAnnotations(name)
Finds annotations with the specified name. If no name is specified, then all annotations
are returned.

Returns: `Array`

__Parameters__
* `name` - The name of the annotation to find.


#### hasAnnotation(name)
Returns true if the symbols has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* `name` - The name of the annotation to find.


#### getType()
Gets the type of the symbol.

Returns: `Type`


#### getDeclaredType()
Gets the type declared by the symbol. For a class getType() returns the static side of the class
and getDeclaredType() returns the instance side of the class.

Returns: `Type`


#### getExports()
Gets all symbols exported by this symbol. This is used to get the members of a module or the static
members of a class.

Returns: `Array`


#### resolve(entityName)
Finds the symbol for the given entity name relative to the current symbol. If a symbol with the given name
cannot be found, undefined is returned.

Returns: `Symbol`

__Parameters__
* `entityName` - The entity name to resolve.


#### getValue(obj)
Gets the value of the property, variable, or accessor represented by the symbol on the given object.

__Parameters__
* `obj` - The object to get the value for.


#### setValue(obj, value)
Sets the value of the property, variable, or accessor represented by the symbol on the given object.

__Parameters__
* `obj` - The object to set the value on.
* `value` - The value to set.


#### isVariable()
Returns true if the symbol is a variable; Otherwise, returns false.


#### isFunction()
Returns true if the symbol is a function; Otherwise, returns false.


#### isClass()
Returns true if the symbol is a class; Otherwise, returns false.


#### isInterface()
Returns true if the symbol is an interface; Otherwise, returns false.


#### isEnum()
Returns true if the symbol is an enum; Otherwise, returns false.


#### isModule()
Returns true if the symbol is a module; Otherwise, returns false.


#### isImport()
Returns true if the symbol is an import; Otherwise, returns false.


#### isTypeParameter()
Returns true if the symbol is a type parameter; Otherwise, returns false.


#### isProperty()
Returns true if the symbol is a class or interface property; Otherwise, returns false.


#### isMethod()
Returns true if the symbol is a class or interface method; Otherwise, returns false.


#### isAccessor()
Returns true if the symbol is an accessor; Otherwise, returns false.


#### isGetAccessor()
Returns true if the symbol is a get accessor; Otherwise, returns false.


#### isSetAccessor()
Returns true if the symbol is a set accessor; Otherwise, returns false.


#### isEnumMember()
Returns true if the symbol is an enum member; Otherwise, returns false.


#### isTypeAlias()
Returns true if the symbol is a type alias; Otherwise, returns false.


### Annotation
Represents a custom annotation.


#### name
The name of the annotation.


#### value
The value of the annotation.


#### getDeclarationFileName()
Returns the name of the file that the annotation was declared in.


### Type
Represents a type.


#### getName()
Gets the name of the type, if any.


#### getFullName()
Gets the qualified name of the type, if any.


#### getDescription()
Gets the description of the type.


#### getAnnotations(inherit)
Gets all annotations declared for the type.

Returns: `Array`

__Parameters__
* `inherit` - True if annotations should be inherited from base types.


#### getAnnotations(name, inherit)
Finds annotations with the specified name. If no name is specified, then all annotations
are returns.

Returns: `Array`

__Parameters__
* `name` - The name of the annotation to find.
* `inherit` - True if annotations should be inherited from base types.


#### hasAnnotation(name, inherit)
Returns true if the type has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* `name` - The name of the annotation to find.
* `inherit` - True if base types should be checked for the annotation as well.


#### getProperties()
Gets a list of all properties of the type. Note that properties include fields, accessors, and
methods.

Returns: `Array`


#### getProperty(name)
Finds a property with the specified name. If no property is found, undefined is returned. Note that
properties include fields, accessors, and methods.

Returns: `Symbol`

__Parameters__
* `name` - The property name to find.


#### getCallSignatures()
Gets all call signatures of the type.

Returns: `Array`


#### getConstructSignatures()
Gets all construct signatures of the type.

Returns: `Array`


#### getStringIndexType()
Gets the string index type of the type. For example, for { [key: string]: boolean }, getStringIndexType()
will return the intrinsic boolean type.

Returns: `Type`


#### getNumberIndexType()
Gets the number index type of the type. For example, for { [key: number]: string }, getNumberIndexType()
will return the intrinsic string type.

Returns: `Type`


#### isIdenticalTo(target, diagnostics)
Returns true if the target type is identical to the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* `target` Type  - The target type.
* `diagnostics` Array  - Array where diagnostic information regarding the differences between the types is placed.


#### isSubtypeOf(target, diagnostics)
Returns true if the target type is a subtype of the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* `target` Type  - The target type.
* `diagnostics` Array  - Array where diagnostic information regarding the differences between the types is placed.


#### isAssignableTo(target, diagnostics)
Returns true if the target type is assignable to the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* `target` Type  - The target type.
* `diagnostics` Array  - Array where diagnostic information regarding the differences between the types is placed.


#### isSubclassOf(target)
Returns true if the target type if a subclass of the current type; Otherwise, returns false.

__Parameters__
* `target` Type  - The target type.


#### getBaseClass()
Gets the base class of a class type.

Returns: `Type`


#### getBaseTypes()
Gets the base types of a class or interface type.

Returns: `Array`


#### hasBaseType(target)
Returns true if the target type is a base type of the current type; Otherwise, returns false.

__Parameters__
* `target` Type  - The target type.


#### isClass()
Returns true if the type is a class; Otherwise, returns false.


#### isInterface()
Returns true if the type is an interface; Otherwise, returns false.


#### isTuple()
Returns true if the type is a tuple; Otherwise, returns false.


#### isUnion()
Returns true if the type is a union type; Otherwise, returns false.


#### isArray()
Returns true if the type is an array; Otherwise, returns false.


#### isIndex()
Returns true if the type is an index type; Otherwise, returns false.


#### isAnonymous()
Returns true if the type is anonymous; Otherwise, returns false.


#### isReference()
Returns true if the type is a generic reference; Otherwise, returns false.


#### isEnum()
Returns true if the type is an enum; Otherwise, returns false.


#### isStringLiteral()
Returns true if the type is a string literal; Otherwise, returns false.


#### isTypeParameter()
Returns true if the type is a type parameter; Otherwise, returns false.


#### isAny()
Returns true if the type is the intrinsic any type; Otherwise, returns false.


#### isString()
Returns true if the type is the intrinsic string type; Otherwise, returns false.


#### isNumber()
Returns true if the type is the intrinsic number type; Otherwise, returns false.


#### isBoolean()
Returns true if the type is the intrinsic boolean type; Otherwise, returns false.


#### isVoid()
Returns true if the type is the intrinsic void type; Otherwise, returns false.


#### isIntrinsic()
Returns true if the type is an intrinsic type; Otherwise, returns false.


#### isObjectType()
Returns true if the type is an object type; Otherwise, returns false.


#### getEnumValue(value, ignoreCase)
Gets the numeric enum value for the given member name.

__Parameters__
* `value` - The enum member name to get the value for.
* `ignoreCase` - True if case should be ignored when finding the member name.


#### getEnumName(value)
Gets the enum member name for the given numeric enum value.

__Parameters__
* `value` - The numeric value to get the name for.


#### getEnumNames()
Gets a list of enum member names.

Returns: `Array`


#### getElementType()
Gets the element type for an array type.

Returns: `Type`


#### getElementTypes()
Gets the element types a tuple type.

Returns: `Array`


#### createInstance(args)
Creates an instance of a class. If arguments are provided then the constructor is called; Otherwise,
the object is created without calling the constructor. To call a parameter-less constructor, pass an empty
array to args.

Note that This method assumes that the .d.json file is in the same directory as the .js file that it contains
type information for. For external modules, Node's require method is used to load the JavaScript module.

__Parameters__
* `args` Array  - The constructor arguments.


#### getConstructor()
Gets the JavaScript constructor for a class type.

Note that This method assumes that the .d.json file is in the same directory as the .js file that it contains
type information for. For external modules, Node's require method is used to load the JavaScript module.

Returns: `Function`


### Signature
Represents a call signature.


#### getDescription()
Gets a description of the signature.


#### getAnnotations(name)
Finds annotations with the specified name. If no name is specified, then all annotations
are returned.

Returns: `Array`

__Parameters__
* `name` - The name of the annotation to find.


#### hasAnnotation(name)
Returns true if the symbols has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* `name` - The name of the annotation to find.


#### getParameters()
Gets a list of all parameters for the call signature.

Returns: `Array`


#### getParameter(name)
Gets a parameter for the signature with the specified name. If no parameter matches the name then undefined
is returned.

Returns: `Symbol`

__Parameters__
* `name` - The parameter name to find.


#### getReturnType()
Gets the return type of the signature.

Returns: `Type`


### Diagnostic
Diagnostic information.


#### filename
The name of the .d.json file that contained the error


#### messageText
Message explaining the error.


#### code
Error code.


### DiagnosticError
Extension of standard Error that includes diagnostic information.


#### diagnostics
Array of Diagnostics that provides details on the error that occurred.
Type: `Array`


#### name
The name of the error.

#### message
The error message.