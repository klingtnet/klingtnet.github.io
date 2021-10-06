```json
{
  "author": "Andreas Linz",
  "title": "The simplest sorting algorithm",
  "description": "",
  "created_at": "2021-10-06",
  "tags": null,
  "hidden": false
}
```

# The simplest sorting algorithm

Not all of us have enjoyed a formal computer science education so let's define what a sorting algorithm actually does:

> A sorting algorithm takes an array and puts it elements in a certain order. What order means depends on the values stored in the array, e.g. in numerical order for numbers or lexicographic order for strings. [^1]

Let's see an example, for an array `a = [4,2,3,1]`, a sorting algorithm applied to `a` will result in `[1,2,3,4]` when numerically sorted in ascending order.

Ok, now that we've defined what sorting means let's head over to why I am writing this article.  I discovered a paper called _Is this the simplest (and most surprising) sorting algorithm ever?_ on [lobste.rs][lobsters] today.  Sorting algorithms are often used in basic computer science lectures to teach algorithms. The first algorithm students encounter is very often [Bubble sort][bubble-sort].

Bubble sort is by no means a complicated algorithm as you can see in the prototypical Go implementation below:

```go
xs := []int{4, 2, 6, 10, 3, -1}

for {
    swapped := false
    for i := 1; i < len(xs); i++ {
        if xs[i-1] > xs[i] {
            xs[i-1], xs[i] = xs[i], xs[i-1]
            swapped = true
        }
    }
    if !swapped {
        break
    }
}
fmt.Println(xs) // prints [-1 2 3 4 6 10]
```

You can play with the implementation in the [Go playground][play-bubble-sort].  It just iterates over the array until there is no element to swap anymore.  The only complicated thing is to keep around the state if two elements got swapped.

To my surprise the [paper][paper] mentioned above is describing an even more straightforward way to sort an array:

```go
xs := []int{4, 2, 6, 10, 3, -1}
for i := 0; i < len(xs); i++ {
  for j := 0; j < len(xs); j++ {
    if xs[i] < xs[j] {
      xs[i], xs[j] = xs[j], xs[i]
    }
  }
}
fmt.Println(xs) // prints [-1 2 3 4 6 10]
```

It does not get much simpler than this and I assume that this might be the first algorithm future computer science students will encounter in their basic lessons. You can try this one out in the [Go playground][play-simple] as well.

[play-simple]: https://play.golang.org/p/jml6LqHApAN
[play-bubble-sort]: https://play.golang.org/p/wlTPJe7B5Ij
[lobsters]: https://lobste.rs/s/gh1ngc/is_this_simplest_most_surprising_sorting
[bubble-sort]: https://en.wikipedia.org/wiki/Bubble_sort
[paper]: https://arxiv.org/pdf/2110.01111.pdf

[^1]: Take this definition with a grain of salt, I wrote this off the top of my head.