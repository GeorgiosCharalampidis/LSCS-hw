#### Ονοματεπώνυμο: Χαραλαμπίδης Γεώργιος
#### Αριθμός Μητρώου: 1115201600193

### Υπολογιστικό Σύστημα

Για την εκτέλεση των πειραμάτων, αρχικά προσπάθησα να χρησιμοποιήσω το κεντρικό μου σύστημα, ένα Lenovo Legion με επεξεργαστή Ryzen 7 4800H και 16GB RAM. Ωστόσο, λόγω της εγκατάστασης του Ubuntu σε VM και όχι natively (υποθέτω;) , αντιμετώπισα προβλήματα κατά την εκτέλεση του εργαλείου perf, όπως τα εξής:

- `<not supported>      branch-instructions`
- `<not supported>      branch-misses`
- `<not supported>      cache-references`
- `<not supported>      cache-misses`
- `<not supported>      instructions`
- `<not supported>      cycles`
- `<not supported>      LLC-loads`
- `<not supported>      LLC-load-misses`

Προσπάθησα να χρησιμοποιήσω το εργαλείο της AMD, uProf, αλλά δεν έβγαλα καμία άκρη. Συνεπώς, αποφάσισα να κάνω μια νέα εγκατάσταση του Ubuntu 24.04 LTS σε ένα παλαιότερο μηχάνημα που είχα, για να τρέξω τα πειράματα χωρίς τη χρήση VM. Παρόλο που οι τεχνικές προδιαγραφές του συστήματος είναι περιορισμένες, η perf λειτουργεί σωστά..

Τα χαρακτηριστικά του υπολογιστικού συστήματος που χρησιμοποιήθηκε για τα πειράματα είναι τα εξής:

- **Επεξεργαστής:** Intel Core i5-4460
- **Αριθμός Πυρήνων:** 4 πυρήνες
- **Οργάνωση Κρυφών Μνημών:**
  - L1 Cache: 32KB ανά πυρήνα (data), 32KB ανά πυρήνα (instruction)
  - L2 Cache: 256KB ανά πυρήνα
  - L3 Cache: 6MB (shared)
- **Κύρια Μνήμη:** 8GB RAM
- **Λειτουργικό Σύστημα:** Ubuntu 24.04 LTS
- **Μεταγλωττιστής:** GCC 13.2.0

### Αναφορά Βήματος 1: Μεταγλώττιση και Εγκατάσταση του XSBench

### Εκτέλεση του XSBench με τα default values

Εκτελέσαμε το XSBench με τα default values.

```c
giorgio@giorgio-Z97P-D3:~/Documents/XSBench/openmp-threading$ ./XSBench
================================================================================
                   __   __ ___________                 _                        
                   \ \ / //  ___| ___ \               | |                       
                    \ V / \ `--.| |_/ / ___ _ __   ___| |__                     
                    /   \  `--. \ ___ \/ _ \ '_ \ / __| '_ \                    
                   / /^\ \/\__/ / |_/ /  __/ | | | (__| | | |                   
                   \/   \/\____/\____/ \___|_| |_|\___|_| |_|                   

================================================================================
                    Developed at Argonne National Laboratory
                                   Version: 20
================================================================================
                                  INPUT SUMMARY
================================================================================
Simulation Method:            History Based
Grid Type:                    Unionized Grid
Materials:                    12
H-M Benchmark Size:           large
Total Nuclides:               355
Gridpoints (per Nuclide):     11,303
Unionized Energy Gridpoints:  4,012,565
Particle Histories:           500,000
XS Lookups per Particle:      34
Total XS Lookups:             34
Threads:                      4
Est. Memory Usage (MB):       5,649
Binary File Mode:             Off
================================================================================
                         INITIALIZATION - DO NOT PROFILE
================================================================================
Intializing nuclide grids...
Intializing unionized grid...
Intializing material data...
Intialization complete. Allocated 5648 MB of data.

================================================================================
                                   SIMULATION
================================================================================
Beginning history based simulation...

Simulation complete.
================================================================================
                                     RESULTS
================================================================================
Threads:     4
Runtime:     24.544 seconds
Lookups:     17,000,000
Lookups/s:   692,636
Verification checksum: 954318 (Valid)
================================================================================
```

### Βήμα 2: Μελέτη της Κλιμάκωσης του XSBench

`run_xsbench.sh`

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

`run_perf_xsbench.sh`

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


### Βήμα 4: Μελέτη του Ανταγωνισμού στους Κοινόχρηστους Πόρους

#### Δημιουργία προγραμμάτων memory_stress και memory_stress_lim

### Βήμα 4: Μελέτη του Ανταγωνισμού στους Κοινόχρηστους Πόρους

#### Προγράμματα C

Δημιουργήσαμε δύο προγράμματα σε γλώσσα C με στόχο να στρεσάρουν το σύστημα μνήμης του επεξεργαστή μας: `memory_stress.c` και `memory_stress_lim.c`. 

Το πρόγραμμα `memory_stress.c` δέχεται ως είσοδο ένα όρισμα για το μέγεθος της μνήμης που θα δεσμεύσει και εκτελεί ατέρμονα έναν βρόχο που διαβάζει και επεξεργάζεται τα δεδομένα στη μνήμη. 

Το πρόγραμμα `memory_stress_lim.c` δέχεται ως είσοδο το μέγεθος της μνήμης και τον αριθμό των επαναλήψεων που θα εκτελέσει ο βρόχος, επιτρέποντας τον καθορισμό ενός συγκεκριμένου αριθμού επαναλήψεων για το στρεσάρισμα της μνήμης.

```c
// memory_stress.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <signal.h>
#include <unistd.h>
#include <time.h>

volatile int keepRunning = 1;

void intHandler(int dummy) {
    keepRunning = 0;
}

void initialize_memory(int *arr, size_t size) {
    for (size_t i = 0; i < size / sizeof(int); i++) {
        arr[i] = rand();
    }
}

void stress_memory(int *arr, size_t size) {
    while (keepRunning) {
        for (size_t i = 0; i < size / sizeof(int); i++) {
            arr[i] = arr[i] * 2;
        }
    }
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Usage: %s <size_in_megabytes>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    size_t size = atoi(argv[1]) * 1024 * 1024;
    int *arr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);

    if (arr == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }

    srand(time(NULL));
    initialize_memory(arr, size);

    signal(SIGINT, intHandler);
    stress_memory(arr, size);

    if (munmap(arr, size) == -1) {
        perror("munmap");
        exit(EXIT_FAILURE);
    }

    return 0;
}
```

```c
// memory_stress_lim.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <unistd.h>
#include <time.h>

void initialize_memory(int *arr, size_t size) {
    for (size_t i = 0; i < size / sizeof(int); i++) {
        arr[i] = rand();
    }
}

void stress_memory(int *arr, size_t size, int iterations) {
    for (int j = 0; j < iterations; j++) {
        for (size_t i = 0; i < size / sizeof(int); i++) {
            arr[i] = arr[i] * 2;
        }
    }
}

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <size_in_megabytes> <iterations>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    size_t size = atoi(argv[1]) * 1024 * 1024;
    int iterations = atoi(argv[2]);
    int *arr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);

    if (arr == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }

    srand(time(NULL));
    initialize_memory(arr, size);
    stress_memory(arr, size, iterations);

    if (munmap(arr, size) == -1) {
        perror("munmap");
        exit(EXIT_FAILURE);
    }

    return 0;
}
```
#### BHMA 4A Εκτέλεση memory_stress_lim με Διαφορετικά Μεγέθη Μνήμης και 1000 Επαναλήψεις

`memory_stress_lim_perf.sh`

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

#### ΒΗΜΑ 4Β Αποτελέσματα Πειραμάτων XSBench (3 Διαφορετικά Σενάρια)

`memory_xsbench_same_core.sh` και `memory_xsbench_different_cores.sh`

Παρακάτω παρουσιάζονται τα αποτελέσματα των πειραμάτων για τα διάφορα σενάρια και μεγέθη μνήμης.

| Σενάριο                              | Μνήμη (MB) | Χρόνος Εκτέλεσης (s) | Instructions       | Branches          | Branch Misses     | Cache References | Cache Misses     | L1 DCache Loads  | L1 DCache Load Misses | L1 ICache Load Misses | dTLB Loads       | dTLB Load Misses | iTLB Loads       | iTLB Load Misses  |
|--------------------------------------|------------|----------------------|--------------------|-------------------|-------------------|------------------|------------------|------------------|------------------------|------------------------|------------------|------------------|------------------|-------------------|
| XSBench μόνο του                     | -          | 82.940               | 100,923,435,955    | 11,739,835,346    | 351,481,176 (2.99%) | 5,211,276,682    | 3,302,443,314 (63.37%) | 21,364,594,654   | 5,479,135,679 (25.65%) | 295,854,092           | 21,405,044,468   | 1,517,084,008 (7.09%) | 8,465,163        | 6,404,531 (75.66%) |
| XSBench + memory_stress (ίδιος πυρήνας) | 100        | 107.883              | 88,587,340,869     | 9,354,049,493     | 318,001,318 (3.40%) | 4,701,281,433    | 3,581,464,152 (76.18%) | 18,559,607,682   | 5,316,356,053 (28.64%) | 22,195,912           | 18,549,956,310   | 1,418,502,982 (7.65%) | 4,469,628        | 3,357,206 (75.11%) |
|                                      | 200        | 116.330              | 88,572,849,394     | 9,352,216,780     | 318,749,281 (3.41%) | 4,719,020,234    | 3,552,338,695 (75.28%) | 18,544,237,618   | 5,300,256,992 (28.58%) | 24,850,595           | 18,537,259,803   | 1,514,884,599 (8.17%) | 4,407,248        | 3,199,943 (72.61%) |
|                                      | 400        | 114.175              | 88,597,612,740     | 9,363,837,844     | 318,445,852 (3.40%) | 4,725,860,157    | 3,566,458,224 (75.47%) | 18,538,228,202   | 5,299,936,665 (28.59%) | 25,696,844           | 18,534,672,474   | 1,436,096,589 (7.75%) | 4,470,575        | 3,141,711 (70.28%) |
| XSBench + memory_stress (διαφορετικός πυρήνας) | 100        | 57.077               | 88,407,158,850     | 9,335,059,191     | 317,953,731 (3.41%) | 4,686,552,266    | 3,551,075,687 (75.77%) | 18,499,326,634   | 5,312,935,790 (28.72%) | 16,061,287           | 18,507,718,239   | 1,371,474,427 (7.41%) | 4,139,464        | 3,323,626 (80.29%) |
|                                      | 200        | 60.848               | 88,566,462,672     | 9,359,224,803     | 318,153,339 (3.40%) | 4,693,029,712    | 3,539,788,810 (75.43%) | 18,499,294,900   | 5,296,388,734 (28.63%) | 17,234,223           | 18,507,403,733   | 1,553,858,676 (8.40%) | 4,216,585        | 3,178,066 (75.37%) |
|                                      | 400        | 59.092               | 88,521,969,669     | 9,351,191,832     | 317,397,926 (3.39%) | 4,721,102,361    | 3,557,715,140 (75.36%) | 18,505,896,961   | 5,308,630,454 (28.69%) | 17,920,648           | 18,511,212,492   | 1,412,109,100 (7.63%) | 4,023,917        | 3,133,381 (77.87%) |

### Συμπεράσματα

Από τα αποτελέσματα των πειραμάτων, παρατηρούμε τα εξής:

1. **Ανταγωνισμός στον Ίδιο Πυρήνα:**
   - Η συνεκτέλεση του XSBench με το microbenchmark στον ίδιο πυρήνα επηρεάζει σημαντικά την απόδοση του XSBench, αυξάνοντας τον χρόνο εκτέλεσης κατά περίπου 30% σε σχέση με την εκτέλεση του XSBench μόνο του.
   - Ο ανταγωνισμός για τη μνήμη cache και τους TLBs είναι έντονος, όπως φαίνεται από την αύξηση των cache misses και των TLB load misses.

2. **Ανταγωνισμός σε Διαφορετικούς Πυρήνες:**
   - Η εκτέλεση των προγραμμάτων σε διαφορετικούς πυρήνες μειώνει τον ανταγωνισμό και βελτιώνει την απόδοση σε σχέση με τη συνεκτέλεση στον ίδιο πυρήνα. Ο χρόνος εκτέλεσης του XSBench είναι κοντά στο σημείο αναφοράς (XSBench μόνο του).
   - Οι επιδόσεις της μνήμης cache παραμένουν επιβαρυμένες, υποδεικνύοντας ότι ο ανταγωνισμός στους κοινούς πόρους (π.χ. L3 cache) εξακολουθεί να υφίσταται.

3. **Επιδράσεις στην Μνήμη Cache και TLBs:**
   - Τα ποσοστά cache misses και TLB load misses υποδεικνύουν ότι η εφαρμογή XSBench έχει σημαντικές απαιτήσεις μνήμης, οι οποίες δεν μπορούν να καλυφθούν πλήρως από την υπάρχουσα αρχιτεκτονική της μνήμης cache.
   - Οι υψηλές τιμές των L1 ICache Load Misses και TLB Load Misses είναι αξιοσημείωτες και δείχνουν τη σημασία της διαχείρισης των πόρων σε πολυπύρηνα συστήματα.

### Βήμα 5: Μελέτη του αντίκτυπου του μηχανισμού των μεγάλων (2ΜΒ) σελίδων

Αρχικά απενεργοποιήσαμε το THP με την εντολή `echo never | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
`
Έπειτα τρέξαμε τα δύο scripts `memory_xsbench_same_core.sh` και `memory_xsbench_different_cores.sh`.

Ενεργοποίησαμε ύστερα το THP με την εντολή `echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
` και τρέξαμε ξανά τα δύο scripts.

### FRESHLY BOOTED

### Δεδομένα Πειραμάτων

#### THP Απενεργοποιημένο

##### Εκτέλεση XSBench και memory_stress στον ίδιο πυρήνα

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 121.640              | 95,284,701,464       | 10,655,124,578      | 3.15              | 4,988,687,849      | 72.04               | 20,016,644,930      | 27.18                    | 20,011,269,281      | 7.09                 | 7,100,758  | 67.56                 |
| 200MB          | 111.559              | 89,502,692,724       | 9,528,517,194       | 3.34              | 4,712,465,799      | 76.04               | 18,696,029,128      | 28.38                    | 18,718,940,684      | 7.69                 | 4,471,787  | 70.15                 |
| 400MB          | 110.777              | 89,437,931,070       | 9,514,748,743       | 3.35              | 4,719,085,044      | 75.74               | 18,675,750,412      | 28.44                    | 18,693,432,253      | 7.60                 | 4,405,850  | 70.70                 |

##### Εκτέλεση XSBench και memory_stress σε διαφορετικούς πυρήνες

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 61.224               | 89,150,316,217       | 9,456,776,447       | 3.36              | 4,693,984,425      | 75.36               | 18,631,137,460      | 28.40                    | 18,597,437,645      | 8.43                 | 3,488,127  | 91.61                 |
| 200MB          | 58.039               | 88,484,796,598       | 9,339,295,145       | 3.40              | 4,693,275,758      | 75.90               | 18,512,642,727      | 28.69                    | 18,503,323,624      | 7.50                 | 3,171,479  | 113.21                |
| 400MB          | 59.591               | 89,247,101,583       | 9,474,072,809       | 3.35              | 4,707,805,210      | 75.86               | 18,653,593,354      | 28.52                    | 18,645,687,638      | 7.62                 | 3,286,810  | 91.29                 |

#### THP Ενεργοποιημένο

##### Εκτέλεση XSBench και memory_stress στον ίδιο πυρήνα

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 93.357               | 81,486,610,347       | 8,141,413,610       | 3.90              | 4,016,398,550      | 90.73               | 16,766,099,876      | 27.74                    | 16,761,905,835      | 0.32                 | 1,001,108  | 65.52                 |
| 200MB          | 92.549               | 81,290,379,544       | 8,106,777,350       | 3.91              | 4,011,253,199      | 91.65               | 16,683,459,461      | 27.93                    | 16,676,883,872      | 0.31                 | 756,597    | 80.72                 |
| 400MB          | 92.777               | 81,170,310,855       | 8,093,429,531       | 3.92              | 4,011,764,513      | 91.37               | 16,679,335,338      | 27.92                    | 16,665,541,948      | 0.29                 | 712,792    | 77.07                 |

##### Εκτέλεση XSBench και memory_stress σε διαφορετικούς πυρήνες

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 56.109               | 81,998,214,244       | 8,257,472,204       | 3.89              | 4,078,422,008      | 91.07               | 16,866,949,702      | 27.76                    | 16,863,019,782      | 1.44                 | 813,967    | 127.50                |
| 200MB          | 48.509               | 80,664,626,967       | 8,008,375,562       | 3.93              | 3,999,072,053      | 90.04               | 16,552,519,278      | 28.10                    | 16,558,313,925      | 0.26                 | 433,995    | 85.70                 |
| 400MB          | 48.351               | 80,733,729,828       | 8,018,453,750       | 3.92              | 4,001,349,181      | 89.81               | 16,573,457,429      | 28.05                    | 16,591,550,486      | 0.27                 | 377,722    | 108.32                |


### AGED

Επαναλάβαμε τα ίδια ακριβώς βήματα και για aged state. Πρακτικά αφού εκτελέσαμε όλα τα παραπάνω, ξεκινήσαμε ξανά την διαδικασία από την αρχή γι'αυτή την περίπτωση.

#### THP Απενεργοποιημένο

##### Εκτέλεση XSBench και memory_stress στον ίδιο πυρήνα

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 123.217              | 89,432,978,597       | 9,517,189,485       | 3.36              | 4,739,391,869      | 78.49               | 18,669,919,334      | 28.57                    | 18,686,391,707      | 8.09                 | 5,573,552  | 62.79                 |
| 200MB          | 115.690              | 89,329,840,650       | 9,491,788,098       | 3.37              | 4,733,475,407      | 77.76               | 18,660,669,155      | 28.60                    | 18,680,743,367      | 7.87                 | 4,951,918  | 64.04                 |
| 400MB          | 105.808              | 89,229,119,170       | 9,482,404,108       | 3.36              | 4,691,739,531      | 75.92               | 18,624,906,161      | 28.50                    | 18,624,928,072      | 7.45                 | 5,011,039  | 62.29                 |

##### Εκτέλεση XSBench και memory_stress σε διαφορετικούς πυρήνες

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 56.856               | 89,095,544,961       | 9,457,554,149       | 3.35              | 4,689,475,686      | 75.64               | 18,603,919,092      | 28.55                    | 18,616,553,569      | 7.33                 | 3,270,470  | 92.52                 |
| 200MB          | 57.256               | 89,155,439,649       | 9,455,311,960       | 3.36              | 4,682,660,066      | 75.69               | 18,621,197,712      | 28.54                    | 18,595,466,940      | 7.49                 | 3,046,213  | 109.33                |
| 400MB          | 58.932               | 89,191,996,779       | 9,471,505,940       | 3.35              | 4,703,666,072      | 76.25               | 18,625,155,244      | 28.58                    | 18,634,130,903      | 7.47                 | 3,248,650  | 92.73                 |

#### THP Ενεργοποιημένο

##### Εκτέλεση XSBench και memory_stress στον ίδιο πυρήνα

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 90.937               | 80,122,737,162       | 7,899,911,493       | 3.99              | 4,006,759,857      | 89.69               | 16,425,828,865      | 28.42                    | 16,408,924,187      | 0.23                 | 195,214    | 102.60                |
| 200MB          | 90.942               | 79,965,725,717       | 7,881,398,484       | 4.00              | 4,008,931,100      | 89.42               | 16,380,456,882      | 28.49                    | 16,380,766,186      | 0.23                 | 109,412    | 145.05                |
| 400MB          | 91.337               | 80,192,150,082       | 7,927,322,618       | 3.98              | 3,980,452,463      | 89.59               | 16,429,057,826      | 28.23                    | 16,430,385,741      | 0.21                 | 202,838    | 147.94                |

##### Εκτέλεση XSBench και memory_stress σε διαφορετικούς πυρήνες

| Μέγεθος Μνήμης | Χρόνος Εκτέλεσης (s) | Instructions         | Branches           | Branch Misses (%) | Cache References   | Cache Misses (%)    | L1 DCache Loads     | L1 DCache Load Misses (%) | dTLB Loads          | dTLB Load Misses (%) | iTLB Loads | iTLB Load Misses (%) |
|----------------|----------------------|----------------------|---------------------|-------------------|--------------------|---------------------|---------------------|--------------------------|---------------------|----------------------|------------|-----------------------|
| 100MB          | 48.185               | 79,867,255,640       | 7,868,187,623       | 3.99              | 4,000,012,237      | 89.83               | 16,359,209,989      | 28.54                    | 16,355,523,098      | 0.23                 | 14,019     | 638.63                |
| 200MB          | 47.993               | 79,856,463,699       | 7,859,074,731       | 4.00              | 3,996,229,598      | 89.64               | 16,360,728,335      | 28.50                    | 16,349,149,608      | 0.22                 | 14,993     | 617.22                |
| 400MB          | 47.889               | 80,045,833,871       | 7,883,625,822       | 3.99              | 3,961,633,619      | 91.27               | 16,402,813,726      | 28.36                    | 16,375,883,566      | 0.21                 | 135,376    | 94.06                 |

### Συνολικό Συμπέρασμα

Με βάση τα αποτελέσματα των πειραμάτων, μπορούμε να βγάλουμε τα εξής συνολικά συμπεράσματα:

#### 1. Διαφορά μεταξύ THP Ενεργοποιημένου και Απενεργοποιημένου

##### Χρόνος Εκτέλεσης:
- **THP Ενεργοποιημένο:** Ο χρόνος εκτέλεσης του XSBench είναι γενικά μικρότερος όταν ο THP είναι ενεργοποιημένος, ιδιαίτερα όταν οι διεργασίες εκτελούνται σε διαφορετικούς πυρήνες.
- **THP Απενεργοποιημένο:** Ο χρόνος εκτέλεσης είναι μεγαλύτερος σε σύγκριση με την ενεργοποίηση του THP.

##### Cache Misses:
- **THP Ενεργοποιημένο:** Το ποσοστό των cache misses είναι υψηλότερο. Αυτό μπορεί να οφείλεται στη μεγαλύτερη ποσότητα δεδομένων που μετακινούνται στη μνήμη cache λόγω των μεγαλύτερων σελίδων.
- **THP Απενεργοποιημένο:** Το ποσοστό των cache misses είναι χαμηλότερο.

##### dTLB Load Misses:
- **THP Ενεργοποιημένο:** Οι dTLB load misses μειώνονται σημαντικά, ιδιαίτερα για μεγέθη μνήμης 200MB και 400MB.
- **THP Απενεργοποιημένο:** Οι dTLB load misses είναι υψηλότερες.

##### iTLB Load Misses:
- **THP Ενεργοποιημένο:** Οι iTLB load misses παρουσιάζουν μεγάλη διακύμανση αλλά γενικά είναι χαμηλότερες.
- **THP Απενεργοποιημένο:** Οι iTLB load misses είναι υψηλότερες και πιο σταθερές.

##### Instructions και Branches:
- **THP Ενεργοποιημένο:** Ο αριθμός των instructions και branches είναι ελαφρώς μειωμένος, υποδεικνύοντας μια μικρή βελτίωση στην απόδοση των κώδικων που εκτελούνται.
- **THP Απενεργοποιημένο:** Ο αριθμός των instructions και branches είναι ελαφρώς υψηλότερος.

#### 2. Διαφορά μεταξύ Freshly Booted και Aged

##### Χρόνος Εκτέλεσης:
- **Freshly Booted:** Ο χρόνος εκτέλεσης είναι γενικά μικρότερος σε σύγκριση με το aged state.
- **Aged:** Ο χρόνος εκτέλεσης είναι μεγαλύτερος, κάτι που μπορεί να οφείλεται στη φθορά της μνήμης και στις επιπτώσεις της συνεχιζόμενης χρήσης.

##### Cache Misses:
- **Freshly Booted:** Το ποσοστό των cache misses είναι χαμηλότερο σε σύγκριση με το aged state.
- **Aged:** Το ποσοστό των cache misses είναι υψηλότερο, πιθανώς λόγω φθοράς της μνήμης και άλλων παραγόντων που επηρεάζουν την απόδοση της cache.

##### dTLB Load Misses:
- **Freshly Booted:** Οι dTLB load misses είναι χαμηλότερες.
- **Aged:** Οι dTLB load misses είναι υψηλότερες, κάτι που μπορεί να οφείλεται στη φθορά της μνήμης και στη μεγαλύτερη ανάγκη για αναζητήσεις στη μνήμη.

##### iTLB Load Misses:
- **Freshly Booted:** Οι iTLB load misses είναι χαμηλότερες και πιο σταθερές.
- **Aged:** Οι iTLB load misses είναι υψηλότερες και πιο διακυμαινόμενες.

##### Instructions και Branches:
- **Freshly Booted:** Ο αριθμός των instructions και branches είναι ελαφρώς μικρότερος.
- **Aged:** Ο αριθμός των instructions και branches είναι ελαφρώς υψηλότερος.

### Συνοπτικά

- **THP Ενεργοποιημένο:** Βελτιώνει την απόδοση του XSBench, μειώνει τα dTLB load misses και βελτιώνει τη γενική απόδοση της μνήμης, ιδιαίτερα όταν οι διεργασίες εκτελούνται σε διαφορετικούς πυρήνες.
- **THP Απενεργοποιημένο:** Προκαλεί μεγαλύτερο χρόνο εκτέλεσης και αυξάνει τα dTLB load misses.
- **Freshly Booted:** Προσφέρει καλύτερη απόδοση συνολικά σε σύγκριση με το aged state.
- **Aged:** Εμφανίζει μειωμένη απόδοση με αυξημένο χρόνο εκτέλεσης, υψηλότερα cache και dTLB load misses, κάτι που πιθανόν οφείλεται στη φθορά της μνήμης και σε άλλους παράγοντες χρήσης.

Η χρήση του THP σε συνδυασμό με την εκτέλεση σε freshly booted σύστημα φαίνεται να προσφέρει την καλύτερη απόδοση για το XSBench.

### Βήμα 6: Μελέτη του Αντίκτυπου του Μηχανισμού της Κλιμάκωσης της Συχνότητας

Εκτελώντας την εντολή ```bash cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor``` είδαμε ότι ο τρέχων frequency governor είναι ο `schedutil`. ο οποίος προσαρμόζει τη συχνότητα του επεξεργαστή δυναμικά ανάλογα με το φορτίο.

### Αλλαγή του Governor σε Performance

```bash
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

Στη συνέχεια, εκτελέστε τα πειράματα με τα scripts `memory_xsbench_same_core.sh` και `memory_xsbench_different_cores.sh` και καταγράψτε τα αποτελέσματα.

### Αλλαγή του Governor σε Powersave

```bash
echo powersave | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

Εκτελέστε ξανά τα πειράματα με τα ίδια scripts και καταγράψτε τα αποτελέσματα.


