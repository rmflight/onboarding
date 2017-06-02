## Package Review

*Please check off boxes as applicable, and elaborate in comments below.  Your review is not limited to these topics, as described in the reviewer guide*

- [x] As the reviewer I confirm that there are no conflicts of interest for me to review this work (If you are unsure whether you are in conflict, please speak to your editor _before_ starting your review).

#### Documentation

The package includes all the following forms of documentation:

- [ ] **A statement of need** clearly stating problems the software is designed to solve and its target audience in README
- [x] **Installation instructions:** for the development version of package and any non-standard dependencies in README
- [ ] **Vignette(s)** demonstrating major functionality that runs successfully locally
- [ ] **Function Documentation:** for all exported functions in R help
- [ ] **Examples** for all exported functions in R Help that run successfully locally
- [x] **Community guidelines** including contribution guidelines in the README or CONTRIBUTING, and `URL`, `Maintainer` and `BugReports` fields in DESCRIPTION 

>##### Paper (for packages co-submitting to JOSS)
>
>The package contains a `paper.md` with:
>
>- [ ] **A short summary** describing the high-level functionality of the software
>- [ ] **Authors:**  A list of authors with their affiliations
>- [ ] **A statement of need** clearly stating problems the software is designed to solve and its target audience.
>- [ ] **References:** with DOIs for all those that have one (e.g. papers, datasets, software).

#### Functionality

- [x] **Installation:** Installation succeeds as documented.
- [x] **Functionality:** Any functional claims of the software been confirmed.
- [x] **Performance:** Any performance claims of the software been confirmed.
- [ ] **Automated tests:** Unit tests cover essential functions of the package
   and a reasonable range of inputs and conditions. All tests pass on the local machine.
- [ ] **Packaging guidelines**: The package conforms to the rOpenSci packaging guidelines


#### Final approval (post-review)

- [ ] **The author has responded to my review and made changes to my satisfaction. I recommend approving this package.**

Estimated hours spent reviewing: 

---

### Review Comments

The package `gitlabr` provides a comprehensive interface to the **gitlab** API, enabling many different **gitlab** operations
from the `R` command line to any http reachable **gitlab** server for which an individual has credentials. This interface
appears to be very useful for anyone wishing to interact with a **gitlab** server from `R`. The breadth of functionality is
impressive, with functions even for creating **CI** files based on a particular template. It appears also that it is already being used
by some in the `R` community besides the author, given the number of downloads / month from CRAN.

Specific areas of concern that need to be addressed are below.

#### README

The README does not follow the packaging guidelines from **ropensci**, however most of the information requested is present
with the exception of a **statement of need**, which could be copied from the DESCRIPTION file.

```
* The package name
* Badges for continuous integration and test coverage (and any other badges)
* Short description of the package
* Installation instructions
* Example usage
```

#### Vignette

- the **quick-start-gitlabr** vignette does not use *generic* code or code that would successfully run in the setup chunk. To enable
this the author should consider having a very generic example with completely fictional *server*, *username* and *password*, and then 
**hide** a chunks that is really run to enable output in the examples. 
- the **quick-start-gitlabr** vignette seems more like a full vignette, one section of which is a **quick-start** guide. The author 
should consider either breaking the vignette into two, or re-titling the current vignette and making the quick-start just a sub-section.
- For either approach, the quick start could still use some actual commentary on what the code is doing outside of comments in the code blocks.
- Under **central features**, before API calls, a better explanation of **how** the base object is instantiated would be appreciated.
- For a quick start, the author might consider highlighting the *set style* **first**, as this is probably the easiest method and the 
one that new users are likely to be most familiar with. The other two styles would then be illustrated as more *functional* in nature
and useful for managing several connections simultaneously.

#### Function Documentation

- function `gl_list_branches` has **List create and delete branches** 3 times
- not sure that `gl_repository` and `gl_list_files` ... belong together in documentation.
- Why do some functions have parameters that are **ignored**?? See `gl_get_project_id` for example.
- in contrast to other exported functions that deal with **projects**, `gl_proj_req` seems out of place. Should it be 
`gl_project_request` instead??
- `gl_connection` documentation and the **vignette** are out of sync with respect to the version of the default API version, one says 
**v3** is default, other says **v4** is default.

#### Examples

In almost all cases, the examples are not code that would actually run without setup first. I understand the reason for this, it is
working with a web-service. However, one potential solution is to do something like:

```r
## Not run
my_connection <- gl_connection(....)
set_gitlab_connection(my_gitlab)
function_being_documented
```

with the example in `\dontrun{}` in the `roxygen2` documentation. Or if the author does not agree with the `set_gitlab_connection`, then one of the other styles. The point is that the examples should fully runnable on a **gitlab** instance somewhere after copying and pasting the code, even if it doesn't run otherwise.

Some functions that have no examples include:
- `gl_*_issue`
- `gl_create_merge_request`

- Comments on **why** certain parameters are set to what they are would also be appreciated in the examples.

In addition, for the `gl_*_ci` functions, some explanation of **how** changing parameters will change the resuling `*.yml` file would
really help. Even in the example field, having a the function call and then the output would be useful. Or this may be where a vignette
just on CI would be a good thing to do. But current documentation of the CI functions (besides pushing the file) are not very clear or 
useful.

#### Automated Tests

- testing setup documentation is in `Contributing.md`, and the code seems to work.
- I was able to run `covr::package_coverage(type = "tests")` by moving the **environment.yml** file to the **testthat** directory, putting the setting of variables at the top of `test_branches`, and disabling the first test in `test_ci`. See [my fork](https://github.com/rmflight/gitlabr/tree/rmfligth_review)
- For `test_ci`, I tried a couple of different solutions, but neither worked. Not sure why. 
- Overall, test coverage seems pretty good at 49%, with some of the code not being tested for good reasons, such as resulting in writing 
to the test gitlab repo, or using an interactive Shiny module.


#### Packaging Guidelines

- **Code duplication**:
  - `gl_connection` and `gl_project_connection` seem to be almost duplicates outside
  of the `project` argument and call to `to_project_id`, seems that `project = NULL` could be used 
  in `gl_connection` and then checked and set appropriately instead of having two separate functions?
  - `http_error_or_content`: without looking in here, can't tell that `ct` is content, thought it was count. Sometimes variable names are too short to be truly informative **where** they are used (eg in `gitlab`).
- **Lack of comments**. There is not a single code comment in any of the source
  code files outside of the `roxygen2` function documentation. Although **much** of the code is self-explanatory and easy to 
  follow, some
  of it (`gitlab` especially) is not easy to follow. Some comments on the non-exported
  functions would be very useful, or the author should consider use the `@noRd` tag to document in source
  using `roxygen2` but not generate Rd files.
