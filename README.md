### Αναφορά Βήματος 1: Μεταγλώττιση και Εγκατάσταση του XSBench

#### Υπολογιστικό Σύστημα

- **Επεξεργαστής:** Intel Core i5-4460
- **Αριθμός Πυρήνων:** 4 πυρήνες
- **Οργάνωση Κρυφών Μνημών:**
  - L1 Cache: 32KB ανά πυρήνα (data), 32KB ανά πυρήνα (instruction)
  - L2 Cache: 256KB ανά πυρήνα
  - L3 Cache: 6MB (shared)
- **Κύρια Μνήμη:** 8GB RAM
- **Λειτουργικό Σύστημα:** Ubuntu 24.04 LTS
- **Μεταγλωττιστής:** GCC 13.2.0

### Βήμα 2: Μελέτη της Κλιμάκωσης του XSBench

### Χρόνοι Εκτέλεσης

| Αριθμός Νημάτων | Χρόνος Εκτέλεσης (s) | Lookups | Lookups/s |
|-----------------|----------------------|---------|-----------|
| 1               | 51.508               | 17,000,000 | 330,047   |
| 2               | 27.213               | 17,000,000 | 624,693   |
| 4               | 16.267               | 17,000,000 | 1,045,050 |

### Παρουσίαση Αποτελεσμάτων

| Αριθμός Νημάτων | Χρόνος Εκτέλεσης (s) | Επιτάχυνση |
|-----------------|----------------------|------------|
| 1               | 51.508               | 1.00       |
| 2               | 27.213               | 1.89       |
| 4               | 16.267               | 3.17       |

### Συμπεράσματα

Από τα αποτελέσματα των πειραμάτων, παρατηρούμε ότι η επιτάχυνση αυξάνεται καθώς αυξάνεται ο αριθμός των νημάτων, αλλά δεν είναι γραμμική. Αυτό μπορεί να οφείλεται σε παράγοντες όπως ο περιορισμός της μνήμης cache ή η επικοινωνία μεταξύ των πυρήνων.

Η αύξηση της απόδοσης με περισσότερα νήματα είναι σημαντική, ωστόσο παρατηρούμε ότι η επιτάχυνση δεν είναι τέλεια γραμμική, κάτι που είναι αναμενόμενο λόγω των επιπτώσεων της πολυνηματικής εκτέλεσης στους κοινούς πόρους του συστήματος, όπως η μνήμη cache και οι TLBs.


### Αναφορά Βήματος 3: Μελέτη της Συμπεριφοράς του XSBench

#### Πειραματικά Δεδομένα

| Μετρική                        | 1 Νήμα                | 2 Νήματα              | 4 Νήματα              |
|-------------------------------|-----------------------|-----------------------|-----------------------|
| **Runtime (seconds)**         | 54.317                | 31.745                | 24.401                |
| **Instructions**              | 88,462,258,422        | 88,446,104,032        | 89,542,893,453        |
| **Branches**                  | 9,334,855,695         | 9,331,034,110         | 9,527,185,400         |
| **Branch Misses**             | 317,077,817 (3.40%)   | 317,099,582 (3.40%)   | 324,940,725 (3.41%)   |
| **Cache References**          | 4,583,999,857         | 4,577,027,226         | 4,642,039,416         |
| **Cache Misses**              | 3,281,945,525 (71.60%)| 3,289,755,224 (71.88%)| 3,402,715,633 (73.30%)|
| **L1 Data Cache Loads**       | 18,495,389,931        | 18,502,747,795        | 18,767,793,178        |
| **L1 Data Cache Load Misses** | 5,201,900,399 (28.13%)| 5,216,466,669 (28.19%)| 5,290,535,888 (28.19%)|
| **L1 Instruction Cache Load Misses** | 15,717,732     | 14,845,855            | 34,524,438            |
| **Data TLB Loads**            | 18,500,930,918        | 18,508,966,411        | 18,772,314,623        |
| **Data TLB Load Misses**      | 1,332,640,581 (7.20%) | 1,339,916,049 (7.24%) | 1,372,055,106 (7.31%) |
| **Instruction TLB Loads**     | 4,394,993             | 4,306,003             | 4,940,370             |
| **Instruction TLB Load Misses** | 5,994,726 (136.40%) | 6,156,592 (142.98%)   | 3,848,348 (77.90%)    |

#### Συμπεράσματα

Τα αποτελέσματα από το `perf stat` δείχνουν τα εξής:

1. **Branch Misses:** Το ποσοστό των branch misses είναι σταθερό γύρω στο 3.40%, δείχνοντας καλή απόδοση της μονάδας πρόβλεψης διακλαδώσεων.
2. **Cache Misses:** Το ποσοστό των cache misses είναι υψηλό (71.60% - 73.30%), υποδεικνύοντας ότι το XSBench έχει σημαντικές απαιτήσεις μνήμης που δεν καλύπτονται πλήρως από την cache.
3. **L1 Data Cache Load Misses:** Το ποσοστό των L1 data cache load misses είναι σταθερό (28.13% - 28.19%), δείχνοντας ότι περίπου το 1/4 των φορτώσεων στην L1 cache αποτυγχάνουν και καταφεύγουν σε χαμηλότερες μνήμες.
4. **Data TLB Load Misses:** Το ποσοστό των data TLB load misses είναι περίπου 7.20% - 7.31%, υποδεικνύοντας σχετικά καλή αποδοτικότητα της TLB.
5. **Instruction TLB Load Misses:** Το ποσοστό των instruction TLB load misses είναι εξαιρετικά υψηλό για 1 και 2 νήματα (136.40%, 142.98%), αλλά μειώνεται σημαντικά στα 4 νήματα (77.90%).

### Προτεινόμενες Ενέργειες

- **Βελτιστοποίηση Κώδικα:** Επανεξέταση και βελτιστοποίηση του κώδικα του XSBench για καλύτερη χρήση της κρυφής μνήμης.
- **Ανάλυση και Βελτιστοποίηση TLB:** Διερεύνηση της χρήσης μεγάλων σελίδων μνήμης (huge pages) για μείωση των TLB misses.
- **Αύξηση της Χωρητικότητας της Κρυφής Μνήμης:** Εξέταση της δυνατότητας χρήσης συστημάτων με μεγαλύτερη χωρητικότητα κρυφής μνήμης.


### Βήμα 4: Μελέτη του Ανταγωνισμού στους Κοινόχρηστους Πόρους

#### Εκτέλεση Πειραμάτων με Διαφορετικά Μεγέθη Μνήμης και 1000 Επαναλήψεις

Κατά τη διάρκεια των πειραμάτων, εκτελέσαμε το πρόγραμμα `memory_stress_lim` με τρία διαφορετικά μεγέθη μνήμης: 100MB, 200MB και 400MB, με 1000 επαναλήψεις. Συλλέξαμε τις μετρικές απόδοσης χρησιμοποιώντας το εργαλείο `perf stat`.

### Αποτελέσματα Πειραμάτων

| Μετρική                        | 100MB, 1000 επαναλήψεις       | 200MB, 1000 επαναλήψεις       | 400MB, 1000 επαναλήψεις       |
|-------------------------------|-------------------------------|-------------------------------|-------------------------------|
| Instructions                  | 371,618,808,066               | 829,254,064,969               | 1,677,591,404,880             |
| Branches                      | 23,275,737,674                | 51,952,989,551                | 105,091,051,477               |
| Branch Misses                 | 1,080,488 (0.00%)             | 2,571,387 (0.00%)             | 5,057,704 (0.00%)             |
| Cache References              | 84,411,618                    | 192,421,725                   | 386,599,886                   |
| Cache Misses                  | 52,993,660 (62.78%)           | 119,616,000 (62.16%)          | 238,587,594 (61.71%)          |
| L1 Data Cache Loads           | 185,709,284,906               | 414,467,387,218               | 838,408,207,070               |
| L1 Data Cache Load Misses     | 1,469,622,897 (0.79%)         | 3,280,603,660 (0.79%)         | 6,633,917,674 (0.79%)         |
| L1 Instruction Cache Load Misses | 9,846,849                 | 23,382,973                    | 47,753,782                    |
| Data TLB Loads                | 185,702,658,455               | 414,417,874,641               | 838,322,109,373               |
| Data TLB Load Misses          | 89,440 (0.00%)                | 303,865 (0.00%)               | 405,104 (0.00%)               |
| Instruction TLB Loads         | 11,020                        | 9,966                         | 57,368                        |
| Instruction TLB Load Misses   | 175,404 (1591.69%)            | 175,735 (1763.35%)            | 317,744 (553.87%)             |
| Χρόνος Εκτέλεσης              | 58.058049817 s                | 128.131886499 s               | 259.260150011 s               |

### Ανάλυση Αποτελεσμάτων

#### L1 Data Cache Load Misses
Το ποσοστό των αστοχιών της L1 data cache παραμένει σταθερό στο 0.79% ανεξάρτητα από το μέγεθος της μνήμης. Αυτό είναι αναμενόμενο, καθώς η L1 cache είναι μικρή και περιορισμένη σε μέγεθος. Ο αυξημένος όγκος δεδομένων δεν επηρεάζει άμεσα την L1 cache, καθώς οι περισσότερες προσβάσεις μπορούν να εξυπηρετηθούν από αυτήν.

#### Cache Misses
Το ποσοστό των cache misses κυμαίνεται γύρω στο 62-63%. Αυτό δείχνει ότι η εφαρμογή προκαλεί υψηλό φόρτο στη μνήμη cache, με πολλές προσβάσεις να μην μπορούν να εξυπηρετηθούν από τις κρυφές μνήμες. Αυτή η συμπεριφορά είναι αναμενόμενη για προγράμματα που χρησιμοποιούν μεγάλες περιοχές μνήμης, καθώς οι κρυφές μνήμες έχουν περιορισμένο μέγεθος.

#### TLB Load Misses
Οι Data TLB load misses παραμένουν εξαιρετικά χαμηλές, υποδεικνύοντας καλή αποδοτικότητα στη μετάφραση των διευθύνσεων δεδομένων. Ωστόσο, οι Instruction TLB load misses είναι πολύ υψηλές, ειδικά για τα μεγαλύτερα μεγέθη μνήμης, υποδεικνύοντας ότι η TLB αντιμετωπίζει σημαντικό φόρτο κατά τη μετάφραση των διευθύνσεων εντολών.

### Συμπεράσματα
Από τα παραπάνω αποτελέσματα, καταλήγουμε στα εξής συμπεράσματα:

- Η απόδοση του προγράμματος `memory_stress_lim` είναι εντατική στη χρήση της μνήμης, προκαλώντας υψηλά ποσοστά cache misses. Αυτό είναι αναμενόμενο για προγράμματα που χρησιμοποιούν μεγάλες περιοχές μνήμης.
- Ο μηχανισμός πρόβλεψης διακλαδώσεων είναι πολύ αποδοτικός, με εξαιρετικά χαμηλά ποσοστά branch misses.
- Οι αστοχίες της L1 data cache παραμένουν χαμηλές, δείχνοντας ότι η L1 cache εξυπηρετεί τις περισσότερες προσβάσεις.
- Οι υψηλές Instruction TLB load misses για τα μεγαλύτερα μεγέθη μνήμης δείχνουν ότι η TLB αντιμετωπίζει αυξημένο φόρτο κατά τη μετάφραση των διευθύνσεων εντολών.

### Παρουσίαση Αποτελεσμάτων

| Σενάριο                              | Χρόνος Εκτέλεσης (s) | Instructions       | Branches          | Branch Misses     | Cache References | Cache Misses     | L1 DCache Loads  | L1 DCache Load Misses | L1 ICache Load Misses | dTLB Loads       | dTLB Load Misses | iTLB Loads       | iTLB Load Misses  |
|--------------------------------------|----------------------|--------------------|-------------------|-------------------|------------------|------------------|------------------|------------------------|------------------------|------------------|------------------|------------------|-------------------|
| Microbenchmark μόνο του              | 37.039               | 216,627,957,856    | 13,578,469,769    | 1,173,538 (0.01%) | 62,181,200       | 40,373,081 (64.93%) | 108,235,438,348  | 858,526,857 (0.79%)  | 7,412,188            | 108,231,604,814  | 270,699 (0.00%)  | 35,102           | 95,978 (273.43%)  |
| XSBench μόνο του                     | 94.672               | 96,152,744,880     | 10,813,080,390    | 341,072,370 (3.15%) | 5,122,876,414    | 3,744,246,835 (73.09%) | 20,313,506,443   | 5,513,288,456 (27.14%) | 193,856,909          | 20,303,539,318   | 1,563,726,967 (7.70%) | 6,052,044        | 5,793,853 (95.73%) |
| XSBench + microbenchmark (ίδιος πυρήνας) | 165.222           | 88,824,193,376     | 9,399,799,249     | 319,291,630 (3.40%) | 4,800,870,379    | 3,859,007,453 (80.38%) | 18,601,463,026   | 5,375,802,715 (28.90%) | 29,640,335           | 18,594,247,951   | 1,595,913,473 (8.58%) | 4,867,657        | 5,279,081 (108.45%) |
| XSBench + microbenchmark (διαφορετικός πυρήνας) | 91.287           | 88,696,021,719     | 9,377,704,517     | 318,002,965 (3.39%) | 4,742,405,012    | 3,916,261,369 (82.58%) | 18,562,545,578   | 5,376,350,616 (28.96%) | 21,726,294          | 18,563,564,433   | 1,787,066,414 (9.63%) | 5,099,690        | 3,536,216 (69.34%)  |

### Συμπεράσματα

Από τα αποτελέσματα των πειραμάτων, παρατηρούμε ότι η συνεκτέλεση του XSBench με το microbenchmark στον ίδιο πυρήνα επηρεάζει σημαντικά την απόδοσή του, ενώ η εκτέλεση σε διαφορετικούς πυρήνες μειώνει τον ανταγωνισμό στους κοινόχρηστους πόρους, αλλά δεν τον εξαλείφει πλήρως. Οι διαφορές στις μετρικές cache misses και TLB load misses είναι αξιοσημείωτες και υποδεικνύουν την επίδραση του ανταγωνισμού στη μνήμη cache και τις TLBs.

### Συμπεράσματα

Από τα αποτελέσματα των πειραμάτων, παρατηρούμε τα εξής:

1. **Ανταγωνισμός στον Ίδιο Πυρήνα:**
   - Η συνεκτέλεση του XSBench με το microbenchmark στον ίδιο πυρήνα επηρεάζει σημαντικά την απόδοση του XSBench, αυξάνοντας τον χρόνο εκτέλεσης κατά σχεδόν 75% σε σχέση με την εκτέλεση του XSBench μόνο του.
   - Ο ανταγωνισμός για την μνήμη cache και τους TLBs είναι έντονος, όπως φαίνεται από την αύξηση των cache misses και των TLB load misses.

2. **Ανταγωνισμός σε Διαφορετικούς Πυρήνες:**
   - Η εκτέλεση των προγραμμάτων σε διαφορετικούς πυρήνες μειώνει τον ανταγωνισμό και βελτιώνει την απόδοση σε σχέση με την συνεκτέλεση στον ίδιο πυρήνα, αλλά ο χρόνος εκτέλεσης του XSBench παραμένει αυξημένος κατά περίπου 5% σε σχέση με την εκτέλεση του μόνο του.
   - Οι επιδόσεις της μνήμης cache παραμένουν επιβαρυμένες, υποδεικνύοντας ότι ο ανταγωνισμός στους κοινούς πόρους (π.χ. L3 cache) εξακολουθεί να υφίσταται.

3. **Επιδράσεις στην Μνήμη Cache και TLBs:**
   - Τα υψηλά ποσοστά cache misses και TLB load misses υποδεικνύουν ότι η εφαρμογή XSBench έχει σημαντικές απαιτήσεις μνήμης, οι οποίες δεν μπορούν να καλυφθούν πλήρως από την υπάρχουσα αρχιτεκτονική της μνήμης cache.

### Συνολική Αναφορά για τη Μελέτη του Ανταγωνισμού στους Κοινόχρηστους Πόρους

Σε αυτό το βήμα, ποσοτικοποιήσαμε τον αντίκτυπο της συνεκτέλεσης εφαρμογών στο ίδιο πολυπύρηνο επεξεργαστικό σύστημα. Εκτελέσαμε το XSBench με ένα νήμα και το microbenchmark `memory_stress` σε ατέρμονο βρόχο, σε τρία διαφορετικά σενάρια και τρία διαφορετικά μεγέθη μνήμης (100MB, 200MB, 400MB).

### Σενάρια Πειραμάτων

1. **Σενάριο 1:** Το XSBench τρέχει μόνο του στο σύστημα.
2. **Σενάριο 2:** Το XSBench τρέχει μαζί με το `memory_stress` στον ίδιο πυρήνα.
3. **Σενάριο 3:** Το XSBench τρέχει σε διαφορετικό πυρήνα από το `memory_stress`.

### Αποτελέσματα Πειραμάτων

| Σενάριο                          | Μνήμη (MB) | Χρόνος Εκτέλεσης XSBench (s) | Lookups/s |
|----------------------------------|------------|------------------------------|-----------|
| XSBench μόνο του                 | -          | 66.537                       | 255,498   |
| XSBench + memory_stress (ίδιος πυρήνας) | 100        | 110.555                      | 153,769   |
|                                  | 200        | 108.252                      | 157,040   |
|                                  | 400        | 109.623                      | 155,076   |
| XSBench + memory_stress (διαφορετικός πυρήνας) | 100        | 60.523                       | 280,883   |
|                                  | 200        | 60.198                       | 282,400   |
|                                  | 400        | 56.904                       | 298,746   |

### Ανάλυση Αποτελεσμάτων

#### Σενάριο 1: Το XSBench τρέχει μόνο του στο σύστημα

Αυτό το σενάριο χρησιμοποιείται ως σημείο αναφοράς. Ο χρόνος εκτέλεσης του XSBench είναι 66.537 δευτερόλεπτα με 255,498 lookups/s.

#### Σενάριο 2: Το XSBench τρέχει μαζί με το `memory_stress` στον ίδιο πυρήνα

1. **100 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 110.555 δευτερόλεπτα
   - Lookups/s: 153,769
   - Ο χρόνος εκτέλεσης αυξήθηκε σημαντικά λόγω του ανταγωνισμού στους κοινόχρηστους πόρους (cache, TLB, κλπ.).

2. **200 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 108.252 δευτερόλεπτα
   - Lookups/s: 157,040
   - Η απόδοση είναι παρόμοια με το 100 MB, με μικρές διακυμάνσεις λόγω της επιπλέον μνήμης.

3. **400 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 109.623 δευτερόλεπτα
   - Lookups/s: 155,076
   - Η απόδοση παραμένει σχεδόν σταθερή σε σύγκριση με τα 100 MB και 200 MB, υποδεικνύοντας ότι ο ανταγωνισμός πόρων επηρεάζει τη συνολική απόδοση.

#### Σενάριο 3: Το XSBench τρέχει σε διαφορετικό πυρήνα από το `memory_stress`

1. **100 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 60.523 δευτερόλεπτα
   - Lookups/s: 280,883
   - Η απόδοση του XSBench βελτιώνεται σημαντικά όταν τρέχει σε διαφορετικό πυρήνα από το `memory_stress`, λόγω του μειωμένου ανταγωνισμού στους πόρους.

2. **200 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 60.198 δευτερόλεπτα
   - Lookups/s: 282,400
   - Η απόδοση παραμένει σχεδόν σταθερή σε σύγκριση με τα 100 MB.

3. **400 MB Μνήμης:**
   - Χρόνος εκτέλεσης: 56.904 δευτερόλεπτα
   - Lookups/s: 298,746
   - Η απόδοση βελτιώνεται ακόμη περισσότερο, υποδεικνύοντας ότι η διαχείριση πόρων είναι πιο αποτελεσματική όταν το XSBench τρέχει σε διαφορετικό πυρήνα από το `memory_stress`.

### Συμπεράσματα

- **Σενάριο 1:** Το XSBench τρέχει με τη μέγιστη απόδοση όταν δεν υπάρχουν άλλες διεργασίες που καταναλώνουν πόρους.
- **Σενάριο 2:** Η συνεκτέλεση του XSBench με το `memory_stress` στον ίδιο πυρήνα προκαλεί σημαντική μείωση στην απόδοση του XSBench λόγω του ανταγωνισμού στους κοινόχρηστους πόρους. Ο αντίκτυπος αυτός είναι εμφανής ανεξάρτητα από το μέγεθος της μνήμης.
- **Σενάριο 3:** Η συνεκτέλεση του XSBench με το `memory_stress` σε διαφορετικούς πυρήνες βελτιώνει την απόδοση σε σχέση με το Σενάριο 2, λόγω του μειωμένου ανταγωνισμού στους πόρους. Η επίδοση είναι καλύτερη όταν το `memory_stress` χρησιμοποιεί μεγαλύτερα μεγέθη μνήμης, καθώς οι πόροι κατανέμονται πιο αποτελεσματικά.
