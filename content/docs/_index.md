---
title: "AL Guidelines"
linkTitle: Docs
weight: 20
description: >
  Patterns and Best Practices for AL Development
---

## Business Central Design Patterns & Best Practices

This site is meant to house some of the community's knowledge about Microsoft Dynamics 365 Business Central AL Development best practices, particularly around hosting Design Patterns.

### What are Design Patterns?

To quote the original NAV Team blog post about the NAV Design Patterns:

> A team of people interested in NAV application design has come together to work on naming and surfacing design solutions to common NAV business needs. When those solutions are generic enough to be applied in various places of the NAV application, with slight variations on implementation but mainly respecting the same base concepts, we can describe them as NAV design patterns.

A design pattern is a repeatable template of how to solve a common development challenge.

### What are Development Best Practices?

They are a set of standardized, proven techniques and methodologies designed to enhance the efficiency, quality, maintainability, and scalability of AL development projects. Below are key categories and examples of these best practices:

#### 1. Planning
- **Define Clear Requirements**: Collaborate with Business Central functional consultants and stakeholders to document requirements in a detailed and unambiguous manner.
- **Create a Roadmap**: Establish timelines and milestones for development.

#### 2. Designing 
- **Adopt Design Patterns**: Use AL design patterns where applicable.
- **Plan for Scalability**: Design systems to handle increased load with minimal changes.
- **Focus on Modularity**: Break down the application into smaller, manageable, and reusable components.

#### 3. Development
- **Write Clean Code**: Follow coding standards like consistent naming conventions and proper indentation.
- **Version Control**: Use tools like Git for source code management and collaboration.
- **Iterative Development**: Use methodologies like Agile or Scrum for incremental progress.

#### 4. Testing
- **Unit Testing**: Validate individual units or components for correctness.
- **Integration Testing**: Ensure that components work together as expected.
- **Continuous Testing**: Use automated testing frameworks to catch bugs early and often.
- **Test-Driven Development (TDD)**: Write tests before coding to guide development.

#### 5. Communication
- **Regular Standups**: Use daily standups to ensure alignment and address blockers.
- **Code Reviews**: Peer reviews help identify potential issues and share knowledge.
- **Documentation**: Maintain clear and concise documentation for code, APIs, and user guides.
- **Tooling**: Use platforms like Auzre DevOps or JIRA for effective communication and task tracking.

#### 6. Deployment
- **CI/CD Pipelines**: Implement Continuous Integration and Continuous Deployment to streamline delivery.
- **Environment Parity**: Ensure development, staging, and production environments are consistent.
- **Rollback Strategies**: Plan for quick rollbacks in case of deployment failures.

#### 7. Post-Deployment
- **Gather User Feedback**: Incorporate user feedback for iterative improvements.
- **Maintenance and Updates**: Regularly update software to fix bugs, address security issues, and improve features.


### History of "NAV Design Patterns"

In 2013, Microsoft NAV Dev Team and prominent members of the community collaborated on a Community and Microsoft collection of commonly needed / used Design Patterns.

For some history on this, waldo's posts [Code is Poetry](https://www.waldo.be/2013/06/14/code-is-poetry/) and [Design is Philosophy](https://www.waldo.be/2013/08/28/design-is-philosophy-2/) are a great read.


#### Behind This Project

This project is a Microsoft Business Central Community initiative with support from the Microsoft Business Central team.  The founding community members are
* waldo ([Twitter](https://twitter.com/waldo1001), [Blog](https://www.waldo.be), [GitHub](https://github.com/waldo1001))
* Arend-Jan Kauffmann ([Twitter](https://twitter.com/ajkauffmann), [Blog](https://www.kauffmann.nl/), [GitHub](https://github.com/ajkauffmann))
* Henrik Helgesen ([Twitter](https://twitter.com/TheDoubleH), [Blog](https://thedoubleh.dev/), [GitHub](https://github.com/thedoubleh))
* Jeremy Vyska ([Twitter](https://twitter.com/JeremyVyska),[Blog](https://jeremy.vyska.info/articles), [GitHub](https://github.com/JeremyVyska))

#### Contributing

To find out more about contributing, read up here:
[Contributing](/docs/contributing/)
