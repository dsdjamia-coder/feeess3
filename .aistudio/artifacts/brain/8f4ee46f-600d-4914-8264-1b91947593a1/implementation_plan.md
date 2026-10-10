# Compact Staff Categories with Comprehensive Field Editor & Collapsible Admin Sidebar

Enhance the Admin Panel with a space-saving, badge-style Staff Category manager with a full configuration modal for field selection, alongside a responsive expand/collapse sidebar that transitions between full labels and clean icon-only navigation.

## User Review & Critical Decisions

> [!IMPORTANT]
> The following user preferences were confirmed during clarification and are incorporated into this plan:
> - **Sidebar Default State on Desktop**: Starts **Expanded** (showing icons, section headers, and tab labels). The user can toggle between Expanded (w-64) and Collapsed (w-16 icon-only) anytime via the hamburger/collapse button.
> - **Staff Category Display**: Displayed as **Compact horizontal badges** with inline edit and delete action buttons, saving vertical screen space.
> - **Category Field Customization**: The Edit popup allows enabling, disabling (unselecting), configuring, and adding input fields specifically for that category.

---

## 1. Overview & Core Concept

- **What It Does**:
  1. **Compact Staff Category Management**: Replaces bulky category cards with lightweight horizontal badge pills containing direct edit/delete icon buttons. Clicking "Edit" launches a comprehensive modal to rename the category, modify display order, adjust public visibility, select/unselect active input fields (Photo, Role, Phone, Address, Email, etc.), and add new custom fields.
  2. **Collapsible Admin Navigation Sidebar**: Upgrades the admin sidebar from a hidden-only overlay drawer to a persistent desktop sidebar with smooth **Expand** (icons + titles + headers) and **Collapse** (icons-only rail) states, while retaining mobile drawer convenience.

- **Target Audience / Persona**: Institute administrators managing staff designations, field requirements, and daily operational navigation on desktop, tablet, and mobile screens.
- **Key Value**: Minimizes page clutter in Staff Management, gives complete control over dynamic staff profile input fields per category, and increases usable workspace area through a collapsible navigation rail.

---

## 2. User Experience & Visual Design

### Key User Flows

1. **Staff Category Compact View & Quick Actions**:
   - In Staff Management (`#manage-staff-page`), the "Staff Categories" section presents a slim creation bar and an auto-wrapping badge list.
   - Each category appears as a compact tag: e.g., `[ Teaching Staff · #1 ✏️ 🗑️ ]` with subtle emerald styling and clear action icons.
   - Clicking `🗑️` prompts quick confirmation and removes the category safely.
   - Clicking `✏️` immediately opens the `#edit-staff-category-popup` modal.

2. **Comprehensive Category Edit Modal**:
   - **Basic Properties**: Category Name input, Display Order number, and "Show in Public Directory" toggle.
   - **Field Checklist & Selection**: A clean interactive grid of available fields with checkboxes:
     - Core fields: Full Name, Staff Photo, Role/Subject, Phone Number, Address, Login Email.
     - Active fields are checked; unselected fields are excluded from that category's profile forms.
   - **Add New Custom Field**: Inline inputs inside the modal to specify a custom Field Label (e.g., "Vehicle Number", "Qualification") and Type (Text, Number, Date, Phone, Textarea), adding it dynamically to the checklist.
   - **Save & Apply**: Clicking "Save Changes" commits updates to Firestore (`webContent.directoryCategories`), re-renders the badges, and updates dynamic staff entry forms instantly.

3. **Collapsible Sidebar Flow**:
   - **Expanded Mode (Default, 256px / `w-64`)**: Displays the institution logo/name, section group headers, full tab labels, and action links.
   - **Collapsed Mode (64px / `w-16`)**: Shrinks smoothly to an icon-only navigation rail. Tab label text and section headers fade out gracefully; icons remain centered with hover tooltips for instant recognition.
   - **Toggle Trigger**: Clicking the hamburger icon in the top header toggles between expanded and collapsed states, persisting the user's choice in `localStorage`.
   - **Content Adaptation**: The main content canvas dynamically adjusts its left margin/padding (`lg:ml-64` vs `lg:ml-16`) with CSS transitions so no content is obscured.
   - **Mobile Behavior**: On small screens (<1024px), the sidebar functions as an overlay drawer with an animated backdrop to ensure mobile usability.

### Visual Identity & Theme Tokens

- **Dominant Neutral Canvas**: Clean slate-50 background (`#F8FAFC`) with white structural cards (`#FFFFFF`).
- **Accent Budget**: Emerald tones (`text-emerald-700`, `bg-emerald-50`, `border-emerald-200`) for category badges, Indigo/Blue (`#4F46E5`, `#2563EB`) for active navigation indicators and primary modal actions.
- **Micro-Interactions**: Smooth 200ms cubic transitions on sidebar width and label opacity; subtle hover scale and color shifts on category badge action icons.

---

## 3. Key Product Decisions & Trade-Offs

- **Decision 1: Compact Category Badges vs. Multi-Line Form Cards**
  - *Chosen Approach*: Horizontal wrapping badges displaying name, order, and inline action buttons, moving all field configuration into a dedicated popup.
  - *Why*: The previous implementation consumed excessive vertical height before the user could even reach the "Add New Staff" form. Badges take under 40px of vertical space.
  - *Alternatives Considered*: Accordion cards (still required multi-tier vertical spacing and cognitive overhead).

- **Decision 2: Modal-Based Field Selection (Checkbox Grid + Custom Adder)**
  - *Chosen Approach*: An interactive checklist inside the edit modal where users toggle which fields are active for that specific category, plus add custom fields.
  - *Why*: Fulfills the user's requirement to select and unselect needed fields without leaving the category workflow or navigating to separate tabs.

- **Decision 3: Desktop Persistent Collapsible Sidebar vs. Overlay Only**
  - *Chosen Approach*: Persistent sidebar on `lg:` screens that transitions between `w-64` and `w-16`, with `localStorage` state retention, while retaining overlay mode for mobile screens.
  - *Why*: Modern SaaS dashboard standard that gives users immediate one-click access across all 11+ admin modules while letting them reclaim screen width when working on dense tables.

---

## 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────────────────────┐
│                          Admin Layout Header                           │
│  [☰ Toggle Sidebar]   [🏫 Institute Title / Logo]   [Admin Actions]   │
└────────────────────────────────────────────────────────────────────────┘
          │                                              │
          ▼                                              ▼
┌───────────────────────────┐         ┌──────────────────────────────────┐
│      Side Navigation      │         │        Main Content Canvas       │
│  State: Expanded (w-64)   │         │    Margin: lg:ml-64 / lg:ml-16   │
│         Collapsed (w-16)  │         │                                  │
│                           │         │  ┌────────────────────────────┐  │
│  • Dashboard              │         │  │ Staff Management           │  │
│  • Academic Years         │         │  │                            │  │
│  • Staff Management       │────────▶│  │ [Compact Category Badges]  │  │
│  • Finance (Groups, Fees) │         │  │ [Teaching ✏️ 🗑️] [Drivers ✏️ 🗑️] │  │
│  • Website Management     │         │  └────────────────────────────┘  │
│  • System Settings        │         │                 │                │
└───────────────────────────┘         └─────────────────┼────────────────┘
                                                        ▼
                                      ┌──────────────────────────────────┐
                                      │  #edit-staff-category-popup      │
                                      │  • Category Name & Order         │
                                      │  • Checkbox Field Selector       │
                                      │    ☑ Name  ☑ Role  ☑ Photo       │
                                      │    ☐ Address  ☑ Phone            │
                                      │  • + Add Custom Field            │
                                      │  • [Save Changes]  [Cancel]      │
                                      └──────────────────────────────────┘
                                                        │
                                                        ▼
                                      ┌──────────────────────────────────┐
                                      │      Firestore webContent        │
                                      │   directoryCategories array      │
                                      └──────────────────────────────────┘
```

### State & Data Mapping

- **Directory Category Schema**:
  ```typescript
  interface DirectoryCategory {
    id: string;
    name: string;
    order: number;
    showPublic: boolean;
    fields: DirectoryField[];
  }

  interface DirectoryField {
    id: string;
    key: string;
    label: string;
    type: 'text' | 'number' | 'date' | 'phone' | 'textarea';
    required: boolean;
    active: boolean; // Controls whether this field is selected/used for this category
    order: number;
  }
  ```
- **Sidebar State Store**:
  - `localStorage.getItem('admin_sidebar_collapsed') === 'true'`
  - Toggled via `toggleSidebarCollapse()` which toggles `.sidebar-collapsed` on the `#admin-app` wrapper or sidebar container.

---

## Implementation Verification Plan

1. **Category Badge Rendering**:
   - Verify categories render as compact horizontal badges below the category adder.
   - Verify inline edit (`✏️`) and delete (`🗑️`) buttons respond correctly.
2. **Category Edit Popup**:
   - Verify clicking edit loads category name, order, public visibility, and fields into the modal.
   - Verify toggling/unselecting fields updates which fields are included.
   - Verify adding a custom field adds it to the list.
   - Verify saving commits to Firestore and immediately re-renders the badges and staff forms.
3. **Sidebar Collapse / Expand**:
   - Verify desktop view starts expanded (`w-64`) with icon + title.
   - Verify clicking the hamburger collapses sidebar to `w-16` (icons only) and main content smoothly widens.
   - Verify clicking again expands back to `w-64`.
   - Verify mobile drawer functionality remains intact.
4. **Code Quality & Build**:
   - Run `node --check` syntax verification and `compile_applet`.
