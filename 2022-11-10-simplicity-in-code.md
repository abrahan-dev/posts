# Simplicity in code

It is always difficult to define a concept that is generally used in a subjective way meaning different things.

That something is simple can have positive or negative connotations depending on the context and the intention. But if I keep the definition to the scope of the code, maybe it will be easier.

Simplicity: Break down complex needs into simple things and compose.

In programming, saying that something is simple often means that it is easy to understand and use.

## The love and death of OOP

I believe that OOP is a paradigm that does not help to divide and conquer but by its nature feeds the agglomeration of ideas and concepts in low quality abstractions.

Because we built OOP we needed design patterns. Maybe OOP makes simple things complex while FP makes complex things simple. Also FP makes micro-things harder and macro things easier while OOP makes micro-things easier and macro things harder. If we use simpler blocks we do not need design patterns.

## A good programmer

A good programmer can have many qualities, but an indispensable one is being able to make good abstractions and after many years developing OOP I have the impression that this paradigm does not help per se. Another quality is being able to translate complex business ideas into simple code.

## A few ideas

Simplicity is likely to be achieved with a combination of ideas from various paradigms, including OOP and FP, where data and functions are first-class passengers, while classes are used only to guarantee invariants  (data changes that triggers idempotent data changes).

- Separate data from code
- Use immutable data whenever possible. It may be prohibitive in data science, but most applications can take advantage of it. We can tolerate some mutation if is occurring in a limited scope we can read at once.
- Use generic data structures separate from the schema (if a hash map is all what you need, use it)
- Use data as a first class citizen
- Use json in native form without transformations / proxies / objects wrapping
- Say things in a more declarative way
- Do not share more than we’d like to

## Where is my function?

I am not an expert FP programmer but given the amount of work that OOP code need and the cost per bug, I'd say that classes are not efficient tools. 

I think with FP the cost per bug can be reduced at a price: the granularity of the system can be overwhelming. 

How do we write our functions and organise them is key.

## Conclusion

This is about changing our mental models as programmers to accomodate simplicity. Think first data and pure functions, then classes only to wrap inseparable and reciprocal concepts.

As stated in the great book "The case against reality" by Donald Hoffman, evolution optimises for fitness, not for truth. Maybe programmers also optimises for something that is just an interface, not for discovering the truth underneath the abstraction. We depend heavily on space (computational types repetition) and time (for loops). 

I think true simplicity will come whenever we get rid of those. There is A LOT of repetition in every code base. 

We are always using the same phrases (computational types) to achieve the same things (homogeneous transformations of inputs and outputs, performing side-effects...). 

But we only have a few things to do in code like assigning a variable, calculating something, evaluating a condition... always the same again and again. 

We also spend of time iterating over collections of data. What if we could just update every element of the collection in a single computation unit of time without any iteration ? In that case we need to accept that time and space are just beatiful abstractions, not reality. But hey, I'm just a programmer, not a physicist.
