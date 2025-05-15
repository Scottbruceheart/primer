# Entropy and Compression

Scott Bruce, 2022-12-26

Compression algorithms are a class of transforms that make data smaller.  We can compress any arbitrary sequence of bytes.

```
compressed = Compress(uncompressed)
decompressed = Uncompress(compressed)
```

Compression can be lossy or lossless.  Lossy compression drops 'low value' information. It is irreversible. Lossless compression reduces the statistical redundancy in the message but removes no information. It is reversible.

```
losslessly_compressed = (uncompressed == decompressed)
```

Lossy compression is a topic for another time. Lossless compression, correctly utilized, results in enormous savings in network transmission, file storage, program performance, and programmer sanity.

Lossless compression works by reducing statistical redundancy in some stream of bytes (ie: piece of data).  Compression is not magic: it requires time, CPU, memory and power during compression and decompression proportional to the input data size and desired compression level.  We still must do computations to decide if compression is an applicable technique.  It just turns out that it almost always is.  A message that looks random (ie: an encrypted message) will be *incompressible*[^1]. The costs of compression and decompression are not inherently symmemtric, and vary enormously between compression systems.

Entropy is a concept from information theory: the amount of 'surprisingness' in the data. We know a compression algorithm is unable to make a message smaller than its entropy content.  011010010101110 repeated 10000 times is low-entropy, while a random stream of 150000 binary digits is high entropy.  We can compute the entropic content (in bits) of any data to get a hard lower bound on compression ratio. Modern compression algorithms get close to the limit. Common formats in modern computing often have low entropy, resulting in excellent compression performance.  'Base64' encoding only uses 64 states of the 256 available in an 8 bit byte. Decimal uses 10[^2]. Compression can translate the low entropy format into a higher entropy one.  It costs CPU, time, and power on every access after, and compression can only approach the lower entropy bound. It is very important to use efficient, binary data formats and avoid semantic redundancy. Any move away from efficient representation can be *partially* recovered by compression, but not entirely.  Compression can only get so close to information theoretic bounds. Computation cost scales with the size of the input, and the size improvement due to compression doesn't help the computation on the data be faster, either, as the data must be uncompressed to be operated on.

The most fundamental limit of compression is that it *cannot* understand the semantics of our data.  Compression will never be able to see we've chosen a wire format with huge overhead, or that we've used stringly typed integers. It can never know we could have stored 4 bit varints instead of 8 byte double precision floating points.  Compression's computation expense is also proportional to input size. Creating a compact data representation is the most effective way to have small data size. More efficient data encoding makes for compounding beneficial effects.

Lossy compression algorithms used to encode media (images, audio, video) all result in almost maximally high entropy streams.  Lossless compression against that data will get no compression against the media part of it. As in all engineering, do the computation to make sure the compression costs (CPU, time, latency) are worth the benefit (reduction in data size). However, if you find that a lossy compression outputs a stream that is not high entropy, the right solution is probably using a better media codec [^3][^4].

Compression is most beneficial when you have a reasonably well encoded message, and you do computations to figure out the benefit of spending CPU, memory and time on compressing and decompressing that message. Often times resources in a datacenter are far cheaper than on a mobile device, so spending a lot of CPU making very small messages that are sent to lots of phones in datacenters is very beneficial.  In the modern cloud environment, dropping egress bytes by using heavier compression algorithms is often a net money and time positive in addition to the other factors.

* Low entropy files and only low entropy files compress at high compression ratios.
* Media files are high entropy: they're compressed by specialist lossy compression algorithms.
* Encrypted data looks completely random[^5] and *does not compress at all*.
* Blindly compressing is wasteful of computational resources: ensure your compression target is compressible.
* Compression has a nontrivial cost. This cost is almost always worth paying if we get a reasonable compression ratio. Non repetitive text tends to compress about 2:1(50%). 20:1 (5%) or better compression might be achieved against very repetitive text (ie: server text logs). A better than 20% data size reduction is generally worth it.
* When considering compression benefits, consider the *future* likely state of your software system. Optimizing your program overly tightly to the execution hardware you happen to be using right now is often a mistake.
* zstd is probably the best general compression algorithm available at this time, edged out in some use cases that require extreme speed by lz4.
* `Uncompress(Compress(data)) == Uncompress(Compress(data))` Lossless compression results in identical input and output data. It is what makes lossless compression lossless.
* *Do not rely on* `Compress(data) == Compress(data)`.  Compression algorithms are deterministic: however they are sensitive to parameterization and tend to be dynamically adjusted when run in production. Ex: It is **NOT SAFE** to rely on the fingerprint of a compressed data block as a unique identifier in any situation.
* * As a common example, the gzip file format contains the timestamp the compressed file was created.
* `C(d) != C(C(d))` The same bytes do not result if compression is applied repeatedly.

Compression is not magic, merely an incredibly useful technqiue.  It still obeys all tractability bounds, computability bounds, and consumes time, CPU, memory and power to work.  Spend your first effort on using efficient and compact data formats, *then* evaluate if compression is a benefit to your software.  Relying on compression to fix design flaws leads to sadness.  


[^1]: The digits of e look random, but a simple function will compute them arbitrarily.  That is compression in some sense, but isn't a generally applicable technique.

[^2]: Bits are a base 2 measure of information content, determined by the logarithm of uniquely representable states.  log2(10) is 3.32 bits, log2(64) is 6 bits, log2(256) is 8 bits.

[^3]: codec is encoder-decoder or compressor-decompressor, depending on who you ask.

[^4]: Uncompressed media streams are astonishingly large, and human perception can't perceive certain kinds of loss in their compression.  The only reasonable way to mass distribute media files is via lossy compression, so almost all media files use it.  A common method is to transform video frames to the frequency domain, and discard higher frequency information.

[^5]: If the encrypted data didn't look completely random, it would be vulnerable to cryptanalysis.


## Appendix



## Advanced to Really advanced Topics

* Illustrative but almost trivial reason compressed blocks of the same data are different: GZip puts a timestamp in the compressed file output format header.
* One claim was 'computing the entropy of a message is easy!'. It is! Treat each byte in the message as a symbol of an alphabet to code. Count to compute frequency. Sum the entropy of each symbol, computed with `-probability * log2(probability`. Using symbols of different lengths will get the same result.  
* The source coding theorem covers the minimal data transmitted to send a message through a lossless channel.  Real channels have errors. Error resistant transmission requires sending additional bits. These noisy channels are covered by the noisy channel coding theorem, which proves what the minimal amount of extra information is required.  This theorem dates to 1948. Rateless erasure and fountain codes were developed 50 years later (around 2002).  They matter as those codes enable efficient WWAN function and are *the* reason cellular data nets work as well as they do.  There was a *fifty year* gap between the minimal proof and practical codes that can actually hit those minimal bounds.
* Compressed messages vs uncompressed messages have a nice error detecting property.  If some bit is flipped in a compressed block, it is likely to change many bits in the message on decompression. This means the decompressed block is more likely to be seen as invalid instead of quietly corrupted.  Single bit errors are fairly frequent, as most internet infrastructure doesn't use error correcting memory. (Bit flips caused by cosmic rays)
* Base 2 information is a 'bit': `-probability * log2(probability)`. Base 10 entropy is a 'ban', and base e entropy is a 'nat'. Bans are frequently used when computing log-odds probabilities, a common technique in ML.  Notably, a tenth of a ban is considered about the finest a human can express their belief in some testable outcome. This is the change in odds from even to 5:4.
* All computation is abstracted to `R = F(D)`.  R and D are simply byte strings. Any computation can have compression placed before or after it. This matches the codec model (enc->transmit->dec).  Note that transmission through time after being stored on disk and transmission through space over a wire are *exactly identical* in this model of computation.
* Algorithmic information theory analyzes the minimal possible representation of any sequence of bytes. What is the smallest self running executable that will produce that sequence of bytes as an output.  Computing the minimal size of an arbitrary string is *wildly* uncomputable. Chaitin's Constant comes out of that analysis. Chaitin's Constant is the probability, given a Turing machine, a randomly constructed program run on it will halt. Chatin's constant itself is transcendental and uncomputable, and there are *infinitely many* different Chaitin constants associated with infinitely many ways to represent a Turing machine.
* The Boltzmann Constant (statistical thermodynamics) converts between physical entropy (heat, joules) and information entropy. Thus, with a conversion factor, your heater is just blasting random yottabytes of information into the environment.  Similarly amusing, questions like, "if we save a megabyte of data to SSD how many megabytes of heat do we create?" have actual meaning.
