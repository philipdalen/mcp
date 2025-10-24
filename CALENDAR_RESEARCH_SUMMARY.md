# Teamwork MCP Calendar Support Research Summary

## Date

October 22, 2025

## Overview

This document summarizes the research and planning completed for adding calendar event support to the Teamwork MCP server.

## Research Findings

### Teamwork Calendar API

Based on research of the Teamwork API and calendar features:

1. **Calendar Events Endpoint**: The Teamwork API provides a `/calendarevents` endpoint for managing calendar events
2. **Key Features**:

   - Create, read, update, and delete calendar events
   - Filter events by date range, project, and user
   - Support for all-day events and timed events
   - Attendee management for events
   - Reminder notifications
   - Private/public event visibility

3. **Data Structures**: Calendar events include:
   - Title and description
   - Start/end dates and times
   - Project association
   - Attendees (users)
   - Reminder settings
   - Privacy settings

### Integration Requirements

To add calendar support to the Teamwork MCP server, the following is required:

#### Phase 1: SDK Updates (Prerequisite)

The `twapi-go-sdk` package needs to be updated first with:

- Calendar event type definitions
- Request/response structures
- API method implementations (Create, Update, Delete, Get, List)
- Tests for all calendar operations

#### Phase 2: MCP Server Implementation

Once the SDK is updated:

- Create `calendarevents.go` with MCP tool wrappers
- Create `calendarevents_test.go` with comprehensive tests
- Register calendar tools in the tool registry
- Add documentation and examples

## Documentation Created

The following documentation has been created to guide future implementation:

### 1. CALENDAR_SDK_TODO.md

Location: `internal/twprojects/CALENDAR_SDK_TODO.md`

This comprehensive guide includes:

- Complete type definitions needed for the SDK
- API method signatures
- Request/response structures
- Step-by-step implementation guide
- Notes for implementers about date formats, timezones, and special considerations

Key sections:

- **Required SDK Changes**: Detailed type definitions for all calendar event operations
- **Teamwork API Endpoints**: Documentation of the API endpoints to use
- **Implementation Status**: Checklist of tasks
- **Next Steps**: Phased approach to implementation
- **References**: Links to Teamwork documentation

### 2. Project Build Verification

- Successfully rebuilt the Go project
- All existing tests pass (90 tests across all packages)
- No breaking changes introduced
- Project remains in working state

## Technical Details

### Type Structures Required

The SDK will need these primary types:

- `CalendarEvent` - Main event structure with all fields
- `CalendarEventCreateRequest` - Request for creating events
- `CalendarEventUpdateRequest` - Request for updating events
- `CalendarEventDeleteRequest` - Request for deleting events
- `CalendarEventGetRequest` - Request for fetching a single event
- `CalendarEventListRequest` - Request for listing events with filters
- Corresponding response types for each operation

### API Endpoints

- `GET /calendarevents.json` - List calendar events
- `POST /calendarevents.json` - Create a calendar event
- `GET /calendarevents/{id}.json` - Get a specific calendar event
- `PUT /calendarevents/{id}.json` - Update a calendar event
- `DELETE /calendarevents/{id}.json` - Delete a calendar event

### MCP Tools to Implement

Five main tools following the existing pattern:

1. `twprojects-create_calendar_event` - Create new calendar events
2. `twprojects-update_calendar_event` - Update existing events
3. `twprojects-delete_calendar_event` - Delete events (gated by allowDelete)
4. `twprojects-get_calendar_event` - Get a single event by ID
5. `twprojects-list_calendar_events` - List events with filtering

## Current Status

✅ **Completed:**

- Research of Teamwork Calendar API and features
- Documentation of implementation requirements
- Type structure design
- Implementation guide creation
- Project verification (builds and tests pass)

⏳ **Blocked on:**

- SDK updates to `twapi-go-sdk` package
  - The MCP server cannot implement calendar support until the SDK has the necessary types and methods

## Next Steps for Implementation

1. **SDK Team**: Update `twapi-go-sdk` with calendar event support

   - Follow the detailed specifications in `CALENDAR_SDK_TODO.md`
   - Add types, methods, and tests
   - Release new version

2. **MCP Team**: Once SDK is updated:
   - Update `go.mod` to new SDK version
   - Implement MCP tools following `CALENDAR_SDK_TODO.md`
   - Add tests
   - Update documentation

## Timeline Estimate

- **SDK Updates**: 2-3 days (depending on team availability and PR review)
- **MCP Implementation**: 1-2 days (straightforward following existing patterns)
- **Testing & Documentation**: 1 day
- **Total**: Approximately 4-6 days of development effort

## Recommendations

1. **Verify API Endpoints**: Before SDK implementation, verify the exact Teamwork API endpoints and field names using API documentation or test requests
2. **Timezone Handling**: Pay special attention to timezone handling for all-day vs timed events
3. **Recurring Events**: Investigate if the API supports recurring events and plan for future enhancement
4. **iCal Integration**: Consider whether iCal feed generation should be part of the initial release
5. **Testing**: Ensure comprehensive integration tests with actual Teamwork API

## Files Created/Modified

### Created:

- `internal/twprojects/CALENDAR_SDK_TODO.md` - Complete implementation guide
- `CALENDAR_RESEARCH_SUMMARY.md` - This summary document

### Modified:

- None (project remains in original working state)

## References

- [Teamwork Calendar Documentation](https://support.teamwork.com/projects/collaboration/calendar-explained)
- [Teamwork API Getting Started](https://apidocs.teamwork.com/guides/teamwork/getting-started-with-the-teamwork-com-api)
- Teamwork API Documentation (for `/calendarevents` endpoints)

---

**Note**: This research provides a complete foundation for implementing calendar support. The actual implementation can proceed once the SDK dependency is resolved.

