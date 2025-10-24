# Calendar Events SDK Implementation TODO

## Overview

This document outlines the requirements for adding calendar event support to the Teamwork MCP server. The implementation requires updates to the `twapi-go-sdk` package first, after which the MCP server tools can be implemented.

## Current Status

Calendar event support is **NOT YET IMPLEMENTED**. The underlying `twapi-go-sdk` package needs to be updated first with calendar event types and API methods before MCP tools can be added.

## Required SDK Changes

The following types and methods need to be added to `github.com/teamwork/twapi-go-sdk/projects`:

### Request Types

```go
// CalendarEventCreateRequest represents a request to create a calendar event
type CalendarEventCreateRequest struct {
    Title                string    `json:"title"`
    Description          *string   `json:"description,omitempty"`
    StartDate            string    `json:"startDate"`           // ISO 8601 format (YYYY-MM-DD)
    StartTime            string    `json:"startTime,omitempty"` // HH:MM format
    EndDate              string    `json:"endDate,omitempty"`   // ISO 8601 format (YYYY-MM-DD)
    EndTime              string    `json:"endTime,omitempty"`   // HH:MM format
    ProjectID            *int      `json:"projectId,omitempty"`
    AllDay               *bool     `json:"allDay,omitempty"`
    AttendeeUserIDs      []int     `json:"attendeeUserIds,omitempty"`
    RemindBeforeMinutes  *int      `json:"remindBeforeMinutes,omitempty"`
    Private              *bool     `json:"private,omitempty"`
}

// CalendarEventUpdateRequest represents a request to update a calendar event
type CalendarEventUpdateRequest struct {
    Path struct {
        ID int `path:"id"`
    }
    Title                *string `json:"title,omitempty"`
    Description          *string `json:"description,omitempty"`
    StartDate            *string `json:"startDate,omitempty"`
    StartTime            *string `json:"startTime,omitempty"`
    EndDate              *string `json:"endDate,omitempty"`
    EndTime              *string `json:"endTime,omitempty"`
    ProjectID            *int    `json:"projectId,omitempty"`
    AllDay               *bool   `json:"allDay,omitempty"`
    AttendeeUserIDs      []int   `json:"attendeeUserIds,omitempty"`
    RemindBeforeMinutes  *int    `json:"remindBeforeMinutes,omitempty"`
    Private              *bool   `json:"private,omitempty"`
}

// CalendarEventDeleteRequest represents a request to delete a calendar event
type CalendarEventDeleteRequest struct {
    Path struct {
        ID int `path:"id"`
    }
}

// CalendarEventGetRequest represents a request to get a calendar event
type CalendarEventGetRequest struct {
    Path struct {
        ID int `path:"id"`
    }
}

// CalendarEventListRequest represents a request to list calendar events
type CalendarEventListRequest struct {
    Filters struct {
        StartDate   string `query:"startDate,omitempty"`
        EndDate     string `query:"endDate,omitempty"`
        ProjectID   *int   `query:"projectId,omitempty"`
        UserID      *int   `query:"userId,omitempty"`
        ShowDeleted *bool  `query:"showDeleted,omitempty"`
        Page        int    `query:"page,omitempty"`
        PageSize    int    `query:"pageSize,omitempty"`
    }
}
```

### Response Types

```go
// CalendarEvent represents a calendar event in Teamwork
type CalendarEvent struct {
    ID                  int       `json:"id"`
    Title               string    `json:"title"`
    Description         string    `json:"description"`
    StartDate           string    `json:"startDate"`
    StartTime           string    `json:"startTime"`
    EndDate             string    `json:"endDate"`
    EndTime             string    `json:"endTime"`
    ProjectID           int       `json:"projectId"`
    ProjectName         string    `json:"projectName"`
    AllDay              bool      `json:"allDay"`
    Private             bool      `json:"private"`
    RemindBeforeMinutes int       `json:"remindBeforeMinutes"`
    CreatedAt           time.Time `json:"createdAt"`
    UpdatedAt           time.Time `json:"updatedAt"`
    Attendees           []struct {
        UserID    int    `json:"userId"`
        FirstName string `json:"firstName"`
        LastName  string `json:"lastName"`
        Email     string `json:"email"`
    } `json:"attendees"`
}

// CalendarEventGetResponse represents the response from getting a calendar event
type CalendarEventGetResponse struct {
    Event CalendarEvent `json:"event"`
}

// CalendarEventListResponse represents the response from listing calendar events
type CalendarEventListResponse struct {
    Events []CalendarEvent `json:"events"`
    Meta   struct {
        Page       int `json:"page"`
        PageSize   int `json:"pageSize"`
        TotalCount int `json:"totalCount"`
        TotalPages int `json:"totalPages"`
    } `json:"meta"`
}

// CalendarEventCreateResponse represents the response from creating a calendar event
type CalendarEventCreateResponse struct {
    ID int `json:"eventId"`
}
```

### API Methods

```go
// CalendarEventCreate creates a calendar event
func CalendarEventCreate(ctx context.Context, engine *twapi.Engine, req CalendarEventCreateRequest) (*CalendarEventCreateResponse, error) {
    var resp CalendarEventCreateResponse
    err := engine.Do(ctx, "POST", "/calendarevents.json", req, &resp)
    return &resp, err
}

// CalendarEventUpdate updates a calendar event
func CalendarEventUpdate(ctx context.Context, engine *twapi.Engine, req CalendarEventUpdateRequest) (*struct{}, error) {
    var resp struct{}
    err := engine.Do(ctx, "PUT", fmt.Sprintf("/calendarevents/%d.json", req.Path.ID), req, &resp)
    return &resp, err
}

// CalendarEventDelete deletes a calendar event
func CalendarEventDelete(ctx context.Context, engine *twapi.Engine, req CalendarEventDeleteRequest) (*struct{}, error) {
    var resp struct{}
    err := engine.Do(ctx, "DELETE", fmt.Sprintf("/calendarevents/%d.json", req.Path.ID), nil, &resp)
    return &resp, err
}

// CalendarEventGet retrieves a calendar event
func CalendarEventGet(ctx context.Context, engine *twapi.Engine, req CalendarEventGetRequest) (*CalendarEventGetResponse, error) {
    var resp CalendarEventGetResponse
    err := engine.Do(ctx, "GET", fmt.Sprintf("/calendarevents/%d.json", req.Path.ID), nil, &resp)
    return &resp, err
}

// CalendarEventList lists calendar events
func CalendarEventList(ctx context.Context, engine *twapi.Engine, req CalendarEventListRequest) (*CalendarEventListResponse, error) {
    var resp CalendarEventListResponse
    err := engine.Do(ctx, "GET", "/calendarevents.json", nil, &resp)
    return &resp, err
}
```

## Teamwork API Endpoints

Based on the Teamwork API documentation, the calendar events endpoints are:

- `GET /calendarevents.json` - List calendar events
- `POST /calendarevents.json` - Create a calendar event
- `GET /calendarevents/{id}.json` - Get a specific calendar event
- `PUT /calendarevents/{id}.json` - Update a calendar event
- `DELETE /calendarevents/{id}.json` - Delete a calendar event

## Implementation Status

- [ ] SDK types added to `twapi-go-sdk/projects`
- [ ] SDK API methods implemented
- [ ] SDK tests for calendar events
- [ ] MCP tool wrappers created (`calendarevents.go`)
- [ ] MCP tool tests created (`calendarevents_test.go`)
- [ ] Tools registered in `tools.go`
- [ ] Integration tests with actual API
- [ ] Documentation updated with calendar event examples

## Next Steps

### Phase 1: SDK Updates (Required First)

1. Fork/clone the `twapi-go-sdk` repository
2. Add calendar event types to `github.com/teamwork/twapi-go-sdk/projects` package:
   - `CalendarEvent` struct
   - `CalendarEventCreateRequest` struct
   - `CalendarEventUpdateRequest` struct
   - `CalendarEventDeleteRequest` struct
   - `CalendarEventGetRequest` struct
   - `CalendarEventListRequest` struct
   - Response types for each operation
3. Implement SDK API methods:
   - `CalendarEventCreate()`
   - `CalendarEventUpdate()`
   - `CalendarEventDelete()`
   - `CalendarEventGet()`
   - `CalendarEventList()`
4. Add SDK tests for all calendar event operations
5. Submit PR to `twapi-go-sdk` repository
6. Wait for PR approval and new version release

### Phase 2: MCP Server Implementation

1. Update `go.mod` to use new SDK version with calendar support
2. Create `calendarevents.go` following the pattern in `milestones.go`:
   - Define method constants
   - Implement tool wrapper functions
   - Register methods in `init()`
3. Create `calendarevents_test.go` with comprehensive tests
4. Register calendar tools in `tools.go`:
   - Add create/update to `writeTools`
   - Add delete to `allowDelete` section
   - Add get/list to `AddReadTools`
5. Build and test locally
6. Run integration tests with actual Teamwork API
7. Update README and usage documentation

### Phase 3: Documentation

1. Add calendar event examples to README
2. Update AGENTS.md with calendar functionality
3. Create calendar-specific usage guide if needed

## References

- [Teamwork Calendar Documentation](https://support.teamwork.com/projects/collaboration/calendar-explained)
- Teamwork API Documentation - look for `/calendarevents` endpoints in the API docs
- [Teamwork API Getting Started Guide](https://apidocs.teamwork.com/guides/teamwork/getting-started-with-the-teamwork-com-api)

## Notes for Implementers

- The calendar events API may use different field names than documented above. Verify with actual API documentation.
- Date formats should follow ISO 8601 (YYYY-MM-DD) for consistency with other Teamwork API endpoints.
- Time formats typically use 24-hour HH:MM format.
- Consider timezone handling for all-day events vs timed events.
- The API may support recurring events - check documentation and add support if available.
- iCal integration may be a separate feature - investigate if this should be part of the initial implementation.
