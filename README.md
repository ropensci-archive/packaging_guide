# rOpenSci Packaging Guide

rOpenSci accepts packages that meet our guidelines via a streamlined [onboarding process](https://github.com/ropensci/onboarding). To ensure a consistent style across all of our tools we have developed this guide. We strongly recommend that package developers read Hadley Wickham's concise but thorough book on package development which is available for [free online](http://r-pkgs.had.co.nz/) (and [print](http://www.amazon.com/dp/1491910593/ref=cm_sw_su_dp?tag=r-pkgs-20)).

* [Package naming](#pkgnaming)
* [Function/variable naming](#funvar)
* [README](#rme)
* [Documentation](#docs)
* [Package dependencies](#deps)
* [Testing](#testing)
* [Continuous integration](#ci)
* [Console messages](#messages)
* [Recommended software scaffolding](#tools)
* [Further guidance](#further)

## <a href="#pkgnaming" name="pkgnaming"></a> Package naming

* We strongly recommend short, descriptive names in lower case. If your package deals with one or more commercial services, please make sure the name does not violate branding guidelines. 

## <a href="#funvar" name="funvar"></a> Function/variable naming & general syntax

* We strongly recommend `snake_case` over all other styles unless you are porting over a package that is already in wide use. 

* Avoid function name conflicts with base packages or other widely used ones (e.g. `ggplot2`, `dplyr`, `magrittr`, `data.table`)

* For more information on how to style your code, name functions, and R scripts inside the `R/` folder, we recommend reading the [code chapter in Hadley's book](http://r-pkgs.had.co.nz/r.html).

## <a href="#rme" name="rme"></a> README

* All packages should have a README file, named `README.md`, in the root of the repository. The README should include, from top to bottom:

```
* The package name
* Badges for Travis-CI (and any other badges)
* Short description of the package
* Installation instructions
* Example usage
* rOpenSci footer image - use this markdown line
```

```
[![ropensci_footer](http://ropensci.org/public_images/github_footer.png)](http://ropensci.org)
```

* We recommend not creating `README.md` directly, but from a `README.Rmd` file (an Rmarkdown file) where possible. The advantage of the `.Rmd` file is you can combine text with code that can be easily updated whenever your package is updated. 

* See `devtools::use_readme_rmd()` to get a template for a `README.Rmd` file in your package.

* Consider setting up pre-commit hooks to ensure that `README.md` is always newer than `README.Rmd` (see `devtools::use_git_hook`)

* See the [gistr README](https://github.com/ropensci/gistr#gistr) for a good example README to follow.


## <a href="#docs" name="docs"></a> Documentation

* We strongly encourage all submissions to use `roxygen2` for documentation.  `roxygen2` is [an R package](http://cran.r-project.org/web/packages/roxygen2/index.html) that automatically compiles `.Rd` files to your `man` folder in your package from simple tags written above each function.

* Read Hadley's chapter on [documentation](http://r-pkgs.had.co.nz/man.html).

* By using `roxygen2`, this means you can generate your `NAMESPACE`. 

* Avoid exporting all functions by default. 


## <a href="#testing" name="testing"></a> Testing

* Use `testthat` for writing tests. Strive to write tests as you write each new function. This serves the obvious need to have proper testing for the package, but allows you to think about various ways in which a function can fail, and to _defensively_ code against those.

* `testthat` has a function `skip_on_cran()` that you can use to not run tests on CRAN. We recommend using this on all functions that are API calls since they are quite likely to fail on CRAN. These tests will still run on Travis.


## <a href="#ver" name="ver"></a> Versioning

* We strongly recommend that rOpenSci packages use semantic versioning. A detailed explanation is available on the [description chapter](http://r-pkgs.had.co.nz/description.html#version).

* Git tag each release. [[more info](http://marker.to/ZYd3kZ)] 

## <a href="#ci" name="ci"></a> Continuous integration

* All rOpenSci packages must use continuous integration. This ensures that all commits, pull requests, and new branches are run through `R CMD CHECK`. R is now a [natively supported language on Travis-CI](http://blog.travis-ci.com/2015-02-26-test-your-r-applications-on-travis-ci/), making it easier than ever to do continuous integration. See [R Packages](http://marker.to/NEr8Bd) for more help. 


## <a href="#egs" name="egs"></a> Examples

* Include extensive examples in the documentation. In addition to demonstrating how to use the package, these can act as an easy way to test package functionality before there are proper tests. However, keep in mind we require tests in contributed packages. If you prefer not to clutter up code with extensive documentation, place further documentation/examples in files in a `man-roxygen` folder in the root of your package, and those will be combined into the manual file by the use of `@template <file name>`, for example. You can run examples with `devtools::run_examples()`. 


## <a href="#deps" name="deps"></a> Package dependencies

* Use `Imports` instead of `Depends` for packages providing functions you use internally only. Use `Depends` only if you intend for the user to have functions available from your package dependencies. Make sure to list packages used for testing (`testthat`), and documentation (`knitr`, `roxygen2`) in your `Suggests` section of package dependencies. If you use any packages in your examples sections, make sure to list those, if not already listed elsewhere, in `Enhances` section of package dependencies. 


## <a href="#tools" name="tools"></a> Recommended scaffolding


* For http requests we strongly recommend using `httr` over `RCurl`. 
* For parsing JSON, use `jsonlite` instead of `rjson` or `RJSONIO`. 
* For parsing XML, if you only need to parse XML data, `xml2` is a good option as it makes parsing easier. However, if you need to create XML (in addition to parsing it), use the `XML` package. The `XML` package is more low level than `xml2`, and allows finer manipulation, and may be favored by those more familiar with XML data. 


## <a href="#messages" name="messages"></a> Console messages

* Use `message()` and `warning()` to communicate with the user in your functions. Please do not use `print()` or `cat()` unless it's for a `print.*()` method, as these methods of printing messages are harder for the user to suppress.


## <a href="#further" name="further"></a> Further guidance

* The [`devtools` package](https://github.com/hadley/devtools) and it's wiki are an excellent resource for in-depth package development help.

* If you are submitting a package to rOpenSci via the [onboarding repo](https://github.com/ropensci/onboarding), you can direct further questions to the rOpenSci team in the issue tracker, or in our [discussion forum](https://discuss.ropensci.org/).

## Suggestions and updates

* These packaging guidelines are a work in progress for packages contributed to the rOpenSci suite. Corrections, suggestions and general improvements are welcome on [our issue tracker](https://github.com/ropensci/packaging_guide/issues).
