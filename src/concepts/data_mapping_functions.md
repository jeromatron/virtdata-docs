Data Mapping Functions
======================

### Audience

This section should be useful to new users as well as experienced 
developers. Here, we explain how and why we use functions for
building data mapping recipes. If you are planning to build your
own data mapping recipes, this section is essential reading.

### Input -> Function -> Output

A function represents a mapping between one set of values and another.
Consider a basic example in which the function N(i) maps an input
number to text form.

{{#nomnoml
#zoom:1.0
#direction:right
#.value: fill=#D0FFD0 visual=frame
#.function: fill=#E0FFE0 visual=sender
[example 1|
[<value> input: 1] ->[<function> F(1)]
[<function>F(1)] -> [<value>output: "one"]
[<value> input: 2] ->[<function>F(2)]
[<function>F(2)] -> [<value>output: "two"]
[<value> input: 3] ->[<function>F(3)]
[<function>F(3)] -> [<value>output: "three"]
]
}}

In this example, as will often be the case, the type of input is different
than that of the output. The input could be a *long*, and the output type
a *String*, for example. This example also shows data flowing through the
function. This is a common representation in flow-based programming.

### Value Semantics

If we consider the words above to be the names of users in a population, it is easy to
see how we could think of ***F*** as simply enumerating their names. In this case,
the word values are semantically *properties* -- the names of some
arbitrary members of a population. We say that it is arbitrary in this case
simply because a property does not uniquely identify a member of a population.
We are not yet speaking about identity semantics.
So far we have chosen only to asign the semantic of property to the output values.

If we take it a step further and say that the words are unique identifiers for users,
then we can say that we are indeed identifying *specific* instances of a population.
This would be the case, for example, if you had a system in which each unique
number name represented a unique user. Depending on your system, this may or may not 
be the case (I would hope it is not). The most important point here is that it is up to you
how uniqueness in your particular system is represented -- and hence how you should 
model it. So long as the semantic relationships in your data mapping functions
mirror the identity and property semantics of your application, it will work.

Both properties and instances are important in a dataset. How do we have both together
in a way that is meaningful? How do we make it so that we can deal with instances 
of things as well as the properties so that we actually get stable properties for
specific instances?

For this, we need to combine functions. To model the relationship between the identity
of a thing at one level and an associated property, we simply mirror the relationship 
in terms of functions. We combine functions. In formal terms, we create a 
*composed* function.

As a starting point, we must have a basic model of how the identities and properties
relate to each other:

{{#nomnoml
#direction:down
#zoom:1.0
[identity: user_id] --> [property: first name]
[identity: user_id] --> [property: last name]
}} 

For the first time, we see a graph structure that represents the association between
instances and properties of those instances in a data set. It is not unlike an
entity-relationship diagram. We will call this type of graph an *entity-property* graph.

Although basic, we see three values and how they are related. *first_name* depends on 
*user_id*, as does *last_name*. This is the same as saying that you must have the
value for user_id before you can calculate the values for first and last name. It also
shows why we draw the lines in this direction when modeling data flow.

It illustrates cardinal relationships but does not capture *how* the data corresponds
from one value to another. For this, we need to fill in the blanks. First, let's talk
about where everything begins in our data flow.

### Input Coordinates

When mapping data via functions we have to have an original input value. For the sake
of simplicity, we will stick to whole numbers in sequence. Knowing that our original
input data is simply the counting numbers allows us to do interesting things later
with cardinality. For now, the most important thing to remember is that we have
a primary input that we call the *coordinate* -- an integer from a sequence.

### Composition and Dataflow

Assume that you have two functions:

*one that maps numbers to user ids*: {{#nomnoml
#zoom:0.75
#direction:right
#.input: fill=#FFFFFF visual=frame
#.function: fill=#FFFFFF visual=sender
#.userid: fill=#E2D58B visual=frame
[<input> input: number] ->[<function>U]
[<function>U] -> [<userid>output: user_id]
}} 

*and another that maps user ids to first names*:
 
{{#nomnoml
#zoom:0.75
#direction:right
#.userid: fill=#E2D58B visual=frame
#.function: fill=#FFFFFF visual=sender
#.firstname: fill=#44BBA4 visual=frame
[<userid> input: user_id] ->[<function>F]
[<function>F] -> [<firstname>output: first_name] 
}}          

If you combine them together in data-flow form, the results looks like this:

{{#nomnoml
#zoom:0.75
#direction:right
#.input: fill=#FFFFFF visual=frame
#.function: fill=#FFFFFF visual=sender
#.userid: fill=#E2D58B visual=frame
#.firstname: fill=#44BBA4 visual=frame
[<input> input: number] ->[<function>U]
[<function>U] -> [<userid> user_id]
[<userid> user_id] ->[<function>F]
[<function>F] -> [<firstname>output: first_name]
}}  


The value of user id is the output of U and the input of F in this configuration.
If we compose these functions *U(i)* and *F(i)* together, they become a single
function that maps a number to an instance to a first name. Somewhat formally,
we would call this F(U(i)). In other words, we would apply F to the result of
applying U to the original input. This wording can get out of hand for larger
compositions, so we will emphasize flow-based descriptions moving forward.

### Function Graphs

Given an entity-property graph, we can build a new kind of picture with more
detail: the *function graph*. You can think of the function graph
as a symbolic encoding of the relationships in a data set. We simply add
the coordinate input value at the front, and then drop in a place-holder for
each required mapping function: 

{{#nomnoml
#zoom:1.0
#direction:down
#.function: fill=#FFFFFF visual=sender
#.userid: visual=frame
#.firstname: visual=frame
#.lastname: visual=frame
[<input>coordinate] ->[<function>U]
[<function>U] -> [<userid>identity: user_id]
[<userid>identity: user_id] ->[<function>F]
[<function>F] -> [<firstname>property: first_name]
[<userid>identity: user_id] ->[<function>L]
[<function>L] -> [<lastname>property: last_name]
}}       

This function graph is merely a template. The functions *U*, *F*, and *L* are 
presently symbolic. We haven't picked anything concrete to put in their 
places. However, we can see the function semantics and signatures that must 
be fulfilled.
 
The key insight here is that **each complete path of this graph represents a composed 
function** that can render self-consistent data. This is the basic building block for 
modeling virtual datasets with interesting data relationships.

### Type Signatures

No matter what type system is used, the input and output
types determine what kind of functions may be plugged in. This is called the 
*signature* of the function. In terms of data flow between pure functions, 
we are really only  concerned about the types of inputs and outputs. Our 
examples above mostly called out semantics and labels, ignoring data types mostly.

There are actual concrete types at every stage of function composition. It is also true
that more than one data type can fulfill the need for a counting number, etc. At some
point we have to start being particular about the data types needed in our data set.
Here, we remove the explanatory "identity and property" markers, and go to concrete
data types and labels:

{{#nomnoml
#zoom:1.0
#direction:down
#.function: fill=#FFFFFF visual=sender
#.userid: visual=frame
#.firstname: visual=frame
#.lastname: visual=frame
[<input>coordinate: long] ->[<function>U]
[<function>U] -> [<userid>user_id: long]
[<userid>user_id: long] ->[<function>F]
[<function>F] -> [<firstname>first_name: String]
[<userid>user_id: long] ->[<function>L]
[<function>L] -> [<lastname>last_name: String]
}}

Even though the function graph above is not realizable yet, it is useful as is.
Function graphs of this type can be referred to as *function graph template*.

From this function graph template, we have two distinct paths -- two different
composed functions to be created. If we traverse each path from the coordinate,
we see two *composed function template*s.

## Data Mapping vs Data Generation

Virtual dataset emphasizes the idea of "data mapping" over "data generation",
but allows for users to break these rules when necessary. Data mapping
implies that pure functions are being used. Data generation implies
that deterministic output is not expected. The choice between these
is simply a matter of whether you use mutable state in your mapping.

### Mutable State?

A functions that depends on mutable state in addition to the input
value will not yield the same result for a given input. Such functions
may produce the same sequence of outputs given the same sequence
of inputs, but this is not sufficient for simulating sampling from
a population with stable properties.

### Immutable State?

A mapping function that does not depend on changing state is
effectively a pure function. This includes functions that depend
on data, as long as that data itself doesn't change.

Parameters to a function that can initialize it are simply another
form of immutable state -- so long as these parameters do not change
for the life of the function.

### Choosing Mapping Functions

The function placeholders in the examples above can be satisfied by
any function instance that fits the type signatures. Further, how
these functions are realized at runtime are left to the individual
data mapping libraries. Each library supports a set of loadable
functions that can be asked for by name. The details of this are
left for later in the usage section. 