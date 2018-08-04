Read `I've been writing ring buffers wrong all these years`__ by Juho Snellman.

.. __: https://www.snellman.net/blog/archive/2016-12-13-ring-buffers/

The article discusses the common methods of implementing a ring buffer on top
of an array, and the obvious issues present in the two obvious solutions to the
problem. But the article's solution isn't graceful: it requires power-of-two
overflow, it requires power-of-two sizes. We can do better; the better
solution, IMO, is in the comments:

    Why not store indices modulo 2*capacity? Same as last solution, but
    prevents overflow. An additional bit in the index can be viewed as a 'fold
    number'. An array is full when indices refer to the same cell on different
    folds, and empty if they are on the same fold. And we don't need more then
    two folds, as indices can't be more than 'capacity' elements apart.

—"By dizzy57 on 2016-12-14"

A `comment on HN <https://news.ycombinator.com/item?id=13176744>`_  by Chuck
McManis restates this answer:

    I have always considered these "double ring" buffers. Along the same lines
    as how you figure out which race car is in the race is in lead by their
    position and lap count. You run your indexes in the range 0 .. (2 * SIZE)
    and then empty is

    ::

        EMPTY -> (read == write)
        FULL -> (read == (write + SIZE) % (2 * SIZE))

    Basically you're full if you're at the same relative index and your on
    different laps, you are empty if you at the same relative index on the same
    lap. If you do this with power of 2 size then the 'lap' is just the bit 2
    << SIZE.

The read/write pointers are indexes, and they're in [0, 2 * SIZE). The lap
analogy is useful for thinking about it. The "2" in the above is that we don't
store a full lap count, in essence, we only store one extra bit: are the
read/write pointers are on the same lap (empty) or different laps (full).
