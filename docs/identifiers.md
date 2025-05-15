# Understanding Identifiers 

Scott Bruce, 2022-12-26

>*There are only two hard problems in computer science: cache invalidation, naming things, and off by one errors.*

All computation is manipulating names.  We use one set of human comprehensible names when writing code, designing systems, and communicating with other humans.  Once code is running, those names becomes *identifiers*.  For efficiency, we must actively choose the smallest, most efficient method of referring to any computational construct.

All computations scale in some way with the data size used in that computation.  This applies regardless of the scale of computation involved.  There is some lower limit for how many bits are required to encode a given construct, and the most efficient system is achievable only when we are near that minimal encoding.

Identifiers act as names for constructs. When machines operate on identifiers or communicate using them, they attach no meaning to any particular name. All identifiers thus reduce to numbers. Having only one name for each construct is a highly valuable property (ie: no temporary names)  There are two broad strategies for choosing identifiers in a given system.  Changing identifier generation method or size after sytem design phase is very difficult; which is why skillful design is critical.

The most compact identifier available is a monotonically increasing integer (a *sequence number*) allocated from some specified namespace.  In distributed systems, a server mediates this by maintaining a mapping from identifier to object.  This provides a minimally sized representation at the cost of some overhead (having to ask something to allocate an identifier).  If the total set size of named objects is N, then the identifiers will be log(N,2) bits.  In a system in which these identifiers are visible publicly, low guessability is often a desired characteristic.  Leaving medium sized (100-1e6 wide) random gaps between sequential ids reduces guessability at the price of using more bits.

Identifiers sometimes refer to huge sets of data or data for which creating and tracking sequence numbers is expensive [^1].  In these situations, we use a fingerprint function [^2] to get a stable identifier of a certain fixed width. Common widths are 32, 64, 80, 96 or 128 bits.  In some cases the constructs have a consistent representation over time: which is to say the construct can always be turned into its name. In other cases the construct changes its contents over time and has no stable, fingerprintable representation.  In those cases, we must randomly create an identifier, then use that identifier as a key (in some storage system) to update the construct over time.

### Likelihood of Identifier Collision

It is common in modern software engineering to choose to use Universally Unique IDentifiers (UUIDs). These cost 128 bits, which provides entirely too strong and expensive a uniqueness guarantee for almost all use cases.  It is a vital part of identifier creation and system design to choose the minimal bit count for its identifiers.  Collision chances for an X-bit identifier, if we have an item set of Y size, are given below.  Ex: a 64 bit id with a collection size of 33M has collision chance 3e-5, so any given such collection has a 1 in 300000 chance of having a single collision.

|item count \ bit count | 64 bit | 80 bit | 96 bit | 128 bit|
|---|----|--|--|--|
|1K    |2.8e-14| <10e-17|<10e-17|<10e-17|
|32K   |2.9e-11|4.5e-16|<10e-17|<10e-17|
|1M    |2.9e-8|4.5e-13|<10e-17|<10e-17|
|33M   |3.0e-5|4.6e-10|5.1e-15|<10e-17|
|1B    |.03|4.7e-7|3.2e-12|<10e-17|
|34B   |.99|4.8e-4|7.4e-9|<10e-17|
|1T    |1.0|.39|7.6e-6|1.77e-15|

The math around collision probability is interesting. The critical question is *how do you choose the optimal size for your identifier*?  We have two considerations: what is the size of the set, and how bad is a collision?  Collision cost is the critical thing to properly estimate.  The cost of collision determines acceptable error rate, and the size of the set applied to acceptable error rate determines how many bits to use for your identifier.  Doing this design step incorrectly can easily result in your dataset being 20%-100% larger than it should be.  Many computational constructs are composed of groupings of identifiers. The cost of doing a computation scales at least linearly with the size of the data involved; having identifiers that are too large causes compounding inefficiencies.

### Methods to Map Identifiers to Constructs

There are four methods to map from some construct to a compact, machine readable identifier: sequence id generation vs a parent scope, call a server and ask, fingerprint() a consistent construct representation, and call random() and remember the result.  Choose your algorithm based on compactness requirements vs cost to generate. If network calls are involved, compactness tends to matter enormously.  If you have subcomponents of a message, using sequence numbers against a parent id (numbering fields of a construct local to that construct) is cheap, compact and should almost always be used.

|Method|Advantages|Disadvantages|
|-|-|-|
|Sequence id vs parent scope| Easy code, very compact| Requires a constrained scope where the identifier is valid|
|Obtain a sequence id|Most compact|Requires interacting with an outside system to receive an identifier, complex code, shifts correctness burden elsewhere|
|fingerprint()|No storage required, can be recreated at will|Requires for-all-time stable fingerprint function and construct format|
|random() and remember|Easy code, no need to remember how randomness happened|Must always remember generated random identifier (registration), must ensure random generator is 'good enough'[^3]|

### UUID storage and transmission.

Modern computing applications commonly use an inefficient pattern that is difficult to recover from.  This failure mode uses inefficient, human readable wire formats (JSON, YML, etc) combined with UUIDs (and other numbers) that are represented as hexadecimal strings (or hex strings with dashes, as standard UUID printing does).  The cost of these fluffy representations compounds.

|UUID encoding | Size in bytes | Size vs binary | Example | When to Use |
|-|-|-|-|-|
|Binary|16|1.0x|XXXXXXXXXXXXXXXX|In memory, wire format is binary|
|Base64 ASCII|23|1.5625x|Base64ASCIIis23bytesxx|Wire format is JSON, ID is in a URL|
|Hex ASCII, no hypens|33|2.0625x|1234567890abcdef1234567890abcdef123456|Never|
|Hex ASCII, hyphens|37|2.3125x|  123456789-0abc-def1-2345-67890abcdef123456|Never Never|

As an example, consider a document with 20 subdocuments in it, from a set of documents sized ~33M. Assume each subdocument is from a set 32K sized. We'll need a way to identify everything, so we'll need 21 identifers.  An inefficient representation would store 21 128 bit identifiers (UUIDs) as 37 bytes of hexadecimal-plus-dashes UUID format.  It's minimal size (ie: no wire format overhead) would therefore be 777 bytes.

An efficient way to do this is to use a 1 random identifier and 20 identifiers that are sequence number scoped. These will be sized 80 bits (global random, collision probability 4.6e-10). The 20 sub identifiers will be `log(32K,2) = 32` bits each using sequence number assignment.  So the minimal representation will take `10 bytes + 20 * 4 bytes = 90 bytes`.

The inefficient format is 8.63x larger in the best case. With a JSON wire format the overhead will increase even more (string keys and fluffy encodings).  When initially prototyping applications this may not seem problematic. Unfortunately, any software that analyzes this data will take at least 8.63 times longer (as computation cost scales with input size) to do anything.  This places the performance breakdown threshold for that software system much lower than it would be with an efficient format.

### Actively Choose Identifier Generation Methods

It is extremely beneficial from a compactness and correctness[^4] perspective to skillfully design how you create identifiers and track identifiers in your software systems.  A simple example provides an existence proof that not actively designing the identifiers your system uses can easily show **eight times** higher memory requirements.

[^1]: Expensive in terms of time or computational cost is only measured in terms of what alternate computational method costs are.  An operation taking some amount of real time may or may not be expensive: it depends on how much computation resources, time and energy were consumed relative to other methods of solving a given problem.  Randomly generating an identifier for some new block of data is much faster and has better correctness properties than sending a (possibly failing) RPC to ask a server to assign an ID.

[^2]: A fingerprint function is a hash function that is stable for all time.  Such a function deterministically transforms a consistent representation of a computational construct into some number of random bits.  Such a function does not need to be a cryptographically strong hash, and rarely is.

[^3]: A cryptographic hash guarantees this, but is very expensive.  Some random generators have short periods (only 48 bit cycles, say), and the entropy (randomness) generated by such a random generator is at most the entropy of its period.  

[^4]: A performance bug is just a correctness bug that only happens when the system is under load.  When is the system under load? More often than one might think.
