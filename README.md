# .NET Testing Practice Guide

*Opinionated .NET Testing practice guide by [@devdigital](//twitter.com/devdigital)*

## Table of Contents

   1. [General Principles](#general-principles)
   2. [Unit Tests](#unit-tests)
   3. [Integration Tests](#integration-tests)
   4. [Acceptance Tests](#acceptance-tests)

## General Principles

### Test Code is Production Code
    Your test code should receive the same love and attention as your production code.

*Why?*

Your tests have to be maintained just as much as your production code. Developers spend significantly more time reading code than they do writing. The easier it is to read existing code, the easier it is to maintain. Additionally, less time can be spent reading code, and more time can be spent writing new code. 

To make your test code maintainable, apply the same principles of cleanliness to your test code that you do to your production code. The includes following consistent style rules (such as those provided by [StyleCop](//stylecop.codeplex.com)).

**Avoid**
```csharp   
using Xunit;
using MyLibrary;

public class tsts
{
   [Fact]
   public void test()
   {
      PasswordPolicy pp = new PasswordPolicy(6);
      Assert.False(pp.IsValid("asd"));
   }
}
```

**Recommended**
```csharp
// ---------------------------------------------------------------------
// <copyright file="PasswordPolicyTests.cs" company="XXX Company">
//   Copyright (c) XXX Company. All rights reserved.
// </copyright>
// <summary>
//   Defines the PasswordPolicy tests.
// </summary>
// ---------------------------------------------------------------------

namespace MyApplication.Security.Domain.UnitTests
{
   using MyLibrary;

   using Xunit;

   /// <summary>
   /// Password policy tests.
   /// </summary>
   public class PasswordPolicyTests
   {
      /// <summary>
      /// A password that violates the minimum length requirements 
      /// should fail the password policy.
      /// </summary>
      [Fact]
      public void MinimumLengthViolatedShouldReturnFalse()
      {
         var passwordPolicy = new PasswordPolicy(minimumLength: 6);
         var result = passwordPolicy.IsValid("asd");
         Assert.False(result);
      }
   }
}
```

### One Test Fixture Per System Under Test (SUT)
    Group related tests for a System Under Test (SUT) under a single test fixture.
   
*Why?*

Spreading tests for a single System Under Test (SUT) over many test fixtures makes it harder to locate tests for the purposes of maintainance, or to see if tests already exist for a SUT when developing new tests.

Likewise, testing multiple SUTs under a single test fixture violates the [Single Responsibility Principle (SRP)](//en.wikipedia.org/wiki/Single_responsibility_principle).
   

### One file Per Test Fixture
    Use one file per test fixture.
    
*Why?*

Just as StyleCop rule [SA1402](//www.stylecop.com/docs/SA1402.html) promotes the standard of one class per C# file, the same should be applied to test fixture classes.

If you have more than one test fixture per file, then it takes a greater amount of time to reason about where a set of tests for a particular SUT will be located.

### Group Test Types Per Project
    Create a test project for each type of test (unit, integration, acceptance)

*Why?*

If you group tests based on their type into separate project, then your likely to have different structures and references per test type. For example, an integration test may need to write to a database, in which case you may have references to an ORM or a folder for database helper classes. 

Your acceptance tests may be written as specs, so would require a different folder name, and may require a reference to an acceptance testing framework such as [SpecFlow](//www.specflow.org/). If a single project contained different types of tests, then you would have to carry those dependencies for all types of tests along with a single project.
    
### Project Postfix Based On Test Type
    Use an appropriate postfix for your test project names based on the type of tests they contain.

*Why?*

If you use a postfix such as *.UnitTests*, *.IntegrationTests*, or *.AcceptanceTests* based on the type of tests that project contains, then it's easier to locate a particular type of test. 

This is important for both developers and the build server, where for example you can configure your build server to only run tests in *.IntegrationTests* and *.AcceptanceTests* projects overnight, where as run *.UnitTests* both on commit and overnight.

### Use Consistent Naming
    Be consistent in naming your test methods and fixtures. Define and follow a standard.

*Why?*

It's important that your test fixtures are named after the SUT that they are testing. For example, if your System Under Test is PasswordPolicy, then it follows that an appropriate test fixture name would be PasswordPolicyTests. This makes it easy for a developer to reason about where to locate tests for a particular SUT.

You should define and adopt a standard naming convention for your test fixture classes and your test methods. An appropriate standard for test fixtures is:

    [SystemUnderTest]Tests

**Avoid**
```csharp
public class Tests
{
   ...
}
```

**Recommended**

```csharp
public class PasswordPolicyTests
{
   ...
}
```

Likewise, the names of your test methods should describe the test they are performing. This is important for a developer to know what the test is attempting to do, and when a test fails within a build environment, then the developer can reason about what change may have caused the regression based on the test name.

There are two popular standards for good test names:
   
#### 3 part naming

Using a three part name for the test tells you what you are testing, the condition you are testing under, and the expected behaviour:

```csharp
public void UnitOfWork_StateUnderTest_ExpectedBehavior()
{
   ...
}
```

Here, UnitOfWork is the method on the SUT that you are testing, for example a PasswordPolicy type may have an IsValid method, so a 3 part unit test name may look like:

```csharp
public void IsValid_PasswordBelowMinimum_ReturnsFalse()
{
   ...
}
```

An alternative description you may see is subject, scenario, and result, but it's describing the same concept:

```csharp
public void Subject_Scenario_Result()
{
   ...
}
```

#### Should naming

The issue with 3 part naming is two fold - firstly and most importantly, it may not be easy to come up with a name for each part for particular types of tests. 

For example, you may have a unit test that ensures that your SUT is assignable from a known interface. In this case, coming up with a name for the UnitOfWork part could be difficult. It may be that the UnitOfWork name becomes the SUT itself, in which case there's a duplication that the test name already describes something covered by the test fixture name.

Secondly, you may not be comfortable using underscores within your method names. If you're using a refactoring tool such as ReSharper, you'll likely to get a warning about the method name not matching ReSharper's default configuration.

An alternative approach, and one that should be favoured if you are unclear about your naming strategy, is to use 'should' naming. In this approach, you use a single long descriptive name for your test, using 'should' in the description to define the expected behaviour:

```csharp
public void WhenPasswordIsBelowMinimumIsValidShouldReturnFalse()
{
   ...
}
```

This naming style can become difficult to read if the description is too long, so try to describe the work the test is doing in a clear but succinct fashion. 

### Favour Good Test Names over Assert Messages 
    Descriptive test names are more valuable than assert messages.
   
TODO

### Consistent Format
    Be consistent in the format of your tests.

#### Arrange, Act, Assert

TODO

#### Four Phase Test

TODO

### Clean Asserts
    Your test's expected behviour should be obvious.
   
Assert.Equal(expected, result)

TODO

### Comment your Code
    Use comments to provide higher level insight to the test.
    
### Code is a Burden
    Keep your tests small yet readable.
    
*Why?*

Every line of code you write is a line of code that needs to be read, understood, and maintained. You should attempt to minimise the number of lines of code per test, but not at the expense of readability.

A good approach is to set yourself a theoritical maximum, for example **16 lines or less**. If your test becomes longer than your maximum, then it's a sign that your test is either has a complex arrangment phase that could be simplified, or is violating the single assert per test rule.

TODO
   
### Follow the DAMP (Descriptive and Meaningful Phrases) Principal 

TODO   

### One Assertion per Test
    Each test should test one thing, and test it well.

- having more than one assert per test breaks the SRP
    
TODO

### Cyclomatic Complexity
    Your tests should have a cycolmatic complexity of one.

TODO

### 'new' is Glue
    Avoid the use of 'new' within your tests.

*Why?*

Using *new* within your tests violates the [Dependency Inversion Principle (DIP)](//en.wikipedia.org/wiki/Dependency_inversion_principle).
   
TODO

### Setup and Teardown with the Test
    Put all setup and teardown required by the test with the test itself.

TODO

### Favour violating the DRY principle over violating Law of Demeter
    You shouldn't violate the Principle of Least Knowledge (POLA) even if it means violating the DRY principle.

TODO

### Favour Composition over Inheritence
    Don't use inhertience in test fixture classes.
    
TODO

## Unit Tests

- **FIRST (Fast, Isolated, Repeatable, Self-verifying, Timely)**

## Integration Tests

Coming soon

## Acceptance Tests

Coming soon
