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

Likewise, testing multiple SUTs under a single test fixture violates the [Single Responsibility Principle (SRP)](//en.wikipedia.org/wiki/Single_responsibility_principle)).
   

### One file Per Test Fixture
   
    
   - **Consistent Naming**
   - 3 part naming
   - or Should naming
   - **Comment your Code**
   - Use comments to provide higher level insight to the test
   - **Code is a Burdon**
   - 16 lines or less
   - **Consistent Format**
   - Arrange, Act, Assert
   - **One Assertion per Test**
   - breaks the SRP
   - **DAMP**
   - Descriptive and Meaningful Phrases
   - **Favour violating the DRY principle over violating Law of Demeter**
   - Shouldn't violate the Principle of Least Knowledge (POLA) even if it means violating the DRY principle
   - **Clean Asserts**
   - Assert.Equal(expected, result)
   - **new is Glue**
   - breaks the DIP
   - **Setup and Teardown with the Test**
   - **Project postfix for each type of Test**
   - .UnitTests, .IntegrationTests, .AcceptanceTests
   - **Cyclomatic Complexity**
   - of 1
   - **Favour Composition over Inheritence**
   - don't use inhertience in test fixture classes

## Unit Tests

- **FIRST (Fast, Isolated, Repeatable, Self-verifying, Timely)**

## Integration Tests

Coming soon

## Acceptance Tests

Coming soon
