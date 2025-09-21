# Logging Instructions for Next AI

## Logging Requirements
All subsequent AI interactions must maintain comprehensive logging of changes made to the project. This ensures traceability and helps in understanding the evolution of the codebase.

## Required Logging Practices

### 1. Change Log Maintenance
- **File**: `CHANGELOG.md` in the project root
- **Format**: Use semantic versioning and date stamps
- **Content**: Document all significant changes, additions, and modifications
- **Frequency**: Update with each substantial change or at the end of each session

### 2. Todo List Updates
- **File**: `TODO.md` in the project root  
- **Purpose**: Track current progress and pending tasks
- **Update Frequency**: After each completed task or discovered requirement
- **Format**: Markdown checklist with status indicators

### 3. Session Logging
- **File**: `SESSION_LOG.md` (to be created by next AI)
- **Content**: Detailed record of each AI session including:
  - Timestamp and duration
  - Tasks attempted and completed
  - Files modified or created
  - Decisions made and rationale
  - Issues encountered and resolutions

### 4. Code Comments
- **Requirement**: All new code must include descriptive comments
- **Purpose**: Explain complex logic, decisions, and dependencies
- **Format**: JSDoc for TypeScript/JavaScript, docstrings for Python

### 5. Error Handling Logs
- **Implementation**: Structured error logging throughout application
- **Levels**: DEBUG, INFO, WARN, ERROR, CRITICAL
- **Storage**: Console output and log files (frontend and backend separately)

## Instructions for Next AI
1. **Start each session** by reading the CHANGELOG and TODO files
2. **Document all changes** in the appropriate log files
3. **Update the TODO list** as tasks are completed or new ones identified
4. **Maintain consistency** with existing project structure and patterns
5. **Ensure backward compatibility** with existing CardCapture codebase
6. **Follow the established** architecture and design patterns

## Example Log Entry Format
```
## [Date] - Session Summary
- **Tasks Completed**: List of accomplished items
- **Files Modified**: Paths to changed files
- **Decisions Made**: Architectural or implementation choices
- **Next Steps**: Planned actions for subsequent sessions
```

## Priority Areas for Logging
- Database schema changes
- API endpoint modifications
- Authentication and security updates
- Performance optimizations
- Bug fixes and patches

By maintaining comprehensive logs, we ensure that the project remains well-documented and easier to maintain and scale over time.