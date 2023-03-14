# Algorithms WIP

## Big O notation

- A way to categorize algorithms time or memory requirements based on input: as the input growths, how fast the computation/memory growths.  
- It generalizes the growth of the algorithm.
- It tells us whether the algorithm will perform well enough in a given context.
- It can help us to choose an appropriate data structure or algorithm to resolve a problem efficiently.

Important concepts:
- The growth is with respect to the input.
- Constants are dropped.
- We usually take into account the worst case scenario.

### O(n)
First we can look for loops in the function to know the complexity.
The relation between the input and the execution time is linear in that case. If the size of the input is 2 times bigger, then the time complexity is 2 times bigger.
```typescript
function sum(numbers: []): number {
    let sum = 0;
    for(let i = 0; i < numbers.length; i++) {
        sum += numbers[i];
    }
    return sum;
}
```
- If the for loop of this function is executed 2 times, the complexity would be still O(n) and not O(2n) because we categorize the algorithm based on the input. So the constants (2) are dropped because they eventually become irrelevant.
I want to know if the algorithm is going to crash my computer, not the exact time it is going to take to complete the task.
- If we return from within the loop because some condition is met, the complexity would be still O(n) because we consider worst case scenario and not let's say O(n-2) because the condition is met in the third to last element. 

### Common complexities

- O(1): Constant time wherever the input is
- O(logn): If you half the input could be this
- O(n): the complexity increases as the input
- O(nlogn): If you half the input could be also this
- O(n^2): It grows pretty fast
- O(2^n): Cannot run on traditional computer!
- O(n!): Cannot run on traditional computer!

### O(logn)
See Binary search trees

### O(nlogn)
See Quicksort

### O(n^2)
Not that the function makes sense but it illustrates N square complexity.
```typescript
function sum(numbers: []): number {
    let sum = 0;
    for(let i = 0; i < numbers.length; i++) {
        for(let j = 0; j < numbers.length; j++) {
            sum += numbers[j];
        }
    }
    return sum;
}
```

### O(n^2)
This is like multiplying matrices
```typescript
function sum(numbers: []): number {
    let sum = 0;
    for(let i = 0; i < numbers.length; i++) {
        for(let j = 0; j < numbers.length; j++) {
            for(let k = 0; k < numbers.length; k++) {
                sum += numbers[k];
            }
        }
    }
    return sum;
}
```

## Data structure: Array

A fixed size, contiguous memory chunk. We can perform a few operations:
- Getting a specific index: multiplying the offset by the size (usually in bytes) of the elements. _O(1)_
- Insertion at specific index: set an element
- Deletion at specific index: it depends on how the program interpret it, it could be by setting a 0
- We cannot grow it and there is not "insertAt", "push" or "pop", but we can create it. Traditional arrays do not have that

Javascript does have "[]" which is not really an array but we can use it as an array

## Search: Linear search

The simpler one. We just compare each element of the array starting at the first element.

O(n)

```typescript
export default function linear_search(haystack: number[], needle: number): boolean {
    for (let i = 0; i < haystack.length; ++i) {
        if (haystack[i] === needle) {
            return true;
        }
    }
    return false;
}
```

## Search: Binary search

If the array is sorted, we half the array n times until we find the element or is not possible to half again.

O(logn)

N/2^k = 1 => N = 2^k => log2N = k => logn
We need to half the array log of n times until we met the condition.
An array of 4096 bytes needs "log of 4096" iterations until we met the condition:
2048 -> 1024 -> 512 -> 256 -> 128 -> 64 -> 32 -> 16 -> 8 -> 4 -> 2 -> 1

```typescript
export default function bs_list(haystack: number[], needle: number): boolean {
    let low_point = 0;
    let high_point = haystack.length;
    
    do {
        const mid_point = Math.floor(low_point + ((high_point - low_point) / 2));
        const value = haystack[mid_point]
        
        if (value === needle) {
            return true;
        } else if (value > needle) {
            high_point = mid_point;
        } else {
            low_point = mid_point + 1;
        }
    } while (low_point < high_point);
    
    return false;
}
```

## Search: Two crystal balls

_When given two crystal balls that will break if dropped from a high enough distance, determine the exact spot in which it will break in the most optimized way._

We can represent the balls breaking as an array of n elements (booleans or numbers) 
meaning that starting at a given position (the first true or 1), the ball will break.

```typescript
const ball_breaks = [0, 0, 0, 0, 0, 1, 1, 1, 1]
```

If we do a linear search, the complexity for each search will be O(n): once we
have found the first 1, we need to start over from the very beginning with the 
second ball.

So instead of linearly search (one element per loop, worst case is looping through
the whole array) or binary searching (half of the elements of the previous loop, 
worst case we loop over the half of the elements in the array) we can jump sqrt(n) 
elements per loop until we find the ball broken, then we start over from the last 
position we know the balls were not broken: we avoid linear searching from the 
beginning each time. So it is much more efficient.

O(sqrt(n))

```typescript
export default function two_crystal_balls(breaks: boolean[]): number {
    const jump_amount = Math.floor(Math.sqrt(breaks.length));
    
    let i = jump_amount;
    for (; i < breaks.length; i += jump_amount) {
        if (breaks[i]) {
            break;
        }
    }
    
    i -= jump_amount;
    
    for (let j = 0; i < breaks.length && j < jump_amount; ++j, ++i) {
        if (breaks[i]) {
            return i;
        }
    }
    
    return -1;
}
```

## Sort: Bubble sort

With bubble sort, on each iteration, the larger element is pushed to the end of
the array.

O(n^2)

A sorted array must meet the condition: any _Xi <= Xi+1_

```typescript
export default function bubble_sort(arr: number[]): void {
    for (let i = 0; i < arr.length; ++i) {
        for (let j = 0; j < arr.length - 1 - i; ++j) {
            if (arr[j] > arr[j + 1]) {
                const tmp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = tmp;
            }
        }
    }
}
```

## Data structure: linked list

A simple array cannot:
- Delete an element
- Insert new elements
- Grow

This is why in here `const a = []`, "a" is not an array as we can push, pop, etc.

Complexity of deletion or insertion is constant O(1) given the node, otherwise 
we have to add the time of the traversal of the list.

```typescript
interface LinkedList<T> {
    get length(): number;
    insertAt(item: T, index: number): void;
    remove(item: T): T | undefined;
    removeAt(index: number): T | undefined;
    append(item: T): void;
    prepend(item: T): void;
    get(index: number): T | undefined;
}
```