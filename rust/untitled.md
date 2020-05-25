# Code Organization

### Code Organization

* Functions
* Modules
* Crates
  * **lib.rs** file on same executable crate
  * Dependency crate specified on **Cargo.toml**
  * Can be specified as
    * Path
    * Git repo
    * crates.io 
* Workspaces
  * Allow multiple crates to be managed as a single project

### Crates

* Similar to packages in other languages
* Individual units of compilation
  * Crate with child file modules merges children into top crate file to form a single compilation unit
* Can produce an executable binary or a library
  * **src/main.rs** is crate root/entry point for a binary crate
  * **src/lib.rs** is entry point for a library crate
    * Created by passing `--lib` switch to _cargo_
* Can create a binary and manually add a library containing logic to be imported into **main.rs**

#### File Path Crates

* Begin with a binary crate and use `cargo new binaryname/libname --lib` to create a library in a folder sibling to the **src** folder for the binary
* Folder _libname_ will contain its own **Cargo.toml** and **src** folders
* To add library to binary, create `[dependencies]` section of **Cargo.toml**
  * Add \`libname = { path = "libname" }

#### Git Crates

* Add entry to Cargo.toml in following format

`rocket = { git = "https://github.com/SergioBenitez/Rocket" }`

`rocket = { git = "https://github.com/SergioBenitez/Rocket", tag = "v0.3.2" }` 

* **branch** and **rev** are also supported to target specific branch or revision of repo

#### Documentation

* Comments in form of `//! Top of crate file` or `//! Top of module` are crate- and module-level documentation
* Cargo generates documentation from these comments when uploading crate
  * All comments form documentation hosted on **docs.rs** for crate
* Readme file may be specified to Cargo by adding **readme** field to Cargo.toml

### Modules

#### Accessing Modules

* Use `modulename::` syntax to write module path to a public member within the module
* As a shortcut, the `use modulename` statement can be used to import either an entire module, or specific objects in that module
  * The objects specified no longer must be prefixed
* Public members of module are available for use
  * Private functions can be called from within the same module OR within a child module that has been nested inside
* `self::` can be used to specify current module if ambiguity exists between names
* `super::` keyword used to access parent module

  * Can be used to access root functions from inside a module



  \*\*\*\*

#### **Modules and Testing**

* Tests for a file are generally written within the same file as the code under test as a child module
  * Applying the macro `#[cfg(test)]` will ensure test module only recompiles when running tests
  * Customary to create module named _tests_ 
  * To gain access to parent scope for systems under test, import root using `use super::*`

#### Declaring Modules

* `mod` keyword defines modules
  * Can place `pub` identifier in front of `mod` to allow module to be imported by other files

### Workspaces

* Manage multiple crates from same project
* Rust uses _shared build directory_ to prevent download and compilation of same dependency multiple times
  * Located under project root when `cargo build` is run from the project root
* Setup for workspace structure
  * Create a directory
  * Add a Cargo.toml file to new directory
  * `cargo new dirname/lib --lib`
  * \`cargo new dirname/
* Top **Cargo.toml** must be configured for workspaces
  * Add `[workspace]` section
  * Define _members_ as an array of project root directories to include
  * Change library's Cargo.toml to set package name to the `dirname`
  * Add library as a dependency to the binary's Cargo.toml by adding dependency to the relative path to the library `../../lib`
  * \`\`

