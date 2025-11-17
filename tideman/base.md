So, this is the first block of code.

```c
#include <cs50.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Max number of candidates
#define MAX 9

// preferences[i][j] is number of voters who prefer i over j
int preferences[MAX][MAX];

// locked[i][j] means i is locked in over j
bool locked[MAX][MAX];

// Each pair has a winner, loser
typedef struct
{
    int winner;
    int loser;
} pair;

// Array of candidates
string candidates[MAX];
pair pairs[MAX * (MAX - 1) / 2];

int pair_count;
int candidate_count;
```

### Here's some ideas:
**1.** ```typedef```: this is needed to create a new type `pair` so that candidates can be paired into groups of winners and losers in a single structure.
```c
typedef struct
{
    int winner;
    int loser;
} pair;
```
**2.** `pair pairs[MAX * (MAX - 1) / 2];`: let's understand why the math works, real quick- example: MAX=3.
Say we have X, Y and Z. `X wins over Y`, `Y wins over X`, `X wins over Z`, `Z wins over X`, `Y wins over Z`, `Z wins over Y` are the only possibilites, right? So, in terms of pairs, we take two in one- we get **3** pairs. Following that, 3*(3-1)/2 = 3 pairs.



