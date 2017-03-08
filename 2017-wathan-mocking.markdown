Alternative Facts You've Been Told About Testing
------------------------------------------------

_Adam Wathan, Laracon Online 2017_

- **"Almost all of your tests should be unit tests."**
- **"Your tests should never touch the database."**
- **"If you can't test your code in isolation, it's poorly designed."**

Are these true?

### An Example... (according to the lies you might have been told)

[Products Controller with injected dependencies, using CommandBus, Repository]

[A look at the test for ProductsController, completely using mocks]

### How Did We Do? (Measuring up to 'best practices')

We passed all the alternative fact tests.  :)  But something isn't right.

And the alternative fact folks would say "YOU'RE STILL DOING IT WRONG!"

### Let's Take Another Look...

TDD is all about the red-green refactoring loop.

- Write a failing test
- Get it to pass
- Refactor at will

_If refactoring is a fundamental part of TDD, and isolated testing gets in the way of refactoring, then isolated unit testing is incompatible with TDD._

### Diagram Time

[Layout of program process and test]

### Let's Try This...

**"Isolated Tests are a Scam."** - Adam Wathan, 2017

### What About External Services?

How do you test making payments with Stripe, for example?

### Don't Worry About Isolation

Instead, introduce boundaries _when they solve a real problem._


