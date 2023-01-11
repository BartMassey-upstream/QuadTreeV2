<https://www.reddit.com/r/learnrust/comments/108kdle/im_struggling_with_the_borrow_checker_while/>

Welcome to Rust! This looks like a fun project.

It looks like your quadtree does not yet handle objects that
span more than one leaf? Or am I misunderstanding that?

-----

You really want your `QuadTree` to own its data. This is the Rust way.

I've made the changes to `quad_tree.rs` to do this, but they're too extensive to post here. Check out the branch `owned-entities` in [my fork](https://github.com/BartMassey-upstream/QuadTreeV2) of your repo for the details.

I mostly had to modify `tree.insert()` and friends to insert entities cleanly. In the process, I removed a whole bunch of unneeded lifetime stuff, cleaned those functions up, and added some extra capabilities.

-----

In the long run I don't think you're going to want to have separate types for every recursion depth of quadtree; the type system is hassling you more than it is helping you here. Instead, go ahead and create the appropriate `enum` and use that instead of a trait and two types.

    pub enum QuadTree<T> {
        Leaf(Vec<T>),
        Quads(Box<[QuadTree<T>; 4]>),
    }

This should help code cleanliness a lot, as well as allowing the creation of arbitrary-depth quadtrees. The downside is that because `Box` the `QuadTree` will live in the heap rather than on the stack: that's probably better anyhow given stack sizes.

-----

In general don't try to write object-oriented programs in Rust: it doesn't work very well. Instead try to figure out your data representation first, and then write code around it after.
