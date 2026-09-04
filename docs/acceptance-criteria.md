# StudySpot Acceptance Criteria

**Version:** 1.0  
**Date:** 2026-09-04

The following scenarios define the minimum behavior required for the first release. Unless stated otherwise, a scenario is accepted only when the expected result is visible to the user and persisted after refresh or a new sign-in.

## AC-01: Register a new student

**Given** a visitor is on the registration page  
**When** they submit a unique valid email address, display name, and password  
**Then** an account is created, the password is not stored in plain text, and the visitor receives a clear success or sign-in prompt.

## AC-02: Reject invalid registration

**Given** a visitor submits missing, invalid, or already registered data  
**When** registration is submitted  
**Then** the account is not created and field-level error messages explain how to correct the input.

## AC-03: Sign in and sign out

**Given** a registered student has valid credentials  
**When** they sign in  
**Then** protected navigation becomes available; when they sign out, protected pages require authentication again.

## AC-04: Search resources

**Given** the catalog contains resources matching a search term  
**When** a student enters the term and submits the search  
**Then** matching resources are displayed with title, type, subject, difficulty, rating, source domain, and availability status.

## AC-05: Filter and sort results

**Given** search results are displayed  
**When** a student selects subject, type, difficulty, language, rating, or sort options  
**Then** the results update to match every selected filter and the selected sort order is shown.

## AC-06: View resource details

**Given** a student selects a search result  
**When** the resource details page opens  
**Then** it shows the title, description, external URL, subject, type, difficulty, tags, creator, rating summary, and current availability status.

## AC-07: Save and unsave a resource

**Given** a signed-in student views an active resource  
**When** they select Save  
**Then** the resource appears in their saved resources; selecting Save again or Unsave removes it without affecting the catalog resource.

## AC-08: Organize a saved resource

**Given** a student has saved a resource  
**When** they create a collection and add the resource  
**Then** the collection appears in their account and contains that resource; duplicate membership is not created.

## AC-09: Track study progress

**Given** a student has saved a resource  
**When** they set a valid status and progress percentage  
**Then** the status and percentage are displayed consistently in the saved-resources view and remain after sign-in again.

## AC-10: Submit a resource

**Given** an authorized contributor is signed in  
**When** they submit a complete resource with a valid URL and required metadata  
**Then** the resource is created with an owner and timestamps and is available according to the configured moderation workflow.

## AC-11: Prevent duplicate resource URLs

**Given** an active resource already uses a canonical URL  
**When** a contributor submits the same canonical URL  
**Then** the submission is rejected with a useful duplicate message and the existing resource remains unchanged.

## AC-12: Rate and review a resource

**Given** a signed-in student views a resource  
**When** they submit a one-to-five-star rating and optional review  
**Then** their feedback is displayed, their second submission updates the original feedback, and the aggregate rating is recalculated.

## AC-13: Report and moderate content

**Given** a student sees inappropriate, broken, or misleading content  
**When** they submit a report  
**Then** the report is recorded with reason, reporter, resource, and timestamp; a moderator can resolve or dismiss it and the action is audited.

## AC-14: Protect private data

**Given** Student A is signed in  
**When** Student A attempts to access Student B's private collections, progress, or profile data  
**Then** access is denied and no private data is revealed.

## AC-15: Responsive and accessible core workflow

**Given** a user opens StudySpot on a supported desktop or mobile viewport  
**When** they complete registration, search, save, and progress workflows using keyboard and standard browser controls  
**Then** controls have accessible names, validation is understandable, and no core action depends solely on a mouse or hover state.

## Release acceptance

The first release is accepted when:

- All Must requirements have passing acceptance evidence.
- No critical or high-severity defects remain open in registration, authentication, search, save, progress, authorization, or data persistence.
- The database migration can create the required schema in a clean environment.
- The application documentation explains how to run the solution and how to validate the core workflows.
