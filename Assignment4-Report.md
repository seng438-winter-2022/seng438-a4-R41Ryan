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

The PIT mitation testing results can be found in the following HTML summary page: [Old Tests](https://htmlpreview.github.io/?https://github.com/seng438-winter-2022/seng438-a4-R41Ryan/blob/main/MutationTests(Old)/org.jfree.data/index.html)

Likewise the results for the updated test suite are found here: [New Tests](https://htmlpreview.github.io/?https://github.com/seng438-winter-2022/seng438-a4-R41Ryan/blob/main/MutationTests(New)/org.jfree.data/index.html)

These are uploaded to the repository in the folders MutationTests(Old) and MutationTests(New) respectively
Overall our mutations score went from 84% to 94% in range and 93% to 100& in DataUtilities, increases beyond this were slow due to many equivalent mutations.

# Analysis drawn on the effectiveness of each of the test classes

Overall I would say both the Range and DataUtilities tests were quite effective in mutation testing. Most of the remaining mutations that were not killed were either very obscure or equivalent mutations. Both of the original test suites covered most of the mutations and most additions were need to account for behaviour we did not anticipate such as incrementing values (a++) resulting in the class member variables being changed even in methods not meant to alter them.

# A discussion on the effect of equivalent mutants on mutation score accuracy

Equivalent mutants tend to decrease mutation score as they cannot be properly killed, this means that the final test score tends to underrepresent the actual score. Several of the mutants, such as those that increment a local variable that is only ever used once in the function or similarly mutates on it's final use. LIkewise, mutants that alter the conditions if statements (ex. > to >=) are often handled reduntantly by the function, for example the constrain method calls the contain method to determine if the value being used is in the range or not then comparisons to see if it is > or >= to the upper bound are redundant as the >= was already covered by the contains call.

As for detecting equivalent mutants, Although there are a limited amount of mutation operations that can be done, which one would result in an equivalent mutation would depend on what the mutation operation was and what the surrounding context was. In order to find an equivalent mutant, you could apply the following steps:
* Perform the mutation operation
* Check to see if the mutant survived
* If it survived, find where the mutation was made and look at the context (e.g. Is it a conditional in a for loop? A while loop? Or was it done to a single line of code?)
* Once the mutation is found and the context is identified, look into a pre-coded operation map to determine if the original transformed into the mutation in the context is classified as an equivalent mutation (e.g. The mutation was made in a for loop conditional that increments by +1, where the original was x < 10 and the mutation was x != 10. This would result in an equivalent mutation.)

This assumes that the code of the test case is syntactically correct, so the context is more easily able to be identified.

The advantage of this algorithm is that, if implemented properly, it is very thorough and will detect almost all equivalent mutations.

The major disadvantage of this algorithm is that it is very complex as it is highly dependent on a pre-coded operation map which would require humans that are able to identify all possible mappings of (original LOC, mutation LOC, context) → (Is it equivalent code?).

Below are a few examples of manually finding equivalent mutants. The process used to manually find these equivalent mutants was by randomly performing random mutations, and seeing if the mutant is logically the same as the original in the context of the code surrounding it.
* calculateColumnTotal() method: replace row < rowCount with row != rowCount (line 155)
  * This conditional was in the context of a for loop that was incremented by 1. In this context, the moment row was no longer less than colCount, it would also no longer be not equal to rowCount.
* calculateRowTotal() method: replace col < colCount with col != colCount (line 206)
  * this conditional was in the context of a for loop that was incremented by 1. In this context, the moment col was no longer less than colCount, it would also no longer be not equal to rowCount.
* getCumulativePercentages() methods: replace double total = 0.0 with double total (line 264)
  * Since, in java, all data is initialized to 0 by default, simply declaring total as a double is logically the same as initializing total as 0.0

# A discussion of what could have been done to improve the mutation score of the test suites
Our strategy for increasing the mutation scores of DataUtilities and Range classes was to use the PIT Mutation Summary of the original test suite and design new test cases that would pass as JUnit tests but would specifically kill the mutation that survived in the original test suites.

# Why do we need mutation testing? Advantages and disadvantages of mutation testing
The advantage of mutation testing is that it can help with catching small, obscure, or otherwise subtle bugs and faults that would've gone unnoticed; Bugs such as null inputs, boundary values, special values, etc.

The disadvantage of mutation testing is that, in order to be effective, it must be very thorough, which means that it would take a lot of time to perform. It takes so much time that, when used practically for a large project, it is required to do it automatically with a tool like Pitclipse.

# Explain your SELENIUM test case design process
Our design process for the test cases was fairly simple. We each chose two different functionalities that we wrote test cases for. During the test writing process we discovered that some functionalities only required a single test case to fully test, so we decided to expand on our existing tests of other functionalities. We also decided to test all three websites to expand our test suite.
# Explain the use of assertions and checkpoints
All of our tests use assertions to ensure that the website is displaying the correct information. All of our tests used the assertText assertion, as it was the simplest way to ensure that the website was displaying correctly. We did not use checkpoints in our test cases.

# Discuss advantages and disadvantages of Selenium vs. Sikulix
We used the selenium IDE in order to do most of our testing. The selenium IDE allows us to do the testing directly on the web browser which made the testing process much easier and assertions were done in this way too. With Sikulix, tests are made through a more programmatic fashion which makes it slightly less efficient and more difficult to make tests. Though Sikulix allows for taking screenshots to create the tests, Selenium is still faster to use as it directly listens to clicks and button presses to make the tests.

# How the team work/effort was divided and managed
For the mutation testing two of us wrote tests for DataUtilities and two of us wrote tests for Range. For the GUI testing, we each chose two functionalities to test, however some of the functionalities were fairly easy to test so the people with those functionalities wrote additional tests for other functionalities to improve our test suite.

# Difficulties encountered, challenges overcome, and lessons learned
For Selenium testing, the website will sometimes change as it is updated, breaking the previous tests that were created which proved to be slightly problematic. We also learned that Selenium is extremely easy to use and can quickly create test cases without much headache for a GUI.

# Comments/feedback on the lab itself

One major issue is that in the lab document, the two different methods to install PIT install two different versions of PIT, which each have a different number of mutators included.
