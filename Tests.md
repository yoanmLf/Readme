## Tests

**Behat** *test folder : `features`*

**Phpunit** *test folder : `tests`*

### Technical tests
**Contributor/code point of view**

*Will change each time code is updated*

Test scope is a public php `class` method

 * **Unit level** (PhpUnit)

*Directory : `tests/Technical/Unit/`*

*Base namespace : `Technical\Unit\VendorNamespace\ProjectNamespace`*

**Any `object` used in the tested class (aka dependency, could be contructor argument or an instanciation inside the class) must be mocked** using **Prophecy**, if "not possible", the test must be moved under Integration level. 

*Most of tests should be done at Unit level as it's the faster one (execution time point of view), so issues are found earlier.*

 * **Integration level** (PhpUnit)

*Directory : `tests/Technical/Integration/`*

*Base namespace : `Technical\Integration\VendorNamespace\ProjectNamespace`*

*Launched after Unit level tests*

Dependencies *could* be mocked but it's not mandatory.

Put here all tests that test :

 * the behavior of class with an another one
 * a class that use a final class as dependency
 * a method that internally instanciate other class
 * ...
 * a method that have dependencies not mocked for any other reasons

### Functional tests
**End-user point of view**

*Should not change for minor release or hotfix*

Test scope is the "public API" of this repository (testing api payloads or website generated html pages for instance).

 * With **Phpunit**

*Directory : `features/bootstrap/`*

*Base namespace : `Functional\VendorNamespace\ProjectNamespace`*

*Launched after Technical Integration tests*

Test could use a slice of repository source code (to ensure a functionality for instance but without taking in account a "upper level" of code)

 * With **Behat**

*Behat context directory : `features/bootstrap/`*

*Behat context base namespace : `Functional\VendorNamespace\ProjectNamespace\BehatContext`*

*Behat features directory : `features/`*

*Launched after Phpunit Functional tests*

Tests will use the complete repository source code and will perform tests to cover production end-user actions

### Example
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
