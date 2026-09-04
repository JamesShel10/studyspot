# StudySpot Database Design

**Version:** 1.0  
**Date:** 2026-09-04  
**Database style:** Relational

## 1. Design goals

- Keep user-owned study data private and enforce ownership at the application boundary.
- Avoid duplicate resources and duplicate collection membership.
- Support fast catalog filtering and search.
- Preserve moderation and progress history needed for reliable user experience.
- Use stable keys and timestamps so records can evolve without breaking links.

## 2. Entity relationship overview

```text
User 1 ---- * UserSubject * ---- 1 Subject
User 1 ---- * Resource
Subject 1 ---- * Resource
Resource 1 ---- * ResourceTag * ---- 1 Tag
User 1 ---- * SavedResource * ---- 1 Resource
User 1 ---- * Collection
Collection 1 ---- * CollectionItem * ---- 1 Resource
User 1 ---- * StudyProgress * ---- 1 Resource
User 1 ---- * Review * ---- 1 Resource
User 1 ---- * Report * ---- 1 Resource
```

## 3. Tables

### users

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Stable user identifier |
| email | VARCHAR(320) | NOT NULL, UNIQUE | Login identifier |
| password_hash | VARCHAR(255) | NOT NULL | One-way password hash |
| display_name | VARCHAR(100) | NOT NULL | Public name |
| role | VARCHAR(20) | NOT NULL, default `Student` | Authorization role |
| profile_image_url | VARCHAR(500) | NULL | Optional profile image |
| created_at | TIMESTAMP | NOT NULL | Creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

### subjects

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Subject identifier |
| name | VARCHAR(100) | NOT NULL, UNIQUE | Display name |
| slug | VARCHAR(120) | NOT NULL, UNIQUE | URL/search-safe name |
| created_at | TIMESTAMP | NOT NULL | Creation time |

### user_subjects

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| user_id | UUID | PK/FK users.id | User preference |
| subject_id | UUID | PK/FK subjects.id | Preferred subject |

### resources

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Resource identifier |
| owner_id | UUID | FK users.id, NOT NULL | Submitting user |
| subject_id | UUID | FK subjects.id, NOT NULL | Primary subject |
| title | VARCHAR(200) | NOT NULL | Search/display title |
| description | TEXT | NOT NULL | Resource summary |
| canonical_url | VARCHAR(2000) | NOT NULL, UNIQUE among active records | Normalized external URL |
| resource_type | VARCHAR(30) | NOT NULL | Article, video, course, book, or other |
| difficulty | VARCHAR(20) | NOT NULL | Beginner, intermediate, or advanced |
| language | VARCHAR(20) | NOT NULL | Content language |
| status | VARCHAR(20) | NOT NULL, default `Pending` | Pending, active, hidden, or archived |
| availability_status | VARCHAR(20) | NOT NULL, default `Unknown` | Unknown, available, or unavailable |
| created_at | TIMESTAMP | NOT NULL | Creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

### tags

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Tag identifier |
| name | VARCHAR(50) | NOT NULL, UNIQUE | Tag label |

### resource_tags

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| resource_id | UUID | PK/FK resources.id | Tagged resource |
| tag_id | UUID | PK/FK tags.id | Applied tag |

### saved_resources

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| user_id | UUID | PK/FK users.id | Saving student |
| resource_id | UUID | PK/FK resources.id | Saved resource |
| saved_at | TIMESTAMP | NOT NULL | Save time |

### collections

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Collection identifier |
| owner_id | UUID | FK users.id, NOT NULL | Collection owner |
| name | VARCHAR(100) | NOT NULL | Collection name |
| description | VARCHAR(500) | NULL | Optional description |
| created_at | TIMESTAMP | NOT NULL | Creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

### collection_items

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| collection_id | UUID | PK/FK collections.id | Parent collection |
| resource_id | UUID | PK/FK resources.id | Included resource |
| added_at | TIMESTAMP | NOT NULL | Membership time |

### study_progress

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| user_id | UUID | PK/FK users.id | Student |
| resource_id | UUID | PK/FK resources.id | Tracked resource |
| study_status | VARCHAR(20) | NOT NULL | Planned, in progress, completed, or archived |
| progress_percent | SMALLINT | NOT NULL, 0-100 | Completion percentage |
| notes | VARCHAR(2000) | NULL | Personal study notes |
| updated_at | TIMESTAMP | NOT NULL | Last progress update |

### reviews

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Review identifier |
| user_id | UUID | FK users.id, NOT NULL | Reviewer |
| resource_id | UUID | FK resources.id, NOT NULL | Reviewed resource |
| rating | SMALLINT | NOT NULL, 1-5 | Star rating |
| review_text | VARCHAR(2000) | NULL | Optional written feedback |
| created_at | TIMESTAMP | NOT NULL | Creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

**Constraint:** `UNIQUE(user_id, resource_id)`.

### reports

| Column | Type | Constraints | Purpose |
|---|---|---|---|
| id | UUID | PK | Report identifier |
| reporter_id | UUID | FK users.id, NOT NULL | Reporting user |
| resource_id | UUID | FK resources.id, NOT NULL | Reported resource |
| reason | VARCHAR(30) | NOT NULL | Broken, misleading, inappropriate, or duplicate |
| details | VARCHAR(2000) | NULL | Additional context |
| status | VARCHAR(20) | NOT NULL, default `Open` | Open, assigned, resolved, or dismissed |
| assigned_to | UUID | FK users.id, NULL | Moderator handling report |
| resolution_notes | VARCHAR(2000) | NULL | Moderator outcome |
| created_at | TIMESTAMP | NOT NULL | Report time |
| resolved_at | TIMESTAMP | NULL | Resolution time |

## 4. Relationships and delete behavior

- A user can own many resources, collections, saved resources, reviews, reports, and progress records.
- A resource belongs to one primary subject and can have many tags, saves, collection memberships, reviews, reports, and progress records.
- A collection belongs to one user and contains many resources through `collection_items`.
- Deleting a collection cascades to its collection items.
- Deleting a saved-resource relationship does not delete the resource.
- User deletion must follow a retention policy; private records should be deleted or anonymized without removing public resource records needed for auditability.
- Resources should normally be archived rather than physically deleted.

## 5. Indexes

- Unique index on `users.email`.
- Unique index on `subjects.slug`.
- Unique index on active `resources.canonical_url`.
- Index on `resources.subject_id, resources.status`.
- Index on `resources.resource_type, resources.difficulty`.
- Index on `resources.created_at`.
- Index on `saved_resources.user_id`.
- Index on `collections.owner_id`.
- Index on `study_progress.user_id, study_progress.study_status`.
- Index on `reviews.resource_id`.
- Index on `reports.status, reports.created_at`.

For full-text search, add the database engine's full-text index over resource title, description, and tag names rather than relying on leading-wildcard queries.

## 6. Validation rules

- Email addresses are normalized before uniqueness checks.
- URLs are parsed, normalized, and limited to allowed schemes such as HTTPS.
- `progress_percent` must be between 0 and 100.
- `rating` must be between 1 and 5.
- Enumerated values must be validated in the application and constrained in the database where supported.
- Ownership checks must be performed on every private-data query and mutation.

## 7. Migration and backup expectations

- Schema changes shall be delivered through versioned Entity Framework Core migrations.
- A clean environment must be able to create the schema from the migration history.
- Production backups should be encrypted, access-controlled, and periodically restore-tested.
