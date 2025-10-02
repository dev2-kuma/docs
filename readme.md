# Timeline API Abstract Documentation

## Overview
This document provides a comprehensive API specification for the Timeline module, covering all necessary endpoints for timeline management, project details, version management, and story tracking.

**Base Path:** `/spaces/:spaceId/timeline`
**Authentication:** SpaceGuard (requires access to the space)
**Date Format:** `YYYY-MM-DD`
**Pagination:** `page` (default 1), `limit` (default 10)

---

## 1. Timeline List Screen APIs

### 1.1 Get Filter Options
**Endpoint:** `GET /spaces/:spaceId/timeline/filter-options`

**Description:** Get available filter options for timeline.

**Response:**
```json
{
  "data": {
    "projects": [
      {
        "id": "string",
        "name": "string",
        "code": "string",
        "jiraCode": "string"
      }
    ],
    "versions": [
      {
        "id": "string",
        "name": "string",
      }
    ],
    "members": [
      {
        "id": "string",
        "username": "string",
        "email": "string",
        "avatarUrl": "string"
      }
    ],
    "teams": [
      {
        "id": "string",
        "name": "string",
      }
    ],
  }
}
```

### 1.2 Get Projects List (Timeline Overview)
**Endpoint:** `GET /spaces/:spaceId/timeline/projects`

**Description:** Get list of projects with essential timeline information (without detailed children).

**Query Parameters:**
- `projectIds`: Comma-separated project IDs
- `portfolioIds`: Comma-separated portfolio IDs
- `search`: Text search across project names and IDs
- `userIds`: Comma-separated assignee IDs
- `teamIds`: Comma-separated team IDs
- `versionIds`: Comma-separated version IDs
- `from`, `to`: Date range filter (YYYY-MM-DD)
- `overdue`: true/false (unreleased versions and stories not in completed statuses)
- `dueToday`, `dueThisWeek`, `dueNextWeek`: true/false flags
- `showReleased`: true/false (include released versions)

**Response:**
```json
{
     "id": "string",
     "name": "string",
     "code": "string",
     "type": "project",
     "progress": "number (0-100)",
     "startDate": "YYYY-MM-DD",
     "dueDate": "YYYY-MM-DD",
     "releasedVersions": "number",
     "markers": [
     {
         "date": "YYYY-MM-DD", // duedate
         "type": "release" | "story",
         "name": "string", // "version name" | "story name",
         "jiraCode": "string",
         "status": "string", // Original status from entity
         "statusProgress": "string", // Calculated progress status
     }
     // ...
     ]
}
```

### 1.3 Get Version/Story Details (Hover)
**Endpoint:** `GET /spaces/:spaceId/timeline/entities/:entityId/details`

**Description:** Get quick details when hovering over timeline markers.

**Path Parameters:**
- `entityId`: Version ID or Story ID

**Response:**
```json
{
  "data": {
    "id": "string",
    "name": "string",
    "type": "release" | "story",
    "status": "string",
    "statusProgress": "string",
    "jiraCode": "string",
    "dueDate": "YYYY-MM-DD"
  }
}
```

### 1.4 Get Workload Details (Hover Progress Bar)
**Endpoint:** `GET /spaces/:spaceId/timeline/entities/:entityId/workload`

**Description:** Get detailed workload information when hovering over progress bars.

**Path Parameters:**
- `entityId`: Version ID or Story ID

**Response:**
```json
{
  "data": {
    "id": "string",
    "name": "string",
    "type": "release" | "story",
    "workload": {
      "total": "number (hours)",
      "burned": "number (hours)",
      "logged": "number (hours)",
      "remaining": "number (hours)"
    }
  }
}
```

### 1.5 Get Project Versions (Expand Project)
**Endpoint:** `GET /spaces/:spaceId/timeline/projects/:projectId/versions`

**Description:** Get list of versions when expanding a project.

**Path Parameters:**
- `projectId`: Project ID

**Query Parameters:**
- `page`: Page number (default: 1) (OPTIONAL)
- `limit`: Page size (default: 10) (OPTIONAL)

**Response:**
```json
{
  "data": [
    {
      "id": "string",
      "name": "string",
      "type": "release",
      "status": "string",
      "statusProgress": "string",
      "progress": "number (0-100)",
      "startDate": "YYYY-MM-DD",
      "dueDate": "YYYY-MM-DD"
    }
  ],
  "meta": {
    "totalItems": "number",
    "itemCount": "number",
    "perPage": "number",
    "page": "number"
  }
}
```

### 1.6 Get Version Stories (Expand Version)
**Endpoint:** `GET /spaces/:spaceId/timeline/versions/:versionId/stories`

**Description:** Get list of stories when expanding a version.

**Path Parameters:**
- `versionId`: Version ID

**Query Parameters:**
- `page`: Page number (default: 1) (OPTIONAL)
- `limit`: Page size (default: 10) (OPTIONAL)

**Response:**
```json
{
  "data": [
    {
      "id": "string",
      "name": "string",
      "type": "story",
      "status": "string",
      "statusProgress": "string",
      "progress": "number (0-100)",
      "startDate": "YYYY-MM-DD",
      "dueDate": "YYYY-MM-DD",
      "jiraCode": "string"
    }
  ],
  "meta": {
    "totalItems": "number",
    "itemCount": "number",
    "perPage": "number",
    "page": "number"
  }
}
```

---

## 2. Project Detail Screen APIs

### 2.1 Get Project Summary
**Endpoint:** `GET /spaces/:spaceId/timeline/projects/:projectId/summary`

**Description:** Get project summary with counts and statistics.

**Path Parameters:**
- `projectId`: Project ID

**Response:**
```json
{
  "data": {
    "project": {
      "id": "string",
      "name": "string",
      "code": "string"
    },
    "summary": {
        "releaseVersions": {
            "released": "number",
            "unreleased": "number",
            "overdue": "number",
            "dueSoon": "number"
        },
        "stories": {
            "todo": "number",
            "inProgress": "number",
            "overdue": "number",
            "dueSoon": "number"
        }
    }
 
  }
}
```

## 3. Version Detail Screen APIs

### 3.1 Get Version Summary
**Endpoint:** `GET /spaces/:spaceId/timeline/versions/:versionId/summary`

**Description:** Get version summary with progress and workload details.

**Path Parameters:**
- `versionId`: Version ID

**Response:**
```json
{
  "data": {
    "version": {
      "id": "string",
      "name": "string",
      "status": "string", 
      "statusProgress": "string", 
      "note": "string",
      "startDate": "YYYY-MM-DD",
      "dueDate": "YYYY-MM-DD",
    },
    "summary": {
      "capacity": {
        "remaining": "number (hours)",
      },
      "workload": {
        "total": "number (hours)",
        "burned": "number (hours)",
        "logged": "number (hours)",
        "remaining": "number (hours)"
      },
    }
  }
}
```

### 3.2 Get Version Assignees
**Endpoint:** `GET /spaces/:spaceId/timeline/versions/:versionId/assignees`

**Description:** Get list of assignees working on this version with their workload distribution across all versions.

**Path Parameters:**
- `versionId`: Version ID

**Query Parameters:**
- `page`: Page number (default: 1) (OPTIONAL)
- `limit`: Page size (default: 10) (OPTIONAL)

**Response:**
```json
{
  "data": [
    {
      "id": "string",
      "username": "string",
      "email": "string",
      "avatarUrl": "string",
      "capacity": "number (hours)",
      "workload": "number (hours)",
      "distributions": [
        {
          "versionId": "string",
          "versionName": "string",
          "capacity": "number (hours)"
        }
      ]
    }
  ],
  "meta": {
    "totalItems": "number",
    "itemCount": "number",
    "perPage": "number",
    "page": "number"
  }
}
```

### 3.3 Change Version Highlight in Timeline
**Endpoint:** `PUT /spaces/:spaceId/timeline/versions/:versionId/highlight`

**Description:** Toggle highlight status of a version in timeline.

**Path Parameters:**
- `versionId`: Version ID

**Request Body:**
```json
{
  "highlighted": boolean
}
```

**Response:**
```json
{
  "data": {
    "id": "string",
    "highlighted": boolean,
  }
}
```

---

## 4. Story Detail Screen APIs

### 4.1 Get Story Summary
**Endpoint:** `GET /spaces/:spaceId/timeline/stories/:storyId/summary`

**Description:** Get story summary with progress and workload details.

**Path Parameters:**
- `storyId`: Story ID

**Response:**
```json
{
  "data": {
    "story": {
      "id": "string",
      "title": "string",
      "status": "string",
      "statusProgress": "string",
      "releaseVersion": "string", // version nó thuộc về
      "startDate": "YYYY-MM-DD",
      "dueDate": "YYYY-MM-DD"
    },
    "summary": {
      "capacity": {
        "remaining": "number (hours)"
      },
      "workload": {
        "total": "number (hours)",
        "burned": "number (hours)",
        "logged": "number (hours)",
        "remaining": "number (hours)"
      }
    }
  }
}
```

### 4.2 Get Story Assignees
**Endpoint:** `GET /spaces/:spaceId/timeline/stories/:storyId/assignees`

**Description:** Get list of assignees working on this story and its subtasks.

**Path Parameters:**
- `storyId`: Story ID

**Query Parameters:**
- `page`: Page number (default: 1)
- `limit`: Page size (default: 10)

**Response:**
```json
{
  "data": [
    {
      "id": "string",
      "username": "string",
      "email": "string",
      "avatarUrl": "string",
      "capacity": "number (hours)",
      "workload": "number (hours)",
      "distributions": [
        {
          "versionId": "string",
          "versionName": "string",
          "capacity": "number (hours)"
        }
      ]
    }
  ],
  "meta": {
    "totalItems": "number",
    "itemCount": "number",
    "perPage": "number",
    "page": "number"
  }
}
```

### 4.3 Get Story Subtasks
**Endpoint:** `GET /spaces/:spaceId/timeline/stories/:storyId/subtasks`

**Description:** Get list of subtasks for a story.

**Path Parameters:**
- `storyId`: Story ID

**Query Parameters:**
- `page`: Page number (default: 1)
- `limit`: Page size (default: 10)

**Response:**
```json
{
  "data": [
    {
      "id": "string",
      "title": "string",
      "status": "string",
      "startDate": "YYYY-MM-DD",
      "dueDate": "YYYY-MM-DD",
      "jiraCode": "string",
      "logged": "number (hours)",
      "remaining": "number (hours)",
      "assignee": {
        "id": "string",
        "username": "string",
        "email": "string",
        "avatarUrl": "string"
      }
    }
  ],
  "meta": {
    "totalItems": "number",
    "itemCount": "number",
    "perPage": "number",
    "page": "number"
  }
}
```

---

## Common Response Fields

### Pagination Meta
```json
{
  "totalItems": "number",
  "itemCount": "number",
  "perPage": "number",
  "page": "number"
}
```

### User/Assignee
```json
{
  "id": "string",
  "username": "string",
  "email": "string",
  "avatarUrl": "string"
}
```

---

## Error Responses

All endpoints return standard HTTP status codes and error responses:

```json
{
  "statusCode": "number",
  "message": "string",
  "error": "string",
  "timestamp": "ISO 8601",
  "path": "string"
}
```

---

## Notes

- All date fields are returned as `YYYY-MM-DD` strings
- Progress values are percentages (0-100)
- Workload values are in hours
- `overdue` logic ignores items in completed statuses (`DONE` or `CANCELLED`)
- Pagination defaults to `limit=10` if not provided
- All endpoints require space access via `SpaceGuard`
- Timeline markers are aggregated by day for visualization
- "Without version" stories are treated as a special category in project versions

## Status and StatusProgress Values

### Status (Original Entity Status)
- **Story**: Uses `IssueStatus` enum values:
  - `BACKLOG`, `TODO`, `REQ_GATHER`, `OPEN`, `REOPENED`, `PAUSED`, `PENDING`
  - `IN_PROGRESS`, `IN_REVIEW`, `IN_BUILD`, `IN_TEST`, `IN_VERIFY`
  - `CANCELLED`, `DONE`
- **Version**: Uses release status:
  - `RELEASED` - Version has been released
  - `UNRELEASED` - Version is not yet released

### StatusProgress (Calculated Progress Status)
- **Story**: Progress-based status:
  - `TODO` - Not started
  - `IN_PROGRESS` - In progress and on track
  - `IN_PROGRESS_OVERDUE` - In progress but overdue
  - `DONE` - Completed
- **Version**: Progress-based status:
  - `RELEASED` - Version is released
  - `ON_TRACK` - On track to meet deadline
  - `AT_RISK` - At risk of missing deadline
  - `OFF_TRACK` - Off track, likely to miss deadline
