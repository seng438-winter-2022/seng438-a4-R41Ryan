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



# Analysis drawn on the effectiveness of each of the test classes

# A discussion on the effect of equivalent mutants on mutation score accuracy

# A discussion of what could have been done to improve the mutation score of the test suites

# Why do we need mutation testing? Advantages and disadvantages of mutation testing

# Explain your SELENUIM test case design process

# Explain the use of assertions and checkpoints

# how did you test each functionaity with different test data

# Discuss advantages and disadvantages of Selenium vs. Sikulix

# How the team work/effort was divided and managed


# Difficulties encountered, challenges overcome, and lessons learned

# Comments/feedback on the lab itself
