# Phase 1 - Development Tasks

## Overview
Phase 1 implementation of State-Manager component as delivered in the MVP `index.html`.

## Completed Tasks

### 1. Global State Variables
**Implementation:** JavaScript global variables for application state management

**Key State Objects:**
- `rateCardData`: Array of parsed role/rate information from CSV
- `teamConfiguration`: Array of current team member configurations
- `nextRowId`: Counter for generating unique team member row IDs

**Code Delivered:**
```javascript
// Global state
let rateCardData = [];
let teamConfiguration = [];
let nextRowId = 1;
```

### 2. Rate Card Data Management
**Implementation:** Storage and management of parsed CSV rate card data

**Key Features:**
- Stores complete role information (name, client rate, internal cost)
- Provides structured access to rate data
- Maintains data integrity across component interactions
- Supports role lookup by ID

**Data Structure:**
```javascript
rateCardData = [
    {
        id: "role_1",
        name: "Senior Developer",
        clientRate: 150,
        internalCost: 120
    },
    {
        id: "role_2", 
        name: "Junior Developer",
        clientRate: 75,
        internalCost: 60
    }
    // ... additional roles
];
```

### 3. Team Configuration State Management
**Implementation:** Dynamic array management for team member configurations

**Key Features:**
- Maintains current team composition
- Tracks individual team member data (role, hours, costs)
- Supports add/remove operations
- Provides real-time state synchronization

**Data Structure:**
```javascript
teamConfiguration = [
    {
        rowId: 1,
        roleId: "role_1",
        hours: 40,
        clientCost: 6000,
        internalCost: 4800
    },
    {
        rowId: 2,
        roleId: "role_2", 
        hours: 80,
        clientCost: 6000,
        internalCost: 4800
    }
    // ... additional team members
];
```

### 4. State Initialization Functions
**Implementation:** Functions to reset and initialize application state

**Key Features:**
- Clears previous state when new data loaded
- Initializes empty configurations
- Resets UI to default state
- Prepares for new user workflows

**Code Delivered:**
```javascript
function setupConfiguration() {
    const configForm = document.getElementById('configForm');
    const noRateCard = document.getElementById('noRateCard');
    
    teamConfiguration = []; // Reset team configuration state
    const teamTableBody = document.getElementById('teamTableBody');
    teamTableBody.innerHTML = '';
    
    configForm.classList.remove('hidden');
    noRateCard.classList.add('hidden');
}
```

### 5. State Update Operations
**Implementation:** Functions to modify state in response to user actions

**Key Features:**
- Add team member to configuration array
- Remove team member from configuration array
- Update individual team member properties
- Maintain state consistency across operations

**Add Operation:**
```javascript
// Add to configuration array (within addTeamMember function)
teamConfiguration.push({
    rowId: rowId,
    roleId: null,
    hours: 0,
    clientCost: 0,
    internalCost: 0
});
```

**Remove Operation:**
```javascript
// Remove from configuration array (within removeTeamMember function)
teamConfiguration = teamConfiguration.filter(config => config.rowId !== rowId);
```

**Update Operation:**
```javascript
// Update configuration properties (within updateTeamMember function)
const config = teamConfiguration.find(c => c.rowId === rowId);
if (config) {
    config.roleId = selectedRoleId;
    config.hours = hours;
    config.clientCost = hours * role.clientRate;
    config.internalCost = hours * role.internalCost;
}
```

### 6. State Query Functions
**Implementation:** Functions to retrieve and filter state data

**Key Features:**
- Find team configurations by row ID
- Filter valid configurations for calculations
- Lookup rate card data by role ID
- Provide data access for other components

**Query Examples:**
```javascript
// Find specific configuration
const config = teamConfiguration.find(c => c.rowId === rowId);

// Filter valid configurations for calculations
const validConfigs = teamConfiguration.filter(config => config.roleId && config.hours > 0);

// Find rate card role by ID
const role = rateCardData.find(r => r.id === selectedRoleId);
```

### 7. Unique ID Management
**Implementation:** Simple counter-based ID generation system

**Key Features:**
- Generates unique IDs for team member rows
- Ensures no ID conflicts
- Simple incrementing counter approach
- Supports dynamic row addition

**Code Implementation:**
```javascript
let nextRowId = 1;

// Usage in addTeamMember function
const rowId = nextRowId++;
```

### 8. State Persistence Strategy
**Implementation:** In-memory state management (Phase 1 approach)

**Key Features:**
- Client-side only storage
- Session-based persistence (lost on page refresh)
- No external storage dependencies
- Immediate state updates

**Limitations Acknowledged:**
- No permanent storage
- No cross-session persistence
- No browser storage utilization
- No state backup/restore

### 9. State Synchronization
**Implementation:** Ensuring UI and state consistency

**Key Features:**
- State changes immediately reflected in UI
- UI actions immediately update state
- Bidirectional synchronization
- Real-time consistency maintenance

**Synchronization Points:**
```javascript
// UI → State (user input)
config.hours = parseFloat(hoursInput.value) || 0;

// State → UI (display update)
clientCostCell.textContent = `$${config.clientCost.toFixed(2)}`;

// Trigger recalculations
calculateCost();
```

### 10. Data Loading and Population
**Implementation:** State population from external data sources

**Key Features:**
- Populates rateCardData from CSV parsing
- Validates data integrity during loading
- Triggers UI updates after data loading
- Handles loading success/failure states

**Loading Process:**
```javascript
// From parseCSV function
if (roles.length === 0) {
    throw new Error('No valid role data found in CSV');
}

rateCardData = roles; // Update global state
displayRateCard();    // Update UI
setupConfiguration(); // Initialize team configuration state
```

## Phase 1 State Management Limitations

### Data Persistence
- No localStorage or sessionStorage utilization
- No database or server-side storage
- State lost on page refresh or browser close
- No export/import of state data

### Validation and Integrity
- Basic client-side validation only
- No schema validation for state objects
- No state history or undo functionality
- No conflict resolution for concurrent updates

### Performance Considerations
- No state caching mechanisms
- No lazy loading of large datasets
- No state compression or optimization
- Linear search operations (adequate for Phase 1 scale)

### Scalability Limitations
- Single-user, single-session design
- No multi-configuration comparison state
- No batch operations on state
- No state partitioning or modularization

## Integration Points

### Input Sources
- **CSV Parser:** Populates rateCardData from parsed CSV
- **Team Configuration Builder:** Updates teamConfiguration array
- **User Interface:** Reflects state changes in UI elements

### Output Consumers
- **Cost Calculator:** Reads teamConfiguration for calculations
- **Basic UI:** Displays state data in various components
- **Validation Engine:** Validates state data integrity

## State Change Event Flow

1. **User Action** → UI component captures input
2. **Validation** → Basic input validation performed
3. **State Update** → Global state variables modified
4. **UI Sync** → Display elements updated to reflect state
5. **Calculation Trigger** → Cost calculations performed with new state
6. **Results Display** → Updated calculations shown to user

## Data Access Patterns Implemented

### Direct Access
```javascript
// Simple global variable access
let currentData = rateCardData;
let currentTeam = teamConfiguration;
```

### Find Operations
```javascript
// Find by ID
const role = rateCardData.find(r => r.id === roleId);
const config = teamConfiguration.find(c => c.rowId === rowId);
```

### Filter Operations
```javascript
// Filter by criteria
const validConfigs = teamConfiguration.filter(config => 
    config.roleId && config.hours > 0
);
```

### Array Manipulation
```javascript
// Add, remove, update operations
teamConfiguration.push(newConfig);
teamConfiguration = teamConfiguration.filter(c => c.rowId !== targetId);
config.hours = newValue;
```