---
description: Perl 6
---

# Rakudo

## Variables

### Sigils

| Sigil | Constraint | Default | Assignment |
| :--- | :--- | :--- | :--- |
| $ | Mu \(none\) | Any | item |
| @ | Positional | Array | list |
| % | Associative | Hash | list |
| & | Callable | Callable | Item |

### Classes

```text
class FailHash is Hash {
    has Bool $!final = False;
    multi method AT-KEY ( ::?CLASS:D: Str:D \key ){
        fail X::OutOfRange.new(:what("Hash key"), :got(key),
          :range(self.keys)) if $!final && !self.EXISTS-KEY(key);
        callsname
    }
    
    method finalize() {
        $!final = True
    }
}

my %h is FailHash = oranges => "round", bananas => "bendy";
say %h<oranges>;
%h.finalize;
```

### Sigilless Variables

* No context enforced
  * Values can be passed without implicit conversion
* No associated containers
  * Variables directly represent immutable values

```text
my \degrees = pi / 180;
my \theta = 15 * degrees;

sub logged(&f, |args) {
    say('Calling ' ~ &f.name ~ ' with arguments ' ~ args.perl);
    my \result = f(|args);
    say(&f.name ~ ' returned ' ~ result.perl);
    return |result;
}
```

### Contexts

* Interpret value of container
  * Coerces value into type or class
  * Forces implicit coercion by calling a specific method on container

#### Sink context

* Void context
* Results of operation and block return values are discarded
* Invoked in warnings and errors when statement does not understand how to use value

```text
sub foo {
    return [<a b c>] does role {
        method sink { say "sink called" }
    }
}
```

#### Number Context

* First of conversion/interpretation contexts
  * Duck-type variables into whatever value is required

```text
my $t = True;
my $f = False;
say $t + $f; # OUTPUT: <<1>>
say $t.Numeric; # 1
say $f.Numeric; # 0

my $list = <a b c>;
say True + $list; # 4
```

### String Context

* Values manipulated as strings for printing
  * Regular expression smartmatching
  * Generally calls Str routine
* ~ is unary string contextualizer
  * Concatenates strings as operator
  * Context operator as prefix
* Empty lists or other containers stringify to an empty string
* Empty buffer in a string context can yield errors

```text
put 333444777 ~~ /(3+)/;

my @array = [ [1,2,3], ]4,5,6]];
say ~@array;

say [~] [3, 5+6i, Set(<a, b, c>), [1,2,3] ];
```

#### Boolean Context

* Nonzero/nonempty is true
* Zero/empty is false
* Context used in if and while predicates
  * Equivalent to calling **so** on predicates
  * .so can be defined to return any Boolean value
* **?** is context operator
* **!** is negation operator

```text
say "Hey" if 7;
say "Ahoy" if not set().so;

say ? 0i; # False
say ! :true; # False
```

#### List Context

* Multiple list contexts
* Contextualizer is **,**
  * Results in invocation of **.list** method

#### Item Context

* Forced when assigning to a scalar variable
* Contextualizer is **$**
  * Calls **.item** method
* Containerizes values in a data structure
  * Prevents flattening into a surrounding list or data structure

### Twigils

* Influence scoping of a variable
  * No influence over primary sigil interpolation

| Twigil | Scope |
| :--- | :--- |
| ! | Attribute \(class member\) |
| \* | Dynamic |
| . | Method |
| : | Self-declared formal named parameter |
| &lt; | Index into match object |
| = | Pod variable |
| ? | Compile-time variable |
| ^ | Self-declared formal positional parameter |
| none | Based only on declarator |
| ~ | Sublanguage seen by parser at lexical location |

#### Compile-Time Variables

| $?FILE | Current file |
| :--- | :--- |
| $?LINE | Current line, indexed from 1 |
| ::?CLASS | Current class |
| %?RESOURCES | Files associated with Distribution of current compilation unit |
| $?PACKAGE | Current package |
| $?MODULE | Current module |
| $?CLASS | Current class \(as variable\) |
| $?ROLE | Current role \(as variable\) |
| $?TABSTOP | Number of spaces per tab within heredoc or virtual margin |
| $?NL | Definition of vertical newline: LF, CR or CRLF |
| $?DISTRIBUTION | Distribution of current compilation unit |
| $?BITS | Number of data bits program is being compiled upon |
| $?ROUTINE | Current routine program is within - Returns instance of **Sub** and supports recursion |
| &?BLOCK | Allows introspection into single block of code |

#### Dynamic Variables

| Variable | Description |
| :--- | :--- |
| @\*ARGS | String array of command line arguments |
| %\*ENV | OS Environment Variables |
| $\*REPO | Modules installed/loaded |
| $\*TZ | System timezone offset as seconds from GMT |
| $\*CWD | Current Working Directory |
| $\*KERNEL | Description of currently running kernel |
| $\*DISTRO | Info about OS distribution and environment-specific values \(path separators, etc\) |
| $\*PERL | Info on current implementation of Perl 6 language and compiler |
| $\*PID | Current Process Identifier |
| $\*PROGRAM-NAME | Path to current executable if entered at command line or **-e** if perl was invoked with the **-e** flag |
| $\*PROGRAM | IO::Path of program being executed |
| $\*EXECUTABLE | IO::Path of Perl executable currently running |
| $\*USER | Info about user currently running program, evaluates to username in a string context and UID if numeric context |
| $\*GROUP | Primary group of user running program |
| $\*HOME | IO::Path of user home directory |
| $\*TMPDIR | IO::Path representing system temporary directory |
| $\*THREAD | **Thread** object representing currently executing thread |

### Scopes

| Declarator | Effect |
| :--- | :--- |
| my | Lexical scope |
| our | Package scope |
| has | Attribute name \(static field on class\) |
| anon | Private to construct |
| state | Lexical scope with persistence |
| augment | Adds definitions to existing name |
| supersede | Replaces definition of existing na,e |

## I/O Operations

### Paths

```text
# Iterate current directory contents
for dir() -> $file {
    say $file;
}

# Name of all .jpg files in ~/Downloads
my @jpegs = $*HOME.dir: test => /:i '.' j[g $/;

# Recursively list all files and directories
sub MAIN($dir = '.') {
    my @todo = $dir.IO;
    while @todo {
        for @todo.pop.dir -> $path {
            say $path.Str;
            @todo.push: $path if $path.d;
        }
    }
}

# Lazy way to find first three Perl 6 files from CWD
my @stack = '.'.IO;
my $perl-files = gather while @stack {
    with @stack.pop {
        when :d { @stack.append: .dir }
        .take when .extension.lc eq 'p6'
    }
}
.put for $perl-files[^3];

# Check if file is readable
`/path/to/file`.IO ~~ :r;

# Multiple tests at once: directory and executable
say '/tmp'.IO ~~ :d & :x;

# Test against open file handle
$fh.path ~~ rw;
$fh.path.l; # Symbolic link

# Lazily read words of file
my $dict := bag 'my-file.txt'.IO.words;
say "Most common words: ", %dict.sort(-*.value).head: 5;

# Make directory
'~/src/project'.mkdir;
```

## Object Orientation

### Typing

* Types are objects
  * `my $int-type-obj = Int;`
* WHAT macro requests type
  * `my $int-type-obj = 1.WHAT;`

