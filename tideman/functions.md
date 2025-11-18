Now, let's start with the first function.

```c
// Update ranks given a new vote
bool vote(int rank, string name, int ranks[]) // Rank is for pref. order.
{
    for (int i = 0; i < candidate_count; i++)
    {
        if (strcmp(candidates[i], name) == 0)
        {
            ranks[rank] = i; // Ranks is for storing our candidate index for the particular position
                             // designated by 'rank'.
            return true;
        }
    }
    return false;
}
```

**1.** Why `bool`?: Because all we need to do here is validate if the person voted for by our voter is a candidate/not. If yes, we return `true`. If not, `false`. 

**2.** `vote(int rank, string name, int ranks[])`: Now, let's bring back a little fragment from `main`.
```c
 if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
```
By the time we finish interpreting this function, this will make complete sense. Now, we start with the example as provided by CS50: `candidates[0] = Alice`, `1` is **Bob**, `2` is **Charlie**.

First voter votes as follows:

1. Alice.
2. Charlie.
3. Bob.

Now, let's move to the line where the voter enters Alice. Then, before choice 2, we enter this function. `j=0`, `name="Alice"`, `ranks=0`(which means '1').

**3.** `for` loop: Here's the block of code:

```c
for (int i = 0; i < candidate_count; i++)
    {
    \\Stuff
    }
```
Alright, we've got 'Alice' as our first vote. First, we loop through our candidate array to find where Alice is. 

**4.** **Conditional statement:** Here's the code.
```c
if (strcmp(candidates[i], name) == 0)
        {
            ranks[rank] = i; // Ranks is for storing our candidate index for the particular position
                             // designated by 'rank'.
            return true;
        }
```
`strcmp` is needed since we're comparing the names, which are strings. `==0` is true when they are the same. Any offset can produce results of `>,<0`. We've got both `name`, and `canditates[0]` as **Alice.**
So, we store the index of where Alice exists since indexes are really far, far easier to deal with than long strings. We know `rank=j=0`, so `ranks[0]=0`  is what we have.

Going back to the `if(!)` condition in `main`. `vote()` does not return false, so the vote is not invalid. We move on to recording our preferences, once `j` loop is done for all the candidates added in the hierarchy.

Let's take a look at the next function.

```c
void record_preferences(int ranks[])
{
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = i + 1; j < candidate_count; j++)
        {
            preferences[ranks[i]][ranks[j]]++; // First and second preference for sets.
        }
    }
    return;
}
```

**1.** Why `void`?: The declaration of our preferences matrix was done globally, so we don't need to return any particular value to main.

**2.** `for` loops: Now, let's take a look at this.

```c
  for (int i = 0; i < candidate_count; i++)
    {
        for (int j = i + 1; j < candidate_count; j++)
        {
            preferences[ranks[i]][ranks[j]]++; // First and second preference for sets.
        }
    }
```
`i` starts from `0`, while `j` starts from `i+1`. The reason lies in the significance of each loop. Now, before we start understanding it all- it is heavily recommended that one visits [tideman](https://cs50.harvard.edu/x/psets/3/tideman/) in cs50's official site to understand how the matrix looks, and what the logic is.

Moving forward, what we're taking in our function is `ranks[]`. The significance of that in understanding these two loops is interpreted by using an example. As already explained above, `ranks[]` contains, at present, after the first voter votes: `ranks[0] = 0`, `ranks[1] = 2`, `ranks[2] = 1`. Where `0` is Alice's index, `2` is Charlie's, and `1` is Bob's from the `candidates[]` array.

Now, with the order we've described above. Let's pair who wins over who.

1. **Alice** defeats **Charlie**.
2. **Alice** defeats **Bob**.
3. **Charlie** defeats **Bob**.

Now, let's break down the loops using `preferences[ranks[i]][ranks[j]]++`. Okay, let's start.

`i = 0`, then `j=1`:Then, `ranks[i]=ranks[0]=0`, `ranks[j]=ranks[1]=2`. We get `preferences[0][2]`. Then, `j` iterates again since it needs to iterate till candidate count. `j=2`, we get `preferences[0][1]`.

What do we observe? The sequence is **winner-loser**. That's exactly what the loops are for. `i` runs for the winner's index, `j` runs for the loser's. As `++` happens, the count increases by 1. And, thus the 3*3 matrix now has three spots filled with 1s, the rest are still 0.

As the `i` loop in main reiterates, the other spots get filled, 1s increase to 2s, and so on, depending on the voter's choices in ranking.

The next function.

```c
void add_pairs(void)
{
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = i + 1; j < candidate_count; j++)
        {
            if (preferences[i][j] > preferences[j][i])
            {
                pairs[pair_count].winner = i;
                pairs[pair_count].loser = j;
                pair_count++;
            }
            else if (preferences[i][j] < preferences[j][i])
            {
                pairs[pair_count].winner = j;
                pairs[pair_count].loser = i;
                pair_count++;
            }
        }
    }
    return;
}
```

In the last function, we determined a winner and a loser in comparison between two and created a matrix. Here we'll put the preferences matrix into use and identify any candidate who beats another. Furthermore, we'll increase our pair count.

**1.** `preferences`: `preferences[i][j]`, and ` preferences[j][i]` are compared in each iteration. The one with more number of wins is considered the winner, and the one with more losses is the loser. `j` starts from `i+1` since we need to avoid comparing them twice, or a candidate with itself. 

**2.** Now, what we're essentially trying to find is- **If we take two candidates clubbed, who wins more times than the other.**

**3.** `pair_count` increases with each iteration. `pairs` is the globally declared array. `winner` and `loser` come from our type `pair`.

Now, let's move to the next function.

```c
void sort_pairs(void)
{
    for (int i = pair_count - 1; i >= 0; i--)
    {
        for (int j = 0; j < i; j++)
        {
            if ((preferences[pairs[j].winner][pairs[j].loser]) <
                (preferences[pairs[j + 1].winner][pairs[j + 1].loser]))
            {
                pair temp = pairs[j];
                pairs[j] = pairs[j + 1];
                pairs[j + 1] = temp;
            }
        }
    }
    return;
}
```
Here we'll be sorting by strength of victory.

**1.** `for` loops: Let's take a look.

```c
 for (int i = pair_count - 1; i >= 0; i--)
    {
        for (int j = 0; j < i; j++)
        {
```
We'll be using bubble sort to get through this function, visibly. So we need two loops which help. `i` is the number of passes we'll need so everything is sorted. `j` is needed for comparison as done in bubble sort's logic. 

**2.** **Bubble sort**: As we already know, we compare the element in the next index. If found smaller, we swap. We use a temporary variable to store the previous in.

**3.** **Comparison:**

```c
if ((preferences[pairs[j].winner][pairs[j].loser]) <
                (preferences[pairs[j + 1].winner][pairs[j + 1].loser]))
```

What we're doing here is using the results we got from `add pairs()`. Now, we're comparing strengths of pairs before and after. Now we aren’t comparing individual candidates — we are comparing how strongly each winner beats their opponent. 

**4.** `pair`: This datatype is used to make temp because of the present winner-loser parts in pairs.

```c
pair temp = pairs[j];
                pairs[j] = pairs[j + 1];
                pairs[j + 1] = temp;
```
Now, we have it all sorted. We need this sorting because the strongest pair needs to be locked in first.

Next function.

```c
void lock_pairs(void)
{
    for (int i = 0; i < pair_count; i++)
    {
        if (!(cycle(pairs[i].loser, pairs[i].winner)))
        {
            locked[pairs[i].winner][pairs[i].loser] = true;
        }
    }
    return;
}

```

What do we do here? We lock victories in order as we got from `sort` to make a directed graph while ensuring we get no cycles. 


**1.** `for` loop: 
```c
for (int i = 0; i < pair_count; i++)
    {
```
We have it all paired from strongest to weakest, so we start locking from the strongest. So `i` starts from `0`.

**2.** `!cycle`: This is a call to the cycle function we'll be showing after this. If there's no cycle, we lock in the winner-loser pair. The `cycle()` function checks whether there is already a path from loser back to winner.

Cycle function.

```c
bool cycle(int end, int cycle_start)
{
    if (end == cycle_start)
    {
        return true;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        if (locked[end][i])
        {
            if (cycle(i, cycle_start))
            {
                return true;
            }
        }
    }
    return false;
}
```
This is a helper function.

**1.** `bool` datatype: Since we only need a true or false from this.

**2.** **Conditional statement:** The first one.

```c
 if (end == cycle_start)
    {
        return true;
    }
```

In the `lock_pairs` function, `cycle` was called in as follows: `(cycle(pairs[i].loser, pairs[i].winner)`. Compare it with `cycle(int end, int cycle_start)`. Now, we know why we cannot have the two being the same. If they're the same, ultimately, it'll form a closed loop, and we cannot lock in. Defining the two:

`cycle_start` is the original candidate we’re checking for a loop back to, and `end` is the current node we’re exploring from in the recursion
(we move this along the graph as we follow edges)

**3.** `for` loop: 

```c
    for (int i = 0; i < candidate_count; i++)
    {
        if (locked[end][i])
        {
            if (cycle(i, cycle_start))
            {
                return true;
            }
        }
    }
```
If the if statement doesn't return true, we simply move to this `for` loop, where the first if statement starts checking if we have any edge from the node `end` to a candidate `i`. If found true, we recursively check if there any return from this `i` to our `cycle_start`. That isn't permissible, as this makes us create a directed cycle. So, cycle returns `true`, which goes to `lock_pairs`, thus stopping any possibility of locking.

Finally, the last function.

```c
void print_winner(void)
{
    for (int i = 0; i < candidate_count; i++)
    {
        int fal_se = 0;
        for (int j = 0; j < candidate_count; j++)
        {
            if (locked[j][i] == false)
            {
                fal_se++;
            }
        }
        if (candidate_count == fal_se)
        {
            printf("%s\n", candidates[i]);
        }
    }
    return;
}
```

Now to clarify the `fal_se` I used instead of a more normal name... I was kinda thinking of Blackpink's Rosé while writing it so... excuse that. Now, we'll identify the winner using all we did.

**1.** `for` loop: 
```c
for (int i = 0; i < candidate_count; i++)
    {
        int fal_se = 0;
        for (int j = 0; j < candidate_count; j++)
        {
            {
            if (locked[j][i] == false)
            {
                fal_se++;
            }
        }
```
`fal_se` is our counter. `locked` is considered as a matrix where `j` is the winner, and `i` is the loser. For each candidate `i`, we loop over every candidate `j`, and check for locks from `j` to `i`. If any combination (`locked[j][i]`) is `false`, we increment `fal_se` and continue checking the next `j`. `fal_se` is the number of candidates for which no arrows from `j` to `i` exist. 

**2.** **Final:** 
```c
 if (candidate_count == fal_se)
        {
            printf("%s\n", candidates[i]);
        }
```

So, if `fal_se` has its value equal to our number of candidates- it indicates that no `j` points to this particular `i`. If so is found true, we have our winner.

> That concludes the code! The next document will have the entire code so that everything can be linked.

