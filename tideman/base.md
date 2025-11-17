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

The rest is kinda self-explanatory, so moving on.  

Skipping the functions, as we'll discuss them in the next file.

The second code block. `int main()`.

```c
int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: tideman [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1;
    if (candidate_count > MAX)
    {
        printf("Maximum number of candidates is %i\n", MAX);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        candidates[i] = argv[i + 1];
    }

    // Clear graph of locked in pairs
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            locked[i][j] = false;
        }
    }

    pair_count = 0;
    int voter_count = get_int("Number of voters: ");

    // Query for votes
    for (int i = 0; i < voter_count; i++) // All voters.
    {
        // ranks[i] is voter's ith preference
        int ranks[candidate_count];

        // Query for each rank
        for (int j = 0; j < candidate_count; j++) // Each voter.
        {
            string name = get_string("Rank %i: ", j + 1);

            if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
        }

        record_preferences(ranks); // One voter has voted. Store the vote before moving to the next.

        printf("\n");
    }

    add_pairs();
    sort_pairs();
    lock_pairs();
    print_winner();
    return 0;
}
```

### Some more ideas.

**1.** `argc` and `argv[]`: Let's start with the preconceived understanding that `argv[0]` is essentially just the name of the program. So, whatever candidate name we're adding starts from `argv[1]`. So, if `argc`, which is essentially just the number of command-line arguments we have here is lesser than **2**, we do not have any candidates. 

Point I'm trying to make is `argc` is the number of candidates (by names seperated by a single space when we enter them in terminal) plus 1, for the program name, and `argv` is the array storing these names+program name.

```c
 if (argc < 2)
    {
        printf("Usage: tideman [candidate ...]\n");
        return 1;
    }
```

Again, we need to put all the candidate names in the candidate array from argv. The i+1 is sort of clear, now.

```c
 for (int i = 0; i < candidate_count; i++)
    {
        candidates[i] = argv[i + 1];
    }
```


**2.** `locked[i][j]=false`: This is extremely crucial because we need to remove any lingering booleans.

```c
for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            locked[i][j] = false;
        }
    }
```

**3.** `for loops:` Consider the code block given below.
```c
for (int i = 0; i < voter_count; i++) // All voters.
    {
        // ranks[i] is voter's ith preference
        int ranks[candidate_count];

        // Query for each rank
        for (int j = 0; j < candidate_count; j++) // Each voter.
        {
            string name = get_string("Rank %i: ", j + 1);

            if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
        }

        record_preferences(ranks); // One voter has voted. Store the vote before moving to the next.

        printf("\n");
    }
```

I've added a comment in it, which kind of clears up what's happening, but let's break it down a bit. There's two for loops, one runs for each voter, another runs for voter to voter. 

The `i` loop is for voter to voter. It begins by initiliazing the `ranks[]` array over and over again so it can be reused by the proceeding voter. Then, it moves into the `j` loop which runs for each voter. It works simply by prompting the specific voter to enter their choices depending on the number of candidates in the election. Since in the CS50 exercise we had- **Alice**, **Bob**, and **Charlie**. That makes **3** positions here, for an example. Each voter can entire their choices in hierachy following the tideman system. Then, it moves to the `votes()` function and then the `record_preferences()` function. That, we shall discuss in the next file. Then, the `i` loop keeps iterating till before `voter_count`, since we start from index `0`.

> That concludes this document. Let's move to building the funcions!



