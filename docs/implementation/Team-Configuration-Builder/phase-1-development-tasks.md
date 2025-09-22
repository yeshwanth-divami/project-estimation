# Phase 1 - Development Tasks

## Overview
Phase 1 implementation of Team-Configuration-Builder component as delivered in the MVP `index.html`.

## Completed Tasks

### 1. Dynamic Team Table Structure
**Implementation:** Interactive table for building team configurations

**Key Features:**
- Dynamic row addition/removal
- Structured table layout with proper headers
- Integration with rate card data
- Real-time cost calculations per row

**Code Delivered:**
```html
<table class="team-table" id="teamTable">
    <thead>
        <tr>
            <th>Role</th>
            <th>Hours</th>
            <th>Client Cost</th>
            <th>Internal Cost</th>
            <th>Action</th>
        </tr>
    </thead>
    <tbody id="teamTableBody"></tbody>
</table>
```

### 2. Add Team Member Functionality
**Implementation:** Function to dynamically add new team member rows

**Key Features:**
- Generates unique row IDs
- Creates role dropdown from rate card data
- Adds hours input field
- Includes remove button for each row
- Auto-populates cost calculation cells

**Code Delivered:**
```javascript
function addTeamMember() {
    const teamTableBody = document.getElementById('teamTableBody');
    const rowId = nextRowId++;
    
    const row = document.createElement('tr');
    row.id = `team-row-${rowId}`;
    
    // Create role dropdown options
    let roleOptions = '<option value="">Select Role...</option>';
    rateCardData.forEach(role => {
        roleOptions += `<option value="${role.id}">${role.name} ($${role.clientRate}/hr)</option>`;
    });
    
    row.innerHTML = `
        <td>
            <select id="role-${rowId}" onchange="updateTeamMember(${rowId})">
                ${roleOptions}
            </select>
        </td>
        <td>
            <input type="number" id="hours-${rowId}" placeholder="0" min="0" step="0.5" 
                   onchange="updateTeamMember(${rowId})" oninput="updateTeamMember(${rowId})">
        </td>
        <td id="client-cost-${rowId}">$0.00</td>
        <td id="internal-cost-${rowId}">$0.00</td>
        <td>
            <button class="remove-btn" onclick="removeTeamMember(${rowId})">Remove</button>
        </td>
    `;
    
    teamTableBody.appendChild(row);
    
    // Add to configuration array
    teamConfiguration.push({
        rowId: rowId,
        roleId: null,
        hours: 0,
        clientCost: 0,
        internalCost: 0
    });
}
```

### 3. Remove Team Member Functionality
**Implementation:** Function to remove team member rows and clean up data

**Key Features:**
- DOM element removal
- Configuration array cleanup
- Automatic cost recalculation
- Data integrity maintenance

**Code Delivered:**
```javascript
function removeTeamMember(rowId) {
    const row = document.getElementById(`team-row-${rowId}`);
    if (row) {
        row.remove();
        teamConfiguration = teamConfiguration.filter(config => config.rowId !== rowId);
        calculateCost();
    }
}
```

### 4. Role Dropdown Population
**Implementation:** Dynamic dropdown creation from rate card data

**Key Features:**
- Populated from parsed CSV data
- Displays role name and client rate
- Default "Select Role..." option
- Real-time availability based on uploaded data

**Code Structure:**
```javascript
// Role dropdown generation
let roleOptions = '<option value="">Select Role...</option>';
rateCardData.forEach(role => {
    roleOptions += `<option value="${role.id}">${role.name} ($${role.clientRate}/hr)</option>`;
});
```

### 5. Hours Input Handling
**Implementation:** Numeric input with validation and real-time updates

**Key Features:**
- Number input type with decimal support (step="0.5")
- Minimum value validation (min="0")
- Real-time updates on both change and input events
- Placeholder guidance

**Input Configuration:**
```html
<input type="number" id="hours-${rowId}" placeholder="0" min="0" step="0.5" 
       onchange="updateTeamMember(${rowId})" oninput="updateTeamMember(${rowId})">
```

### 6. Team Member Update Logic
**Implementation:** Core function to handle changes in role selection or hours

**Key Features:**
- Handles both role selection and hours input changes
- Calculates client and internal costs in real-time
- Updates display values immediately
- Triggers total cost recalculation
- Maintains data consistency

**Code Delivered:**
```javascript
function updateTeamMember(rowId) {
    const roleSelect = document.getElementById(`role-${rowId}`);
    const hoursInput = document.getElementById(`hours-${rowId}`);
    const clientCostCell = document.getElementById(`client-cost-${rowId}`);
    const internalCostCell = document.getElementById(`internal-cost-${rowId}`);
    
    const config = teamConfiguration.find(c => c.rowId === rowId);
    if (!config) return;
    
    const selectedRoleId = roleSelect.value;
    const hours = parseFloat(hoursInput.value) || 0;
    
    config.roleId = selectedRoleId;
    config.hours = hours;
    
    if (selectedRoleId && hours > 0) {
        const role = rateCardData.find(r => r.id === selectedRoleId);
        if (role) {
            config.clientCost = hours * role.clientRate;
            config.internalCost = hours * role.internalCost;
            
            clientCostCell.textContent = `$${config.clientCost.toFixed(2)}`;
            internalCostCell.textContent = `$${config.internalCost.toFixed(2)}`;
        }
    } else {
        config.clientCost = 0;
        config.internalCost = 0;
        clientCostCell.textContent = '$0.00';
        internalCostCell.textContent = '$0.00';
    }
    
    calculateCost();
}
```

### 7. Configuration Data Structure
**Implementation:** JavaScript array to maintain team configuration state

**Data Schema:**
```javascript
teamConfiguration = [
    {
        rowId: 1,              // Unique row identifier
        roleId: "role_1",      // Reference to rate card role
        hours: 40.5,           // Hours allocated
        clientCost: 6075.00,   // Calculated client cost
        internalCost: 4860.00  // Calculated internal cost
    }
];
```

### 8. Setup and Initialization
**Implementation:** Function to prepare the configuration interface

**Key Features:**
- Clears previous configuration data
- Resets the team table
- Shows/hides appropriate UI elements
- Prepares for new team building

**Code Delivered:**
```javascript
function setupConfiguration() {
    const configForm = document.getElementById('configForm');
    const noRateCard = document.getElementById('noRateCard');
    
    teamConfiguration = [];
    const teamTableBody = document.getElementById('teamTableBody');
    teamTableBody.innerHTML = '';
    
    configForm.classList.remove('hidden');
    noRateCard.classList.add('hidden');
}
```

### 9. Add Team Member Button
**Implementation:** Primary action button to add new team members

**Styling and Behavior:**
```css
.add-role-btn {
    background: #27ae60;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
    margin-bottom: 1rem;
}
```

**HTML Integration:**
```html
<button class="add-role-btn" onclick="addTeamMember()">+ Add Team Member</button>
```

## Phase 1 Limitations & Assumptions
- No drag-and-drop reordering of team members
- No bulk import of team configurations
- No team member templates or presets
- No validation for duplicate roles
- No advanced filtering or search in role dropdown
- No hours range validation beyond minimum 0
- No time tracking or allocation percentage features

## Integration Points
- **CSV Parser:** Receives rate card data for role dropdown population
- **State Manager:** Maintains team configuration data
- **Cost Calculator:** Triggers calculations on data changes
- **Basic UI:** Renders within the UI framework
- **Validation Engine:** Basic input validation

## Data Flow
1. **Initialization:** Rate card data loaded from CSV Parser
2. **User Action:** User clicks "Add Team Member"
3. **Row Creation:** New table row with controls generated
4. **User Input:** User selects role and enters hours
5. **Calculation:** Real-time cost calculations performed
6. **State Update:** Team configuration array updated
7. **UI Refresh:** Display values updated immediately

## Validation Rules Implemented
- ✅ Hours must be numeric and >= 0
- ✅ Role selection required for cost calculation
- ✅ Empty hours default to 0
- ✅ Invalid hours input ignored (falls back to 0)
- ✅ Configuration array stays synchronized with UI