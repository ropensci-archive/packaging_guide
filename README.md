# rOpenSci Style Guide

The following are in-development style guidelines for contributed packages to rOpenSci. See [issues]() to discuss these.

## Package naming

Although we have been inconsistent in the past, consider the following: 

* use all lowercase letters, unless camelCase, etc. makes more sense
* make sure that the name isn't already taken 
    * Look [here](http://cran.r-project.org/web/packages/available_packages_by_name.html)
    * When you run check on your package, the process will ping CRAN to make sure there isn't pkg name conflict
* the github repository name should match the package name. Note that valid package names cannot have hyphens. This makes everything much easier in the long run
* make sure that the name you choose does not make the entity that provides the data upset - e.g., they may not want you to use their name as part of the package name (e.g., Acme may not want you to name R package `acme`)

## Function/variable naming

We prefer `snake_case` over all other styles.

## Documentation

We strongly encourage all submissions to use `roxygen2` for documentation.  `roxygen2` is [an R package](http://cran.r-project.org/web/packages/roxygen2/index.html) that automatically compiles your `.Rd` files in your man folder in your package if you write the documentation correctly following certain rules.

Check out [Hadley's devtools wiki](https://github.com/hadley/devtools/wiki/Documenting-functions). It has some great advice for using `roxygen2` to document R functions.

By using `roxygen2`, this means you can generate your `NAMESPACE` automatically - please avoid simply exporting all your internal functions.

## Examples

Include extensive examples in the documentation. In addition to demonstrating how to use the package, these can act as an easy way to test package functionality. However, keep in mind we require tests. If you prefer not to clutter up code with extensive documentation, place further documentation/examples in files in a `man-roxygen` folder in the root of your package, and those will be combined into the manual file by the use of `@template <file name>`.

## Package dependencies

Use `Imports` instead of `Depends` for packages providing functions you use internally only. Use `Depends` only if you intend for the user to have functions available from your package dependencies.

## Expose options to the user

When calling another function internally, such as `optim()`, pass all the function options up to the user as options with defaults, rather than hard-coding in a certain choice, such as the method for optimization.

## Console messages

Use `message()` and `warning()` to communicate with the user in your functions. Please do not use `print()` or `cat()` unless it's for a `print.*()` method, as these methods of printing messages are harder for the user to suppress.

## Best practices for working with APIs

xxxx

## Further help

The [`devtools` package](https://github.com/hadley/devtools) and it's wiki are an excellent resource for in-depth package development help.
