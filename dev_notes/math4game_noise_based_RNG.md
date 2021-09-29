
# Math for Game Programmers: Noise-Based RNG


## Limitations of traditional RNG

- Order-dependent
    - generating near-infinite planets ? Mincreaft chunks ? Uh oh...
- Poor random-access
    - Fast-forward 100 numbers in the sequence ?
    - Rewind 3 numbers in the sequence ? 
- Non-trivial instaniation
    - We take LOTS of algorithmic options off the table if we can't make lots of RNGs, fast
- Lack of temporal / spatial coherence
    - have to manually remember previous results in order to interpolate, smooth, etc.


## Noise Functions

### Noise Functions 1

x=0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 
--- |  --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |---
0.44 | 0.14 | 0.50 | 0.89 | 0.13 | 0.69 | 0.90 | 0.09 | 0.05 | 0.87 | 0.68 | 0.49 | 0.64


- Put any integer "position" in; get a random float [0,1] ( or uint ) back out
- For any give input value, output is guaranteed to always be the same.
- Feels like a lookup table, but takes no space and is infinitely large


### Noise Function 2 

-  Stateless! Zero bytes ( if you want )!
- No recurrence Relation!
- Thread-safe ( pure function, no external state) !
- Random access ! Order-independence !
- True seeds -- each seed gives a unique (infinite) table, **not** just an offset into a different position in the sequence.

### Noise Function 3

- ![](../imgs/noise_func_0.png)
- ![](../imgs/noise_func_1.png)

- N-Dimensional
    - 1D position is hash of seed and position (x,y,z...)
- 2D: put in any integer (x,y), get out random value
- For any given input (x,y) , output value is identical
- like an infinite 2D table!


### Noise Function 4

- Okay, but Noise is only good for certain things, right ?
    - Tile variation
        - grass tile #2, grass tile #3
    - Base function for smoothed fractal noise ( e.g. Perlin, simplex )


### Noise Function 5

- Add variance to make things more organic !
- ![](../imgs/gpu_add_more_var_make_organic.png)


### Noise Function 6

- Order-independent RNG !
    - Generate world chunks, planets, villages, NPCs in any order!
    - Consistent results for a seed, regardless of player traversal order !
    - No need to generate everything up front !
        - this is, if want to know where things are going to be, no need to generate everything up front, we can always generate the numbers on demand, and they're always going to be consistent.
        - and because we can generate them in a new order, that means in x order or in y order, that is really a hard problem in RNGs.
    - No need to store/remember massive amounts of RNG data !


### Noise Function 7

Noise Functions are also really useful for 

- Placing scattered placement of objects within an infinite space!
    - this is a very hard problem but its one that you're going to encounter if you do a lot of procedural generation.
    - Use local maxima 
    - Works even better with smoothed fractal noise, e.g. Perlin Noise, Super-easy to adjust object "density" by changing "persistence" !
- Real seeds means infinite infinities of random numbers!
    - Eliminates chance of "sequence syncing"


### RNG vs. Noise

```c++
uint32_t SomeRNG::Rand() {
    m_state = DoSomethingFunkyTo( m_state );
    return m_state;
}

uint32_t SomeNoiseFunction( int position ) {
    return DoSomethingFunkyTo( position ) ;
}
```

### RNG-based Noise ?

```cpp
unit32_t NoiseFunctionMakeFromRNG( int position ) {
    SomeRNG rng( position ) ; // Create a new RNG; use "position" as seed
    return rng.Rand();
}
```

- This totally works, using this generate minecraft worlds.
- But it's not fast. We pay for construction, initialization, seeding, AND rand.

### Noise-based RNG ?

```cpp
Class RngBasedOnNoise {
    RngBasedOnNoise() { m_position = 0;} // constructor
    unit32_t Rand(); // Get the next ranomd number

    int m_positon; // RNG keeps position as only internal state
}

uint32_t RngBasedOnNoise::Rand() {
    return DoSomethingFunkyTo( m_postion++ ) ;
}
```

- No problem.


### 1D noise function

- ![](../imgs/math4game_noise_func_some.png)
- ![](../imgs/math4game_noise_sonoise.png)
- ![](../imgs/math4game_noise_libnoise.png)
    - ...are really just a very special form of Hash function (which hashes an "int" into a pseudorandom "unsigned int").

- ![](../imgs/math4game_noise_perlinnoise.png)
- squirrel3
    ```python
    # The base bit-noise constants were crafted to have distinctive and interesting
    # bits, and have so far produced excellent experimental test results.
    NOISE1 = 0xb5297a4d  # 0b0110'1000'1110'0011'0001'1101'1010'0100
    NOISE2 = 0x68e31da4  # 0b1011'0101'0010'1001'0111'1010'0100'1101
    NOISE3 = 0x1b56c4e9  # 0b0001'1011'0101'0110'1100'0100'1110'1001

    CAP = 1 << 32

    def squirrel3(n, seed=0):
        """Returns an unsigned integer containing 32 reasonably-well-scrambled
        bits, based on a given (signed) integer input parameter `n` and optional
        `seed`.  Kind of like looking up a value in an infinitely large
        non-existent table of previously generated random numbers.
        """
        n *= NOISE1
        n += seed
        n ^= n >> 8
        n += NOISE2
        n ^= n << 8
        n *= NOISE3
        n ^= n >> 8
        # Cast into uint32 like the original `Squirrel3`.
        return n % CAP
    ```
    - [squirrel3 on github](https://github.com/sublee/squirrel3-python/blob/master/squirrel3.py)


### Multidimensional Noise functions

- Q: how do you generate noise for a 2D, 3D, 4D position ?
- A:
    ```go
    func Get2dNoiseUint(indexX int32, indexY int32, seed uint32) uint32 {
        const PrimeNumber int32 = 198491317 // Large prime number with non-boring bits
        return Get1dNoiseUint(indexX+(PrimeNumber*indexY), seed)
    }

    func Get3dNoiseUint(indexX int32, indexY int32, indexZ int32, seed uint32) uint32 {
        const Prime1 int32 = 198491317 // Large prime number with non-boring bits
        const Prime2 int32 = 6542989   // Large prime number with distinct and non-boring bits
        return Get1dNoiseUint(indexX+(Prime1*indexY)+(Prime2*indexZ), seed)
    }
    ```
    - [go-squirrelnoise](https://github.com/EDKarlsson/go-squirrelnoise/blob/fe9bd5e6d2248fe71acd702e4bb8a015d0e7914f/SquirrelNoise5.go#L85)


## Tidbits  and Takeaways

- Noise is awesome, and can do lots of things RNGs can't !
- Noise also happens to make better RNG than most RNGs !
- Forget rand()
- Keep it small, smiple, fast, and flexible




