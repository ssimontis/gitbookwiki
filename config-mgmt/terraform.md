# Terraform

## Purpose

* Infrastructure as Code
* Efficient Execution Plans
* Detection of Drift in Plans vs Actual Infrastructure
* Changeset Automation

## Configuration Language

### Resources and Modules

* Resource = Single infrastructure object
* Module = Group of resources
  * Describes relationship between set of objects to create higher-level system
* Configuration begins in a root module
  * Tree of child modules created when one module calls into others

