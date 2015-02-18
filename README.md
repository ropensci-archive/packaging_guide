# rOpenSci Style Guide

The following are in-development style guidelines for contributed packages to rOpenSci. See [issues]() to discuss these.

## Package naming

## 

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
