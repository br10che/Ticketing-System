# Τεχνική Τεκμηρίωση
## Ticketing System με Microsoft Power Platform

**Ομάδα 5**  
**Ημερομηνία:** Ιανουάριος 2025

---

## Μέλη Ομάδας και Κατανομή Ρόλων

| Όνομα | Ρόλος | Αρμοδιότητες |
|-------|-------|--------------|
| **Viola Sanyan** | Team Leader | Τεκμηρίωση, SharePoint, Λειτουργικότητα Canvas App |
| **Christos Polias** | UI/UX Designer | Σχεδιασμός Canvas App |
| **Loukas Sotiriadis** | Automation Specialist | Power Automate Flows, PowerPoint Presentation |
| **Georgios Stylianou** | Data Analyst | Power BI Dashboard |

---

## 1. Ανάλυση Απαιτήσεων

### 1.1 Σκοπός του Συστήματος

Το σύστημα Ticketing αναπτύχθηκε για να εξυπηρετήσει δύο κατηγορίες χρηστών:
- **Εξωτερικοί Χρήστες (Πελάτες)**: Δημιουργία και παρακολούθηση tickets
- **Εσωτερικό Προσωπικό (Staff)**: Διαχείριση, ανάθεση και επίλυση tickets

### 1.2 Βασικές Λειτουργικές Απαιτήσεις

**Για Πελάτες:**
- Δημιουργία νέου ticket με τίτλο, περιγραφή, κατηγορία και προτεραιότητα
- Προβολή των δικών τους tickets
- Επεξεργασία περιγραφής ticket (σε περίπτωση λάθους)
- Παρακολούθηση κατάστασης ticket

**Για Staff:**
- Προβολή όλων των tickets
- Φιλτράρισμα tickets βάσει κατάστασης
- Ανάθεση tickets σε μέλη του προσωπικού
- Ενημέρωση κατάστασης (New, In Progress, Resolved, Closed)
- Προσθήκη απαντήσεων/σχολίων για πελάτες
- Πρόσβαση σε αναλυτικά στοιχεία μέσω dashboard

### 1.3 Μη Λειτουργικές Απαιτήσεις

- User-friendly interface
- Γρήγορος χρόνος απόκρισης
- Ασφάλεια δεδομένων με έλεγχο δικαιωμάτων
- Responsive design για tablet και desktop

---

## 2. Αρχιτεκτονική Λύσης

### 2.1 Τεχνολογίες

| Τεχνολογία | Χρήση |
|------------|-------|
| **Power Apps (Canvas App)** | Frontend Interface |
| **SharePoint Online** | Database |
| **Power Automate** | Αυτοματισμοί |
| **Power BI** | Analytics Dashboard |

### 2.2 Δομή Δεδομένων (SharePoint List: "Tickets")

| Στήλη | Τύπος | Περιγραφή |
|-------|-------|-----------|
| **ID** | Number (Auto) | Μοναδικός αριθμός ticket |
| **Title** | Single line of text | Τίτλος ticket |
| **Description** | Multiple lines of text | Αναλυτική περιγραφή προβλήματος |
| **Category** | Choice | Technical, Account, Other |
| **Priority** | Choice | High, Medium, Low |
| **Status** | Choice | New, In Progress, Resolved, Closed |
| **CreatedByEmail** | Single line of text | Email δημιουργού |
| **AssignedTo** | Person or Group | Υπεύθυνο μέλος προσωπικού |
| **Created** | Date (Auto) | Ημερομηνία δημιουργίας |
| **ClosedDate** | Date | Ημερομηνία κλεισίματος |
| **Reply** | Multiple lines of text | Απάντηση από staff |

### 2.3 Δομή Canvas App

**Οθόνες (Screens):**

1. **Home Screen**
   - Κεντρική οθόνη επιλογής ρόλου
   - Τρία κουμπιά πλοήγησης: "Status of Tickets", "Create a New Ticket", "Check Analytics"

2. **Status Screen**
   - 5 κουμπιά φιλτραρίσματος με counters (All, New, In Progress, Closed, Resolved)
   - Gallery με λίστα tickets
   - Πληροφορίες: ID, Title, Priority, AssignedTo
   - OnClick → μεταφορά στο Update Ticket Screen

3. **Update Ticket Screen**
   - Αριστερή στήλη: Ticket details (ID, Created Date, Closed Date, Created By)
   - Δεξιά στήλη: Editable fields
   - Save button για αποθήκευση αλλαγών
   - Back button για επιστροφή

4. **Create New Ticket Screen**
   - Φόρμα με: Subject, Description, Category, Priority
   - Auto-fill: CreatedByEmail, Status
   - Submit button για δημιουργία ticket

---

## 3. Διάγραμμα Ροής

### 3.1 Ροή Δημιουργίας Ticket (Customer)

```
[Customer Opens App]
       ↓
[Home Screen - Click "Create New Ticket"]
       ↓
[Fill Form: Title, Description, Category, Priority]
       ↓
[Click Submit]
       ↓
[Patch to SharePoint]
       ↓
[Auto-set: Status=New, CreatedByEmail=User().Email]
       ↓
[Success Message]
       ↓
[Navigate to Status Screen]
```

### 3.2 Ροή Διαχείρισης Ticket (Staff)

```
[Staff Opens App]
       ↓
[Home Screen - Click "Status of Tickets"]
       ↓
[Status Screen - View All Tickets]
       ↓
[Filter by Status (Optional)]
       ↓
[Click on Ticket]
       ↓
[Update Ticket Screen Opens]
       ↓
[Edit: Status, Priority, Category, Add Reply]
       ↓
[Click Save]
       ↓
[Patch to SharePoint]
       ↓
[Success Message]
       ↓
[Navigate back to Status Screen]
```

### 3.3 Έλεγχος Δικαιωμάτων

```
[App OnStart]
       ↓
[Check: User().Email exists in StaffMembers list?]
       ↓
    YES → varIsStaff = true
       ↓
    [Staff Permissions: View All, Edit All]
       
       ↓
    NO → varIsStaff = false
       ↓
    [Customer Permissions: View Own, Edit Description Only]
```

---

## 4. Υλοποίηση

### 4.1 SharePoint Configuration

**Site:** Ticketing System App

**Lists:**
1. **Tickets** - Κύρια λίστα με όλα τα tickets
2. **StaffMembers** - Λίστα με emails των μελών προσωπικού

**Permissions:**
- Όλοι οι χρήστες: Read/Write access στο Tickets
- Μόνο admins: Manage StaffMembers list

### 4.2 Canvas App - Βασικές Μεταβλητές

| Μεταβλητή | Τύπος | Περιγραφή |
|-----------|-------|-----------|
| `varIsStaff` | Boolean | True αν ο χρήστης είναι staff |
| `varUserEmail` | Text | Email του τρέχοντος χρήστη |
| `varCurrentStatus` | Text | Επιλεγμένο φίλτρο κατάστασης |
| `varSelectedTicket` | Record | Επιλεγμένο ticket για προβολή/επεξεργασία |

### 4.3 Βασικές Λειτουργίες (Formulas)

**App OnStart:**
```powerfx
Set(
    varIsStaff,
    !IsBlank(
        LookUp(
            StaffMembers,
            Email = User().Email
        )
    )
);
Set(varUserEmail, User().Email);
Set(varCurrentStatus, "All");
```

**Gallery3 Items (Filter Tickets):**
```powerfx
Filter(
    If(varIsStaff, 
        tickets, 
        Filter(tickets, CreatedByEmail = varUserEmail)
    ),
    varCurrentStatus = "All" || Status.Value = varCurrentStatus
)
```

**Save Changes (Update Ticket)-Updated with AutoCloseddate:**
```powerfx
Patch(
    tickets,
    LookUp(tickets, ID = varSelectedTicket.ID),
    {
        Description: DescriptionTB.Text,
        Reply: StaffReplyTB.Text,
        Status: {Value: 'Status Dropdown'.Selected.Value},
        Priority: {Value: 'Priority Dropdown'.Selected.Value},
        Category: {Value: 'Category Dropdown'.Selected.Value},
        ClosedDate: If(
            'Status Dropdown'.Selected.Value = "Closed",
            Now(),
            Blank()
        )
    }
);
Notify("Ticket updated successfully!", NotificationType.Success);
Navigate('Status Screen', ScreenTransition.Fade)
```
**Επεξήγηση:** Η ClosedDate ενημερώνεται αυτόματα στην τρέχουσα ημερομηνία όταν το Status αλλάζει σε "Closed". Σε άλλες περιπτώσεις, παραμένει κενή (Blank).

**Create New Ticket:**
```powerfx
Patch(
    tickets,
    Defaults(tickets),
    {
        Title: TextInputSubject.Text,
        Description: DescriptionB.Text,
        Category: {Value: DepartmentDD.Selected.Value},
        Priority: {Value: PriorityDD.Selected.Value},
        Status: {Value: "New"},
        CreatedByEmail: User().Email
    }
);
Notify("Ticket created successfully!", NotificationType.Success);
Navigate('Status Screen', ScreenTransition.Fade)
```

### 4.4 Power Automate Flows

**Flow 1: Ειδοποίηση Δημιουργίας Ticket**
- Trigger: Όταν δημιουργείται νέο item στο Tickets
- Action: Αποστολή email στο staff team και τον πελάτη

**Flow 2: Ειδοποίηση Ανάθεσης**
- Trigger: Όταν το Reply ενημερώνεται
- Action: Αποστολή email στον πελάτη

**Flow 3: Ειδοποίηση Ανάθεσης**
- Trigger: Όταν το Status γίνεται Resolved
- Action: Αποστολή email στον πελάτη

### 4.5 Power BI Dashboard

#### 4.5.1 Σύνδεση με SharePoint

Το Power BI Dashboard συνδέθηκε απευθείας με το SharePoint list "Tickets" μέσω του SharePoint Online List connector:

**Βήματα Σύνδεσης:**
1. Power BI Desktop → Get Data → SharePoint Online List
2. SharePoint Site URL: `https://studentscycollegeac.sharepoint.com/sites/Ticketing_System_Group5`
3. Επιλογή του "Tickets" list
4. Load data στο Power BI

**Δεδομένα που φορτώθηκαν:**
- Όλες οι στήλες από το Tickets list
- Auto-refresh: Κάθε ώρα (όταν published στο Power BI Service)
- Tickets για testing και visualization

#### 4.5.2 Visualizations που Δημιουργήθηκαν

**1. Total Tickets Card**
- **Τύπος:** Card Visual
- **Μέτρηση:** Count of ID
- **Σκοπός:** Εμφάνιση συνολικού αριθμού tickets

**2. Open Tickets Card**
- **Τύπος:** Card Visual
- **Measure:** 
  ```dax
  Open Tickets = CALCULATE(COUNTROWS(Tickets), Tickets[Status] <> "Closed")
  ```
- **Σκοπός:** Tickets που χρειάζονται attention (New + In Progress + Resolved)

**3. Closed Tickets Card**
- **Τύπος:** Card Visual
- **Measure:**
  ```dax
  Closed Tickets = CALCULATE(COUNTROWS(Tickets), Tickets[Status] = "Closed")
  ```
- **Σκοπός:** Ολοκληρωμένα tickets

**4. Tickets by Status (Pie Chart)**
- **Τύπος:** Pie Chart
- **Legend:** Status
- **Values:** Count of ID
- **Σκοπός:** Κατανομή tickets ανά κατάσταση

**5. Priority Distribution (Bar Chart)**
- **Τύπος:** Clustered Bar Chart
- **Y-axis:** Priority (High, Medium, Low)
- **X-axis:** Count of ID
- **Σκοπός:** Visualization προτεραιοτήτων

**6. Category Breakdown (Column Chart)**
- **Τύπος:** Clustered Column Chart
- **X-axis:** Category (Account, Technical, Other)
- **Y-axis:** Count of ID
- **Σκοπός:** Ανάλυση κατηγοριών προβλημάτων
- **Insights:** Περισσότερα Technical tickets από Account/Other

**7. Tickets Over Time (Line Chart)**
- **Τύπος:** Line Chart
- **X-axis:** Created (Date)
- **Y-axis:** Count of ID
- **Σκοπός:** Trend analysis δημιουργίας tickets
- **Χρησιμότητα:** Εντοπισμός patterns και peak periods

#### 4.5.3 Dashboard Design

**Layout:**
```
┌────────────────────────────────────────────────────┐
│        TICKETING SYSTEM DASHBOARD                  │
├──────────────┬──────────────┬──────────────────────┤
│ Total        │ Open         │ Closed               │
│ Tickets: 4   │ Tickets: 2   │ Tickets: 2           │
├──────────────┴──────────────┴──────────────────────┤
│                                                     │
│  Tickets by Status         Priority Distribution   │
│  [Pie Chart]               [Bar Chart]             │
│                                                     │
├──────────────────────┬─────────────────────────────┤
│ Category Breakdown   │ Tickets Over Time           │
│ [Column Chart]       │ [Line Chart]                │
└──────────────────────┴─────────────────────────────┘
```
#### 4.5.4 Integration με Canvas App

**Μέθοδος Ενσωμάτωσης:** Launch Button

Λόγω περιορισμών του organizational account (embed codes disabled), η ενσωμάτωση έγινε με button που ανοίγει το dashboard σε νέο browser tab.

**Υλοποίηση στο Canvas App:**

**1. Launch Button Approach :**

Στο **Status Screen**, προστέθηκε button:

**Button Properties:**
- **Text:** "Check Analytics"
- **OnSelect:**
  ```powerfx
  Launch("https://app.powerbi.com/groups/me/reports/b3d1aa7d-76dd-48eb-a829-e3446994beef/9b20755960db78643840?experience=power-bi")
  ```
- **Visible:** 
  ```powerfx
  varIsStaff
  ```
  (Μόνο staff members βλέπουν το button)

**Πλεονεκτήματα αυτής της προσέγγισης:**
-  Ανοίγει το full Power BI interface με όλα τα features
-  Automatic data refresh από SharePoint
-  Professional solution που χρησιμοποιείται σε πολλούς οργανισμούς

#### 4.5.5 Data Refresh & Updates

**Automatic Refresh:**
- Power BI Service auto-refreshes data from SharePoint every 1 hour
- Manual refresh available στο Power BI Service
- Real-time updates όταν γίνονται αλλαγές στο SharePoint

**Data Flow:**
```
[User creates/updates ticket in Canvas App]
           ↓
[Patch to SharePoint List]
           ↓
[SharePoint stores data]
           ↓
[Power BI refreshes (max 1 hour delay)]
           ↓
[Dashboard shows updated analytics]
```

## 5. Testing & Quality Assurance

### 5.1 Test Scenarios

**Customer Flow:**
- Δημιουργία νέου ticket
- Προβολή μόνο των δικών του tickets
- Επεξεργασία περιγραφής
- Αδυναμία επεξεργασίας status/priority

**Staff Flow:**
- Προβολή όλων των tickets
- Φιλτράρισμα βάσει status
- Ανάθεση tickets
- Ενημέρωση status και priority
- Προσθήκη replies

**Data Integrity:**
- Tickets αποθηκεύονται σωστά στο SharePoint
- Auto-generated ID λειτουργεί
- Ενημερώσεις αποθηκεύονται άμεσα

### 5.2 Known Issues & Limitations

**Resolved:**
- ~~Status filter not working~~ (Fixed: Variable initialization)
- ~~SharePoint column naming~~ (Fixed: Corrected "Category" column name)
- ~~ClosedDate field not auto-populated~~ (Fixed: Added automatic population in Canvas App Save formula)


**Outstanding:**
- AssignedTo field δεν ενημερώνεται (Person column complexity)
- Attachments δεν υλοποιήθηκαν πλήρως

---

## 6. Μελλοντικές Βελτιώσεις

### 6.1 Υψηλής Προτεραιότητας

1. **Ανάθεση Tickets (AssignedTo Field)**
   - **Πρόβλημα:** Το Person/Group column του SharePoint δεν ενημερώνεται σωστά
   - **Λύση:** Χρήση του Office365Users connector ή μετατροπή σε text field

2. **Attachments Support**
   - **Πρόβλημα:** Attachments δεν εμφανίζονται στο app
   - **Λύση:** Προσθήκη Attachment Control στο Update Ticket Screen

3. **Power BI Dashboard Integration**
   - **Πρόβλημα:** Dashboard δεν έχει ενσωματωθεί
   - **Λύση:** Embed Power BI report σε νέα οθόνη

### 6.2 Μεσαίας Προτεραιότητας
4. **Search Functionality**
   - Αναζήτηση tickets με keywords
   - Φίλτρα: Ημερομηνία, κατηγορία, assigned to

5. **Ticket History/Audit Log**
   - Καταγραφή όλων των αλλαγών
   - Ποιος άλλαξε τι και πότε

### 6.3 Χαμηλής Προτεραιότητας

7. **SLA (Service Level Agreement) Tracking**
   - Αυτόματος υπολογισμός response time
   - Προειδοποιήσεις για tickets που πλησιάζουν deadline

8. **Customer Satisfaction Rating**
   - Rating system μετά το κλείσιμο ticket
   - Analytics για satisfaction scores

9. **Mobile App Version**
   - Βελτιστοποίηση για mobile (Phone layout)
   - Push notifications

10. **Multi-language Support**
    - Υποστήριξη Ελληνικών και Αγγλικών
    - User preference για γλώσσα

---

## 7. Deployment & Access

### 7.1 SharePoint Access

**Site URL:** `https://studentscycollegeac.sharepoint.com/sites/Ticketing_System_Group5`

**Lists:**
- Tickets: `/Lists/Tickets/AllItems.aspx`
- StaffMembers: `/Lists/StaffMembers/AllItems.aspx`

**Permissions για καθηγητή:**
- Full access στο SharePoint site
- View/Edit όλα τα lists

### 7.2 Canvas App Access

**App Name:** Ticketing System App

**Publishing:**
- Published version: Latest
- Share με: Καθηγητές, μέλη ομάδας

**Testing Accounts:**
- **Staff:** sanyan.viola2@gmail.com, 2025ccl88@students.cy.college.ac.cy, 2025ccl25@students.cy.college.ac.cy, 2025ccl118@students.cy.college.ac.cy
- **Customer:** Οποιοδήποτε student email

### 7.3 Power BI Dashboard Access

**Dashboard URL:** `https://app.powerbi.com/groups/me/reports/b3d1aa7d-76dd-48eb-a829-e3446994beef/9b20755960db78643840?experience=power-bi`

**Workspace:** My Workspace

**Report Name:** Ticketing System Dashboard

**Access Method:** 
- Μέσω Launch button στο Canvas App (Status Screen)
- Direct link sharing με authorized users
- Visible μόνο για staff members (varIsStaff = true)

**Shared With:**
- Teaching team (professors)
- Group members: Viola Sanyan, Christos Polias, Loukas Sotiriadis, Georgios Stylianou

**Permissions:**
- Viewer access για professors
- Editor access για group members

**Data Source:** 
- SharePoint List "Tickets"
- Auto-refresh: Every 1 hour
- Manual refresh available στο Power BI Service

**Visualizations:**
- 3 KPI Cards (Total, Open, Closed Tickets)
- 4 Charts (Status Pie, Priority Bar, Category Column, Time Line)

---

## 8. Συμπεράσματα

### 8.1 Επιτεύγματα

Η ομάδα υλοποίησε επιτυχώς:
-  Πλήρως λειτουργικό Canvas App με 4 οθόνες
- SharePoint database με test tickets
- Role-based access control (Staff vs Customer)
- Filtering και search capabilities
- CRUD operations (Create, Read, Update)
- Professional UI/UX design
- Power Automate flows για notifications
- Power BI Dashboard με 7 visualizations και real-time data integration

### 8.2 Μαθησιακά Αποτελέσματα

Μέσα από αυτό το project, η ομάδα:
- Εξοικειώθηκε με το Power Platform ecosystem
- Κατανόησε τη δομή των Canvas Apps
- Έμαθε SharePoint list management
- Εφάρμοσε formulas και logic στο Power Apps
- Συνεργάστηκε αποτελεσματικά σε agile περιβάλλον

### 8.3 Προκλήσεις που Αντιμετωπίστηκαν

1. **SharePoint Person Columns:** Η σύνδεση Person/Group columns με dropdowns ήταν πολύπλοκη
2. **Button Clickability:** DisplayMode properties προκαλούσαν θέματα
3. **Data Type Matching:** Choice columns χρειάζονταν `.Value` syntax
4. **Variable Initialization:** OnStart timing issues

### 8.4 Τελική Αξιολόγηση

Το σύστημα είναι **παραγωγικό και έτοιμο για χρήση** με μικρές βελτιώσεις που μπορούν να προστεθούν στο μέλλον. Καλύπτει όλες τις βασικές απαιτήσεις και παρέχει solid foundation για επέκταση.

---

## 9. Παραρτήματα

### 9.1 Χρήσιμα Links

- Power Apps Documentation: https://docs.microsoft.com/power-apps
- SharePoint Online: https://docs.microsoft.com/sharepoint
- Power Automate: https://docs.microsoft.com/power-automate
- Power BI: https://docs.microsoft.com/power-bi

### 9.2 Επικοινωνία Ομάδας

**Team Lead:** Viola Sanyan - sanyan.viola2@gmail.com

---

**Τέλος Τεχνικής Τεκμηρίωσης**

*Ημερομηνία Τελευταίας Ενημέρωσης: Ιανουάριος 2025*
