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

## Package development

1. Use `message()` and `warning()` to communicate with user in your functions.  Please do not use `print()`
1. Use `INCLUDE` instead of `DEPENDS` for packages providing functions you use internally only.
1. Use a `NAMESPACE` file instead of exporting all your internal functions.
1. Consider following an R style guide, or `formatR`
1. Include examples in the documentation. (Provide working illustrations and act as a poor man's unit test).
1. When calling another function internally, such as `optim()`, pass all the function options up to the user as options with defaults, rather than hard-coding in a certain choice, such as the method for optimization.
1. Definitely use `roxygen2` documentation for R functions.
1. Use a version management repository like GitHub.
1. [`devtools` package](https://github.com/hadley/devtools) and it's wiki are an excellent resource.
