# Tests Strategy

 * [Explanation](#explanation)
   * [Technical tests](#technical-tests-explanation)
     * [Unit level](#technical-tests-unit-level-explanation)
     * [Integration level](#technical-tests-integration-level-explanation)
   * [Functional tests](#functional-tests-explanation)
 * [Rules](#rules)
   * [Early stop](#rules-early-stop)
   * [Strict mode](#rules-strict-mode)
   * [Risky tests](#rules-risky-tests)
   * [Tests isolation](#rules-tests-isolation)
   * [Real coverage](#rules-real-coverage)
   * [Test documentation](#rules-test-documentation)
   * [Tests Root directory](#tests-root-directory)
   * [Tests order](#tests-order)
   * [Base namespace](#base-namespace)
 * [Example](#example)
   * [PhpUnit implementation](#example-phpunit-implementation)
   * [Composer implementation](#example-composer-implementation)

## Explanation
<a name="technical-tests-explanation"></a>
### Technical tests
**Contributor/code point of view**

*Will change each time code is updated*

Test scope is a public php `class` method

<a name="technical-tests-unit-level-explanation"></a>
 * **Unit level** (PhpUnit)

**Any `object` used in the tested class (aka dependency, could be contructor argument or an instanciation inside the class) must be mocked** using **Prophecy**, if "not possible", the test must be moved under Integration level. 

*Most of tests should be done at Unit level as it's the faster one (execution time point of view), so issues are found earlier.*

<a name="technical-tests-integration-level-explanation"></a>
 * **Integration level** (PhpUnit)

Dependencies *could* be mocked but it's not mandatory.

Put here all tests that test :

 * the behavior of class with an another one
 * a class that use a final class as dependency
 * a method that internally instanciate other class
 * ...
 * a method that have dependencies not mocked for any other reasons

<a name="functional-tests-explanation"></a>
### Functional tests
**End-user point of view**

*Should not change for minor release or hotfix*

Test scope is the "public API" of this repository (testing api payloads or website generated html pages for instance).

 * With **Phpunit**

Test could use a slice of repository source code (to ensure a functionality for instance but without taking in account a "upper level" of code)

 * With **Behat**

Tests will use the complete repository source code and will perform tests to cover production end-user actions

## Rules 

*See [Phpunit config example](example-phpunit-configuration-example)*

<a name="rules-early-stop"></a>
### Early stop
**Tests execution must be stop at first failure or error**

Stopping execution is useful for CI, as it will produce builds with a smaller execution time

It's not really useful for the developer, but it's better to always keep the same behavior than CI, even for developer

<a name="rules-strict-mode"></a>
### Strict mode
<a name="rules-strict-mode-exit-code"></a>
#### Exit status
**Tests execution must failed if contains one failed or on error test**

<a name="rules-strict-mode-fails-if"></a>
#### Fails if
Following test must be converted into failed test : 
<a name="rules-strict-mode-fails-if-php-errors"></a>
 * A **test that have php notices/warnings/errors**
<a name="rules-strict-mode-fails-if-risky-tests"></a>
 * A **[Risky test](#rules-risky-tests)**

<a name="rules-risky-tests"></a>
### Risky tests
Following behavior must turn test into Risky test
<a name="rules-risky-tests-output"></a>
 * A **test that have an output**
   * Usually it's the result of forgotten debug statements
<a name="rules-risky-tests-manipulate-globals"></a>
 * A **test that manipulates globals**
   * Using globals create unexpected behavior
   * Except for 0.01% of tests, we now have enought php library to avoid using globals
<a name="rules-risky-tests-test-nothing"></a>
 * A **test that do not test anything**
   * Do not allow unnecessary testing to pollute the test class
   * Test should probably be completed

<a name="rules-tests-isolation"></a>
### Tests isolation
A test must not impact another (and so a test must not expect that another has been executed)
<a name="rules-tests-isolation-globals"></a>
 * **Globals must be backuped and restored between each test**
<a name="rules-tests-isolation-static-class-member"></a>
 * **Static class member must be backuped and restored between each test**
<a name="rules-tests-isolation-different-process"></a>
 * *Test should be launched in different process*

<a name="rules-real-coverage"></a>
### Real coverage
<a name="rules-real-coverage-overflow"></a>
 * **No coverage overflow**
   
   A test for a class `A` must not impact coverage or C.R.A.P. score of an another class (including class `A` dependencies)

<a name="rules-real-coverage-risky-tests"></a>
 * **[Risky test](#rules-risky-tests) must not count in coverage or C.R.A.P. score**
 
<a name="rules-test-documentation"></a>
### Test documentation
<a name="rules-test-documentation-tested-class-description"></a>
 * *A test class should describe which class it tests*
<a name="rules-test-documentation-tested-class-dependencies-description"></a>
 * *A test class should describe what are the tested class dependencies used during tests*

### Tests root directory
**Behat** folder : `features/`

**Phpunit** folder : `tests/`

#### Technical tests
 * **Unit level** : `tests/Technical/Unit/`
 * **Integration level** : `tests/Technical/Integration/`

#### Functional tests
 * With **Phpunit** : `tests/Functional/`
 * With **Behat**
   
  * **Context** root directory : `features/bootstrap/` 
   
  * **Features** root directory : `features/SOMETHING/` (`SOMETHING` could be `methods` for an API project for instance)
   
### Tests order
 * To have the smaller execution time, run tests from faster to slower.
 * Order to run tests : 
   * Technical tests
      
     1 - **Unit level** 

     2 -  **Integration level** : Slower than previous ones
   * Functional tests
   
     No need to run functional tests if technical ones have failed
      
     3 - With **Phpunit**

     4 - With **Behat** : Slower than previous ones

### Base namespace

*See [Composer config example](example-composer-configuration-example)*

#### Technical tests
 * **Unit level** : `Technical\Unit\VendorNamespace\ProjectNamespace`
 * **Integration level** : `Technical\Integration\VendorNamespace\ProjectNamespace`

#### Functional tests
 * With **Phpunit** : `Functional\VendorNamespace\ProjectNamespace`
 * With **Behat**

   **Context** base namespace : `Functional\VendorNamespace\ProjectNamespace\BehatContext`
   
## Example
Let's say we have a class called `ExampleHelper` with the following namespace `VendorNamespace\ProjectNamespace\Helper\ExampleHelper`

A test for ExampleHelper class must have one of the following path and namespace:

 * Technical Unit test
    * namespace `Technical\Unit\VendorNamespace\ProjectNamespace\Helper`
    * path `tests/Technical/Unit/Helper/ExampleHelperTest.php`
 * Technical Integration test
    * namespace `Technical\Integration\VendorNamespace\ProjectNamespace\Helper`
    * path `tests/Technical/Integration/Helper/ExampleHelperTest.php`
 * Functional test - Phpunit only
    * namespace `Functional\VendorNamespace\ProjectNamespace\Helper`
    * path `tests/Functional/Helper/ExampleHelperTest.php`
 
 Or
    * namespace `Functional\VendorNamespace\ProjectNamespace`
    * path `tests/Functional/*TestedFunctionalityName*Test.php`


A Behat context must have the following namespace and path:
 * Path `features/bootstrap/MyContext.php`
 * Namespace `Functional\VendorNamespace\ProjectNamespace\BehatContext`
 
<a name="example-phpunit-implementation"></a>
### PhpUnit implementation
See [there](./TestStrategy/PHPUNIT_IMPLEMENTATION.md)

<a name="example-composer-implementation"></a>
### Composer implementation
```json
  "autoload-dev": {
    "psr-4": {
      "Technical\\Unit\\VendorNamespace\\ProjectNamespace\\": "tests/Technical/Unit",
      "Technical\\Integration\\VendorNamespace\\ProjectNamespace\\": "tests/Technical/Integration",
      "Functional\\VendorNamespace\\ProjectNamespace\\": "tests/Functional",
      "Functional\\VendorNamespace\\ProjectNamespace\\BehatContext\\": "features/bootstrap"
    }
  }
```
