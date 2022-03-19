**SENG 438 - Software Testing, Reliability, and Quality**

**Lab. Report \#4 – Mutation Testing and Web app testing**

| Group \#:       | 34  |
|-----------------|---|
| Student Names:  |  Mathew Pelletier |
|                 |  Jonas Wong |
|                 | Mason Harris  |
|                 |  Ryan Huynh|

# Introduction
In this lab we used the test suite developed over the two previous labs to perform mutation testing on the classes Range and DataUtilies.
This was done with the PITclipse plugin for Eclipse to generate mutations within the two aforementioned classes. We then developed additional tests in order to increase the mutation score where possible. 

Additionally, we used Selenium IDE to test a few web page GUIs. We used Selenium to automate test cases for a few different functionalities of the system. Any defects found using these tests were then documented.

# Analysis of 10 Mutants of the Range class 

Range() - Change conditional (lower > upper) to (lower >= upper).
* This would mean an illegal arg exception would be thrown when the lower bound = upper bound
* This is killed by any test that uses a zero width range of which there are a few

getLowerBound() - Replaced return with 0.0d
* This returns 0 regardless of the range
* Killed by any test where the range is expected to be non-zero

getLength() - Replaced subtraction with addition
* Results in upper + lower meaning the length will be incorrect for any case with a non-zero lower bound
* Killed by any test that uses a range with a non-zero lower

getCentralValue - Replace division with multiplication
* Will lead to incorrect central values when the ranges are asymmetrical
* Killed by the test centralValuePositiveRange()

contains() - negated conditional
* Value >=this.lower to value<this.lower
* Means any value that is beneath the lower and upper bound will return true, indicating it is in range.
* Killed by any test where the value tested is below the lower bound

combine() - negated conditional
* (range1 == null) to (range1 != null)
* Means if range 1 is non-null then it will automatically return range2
* Killed by any test where range 1 is not null and not equivalent to range 2 as it will expect at a bare minimum a range containing range1

combineIgnoringNan() - changed return value to NULL
* This causes the method to return null even if its successfully combines two ranges
* This is killed by any test where both range1 and range2 are not null and we assert that the result is not null.

intersects() - Incremented (a++) double field lower
* This causes the field for the lower variable to be permanently increased whenever intersects is called.
* This survived initial testing, will be killed by calling the method then asserting the original range is unaltered

constrain() - negated value
* This negates value in value > this.upper
* This means if the upper was say 10 and value was -15 then this would evaluate to true
* This would then return the upper bound instead of the expected lower bound -10
* Killed by testConstrainValueBelowRange()

expandToInclude() - negated conditional
* This means the conditional is (range!=null) [302]
* So if the range was not null it would return a new range [value,value] regardless of if the original range contained the value or not.
* Likewise if the range was null then the following conditionals will fail and cause errors as well
* Killed by most tests, such as: testExpandToIncludeValueInRange()


# Report all the statistics and the mutation score for each test class

The PIT mitation testing results can be found in the following HTML summary page: [Old Tests](https://htmlpreview.github.io/?https://github.com/seng438-winter-2022/seng438-a4-R41Ryan/blob/main/MutationTests(OLD)/org.jfree.data/index.html)

Likewise the results for the updated test suite are found here: [New Tests](https://htmlpreview.github.io/?https://github.com/seng438-winter-2022/seng438-a4-R41Ryan/blob/main/MutationTests(New)/org.jfree.data/index.html)


# Analysis drawn on the effectiveness of each of the test classes

# A discussion on the effect of equivalent mutants on mutation score accuracy

# A discussion of what could have been done to improve the mutation score of the test suites

# Why do we need mutation testing? Advantages and disadvantages of mutation testing

# Explain your SELENIUM test case design process
Our design process for the test cases was fairly simple. We each chose two different functionalities that we wrote test cases for. During the test writing process we discovered that some functionalities only required a single test case to fully test, so we decided to expand on our existing tests of other functionalities. We also decided to test all three websites to expand our test suite.
# Explain the use of assertions and checkpoints
All of our tests use assertions to ensure that the website is displaying the correct information. All of our tests used the assertText assertion, as it was the simplest way to ensure that the website was displaying correctly. We did not use checkpoints in our test cases.
# how did you test each functionality with different test data

# Discuss advantages and disadvantages of Selenium vs. Sikulix
We used the selenium IDE in order to do most of our testing. The selenium IDE allows us to do the testing directly on the web browser which made the testing process much easier and assertions were done in this way too. With Sikulix, tests are made through a more programmatic fashion which makes it slightly less efficient and more difficult to make tests. Though Sikulix allows for taking screenshots to create the tests, Selenium is still faster to use as it directly listens to clicks and button presses to make the tests.

# How the team work/effort was divided and managed
For the mutation testing two of us wrote tests for DataUtilities and two of us wrote tests for Range. For the GUI testing, we each chose two functionalities to test, however some of the functionalities were fairly easy to test so the people with those functionalities wrote additional tests for other functionalities to improve our test suite.

# Difficulties encountered, challenges overcome, and lessons learned
For Selenium testing, the website will sometimes change as it is updated, breaking the previous tests that were created which proved to be slightly problematic. We also learned that Selenium is extremely easy to use and can quickly create test cases without much headache for a GUI.

# Comments/feedback on the lab itself
