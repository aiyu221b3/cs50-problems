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

Now, let's move to the line where the voter enters Alice. Then, before choice 2, we enter this function: `j=0`, `name="Alice"`, `ranks=0`(which means '1').

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
`strcmp` is needed since we're comparing the names, which are strings. `==0` is true when they are the same. Any offset can produce results of `>,<0`. We've got both `name`, and `candidates [0]` as **Alice.**
So, we store the index of where Alice exists since indexes are really far, far easier to deal with than long strings. We know `rank=j=0`, so `ranks[0]=0`  is what we have.

Going back to the `if(!)` condition in `main`; `vote()` does not return false, so the vote is not invalid. We move on to recording our preferences, once `j` loop is done for all the candidates added in hierarchy.

