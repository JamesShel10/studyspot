# StudySpot

**Stop searching. Start studying.**

StudySpot is a study-resource discovery and organization platform. It helps students find trustworthy learning materials, save useful resources, organize them by subject, and return to focused study faster.

This repository contains the initial product documentation for the StudySpot project:

- [Project Charter](docs/project-charter.md)
- [Requirements Specification](docs/requirements-specification.md)
- [Acceptance Criteria](docs/acceptance-criteria.md)
- [Database Design](docs/database-design.md)

## Product direction

StudySpot is planned as a web application that combines resource discovery with personal study organization. The first release focuses on:

1. Account creation and secure sign-in.
2. Searching and filtering study resources.
3. Saving resources into subject-based collections.
4. Tracking study progress and resource status.
5. Reporting low-quality or inappropriate content.

## Documentation status

The documents in this repository define the baseline for implementation in Visual Studio using an AI-assisted development workflow. They are intentionally technology-neutral where implementation decisions can be made during development.

## Suggested implementation stack

The project can be implemented with:

- Visual Studio
- ASP.NET Core Web API and/or ASP.NET Core MVC
- Entity Framework Core
- PostgreSQL or SQL Server
- A modern browser-based frontend
- Automated unit and integration tests

The database design is relational and can be adapted to either PostgreSQL or SQL Server with minor syntax changes.

## Definition of success for the first release

The first release is successful when a registered student can search for a relevant resource, understand why it matches, save it to a collection, mark progress, and return to it later without losing their place.
