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

- Φιλικό περιβάλλον χρήσης (User-friendly interface)
- Γρήγορος χρόνος απόκρισης
- Ασφάλεια δεδομένων με έλεγχο δικαιωμάτων
- Responsive design για tablet και desktop

---

## 2. Αρχιτεκτονική Λύσης

### 2.1 Τεχνολογίες

| Τεχνολογία | Χρήση | Υλοποίηση |
|------------|-------|-----------|
| **Power Apps (Canvas App)** | Frontend Interface |  Ολοκληρωμένο |
| **SharePoint Online** | Database |  Ολοκληρωμένο |
| **Power Automate** | Αυτοματισμοί |  Ολοκληρωμένο |
| **Power BI** | Analytics Dashboard |  Σε εξέλιξη |

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
   - Δύο κουμπιά πλοήγησης: "Status of Tickets", "Create a New Ticket"

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

**Site:** Ticketing_System_Group5

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

**Save Changes (Update Ticket):**
```powerfx
Patch(
    tickets,
    LookUp(tickets, ID = varSelectedTicket.ID),
    {
        Description: DescriptionTB.Text,
        Reply: StaffReplyTB.Text,
        Status: {Value: 'Status Dropdown'.Selected.Value},
        Priority: {Value: 'Priority Dropdown'.Selected.Value},
        Category: {Value: 'Category Dropdown'.Selected.Value}
    }
);
Notify("Ticket updated successfully!", NotificationType.Success);
Navigate('Status Screen', ScreenTransition.Fade)
```

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
- Action: Αποστολή email στο staff team

**Flow 2: Ειδοποίηση Ανάθεσης**
- Trigger: Όταν το Reply ενημερώνεται
- Action: Αποστολή email στον πελάτη

### 4.5 Power BI Dashboard

**Σημείωση:** Το Power BI Dashboard βρίσκεται υπό ανάπτυξη από τον Georgios Stylianou.

**Προγραμματισμένα Visualizations:**
- Σύνολο tickets ανά κατάσταση (pie chart)
- Tickets ανά προτεραιότητα (bar chart)
- Tickets ανά κατηγορία (column chart)
- Μέσος χρόνος επίλυσης (KPI card)
- Trend line tickets ανά ημέρα

**Integration Plan:**
- Το dashboard θα ενσωματωθεί στο Canvas App μέσω Power BI Embedded
- Θα είναι ορατό μόνο για staff (varIsStaff = true)

---

## 5. Styling & User Experience

### 5.1 Χρωματική Παλέτα

- **Primary**: #0078D4 (Microsoft Blue)
- **Success**: #107C10 (Green - για Resolved)
- **Warning**: #ED8B00 (Amber - για Medium Priority)
- **Danger**: #C53030 (Dark Red - για High Priority)
- **Neutral**: #F8F9FA (Light Gray - backgrounds)

### 5.2 Status Color Coding

| Status | Χρώμα | Σημασία |
|--------|-------|---------|
| New | Orange | Νέο ticket, χρειάζεται ανάθεση |
| In Progress | Blue | Υπό επεξεργασία |
| Resolved | Green | Επιλύθηκε, περιμένει επιβεβαίωση |
| Closed | Gray | Ολοκληρώθηκε και κλείσιμο |

### 5.3 Priority Color Coding

| Priority | Χρώμα | Response Time Target |
|----------|-------|----------------------|
| High | Dark Red | < 4 ώρες |
| Medium | Amber | < 24 ώρες |
| Low | Green | < 48 ώρες |

---

## 6. Testing & Quality Assurance

### 6.1 Test Scenarios

**Customer Flow:**
-  Δημιουργία νέου ticket
-  Προβολή μόνο των δικών του tickets
-  Επεξεργασία περιγραφής
-  Αδυναμία επεξεργασίας status/priority

**Staff Flow:**
-  Προβολή όλων των tickets
-  Φιλτράρισμα βάσει status
-  Ανάθεση tickets
-  Ενημέρωση status και priority
-  Προσθήκη replies

**Data Integrity:**
-  Tickets αποθηκεύονται σωστά στο SharePoint
-  Auto-generated ID λειτουργεί
-  Ενημερώσεις αποθηκεύονται άμεσα

### 6.2 Known Issues & Limitations

**Resolved:**
- ~~Button clickability issues~~ (Fixed: DisplayMode property)
- ~~Status filter not working~~ (Fixed: Variable initialization)
- ~~SharePoint column naming~~ (Fixed: Corrected "Category" column name)

**Outstanding:**
- AssignedTo field δεν ενημερώνεται (Person column complexity)
- Attachments δεν υλοποιήθηκαν πλήρως

---

## 7. Μελλοντικές Βελτιώσεις

### 7.1 Υψηλής Προτεραιότητας

1. **Ανάθεση Tickets (AssignedTo Field)**
   - **Πρόβλημα:** Το Person/Group column του SharePoint δεν ενημερώνεται σωστά
   - **Λύση:** Χρήση του Office365Users connector ή μετατροπή σε text field
   - **Εκτίμηση:** 2-3 ώρες

2. **Attachments Support**
   - **Πρόβλημα:** Attachments δεν εμφανίζονται στο app
   - **Λύση:** Προσθήκη Attachment Control στο Update Ticket Screen
   - **Εκτίμηση:** 1-2 ώρες

### 7.2 Μεσαίας Προτεραιότητας

5. **Search Functionality**
   - Αναζήτηση tickets με keywords
   - Φίλτρα: Ημερομηνία, κατηγορία, assigned to
   - **Εκτίμηση:** 2 ώρες

6. **Ticket History/Audit Log**
   - Καταγραφή όλων των αλλαγών
   - Ποιος άλλαξε τι και πότε
   - **Εκτίμηση:** 4-5 ώρες

### 7.3 Χαμηλής Προτεραιότητας

7. **SLA (Service Level Agreement) Tracking**
   - Αυτόματος υπολογισμός response time
   - Προειδοποιήσεις για tickets που πλησιάζουν deadline
   - **Εκτίμηση:** 5-6 ώρες

8. **Customer Satisfaction Rating**
   - Rating system μετά το κλείσιμο ticket
   - Analytics για satisfaction scores
   - **Εκτίμηση:** 3-4 ώρες

10. **Multi-language Support**
    - Υποστήριξη Ελληνικών και Αγγλικών
    - User preference για γλώσσα
    - **Εκτίμηση:** 6-8 ώρες

---

## 8. Deployment & Access

### 8.1 SharePoint Access

**Site URL:** `https://studentscycollegeac.sharepoint.com/sites/Ticketing_System_Group5`

**Lists:**
- Tickets: `/Lists/Tickets/AllItems.aspx`
- StaffMembers: `/Lists/StaffMembers/AllItems.aspx`

**Permissions για καθηγητή:**
- Full access στο SharePoint site
- View/Edit όλα τα lists

### 8.2 Canvas App Access

**App Name:** TicketingSystem_Group5

**Publishing:**
- Published version: Latest
- Share με: Καθηγητές, μέλη ομάδας

**Testing Accounts:**
- **Staff:** sanyan.viola2@gmail.com, 2025ccl88@students.cy.college.ac.cy, 2025ccl25@students.cy.college.ac.cy, 2025ccl118@students.cy.college.ac.cy
- **Customer:** Οποιοδήποτε student email

### 8.3 Power BI Dashboard Access

**Workspace:** Ticketing_System_Group5 (όταν ολοκληρωθεί)

**Report:** TicketAnalytics

**Access:** Shared με καθηγητές

---

## 9. Συμπεράσματα

### 9.1 Μαθησιακά Αποτελέσματα

Μέσα από αυτό το project, η ομάδα:
- Εξοικειώθηκε με το Power Platform ecosystem
- Κατανόησε τη δομή των Canvas Apps
- Έμαθε SharePoint list management
- Εφάρμοσε formulas και logic στο Power Apps
- Συνεργάστηκε αποτελεσματικά σε agile περιβάλλον

### 9.2 Προκλήσεις που Αντιμετωπίστηκαν

1. **SharePoint Person Columns:** Η σύνδεση Person/Group columns με dropdowns ήταν πολύπλοκη
2. **Button Clickability:** DisplayMode properties προκαλούσαν θέματα
3. **Data Type Matching:** Choice columns χρειάζονταν `.Value` syntax
4. **Variable Initialization:** OnStart timing issues

### 9.3 Τελική Αξιολόγηση

Το σύστημα είναι **παραγωγικό και έτοιμο για χρήση** με μικρές βελτιώσεις που μπορούν να προστεθούν στο μέλλον. Καλύπτει όλες τις βασικές απαιτήσεις και παρέχει solid foundation για επέκταση.

---

## 10. Παραρτήματα

### 10.1 Χρήσιμα Links

- Power Apps Documentation: https://docs.microsoft.com/power-apps
- SharePoint Online: https://docs.microsoft.com/sharepoint
- Power Automate: https://docs.microsoft.com/power-automate
- Power BI: https://docs.microsoft.com/power-bi

### 10.2 Επικοινωνία Ομάδας

**Team Lead:** Viola Sanyan - sanyan.viola2@gmail.com

---

**Τέλος Τεχνικής Τεκμηρίωσης**

*Ημερομηνία Τελευταίας Ενημέρωσης: Ιανουάριος 2025*
