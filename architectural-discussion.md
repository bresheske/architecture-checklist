# Architectural Discussion
The purpose of this document is to identify a list of must-have, should-have, and could-have architectural features when building a new product.

_Note: This document is the opinion of the owner, and is always open to suggestions._

**TL;DR**: This page is more of a discussion as to the reasons why an item appears on the checklist. [The checklist itself is located here](./architectural-checklist.md).

## Engineering Workflow
As a development organization, even if your products make heavy profits, your products are _absolutely not_ your most valuable assets, your workforce is. If you rely on software for the success of your organization, then your developers are your most valuable asset. Your team likely has 2-3 top-quality developers and if you lost them, your product will come to an expensive stand-still for the foreseeable future.

Keeping developers happy is a tricky trade, but, as a developer myself, I find the most satisfaction when I am:
 - producing output (code & business value)
 - producing efficiently
 - producing with quality
 - producing with creative freedom

### **Requirement 1: New engineering workstations are deployed easiely and quickly.**
When brand-new enginners join a team and pressure is high to show they can preform well early, nothing is more demoralizing than an extreme, tedious, development environment set-up experience written as a confluence document. Furthermore, this doesn't allow the developer to produce any output.

Aside from the potential woes the developer might go though, from simply an architectural standpoint, if a workstation setup experience is rather tedious, this is a strong sign of long lists of dependencies. If a product is strongly dependent on a number of things, deployments are equally as difficult, moreso if we're targetting automated deployments.

**Suggestion:** Try to reduce external dependencies as much as possible.  If using a package manager, try to use packages freely available there. This way installing dependencies are easy with a single script. 

**Suggestion:** If external dependencies are your target, (for example, you're deploying a web app out to IIS hosting) then make sure you take the time to script this. Detect is IIS exists and your user has permission to it. If not, install it and/or set appropriate permissions.

### **Requirement 2: An engineer may develop independently, in an isolated environment.**
This point might be expected, but sometimes taken for granted. 

**Story Time:** I once worked on a large cloud-based AWS lambda application architected with the idea to _not support local development._ The architect's reason was simply: 

> When testing your application, you're not testing the actual application when running locally. You don't want to run the whole cloud locally. There's a new way to develop applications now.

Well, he wan't totally wrong. Localhost development of a cloud-targetted application truely won't be testing what is deployed out there, but that absolutely doesn't mean that localhost development has zero value. His architectural decision for development workflow was the following:
1. Develop some code.
1. Deploy it to your team AWS environment.
1. Cross your fingers that you didn't just overwrite someone else's code.
1. Cross your fingers that your code actually works.
1. Dig through cloudwatch logs (which is extremely slow I might add) to look for causes to bugs.
1. Rinse and repeat!

I was not particularly happy about this. Lots of others shared the same emotional state.

Developers want to produce efficiently and with quality. Local development gives them both, and an isolated environment gives them the time to take pride in their work before showing alpha bug-ridden code to the rest of their team. 

**Suggestion:** Simply pick a platform that doesn't differ too greatly from isolated development. In my opinion, FAAS (function-as-a-service) architectures are lacking in this category at the moment. The future might change that.

**Suggestion:** Development experience is your product's number one feature. If isolated environment development (localhost development) is not supported, be prepared to pay for that in every single feature that is developed from here on out.

### **Requirement 3: Engineering experiences a quick, accurate, and responsive feedback loop.**
Expanding on the story illustrated above, when developers finally were comfortable with the work they just accomplished for their feature, they could finally hand it off to quality assurance. However, getting to that point was extremely slow. Most of this time was spent sitting on our hands and digging through endless cloudwatch logs. It almost got to the point where it made sense to hire a team of log experts to dig through logs to find useful information for the development teams.

This experience exhibited a _massive_ feedback loop. By the time developers received feedback on their work, it was far too late. This often happened when the code was already pushed to production.

Again, developers _want_ to produce quality, and _want_ to take pride in their work. 

**Suggestion:** Ensure _automated_ quality is prioritized alongside of any feature. I recommend an exhaustive suite of unit and integration tests. However, you must also ensure to keep these tests executing quickly, otherwise they become a hindrance.

## Testability
No feature is complete with quick, reproduceable evidence that it's working as expected. If unit, integration, and full E2E testing is prioritized from day one, this effort will pay off in dividends. Inversely, if no quality automation is prioritized be prepared to pay for that exponentially. 

The benefits are extensive.
 - Unit and Integration test experiences provide developers with a way to execute their business logic quickly, and in an isolated environment. 
 - Test suites built from day one remove the need for week-long full regression cycles before a release is certified.
 - Test suites will ensure bugs are caught early, long before the code is deployed to production.
 - Less bugs reported from customers means happier customers and more revenue for the organization.
 - High-coverage test suites allows for a full CI/CD experience and makes releases much quicker.

 The costs are low
  - Each new feature developed will have to maintain the test suite.
  - If a feature requires significant refactoring, significant refactoring of the tests would be required as well.

 ### **Requirement 1: A development engineer reaches full confidence before any new code leaves their isolated environment.**
One thing I have learned over the course of my career, is that developers _hate_ repetition and not-automative efforts. Meaning, if an architect said each developer was in charge of quality testing before it left their hands, you can gaurentee the developer will have automated the tested effort. Furthermore these tests would be added to a suite of self-contained, consistent test cases. 

Using a suite of test cases also transforms a development workflow. Traditionally, developers would make a change, run everything locally, and click around to make sure it works. Now, with a large set of unit and integration tests, developers follow these steps:
1. Developer reads and understands story/feature.
1. Developer writes some unit and integration tests.
1. Developer watches the test fail. 
1. Developer develops the feature, and slowly starts to watch their tests pass.
1. Rinse and repeat until the whole feature is complete.

This is also considered _test driven development_, which is not required for the developer gaining full confidence in the quality of code before handing it off, but it sure helps build up a great, reuseable suite a test that the next individual is going to use as well.

**Suggestion:** From ground zero, unit and integration tests must be prioritized and are a must-have for any feature before it goes into production.
