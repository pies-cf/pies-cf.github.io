# PieLang
This is the docs for PieLang

## Comments
Comments can be made using `#` at the start of a line
```
@main
# This is a comment
log Hello, World!
```

## Sections
You can define a section by starting a line with an `@`\
By default the program will start in the `main` section
```
@main
log Hello, World!
```
**Note: The tmp section cannot be used. (see [tmp section](#tmp-section))**

## Include section
The include section can list a set of `.pi` files which will be included at runtime. The include will take their into your file as `{fileName}.{sectionName}`

**main.pi**
```
@include
logger.pi

@main
call logger.info Hello world!
```

**logger.pi**
```
@info
log [INFO] $data
```

Output:
```
[INFO] Hello World!
```

## Module Section
**Note: This is quite an advanced feature.**

The module section can be used to include custom modules

**main.pi**
```
@module
example.js

@main
exampleMoudle hello!
```

**example.js**
```js
module.exports = [
  {
    names: ["exampleMoudle"],
    async execute({ args }) {
      console.log(args.join(" "))
    }
  }
]
```

Output:
```
hello!
```

## Keys and Args
The language reads the document based on a set of `keys` and `args`

`keys` are also refered to as `names` or `keywords`
```
key arg1 arg2 ...
```

## Variables
You can use the `set` keyword to set variables. You can use `$variableName` to referance a variable.

```
@main
set word1 hello
set word2 world

log $world1 $world2
```

Output:
```
hello world
```

---

You can set a variable based on a return using the `->` operator

```
@main
set example 15
isInteger->is.int example

log $isInteger
```

Output:
```
true
```

## Embeding Code
You can write some code in `{}` to embed it
```
@main
log {math 1 + 2}
```

Which would complie to:
```
@main
log 3
```
## tmp section
The tmp section can be used with `{{}}`

```
@main
call {{log hi}}
```

Which would complie to:
```
@main
call tmp

@tmp
log Hello, World!
```

## Call and Jump
The call and jump can be used to jump to sections (also known as goto)

### Jump
Jump will goto a section and clear all variables

```
@main
set a 1
jump test
# Never called
log After jump!

@test
# $a = NULL
log $a
```

Output:
```
NULL
```


### Call
A call can be used like a function. Call will jump to a section and preserve variables. Once the section has completed it will return to run.

```
@main
set a 1
call test
log After call!

@test
# $a = 1
log $a
```

Output:
```
1
After call!
```

### Callif and Jumpif
callif and jumpif can be used to only jump if a condition is true

callif and jumpif can be used with the same syntax, for this example we will use jumpif.

```
@main
example->readin True or False?: 
jumpif $example onlyIfTrue

@onlyIfTrue
log It's true!
```