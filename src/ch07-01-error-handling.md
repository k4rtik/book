# Error Handling

Rust's laser-focus on safety spills over into a related area: error handling.
Errors are a fact of life in software, and Rust encourages you to face that
fact early on in the development of your programs in order to limit the
unexpected states your program might leave the system in. Luckily, Rust has a
number of tools that you can use when something bad happens to handle the
situation appropriately.

Rust splits errors into two major kinds: errors that are recoverable and
errors that are not recoverable. It has two different strategies to handle
these two different forms of errors.

What does it mean to "recover" from an error? In the simplest sense, it
relates to the answer of this question:

> If I call a function, and something bad happens, can I do something
> meaningful? Or should execution stop?

Some kinds of problems have solutions, but with other kinds of errors,
all you can do is throw up your hands and give up.

We'll start off our examination of error handling by talking about the
unrecoverable case first. Why? You can think of unrecoverable errors as a
subset of recoverable errors. If you choose to treat an error as unrecoverable,
then the function that calls your code has no choice in the matter. However, if
your function returns a recoverable error, the calling function has a choice:
handle the error properly, or convert it into an unrecoverable error. This is
one of the reasons that most Rust code chooses to treat errors as recoverable:
it's more flexible. We're going to explore an example that starts off as
unrecoverable, and then, in the next section, we will convert it to a
recoverable error. Finally, we'll talk about how to create our own custom error
types.
