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


[`require`](#require)
[`reference`](#reference)
[`load`](#load)
[`resolve`](#resolve)
[`Symbol`](#Symbol)
[`Annotation`](#Annotation)
[`Type`](#Type)
[`Signature`](#Signature)
[`Diagnostic`](#Diagnostic)
[`DiagnosticError`](#DiagnosticError)

<a name=require />
### require(moduleName)
Load type information for an external module. Analogous to Node's require function.

This method assumes that the .d.json file is in the same directory as the .js file that it contains type
information for. Just like Node's require function, if a relative path is specified, it is considered to be
relative to the source file that called require. Also like Node's require function, files are loaded
synchronously. If you would like to load type information asynchronously, see the load function.

__Parameters__
* moduleName `string`  - The name of the module to load.

__Returns:__ `Symbol`


<a name=reference />
### reference(fileName)
Load type information for an internal module or global declarations. Analogous to TypeScript's reference tags.

This method assumes that the .d.json file is in the same directory as the .js file that it contains type
information for. Just like TypeScript's /// <reference path="... tags, the path is considered to be relative to
the source file that called reference. Files are loaded synchronously. If you would like to load type information
asynchronously, see the load function.

__Parameters__
* fileName `string`  - The name of the file to load.

__Returns:__ `void`


<a name=load />
### load(path, callback)
Asynchronously load type information for the given filename pattern(s).

This method assumes that the .d.json files are in the same directory as the .js file that they contain type
information for. The load method supports [glob](https://github.com/isaacs/node-glob) patterns for filename
matching. Relative paths are considered to be relative to the current working directory.

Once all declaration files have been loaded, the callback is called with the symbols for any external
modules. If no external modules were loaded an empty array is passed to the callback. The list of symbols
does not include any global symbols that were loaded.

__Parameters__
* path `string`  - A string containing the path to load or an array containing the paths to load. Glob patterns are
supported.
* callback - Called when the load operation completes.

__Returns:__ `void`


### load(paths, callback)

__Parameters__
* paths `string[]`
* callback

__Returns:__ `void`


<a name=resolve />
### resolve(entityName)
Finds the symbol for the given entity name in the global scope. If a global symbol with the given name cannot
be found, undefined is returned.

__Parameters__
* entityName `string`  - The global entity name to resolve.

__Returns:__ `Symbol`


<a name=Symbol />
### Symbol
--------------------
Represents a named identifier.
[`getName`](#getName)
[`getFullName`](#getFullName)
[`getDescription`](#getDescription)
[`getAnnotations`](#getAnnotations)
[`hasAnnotation`](#hasAnnotation)
[`getType`](#getType)
[`getDeclaredType`](#getDeclaredType)
[`getExports`](#getExports)
[`resolve`](#resolve)
[`getValue`](#getValue)
[`setValue`](#setValue)
[`isVariable`](#isVariable)
[`isFunction`](#isFunction)
[`isClass`](#isClass)
[`isInterface`](#isInterface)
[`isEnum`](#isEnum)
[`isModule`](#isModule)
[`isImport`](#isImport)
[`isTypeParameter`](#isTypeParameter)
[`isProperty`](#isProperty)
[`isMethod`](#isMethod)
[`isAccessor`](#isAccessor)
[`isGetAccessor`](#isGetAccessor)
[`isSetAccessor`](#isSetAccessor)
[`isEnumMember`](#isEnumMember)
[`isTypeAlias`](#isTypeAlias)

<a name=getName />
#### getName()
Gets the name of the symbol.

__Returns:__ `string`


<a name=getFullName />
#### getFullName()
Gets the qualified name of the symbol.

__Returns:__ `string`


<a name=getDescription />
#### getDescription()
Gets the description of the symbol.

__Returns:__ `string`


<a name=getAnnotations />
#### getAnnotations(name)
Finds annotations with the specified name. If no name is specified, then all annotations
are returned.

__Parameters__
* name `string`  - The name of the annotation to find.

__Returns:__ `Annotation[]`


<a name=hasAnnotation />
#### hasAnnotation(name)
Returns true if the symbols has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* name `string`  - The name of the annotation to find.

__Returns:__ `boolean`


<a name=getType />
#### getType()
Gets the type of the symbol.

__Returns:__ `Type`


<a name=getDeclaredType />
#### getDeclaredType()
Gets the type declared by the symbol. For a class getType() returns the static side of the class
and getDeclaredType() returns the instance side of the class.

__Returns:__ `Type`


<a name=getExports />
#### getExports()
Gets all symbols exported by this symbol. This is used to get the members of a module or the static
members of a class.

__Returns:__ `Symbol[]`


<a name=resolve />
#### resolve(entityName)
Finds the symbol for the given entity name relative to the current symbol. If a symbol with the given name
cannot be found, undefined is returned.

__Parameters__
* entityName `string`  - The entity name to resolve.

__Returns:__ `Symbol`


<a name=getValue />
#### getValue(obj)
Gets the value of the property, variable, or accessor represented by the symbol on the given object.

__Parameters__
* obj `any`  - The object to get the value for.

__Returns:__ `any`


<a name=setValue />
#### setValue(obj, value)
Sets the value of the property, variable, or accessor represented by the symbol on the given object.

__Parameters__
* obj `any`  - The object to set the value on.
* value `any`  - The value to set.

__Returns:__ `void`


<a name=isVariable />
#### isVariable()
Returns true if the symbol is a variable; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isFunction />
#### isFunction()
Returns true if the symbol is a function; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isClass />
#### isClass()
Returns true if the symbol is a class; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isInterface />
#### isInterface()
Returns true if the symbol is an interface; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isEnum />
#### isEnum()
Returns true if the symbol is an enum; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isModule />
#### isModule()
Returns true if the symbol is a module; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isImport />
#### isImport()
Returns true if the symbol is an import; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isTypeParameter />
#### isTypeParameter()
Returns true if the symbol is a type parameter; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isProperty />
#### isProperty()
Returns true if the symbol is a class or interface property; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isMethod />
#### isMethod()
Returns true if the symbol is a class or interface method; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isAccessor />
#### isAccessor()
Returns true if the symbol is an accessor; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isGetAccessor />
#### isGetAccessor()
Returns true if the symbol is a get accessor; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isSetAccessor />
#### isSetAccessor()
Returns true if the symbol is a set accessor; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isEnumMember />
#### isEnumMember()
Returns true if the symbol is an enum member; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isTypeAlias />
#### isTypeAlias()
Returns true if the symbol is a type alias; Otherwise, returns false.

__Returns:__ `boolean`




<a name=Annotation />
### Annotation
--------------------
Represents a custom annotation.
[`name`](#name)
[`value`](#value)
[`getDeclarationFileName`](#getDeclarationFileName)

<a name=name />
#### name
The name of the annotation.

__Type:__ `string`


<a name=value />
#### value
The value of the annotation.

__Type:__ `any`


<a name=getDeclarationFileName />
#### getDeclarationFileName()
Returns the name of the file that the annotation was declared in.

__Returns:__ `string`




<a name=Type />
### Type
--------------------
Represents a type.
[`getName`](#getName)
[`getFullName`](#getFullName)
[`getDescription`](#getDescription)
[`getAnnotations`](#getAnnotations)
[`hasAnnotation`](#hasAnnotation)
[`getProperties`](#getProperties)
[`getProperty`](#getProperty)
[`getCallSignatures`](#getCallSignatures)
[`getConstructSignatures`](#getConstructSignatures)
[`getStringIndexType`](#getStringIndexType)
[`getNumberIndexType`](#getNumberIndexType)
[`isIdenticalTo`](#isIdenticalTo)
[`isSubtypeOf`](#isSubtypeOf)
[`isAssignableTo`](#isAssignableTo)
[`isSubclassOf`](#isSubclassOf)
[`getBaseClass`](#getBaseClass)
[`getBaseTypes`](#getBaseTypes)
[`hasBaseType`](#hasBaseType)
[`isClass`](#isClass)
[`isInterface`](#isInterface)
[`isTuple`](#isTuple)
[`isUnion`](#isUnion)
[`isArray`](#isArray)
[`isIndex`](#isIndex)
[`isAnonymous`](#isAnonymous)
[`isReference`](#isReference)
[`isEnum`](#isEnum)
[`isStringLiteral`](#isStringLiteral)
[`isTypeParameter`](#isTypeParameter)
[`isAny`](#isAny)
[`isString`](#isString)
[`isNumber`](#isNumber)
[`isBoolean`](#isBoolean)
[`isVoid`](#isVoid)
[`isIntrinsic`](#isIntrinsic)
[`isObjectType`](#isObjectType)
[`getEnumValue`](#getEnumValue)
[`getEnumName`](#getEnumName)
[`getEnumNames`](#getEnumNames)
[`getElementType`](#getElementType)
[`getElementTypes`](#getElementTypes)
[`createInstance`](#createInstance)
[`getConstructor`](#getConstructor)

<a name=getName />
#### getName()
Gets the name of the type, if any.

__Returns:__ `string`


<a name=getFullName />
#### getFullName()
Gets the qualified name of the type, if any.

__Returns:__ `string`


<a name=getDescription />
#### getDescription()
Gets the description of the type.

__Returns:__ `string`


<a name=getAnnotations />
#### getAnnotations(inherit)
Gets all annotations declared for the type.

__Parameters__
* inherit `boolean`  - True if annotations should be inherited from base types.

__Returns:__ `Annotation[]`


#### getAnnotations(name, inherit)
Finds annotations with the specified name. If no name is specified, then all annotations
are returns.

__Parameters__
* name `string`  - The name of the annotation to find.
* inherit `boolean`  - True if annotations should be inherited from base types.

__Returns:__ `Annotation[]`


<a name=hasAnnotation />
#### hasAnnotation(name, inherit)
Returns true if the type has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* name `string`  - The name of the annotation to find.
* inherit `boolean`  - True if base types should be checked for the annotation as well.

__Returns:__ `boolean`


<a name=getProperties />
#### getProperties()
Gets a list of all properties of the type. Note that properties include fields, accessors, and
methods.

__Returns:__ `Symbol[]`


<a name=getProperty />
#### getProperty(name)
Finds a property with the specified name. If no property is found, undefined is returned. Note that
properties include fields, accessors, and methods.

__Parameters__
* name `string`  - The property name to find.

__Returns:__ `Symbol`


<a name=getCallSignatures />
#### getCallSignatures()
Gets all call signatures of the type.

__Returns:__ `Signature[]`


<a name=getConstructSignatures />
#### getConstructSignatures()
Gets all construct signatures of the type.

__Returns:__ `Signature[]`


<a name=getStringIndexType />
#### getStringIndexType()
Gets the string index type of the type. For example, for { [key: string]: boolean }, getStringIndexType()
will return the intrinsic boolean type.

__Returns:__ `Type`


<a name=getNumberIndexType />
#### getNumberIndexType()
Gets the number index type of the type. For example, for { [key: number]: string }, getNumberIndexType()
will return the intrinsic string type.

__Returns:__ `Type`


<a name=isIdenticalTo />
#### isIdenticalTo(target, diagnostics)
Returns true if the target type is identical to the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* target `Type`  - The target type.
* diagnostics `Diagnostic[]`  - Array where diagnostic information regarding the differences between the types is placed.

__Returns:__ `boolean`


<a name=isSubtypeOf />
#### isSubtypeOf(target, diagnostics)
Returns true if the target type is a subtype of the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* target `Type`  - The target type.
* diagnostics `Diagnostic[]`  - Array where diagnostic information regarding the differences between the types is placed.

__Returns:__ `boolean`


<a name=isAssignableTo />
#### isAssignableTo(target, diagnostics)
Returns true if the target type is assignable to the current type; Otherwise, returns false. If diagnostic
information regarding the differences between the types is desired, any empty array should be passed to
the diagnostics parameter.

__Parameters__
* target `Type`  - The target type.
* diagnostics `Diagnostic[]`  - Array where diagnostic information regarding the differences between the types is placed.

__Returns:__ `boolean`


<a name=isSubclassOf />
#### isSubclassOf(target)
Returns true if the target type if a subclass of the current type; Otherwise, returns false.

__Parameters__
* target `Type`  - The target type.

__Returns:__ `boolean`


<a name=getBaseClass />
#### getBaseClass()
Gets the base class of a class type.

__Returns:__ `Type`


<a name=getBaseTypes />
#### getBaseTypes()
Gets the base types of a class or interface type.

__Returns:__ `Type[]`


<a name=hasBaseType />
#### hasBaseType(target)
Returns true if the target type is a base type of the current type; Otherwise, returns false.

__Parameters__
* target `Type`  - The target type.

__Returns:__ `boolean`


<a name=isClass />
#### isClass()
Returns true if the type is a class; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isInterface />
#### isInterface()
Returns true if the type is an interface; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isTuple />
#### isTuple()
Returns true if the type is a tuple; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isUnion />
#### isUnion()
Returns true if the type is a union type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isArray />
#### isArray()
Returns true if the type is an array; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isIndex />
#### isIndex()
Returns true if the type is an index type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isAnonymous />
#### isAnonymous()
Returns true if the type is anonymous; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isReference />
#### isReference()
Returns true if the type is a generic reference; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isEnum />
#### isEnum()
Returns true if the type is an enum; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isStringLiteral />
#### isStringLiteral()
Returns true if the type is a string literal; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isTypeParameter />
#### isTypeParameter()
Returns true if the type is a type parameter; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isAny />
#### isAny()
Returns true if the type is the intrinsic any type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isString />
#### isString()
Returns true if the type is the intrinsic string type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isNumber />
#### isNumber()
Returns true if the type is the intrinsic number type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isBoolean />
#### isBoolean()
Returns true if the type is the intrinsic boolean type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isVoid />
#### isVoid()
Returns true if the type is the intrinsic void type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isIntrinsic />
#### isIntrinsic()
Returns true if the type is an intrinsic type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=isObjectType />
#### isObjectType()
Returns true if the type is an object type; Otherwise, returns false.

__Returns:__ `boolean`


<a name=getEnumValue />
#### getEnumValue(value, ignoreCase)
Gets the numeric enum value for the given member name.

__Parameters__
* value `string`  - The enum member name to get the value for.
* ignoreCase `boolean`  - True if case should be ignored when finding the member name.

__Returns:__ `number`


<a name=getEnumName />
#### getEnumName(value)
Gets the enum member name for the given numeric enum value.

__Parameters__
* value `number`  - The numeric value to get the name for.

__Returns:__ `string`


<a name=getEnumNames />
#### getEnumNames()
Gets a list of enum member names.

__Returns:__ `string[]`


<a name=getElementType />
#### getElementType()
Gets the element type for an array type.

__Returns:__ `Type`


<a name=getElementTypes />
#### getElementTypes()
Gets the element types a tuple type.

__Returns:__ `Type[]`


<a name=createInstance />
#### createInstance(args)
Creates an instance of a class. If arguments are provided then the constructor is called; Otherwise,
the object is created without calling the constructor. To call a parameter-less constructor, pass an empty
array to args.

Note that This method assumes that the .d.json file is in the same directory as the .js file that it contains
type information for. For external modules, Node's require method is used to load the JavaScript module.

__Parameters__
* args `any[]`  - The constructor arguments.

__Returns:__ `any`


<a name=getConstructor />
#### getConstructor()
Gets the JavaScript constructor for a class type.

Note that This method assumes that the .d.json file is in the same directory as the .js file that it contains
type information for. For external modules, Node's require method is used to load the JavaScript module.

__Returns:__ `Function`




<a name=Signature />
### Signature
--------------------
Represents a call signature.
[`getDescription`](#getDescription)
[`getAnnotations`](#getAnnotations)
[`hasAnnotation`](#hasAnnotation)
[`getParameters`](#getParameters)
[`getParameter`](#getParameter)
[`getReturnType`](#getReturnType)

<a name=getDescription />
#### getDescription()
Gets a description of the signature.

__Returns:__ `string`


<a name=getAnnotations />
#### getAnnotations(name)
Finds annotations with the specified name. If no name is specified, then all annotations
are returned.

__Parameters__
* name `string`  - The name of the annotation to find.

__Returns:__ `Annotation[]`


<a name=hasAnnotation />
#### hasAnnotation(name)
Returns true if the symbols has an annotation with the specified name; Otherwise, returns false.

__Parameters__
* name `string`  - The name of the annotation to find.

__Returns:__ `boolean`


<a name=getParameters />
#### getParameters()
Gets a list of all parameters for the call signature.

__Returns:__ `Symbol[]`


<a name=getParameter />
#### getParameter(name)
Gets a parameter for the signature with the specified name. If no parameter matches the name then undefined
is returned.

__Parameters__
* name `string`  - The parameter name to find.

__Returns:__ `Symbol`


<a name=getReturnType />
#### getReturnType()
Gets the return type of the signature.

__Returns:__ `Type`




<a name=Diagnostic />
### Diagnostic
--------------------
Diagnostic information.
[`filename`](#filename)
[`messageText`](#messageText)
[`code`](#code)

<a name=filename />
#### filename
The name of the .d.json file that contained the error

__Type:__ `string`


<a name=messageText />
#### messageText
Message explaining the error.

__Type:__ `string`


<a name=code />
#### code
Error code.

__Type:__ `number`




<a name=DiagnosticError />
### DiagnosticError
--------------------
Extension of standard Error that includes diagnostic information.
[`diagnostics`](#diagnostics)
[`name`](#name)
[`message`](#message)

<a name=diagnostics />
#### diagnostics
Array of Diagnostics that provides details on the error that occurred.

__Type:__ `Diagnostic[]`


<a name=name />
#### name

__Type:__ `string`


<a name=message />
#### message

__Type:__ `string`
