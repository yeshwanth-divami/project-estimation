# Technical Specification

## 1. Overview

**Component Name:** TEAM-BUILDER  
**Type:** Frontend Interface Component  
**Technology Stack:** SvelteKit + Skeleton UI  
**Purpose:** Interactive table interface for building team configurations with real-time cost calculations  
**Build Phase:** Milestone 1 (Foundation)  
**Dependencies:** LOCAL-CALCULATION component  

## 2. Component Architecture

```mermaid
graph TD
    A[TEAM-BUILDER Component] --> B[TeamConfigTable]
    A --> C[ConfigurationSummary]
    A --> D[QuoteCalculator]
    A --> E[DiscountManager]
    
    B --> B1[RoleRow]
    B --> B2[AddRoleButton]
    B --> B3[RowActions]
    
    C --> C1[CostSummary]
    C --> C2[MarginDisplay]
    
    D --> D1[QuoteInput]
    D --> D2[EBITDACalculator]
    
    E --> E1[PercentageDiscount]
    E --> E2[FixedAmountDiscount]
    E --> E3[DiscountToggle]
    
    F[LOCAL-CALCULATION] --> A
    G[Store: configurationState] --> A
    H[Store: rateCardData] --> A
```

## 3. Data Models

### 3.1 Core Data Structures

```typescript
interface RoleAllocation {
  id: string;
  roleId: string;
  roleName: string;
  hourlyRate: number;
  internalCost: number;
  timeAllocation: number;
  allocationType: 'hours' | 'days';
  hoursPerDay: number;
  calculatedCost: number;
  sortOrder: number;
}

interface TeamConfiguration {
  id: string;
  name: string;
  roles: RoleAllocation[];
  clientQuote: number;
  totalCost: number;
  totalRevenue: number;
  ebitdaMargin: number;
  appliedDiscount?: Discount;
  createdDate: Date;
  lastModified: Date;
}

interface Discount {
  type: 'percentage' | 'fixed';
  value: number;
  discountAmount: number;
  originalQuote: number;
  discountedQuote: number;
}

interface RateCardEntry {
  id: string;
  roleName: string;
  hourlyRate: number;
  internalCost: number;
  department: string;
  experienceLevel: string;
  isActive: boolean;
}
```

### 3.2 Svelte Store Structure

```typescript
// stores/configuration.ts
interface ConfigurationStore {
  activeConfiguration: TeamConfiguration;
  savedConfigurations: TeamConfiguration[];
  rateCard: RateCardEntry[];
  calculationSettings: {
    hoursPerDay: number;
    currency: string;
    precision: number;
  };
  uiState: {
    isCalculating: boolean;
    errors: ValidationError[];
    warnings: string[];
  };
}
```

## 4. Component Specifications

### 4.1 TeamConfigTable Component

**Purpose:** Main table interface for role configuration  
**File:** `src/lib/components/TeamConfigTable.svelte`

**Props:**
```typescript
interface TeamConfigTableProps {
  configuration: TeamConfiguration;
  rateCard: RateCardEntry[];
  readonly?: boolean;
  maxRows?: number;
}
```

**Key Features:**

- Dynamic row addition/removal
- Role dropdown with search functionality
- Time allocation input with hours/days toggle
- Real-time cost calculation per row
- Drag-and-drop row reordering
- Inline validation feedback

**Performance Requirements:**

- Row calculations must complete within 100ms
- Support up to 20 rows without performance degradation
- Debounced input handling for rapid typing

### 4.2 RoleRow Component

**Purpose:** Individual configuration row with role selection and time allocation  
**File:** `src/lib/components/RoleRow.svelte`

**Props:**
```typescript
interface RoleRowProps {
  roleAllocation: RoleAllocation;
  availableRoles: RateCardEntry[];
  index: number;
  onUpdate: (allocation: RoleAllocation) => void;
  onRemove: (id: string) => void;
  readonly?: boolean;
}
```

**Key Features:**
- Role selection dropdown with autocomplete
- Time allocation input with format toggle (hours/days)
- Calculated cost display with currency formatting
- Remove button with confirmation
- Validation state indicators

### 4.3 ConfigurationSummary Component

**Purpose:** Display total costs and profit analysis  
**File:** `src/lib/components/ConfigurationSummary.svelte`

**Props:**
```typescript
interface ConfigurationSummaryProps {
  configuration: TeamConfiguration;
  showDetails?: boolean;
  compact?: boolean;
}
```

**Displays:**

- Total internal cost
- Total client revenue
- EBITDA margin (percentage and absolute)
- Applied discount details
- Profit/loss indicators

### 4.4 QuoteCalculator Component

**Purpose:** Client quote input and EBITDA calculation  
**File:** `src/lib/components/QuoteCalculator.svelte`

**Props:**
```typescript
interface QuoteCalculatorProps {
  totalCost: number;
  currentQuote: number;
  onQuoteChange: (quote: number) => void;
  readonly?: boolean;
}
```

**Features:**
- Currency-formatted input
- Real-time EBITDA calculation
- Margin threshold warnings
- Quote validation

### 4.5 DiscountManager Component

**Purpose:** Discount application and impact analysis  
**File:** `src/lib/components/DiscountManager.svelte`

**Props:**
```typescript
interface DiscountManagerProps {
  originalQuote: number;
  currentDiscount?: Discount;
  onDiscountChange: (discount: Discount | null) => void;
  readonly?: boolean;
}
```

**Features:**
- Discount type toggle (percentage/fixed)
- Impact preview before application
- Margin impact warnings
- Discount removal functionality

## 5. State Management

### 5.1 Reactive State Flow

```mermaid
graph LR
    A[User Input] --> B[Role/Time Change]
    B --> C[Update Store]
    C --> D[Trigger Calculations]
    D --> E[Update UI]
    E --> F[Validate State]
    F --> G[Show Feedback]
    
    C --> H[LOCAL-CALCULATION]
    H --> I[Cost Totals]
    H --> J[EBITDA Margin]
    I --> D
    J --> D
```

### 5.2 Store Actions

```typescript
// Configuration actions
export const configurationActions = {
  addRole: (roleId: string, timeAllocation: number) => void;
  removeRole: (allocationId: string) => void;
  updateRole: (allocationId: string, updates: Partial<RoleAllocation>) => void;
  setQuote: (amount: number) => void;
  applyDiscount: (discount: Discount) => void;
  removeDiscount: () => void;
  saveConfiguration: (name: string) => void;
  loadConfiguration: (id: string) => void;
  resetConfiguration: () => void;
};
```

## 6. User Interface Design

### 6.1 Layout Structure

```
┌─────────────────────────────────────────────────────────┐
│ Team Configuration Builder                              │
├─────────────────────────────────────────────────────────┤
│ ┌─ Configuration Table ─────────────────────────────┐   │
│ │ Role Name    │ Time Allocation │ Cost     │ [×]   │   │
│ │ [Dropdown]   │ [Input] [hrs/d] │ $1,200   │       │   │
│ │ Developer    │ 40 hrs         │ $1,200   │ [×]   │   │
│ │ Designer     │ 5 days         │ $800     │ [×]   │   │
│ │ + Add Role                                        │   │
│ └───────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│ ┌─ Quote & Profit Analysis ─────────────────────────┐   │
│ │ Client Quote: $5,000                             │   │
│ │ Total Cost:   $2,000                             │   │
│ │ EBITDA Margin: 60% ($3,000)                     │   │
│ │                                                  │   │
│ │ Discount: [None] [%] [Fixed] [$100]             │   │
│ │ Final Quote: $4,900  |  Final Margin: 58%       │   │
│ └───────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│ [Save Configuration] [Export] [Reset]                   │
└─────────────────────────────────────────────────────────┘
```

### 6.2 Responsive Breakpoints

- **Desktop (>1024px):** Full table layout with all columns visible
- **Tablet (768-1024px):** Collapsed cost column, expandable details
- **Mobile (<768px):** Stack layout, one role per card

### 6.3 Visual States

**Normal State:**
- Clean table rows with clear visual hierarchy
- Subtle hover effects on interactive elements
- Currency formatting with proper alignment

**Calculation State:**
- Loading spinners during complex calculations
- Debounced input feedback
- Progressive calculation updates

**Error State:**
- Red border on invalid inputs
- Inline error messages below fields
- Summary error panel for critical issues

**Warning State:**
- Yellow/orange indicators for margin warnings
- Informational tooltips for guidance
- Preventive validation messages

## 7. Performance Requirements

### 7.1 Calculation Performance

- **Individual Row Updates:** <50ms per change
- **Total Recalculation:** <100ms for up to 20 roles
- **UI Updates:** <16ms for smooth 60fps interactions
- **Memory Usage:** <10MB for typical configurations

### 7.2 Optimization Strategies

```typescript
// Debounced input handling
const debouncedUpdate = debounce((allocation: RoleAllocation) => {
  updateConfiguration(allocation);
}, 300);

// Memoized calculations
$: totalCost = useMemo(() => 
  calculateTotalCost($configuration.roles), 
  [$configuration.roles]
);

// Virtual scrolling for large role lists
const virtualList = virtualizeRows(availableRoles, {
  itemHeight: 48,
  overscan: 5
});
```

## 8. Validation & Error Handling

### 8.1 Input Validation Rules

```typescript
interface ValidationRules {
  timeAllocation: {
    min: 0.1;
    max: 2000; // reasonable maximum hours
    precision: 1; // one decimal place
  };
  clientQuote: {
    min: 0;
    max: 10000000; // reasonable business limit
    precision: 2; // currency precision
  };
  discountPercentage: {
    min: 0;
    max: 100;
    precision: 2;
  };
  discountFixed: {
    min: 0;
    max: 'clientQuote'; // cannot exceed quote
    precision: 2;
  };
}
```

### 8.2 Error Types & Handling

```typescript
enum ValidationErrorType {
  INVALID_TIME_ALLOCATION = 'invalid_time_allocation',
  INVALID_QUOTE_AMOUNT = 'invalid_quote_amount',
  INVALID_DISCOUNT = 'invalid_discount',
  NEGATIVE_MARGIN = 'negative_margin',
  DUPLICATE_ROLE = 'duplicate_role',
  CALCULATION_ERROR = 'calculation_error'
}

interface ValidationError {
  type: ValidationErrorType;
  field: string;
  message: string;
  severity: 'error' | 'warning' | 'info';
}
```

## 9. Testing Strategy

### 9.1 Unit Tests

```typescript
// Component testing with Vitest + Testing Library
describe('RoleRow Component', () => {
  test('calculates cost correctly when time allocation changes');
  test('validates time allocation input ranges');
  test('emits update events with correct data');
  test('handles role selection changes');
  test('displays proper currency formatting');
});

describe('LOCAL-CALCULATION Integration', () => {
  test('real-time calculation performance under 100ms');
  test('calculation accuracy to 2 decimal places');
  test('handles edge cases (zero values, large numbers)');
});
```

### 9.2 E2E Tests

```typescript
// Playwright tests for user workflows
test('Complete configuration creation workflow', async ({ page }) => {
  // 1. Add roles to configuration
  // 2. Set time allocations
  // 3. Enter client quote
  // 4. Apply discount
  // 5. Verify calculations
  // 6. Save configuration
});
```

## 10. Integration Points

### 10.1 LOCAL-CALCULATION Component

```typescript
interface CalculationAPI {
  calculateRoleCost(allocation: RoleAllocation): number;
  calculateTotalCost(roles: RoleAllocation[]): number;
  calculateEBITDAMargin(revenue: number, cost: number): number;
  applyDiscount(quote: number, discount: Discount): number;
  validateConfiguration(config: TeamConfiguration): ValidationError[];
}
```

### 10.2 Future API Integration (Milestone 3)

```typescript
interface FutureAPIIntegration {
  // Will replace hardcoded rate card data
  fetchRateCard(): Promise<RateCardEntry[]>;
  saveConfiguration(config: TeamConfiguration): Promise<string>;
  loadConfiguration(id: string): Promise<TeamConfiguration>;
}
```

## 11. Implementation Checklist

### Phase 1: Core Structure
- [ ] Set up SvelteKit project with Skeleton UI
- [ ] Create basic component structure
- [ ] Implement TeamConfigTable with static data
- [ ] Add/remove row functionality
- [ ] Basic styling and responsive layout

### Phase 2: Calculations
- [ ] Integrate LOCAL-CALCULATION component
- [ ] Real-time cost calculations per row
- [ ] Total cost calculation and display
- [ ] EBITDA margin calculation
- [ ] Performance optimization for calculations

### Phase 3: Advanced Features
- [ ] Quote input and profit analysis
- [ ] Discount application (percentage and fixed)
- [ ] Configuration saving to localStorage
- [ ] Import/export functionality
- [ ] Input validation and error handling

### Phase 4: Polish & Testing
- [ ] Complete responsive design
- [ ] Accessibility compliance (WCAG 2.1)
- [ ] Comprehensive test coverage
- [ ] Performance testing and optimization
- [ ] User experience refinements

## 12. Success Criteria

- ✅ Add/remove team roles with intuitive UI
- ✅ Real-time cost calculations under 100ms
- ✅ Support 20+ roles without performance issues
- ✅ Accurate EBITDA margin calculations
- ✅ Discount application with impact preview
- ✅ Responsive design across device sizes
- ✅ Input validation with clear error messages
- ✅ Configuration persistence in browser session
- ✅ Clean, professional UI matching design standards
- ✅ Comprehensive test coverage (>90%)
