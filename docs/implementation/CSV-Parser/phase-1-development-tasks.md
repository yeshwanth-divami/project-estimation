# Phase 1 - Development Tasks

## Overview
Phase 1 implementation of CSV-Parser component as delivered in the MVP `index.html`.

## Completed Tasks

### 1. Core CSV Parsing Functionality
**Implementation:** JavaScript function `parseCSV(csvContent)`

**Key Features:**
- Parse CSV content from uploaded files
- Handle comma-delimited format
- Extract role names, client rates, and internal costs
- Support flexible column naming (role/title/position/name, client rate/rate/hourly, internal cost/cost/internal)
- Generate unique IDs for each role
- Default internal cost to 80% of client rate if not provided

**Code Delivered:**
```javascript
function parseCSV(csvContent) {
    try {
        const lines = csvContent.trim().split('\n');
        if (lines.length < 2) {
            throw new Error('CSV must have at least a header row and one data row');
        }
        
        // Parse header to find role, client rate, and internal cost columns
        const header = lines[0].split(',').map(col => col.trim().replace(/"/g, ''));
        const roleIndex = findColumnIndex(header, ['role', 'title', 'position', 'name']);
        const clientRateIndex = findColumnIndex(header, ['client rate', 'rate', 'hourly', 'client', 'bill']);
        const internalCostIndex = findColumnIndex(header, ['internal cost', 'cost', 'internal', 'loaded']);
        
        // Validation and parsing logic...
    } catch (error) {
        showMessage('Error parsing CSV: ' + error.message, 'error');
    }
}
```

### 2. Column Detection Algorithm
**Implementation:** Helper function `findColumnIndex(header, possibleNames)`

**Purpose:** Intelligent column detection for flexible CSV formats

**Code Delivered:**
```javascript
function findColumnIndex(header, possibleNames) {
    for (const name of possibleNames) {
        const index = header.findIndex(col => 
            col.toLowerCase().includes(name.toLowerCase())
        );
        if (index !== -1) return index;
    }
    return -1;
}
```

### 3. File Processing Integration
**Implementation:** File reading and processing chain

**Key Features:**
- FileReader API integration
- CSV file type validation
- Error handling for file reading failures
- Integration with drag-and-drop functionality

**Code Delivered:**
```javascript
function processFile(file) {
    if (!file.name.toLowerCase().endsWith('.csv')) {
        showMessage('Please select a CSV file.', 'error');
        return;
    }
    
    const reader = new FileReader();
    reader.onload = function(e) {
        try {
            const csvContent = e.target.result;
            parseCSV(csvContent);
        } catch (error) {
            showMessage('Error reading file: ' + error.message, 'error');
        }
    };
    reader.readAsText(file);
}
```

### 4. Data Structure Output
**Implementation:** Standardized role data objects

**Data Schema:**
```javascript
{
    id: `role_${i}`,           // Generated unique identifier
    name: roleName,            // Role title from CSV
    clientRate: clientRate,    // Hourly client rate
    internalCost: internalCost // Internal hourly cost
}
```

### 5. Error Handling & Validation
**Implementation:** Comprehensive error checking

**Validation Rules:**
- Minimum 2 lines (header + data)
- Required role column presence
- Required client rate column presence
- Numeric validation for rates
- Positive value validation
- Empty/invalid row filtering

## Phase 1 Limitations & Assumptions
- Only supports comma-delimited CSV format
- No support for quoted fields with commas
- Basic string trimming and quote removal
- Single file processing (no batch upload)
- Client-side only (no server validation)

## Integration Points
- **Input:** File upload from Basic-UI component
- **Output:** Parsed data array to State-Manager
- **Error Reporting:** User messages via Basic-UI

## Testing Scenarios Validated
- ✅ Valid CSV with role, client rate, internal cost columns
- ✅ CSV with only role and client rate (internal cost defaulted)
- ✅ CSV with alternative column names (title, hourly, etc.)
- ✅ Invalid file types (.txt, .xlsx)
- ✅ Empty files
- ✅ Malformed CSV data
- ✅ Non-numeric rate values
- ✅ Missing required columns

## Sample Data Supported
```csv
Role,Client Rate,Internal Cost
Senior Developer,150,120
Junior Developer,75,60
Tech Lead,180,140
```