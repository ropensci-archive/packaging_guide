# rOpenSci Packaging Guide

The following are in-development packaging guidelines for contributed packages to rOpenSci. Use [issues](https://github.com/ropensci/packaging_guide/issues) to discuss these.

There are other style guides out there, though of slightly different scope. Google has an [R code style guide](https://google-styleguide.googlecode.com/svn/trunk/Rguide.xml), though doesn't cover packaging or working with APIs. Hadley Wickham has a [syntax style guide](http://adv-r.had.co.nz/Style.html), and a [book on writing R packages](http://r-pkgs.had.co.nz/).

__Sections:__

* [Package naming](#pkgnaming)
* [Function/variable naming](#funvar)
* [Syntax](#syntax)
* [Files and file names](#files)
* [README](#rme)
* [Vignettes](#vign)
* [Documentation](#docs)
* [Examples](#egs)
* [Package dependencies](#deps)
* [Expose options to the user](#options)
* [Console messages](#messages)
* [DRY out your code](#dry)
* [Best practices for working with APIs](#apis)
    * [Tools](#tools)
    * [Authentication](#auth)
    * [Testing](#testing)
* [Continuous integration](#ci)
* [Versioning](#ver)
* [Further guidance](#further)

## <a href="#pkgnaming" name="pkgnaming"></a> Package naming

Although we have been inconsistent in the past, we'd like to make package naming consistent moving forward. Consider the following: 

* Use all lowercase letters, unless `camelCase`, etc. makes more sense, e.g., the `XML` package makes sense to have uppercase letters since the XML is most often referred to in uppercase
* Make sure that the package name isn't already taken 
    * Look at the [list of available packages](http://cran.rstudio.com/web/packages/available_packages_by_name.html)
    * When you run check on your package, the process will ping CRAN to make sure there isn't a package name conflict
* The GitHub repository name should match the package name. Note that valid package names cannot have hyphens. This makes everything much easier in the long run. In addition, avoid having the package as a sub-directory within the root of the repository.
* Make sure that the name you choose does not make the entity that provides the data upset - e.g., they may not want you to use their name as part of the package name (e.g., Acme may not want you to name R package `acme`)

## <a href="#funvar" name="funvar"></a> Function/variable naming

We prefer `snake_case` over all other styles, though if you're bringing over a package that's been around for a while with something else, e.g., `camelCase`, that's fine. Do not in most cases use function or variable names that already exist in base R or other packages. Of course, you aren't expected to go check all other packages, though you can do a quick check at, for example, [R-Documentation.org](http://www.rdocumentation.org/).

## <a href="#syntax" name="syntax"></a> Syntax

Follow [Hadley's guidelines on syntax](http://adv-r.had.co.nz/Style.html). In short: 

* Spacing: use appropriate spacing around infix operators (`x = 5` instead of `x=5`)
* Curly braces: open curly brace never goes on its own line, closing curly brace goes on its own line unless followed by `else`
* Line length: Limit each line to 80 characters. This is easier to read, and `R CMD CHECK` does check this (though checks for `< 100`)
* Assignment: Use `<-`, not `=`

## <a href="#files" name="files"></a> Files and file names

Use meaningful names like `optim.R` instead of `aaaa.r` if the function within is named `optim()`. In addition, try to avoid piling many unrelated functions in a single file, so that the file `optim.R` really only has the function `optim()` and any helper functions only used by it. Internal helper functions used across many functions should go in a separate file, e.g. `utils.R`. Naming files so that others can easily find things makes collaborative development easier

## <a href="#rme" name="rme"></a> README

All packages should have a README file, named `README.md`, in the root of the repository. The README should include, from top to bottom:

* The package name
* Badges for Travis-CI (and any other badges)
* Short description of the package
* Installation instructions
* Example usage
* rOpenSci footer image - use this markdown line

```
[![ropensci_footer](http://ropensci.org/public_images/github_footer.png)](http://ropensci.org)
```

We recommend not creating `README.md` directly, but from a `README.Rmd` file (an Rmarkdown file). The advantage of the `.Rmd` file is you can combine text with code that can be easily updated whenever needed. That is, you don't need to copy and paste output of your examples into a `.md` file. Once you have the `README.Rmd` file, you can compile it to `README.md` easily by doing `knitr::knit("README.Rmd")`. Make sure to add appropriate options to your knitr chunks (e.g., you probably don't want your instructions on installing the package running every time your update your README). See `devtools::use_readme_rmd()` to get a template for a `README.Rmd` file in your package.

See the [gistr README](https://github.com/ropensci/gistr#gistr) for a good example README to follow.

## <a href="#vign" name="vign"></a> Vignettes

Vignettes are an important piece of R packages that many users look for when they install and load a package. Vignettes are more than a help/man file, and can have long (or short) examples/use cases of working with the package. They can be written in markdown or latex. We recommend using markdown - see the [R Packages book](http://r-pkgs.had.co.nz/vignettes.html) for help. Though if you prefer latex, go for it. 

We want all rOpenSci packages to have at least one vignette. Ideally, each package will have more than one vignette, covering additional use cases, package functionality, etc.

You can include a vignette in your `vignettes/` directory in the root of your package that builds on package installation and `R CMD CHECK`, or one that is already built (e.g., rendered to markdown).

The `roxygen2` package will soon include a roclet to automatically re-build  vignettes when they change.

## <a href="#docs" name="docs"></a> Documentation

We strongly encourage all submissions to use `roxygen2` for documentation.  `roxygen2` is [an R package](http://cran.r-project.org/web/packages/roxygen2/index.html) that automatically compiles your `.Rd` files to your `man` folder in your package if you write the documentation following a few rules.

Check out [Hadley's devtools wiki](https://github.com/hadley/devtools/wiki/Documenting-functions) for advice on using `roxygen2` to document R functions.

By using `roxygen2`, this means you can generate your `NAMESPACE` automatically - please avoid simply exporting all your internal functions.

## <a href="#egs" name="egs"></a> Examples

Include extensive examples in the documentation. In addition to demonstrating how to use the package, these can act as an easy way to test package functionality before there are proper tests. However, keep in mind we require tests in contributed packages. If you prefer not to clutter up code with extensive documentation, place further documentation/examples in files in a `man-roxygen` folder in the root of your package, and those will be combined into the manual file by the use of `@template <file name>`, for example. You can run examples with `devtools::run_examples()`. 

## <a href="#deps" name="deps"></a> Package dependencies

Use `Imports` instead of `Depends` for packages providing functions you use internally only. Use `Depends` only if you intend for the user to have functions available from your package dependencies. Make sure to list packages used for testing (`testthat`), and documentation (`knitr`, `roxygen2`) in your `Suggests` section of package dependencies. If you use any packages in your examples sections, make sure to list those, if not already listed elsewhere, in `Enhances` section of package dependencies. 

## <a href="#options" name="options"></a> Expose options to the user

When calling another function internally, such as `optim()`, pass all the function options up to the user as options with defaults, rather than hard-coding in a certain choice, such as the method for optimization.

A common set of options you want to expose to users when working with web APIs are curl options. When using `httr`, this is as simple as adding `...` as a parameter in your functions. For example:

```r
foo <- function(...) {
  GET("http://google.com", ...)
}
```

Allows the user to pass in curl options like:

```r
foo(config = verbose())
```

In that case we can print curl information to the console as curl does its thing. See [our blog post](http://ropensci.org/blog/2014/12/18/curl-options/) for more on curl options.

## <a href="#messages" name="messages"></a> Console messages

Use `message()` and `warning()` to communicate with the user in your functions. Please do not use `print()` or `cat()` unless it's for a `print.*()` method, as these methods of printing messages are harder for the user to suppress.

## <a href="#dry" name="dry"></a> DRY out your code

Attempt to follow the [Don't Repeat Yourself (DRY) principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). This generally boils down to: keep your functions small. Even we can't claim to have perfectly DRYed code, but we are getting better at this :)

This is an essential component of sustainabile, reliable software. If you repeat yourself often, you have a higher chance of making mistakes. In addition, it's simply easier to change something once instead of multiple times. 

## <a href="#apis" name="apis"></a> Best practices for working with APIs

Again, Hadley has good advice here. See his vignette [Best practices for writing an API package](http://cran.r-project.org/web/packages/httr/vignettes/api-packages.html) in the `httr` package.

### <a href="#tools" name="tools"></a> Tools

* For http requests, use `httr` instead of `RCurl`. `RCurl` is a low level wrapper around the `libcurl` library, and has a lot of flexibility. `httr` is a higher level client, making it much easier to do http requests. In addition, `httr` will be maintained moving forward, and is actively incorporating new feaures.
* For parinsg JSON, use `jsonlite` instead of `rjson` or `RJSONIO`. These packages do similar things, but `jsonlite` is now the most used JSON client for R, and we believe will get the most support moving forward.
* For parinsg XML, if you only need to parse XML data, `xml2` is a good option as it makes parsing easier. However, if you need to create XML, use the `XML` package. The `XML` package is more low level than `xml2`, and allows finer manipulation, and may be favored by those more familiar with XML data. 

... more to come

### <a href="#auth" name="auth"></a> Authentication

The `httr` package should have all the tools you'll need for authentication:

* Simple user/password: The `httr::authenticate()` function accepts username and password, an the type of of authentication. 
* API key: If the API you work with supports API key authentication, you can pass the key as a query parameter (see `query` parameter), or in the header of the request (see `httr::add_headers()`), depending on what the API supports. If both are supported we recommend passing the key in the header as it is more secure. 
* OAuth: `httr` supports OAuth1 and OAuth2 schemes, and are quite easy to work with. See the [httr demos](https://github.com/hadley/httr/tree/master/demo) for examples for various OAuth schemes. 
* OAuth token: If the API you are working with supports OAuth tokens, this is often preferrable to using the full OAuth flow since its simpler. 
* https: You shouldn't have a problem working with APIs that use https in `httr`. If you do run into problems, get in touch with us.

### <a href="#testing" name="testing"></a> Testing

Use `testthat` for writing tests. Strive to write tests as you write each new function. This serves the obvious need to have proper testing for the package, but allows you to think about various ways in which a function can fail, and to _defensively_ code against those.

`testthat` has a function `skip_on_cran()` that you can use to not run tests on CRAN. If you suspect that the web API your package works with could fail, you __do not__ want to run tests that couuld fail on CRAN, else CRAN maintainers will complain. However, do always run tests on Travis-CI (and other continuous integration tools). See the section on [continuous integration](#ci).

Tests are especially tough when working with APIs since you are likely not in control of the server serving the API. If you write thorough tests, your tests should catch any time the API is down. 

## <a href="#ci" name="ci"></a> Continuous integration

All rOpenSci packages must use continuous integration. This ensures that all commits, pull requests, and new branches are run through `R CMD CHECK`. R is now a [natively supported language on Travis-CI](http://blog.travis-ci.com/2015-02-26-test-your-r-applications-on-travis-ci/), making it easier than ever to do continuous integration. See [R Packages](http://r-pkgs.had.co.nz/check.html#travis) for more help. 

## <a href="#ver" name="ver"></a> Versioning

* Semantic versioning: rOpenSci packages should generally follow [semantic versioning][semver], of the form `major.minor.patch (x.y.z)`, e.g., `0.1.7`. In an ideal world, rOpenSci packages may follow Yihui's rule of only sending minor versions to CRAN per his blog post [R Package Versioning][yihuiver]. However, given that most rOpenSci packages interact with web resources that can change at any time (both minor non-breaking, and major breaking changes), we sometimes need to push an immediate fix to CRAN. This fix may not have changed much, so usually doesn't warrant a bump in the minor version, but we'll instead just bump patch version. A version `1.0` does not necessarily indicate maturity - rather if a package is going to be considered stable, and e.g., in maintenance mode into the future, then indicate that at whatever version.
* Bump the version number after going through CRAN. So if you push `0.4.0` to CRAN, then immediately after its on CRAN, bump the development version on GitHub to e.g., `0.4.1`.
* Development version numbers: We are moving towards adopting the convention used by Hadley Wickham of appending e.g., `.999` to the end of the version number for GitHub versions, such that there are 4 places (e.g., `0.1.1.999`). This distinctly separates CRAN versions that never have the fourth slot (e.g., `0.1.1`). 
* Make sure to tag all versions pushed to CRAN. Use `git tag` (e.g., `git tag v0.1.1`). In addition, put related NEWS for each version in a new release for that tag on the Releases page (see for example this release for [ropensci/rplos](https://github.com/ropensci/rplos/releases/tag/v0.4.6)).
* Use Github Milestones to help track issues associated with versions. Go to the Issues tab on the side, then to Milestones, and create a milestone labeled with the issue in question. Then assign issues as needed to that milestone. This helps make sure you don't miss something that should go into a version.
* `devtools::session_info()` reports versions, and whether a package was installed from CRAN, locally or from GitHub (and git commit from which it was installed). 

## <a href="#further" name="further"></a> Further guidance

The [`devtools` package](https://github.com/hadley/devtools) and it's wiki are an excellent resource for in-depth package development help.

[semver]: http://semver.org/
[yihuiver]: http://yihui.name/en/2013/06/r-package-versioning/
