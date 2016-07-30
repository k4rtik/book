# Guessing Game

Let’s learn some Rust! For our first project, we’ll implement a classic
beginner programming problem: the guessing game. Here’s how it works: Our
program will generate a random integer between one and a hundred. It will then
prompt us to enter a guess. Upon entering our guess, it will tell us if we’re
too low or too high. Once we guess correctly, it will congratulate us. Sound
good?


## Set up

Let’s set up a new project. Go to your projects directory. Remember the end of
the `hello world` example that mentioned `cargo new` to create new cargo
projects? Let’s give it a shot:

```bash
$ cd ~/projects
$ cargo new guessing_game --bin
$ cd guessing_game
```

We pass the name of our project to `cargo new`, then the `--bin` flag, since
we’re making a binary, rather than a library.

Take a look at the generated `Cargo.toml`:

```toml
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]

[dependencies]
```

Cargo gets this information from your environment. If it’s not correct, go ahead
and fix that.

Finally, Cargo generated a ‘Hello, world!’ for us. Check out `src/main.rs`:

```rust
fn main() {
    println!("Hello, world!");
}
```

Let’s try compiling what Cargo gave us:

```bash
$ cargo build
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
```

Before we move on, let me show you one more Cargo command: `run`. `cargo run`
is kind of like `cargo build`, but it also then runs the produced executable.
Try it out:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/debug/guessing_game`
Hello, world!
```

Great! The `run` command comes in handy when you need to rapidly iterate on a
project. Our game is such a project: we want to quickly test each
iteration before moving on to the next one.

Now open up your `src/main.rs` again. We’ll be writing all of our code in this
file.

## Processing a Guess

Let’s get to it! The first thing we need to do for our guessing game is
allow our player to input a guess. Put this in your `src/main.rs`:

```rust,ignore
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

There’s a lot here! Let’s go over it, bit by bit.

```rust,ignore
use std::io;
```

We’ll need to take user input and then print the result as output. As such, we
need the `io` library from the standard library. Rust only imports a few things
by default into every program, [the ‘prelude’][prelude]. If it’s not in the
prelude, you’ll have to `use` it directly. There is also a second ‘prelude’, the
[`io` prelude][ioprelude], which serves a similar function: when you import it,
you get a number of useful, `io`-related things, so that's what we've done here.

[prelude]: ../std/prelude/index.html
[ioprelude]: ../std/io/prelude/index.html

```rust,ignore
fn main() {
```

As you’ve seen in Chapter 1, the `main()` function is the entry point into the
program. The `fn` syntax declares a new function, the `()`s indicate that
there are no arguments, and `{` starts the body of the function. Because
we didn’t include a return type, it’s assumed to be `()`, an empty
tuple. We will go over tuples in Chapter XX.

```rust,ignore
println!("Guess the number!");

println!("Please input your guess.");
```

We previously learned in Chapter 1 that `println!()` is a macro that
prints a string to the screen.

```rust,ignore
let mut guess = String::new();
```

Now we’re getting interesting! There’s a lot going on in this little line.
The first thing to notice is that this is a let statement, which is
used to create what are called ‘variable bindings’. Here's an example:

```rust,ignore
let foo = bar;
```

This will create a new binding named `foo`, and bind it to the value `bar`. In
many languages, this is called a ‘variable’, but Rust’s variable bindings have
a few tricks up their sleeves.

For example, they’re immutable by default. That’s why our example
uses `mut`: it makes a binding mutable, rather than immutable.

```rust
let foo = 5; // immutable.
let mut bar = 5; // mutable
```

Oh, and `//` will start a comment, until the end of the line. Rust ignores
everything in comments.

So now we know that `let mut guess` will introduce a mutable binding named
`guess`, but we have to look at the other side of the `=` for what it’s
bound to: `String::new()`.

`String` is a string type, provided by the standard library. A
[`String`][string] is a growable, UTF-8 encoded bit of text.

[string]: ../std/string/struct.String.html

The `::new()` syntax uses `::` because this is an ‘associated function’ of
a particular type. That is to say, it’s associated with `String` itself,
rather than a particular instance of a `String`. Some languages call this a
‘static method’.

This function is named `new()`, because it creates a new, empty `String`.
You’ll find a `new()` function on many types, as it’s a common name for making
a new value of some kind.

Let’s move forward:

```rust,ignore
io::stdin().read_line(&mut guess)
    .expect("Failed to read line");
```

That’s a lot more! Let’s go bit-by-bit. The first line has two parts. Here’s
the first:

```rust,ignore
io::stdin()
```

Remember how we `use`d `std::io` on the first line of the program? We’re now
calling an associated function on it. If we didn’t `use std::io`, we could
have written this line as `std::io::stdin()`.

This particular function returns a handle to the standard input for your
terminal. More specifically, a [std::io::Stdin][iostdin].

[iostdin]: ../std/io/struct.Stdin.html

The next part will use this handle to get input from the user:

```rust,ignore
.read_line(&mut guess)
```

Here, we call the [`read_line()`][read_line] method on our handle. Methods are
like associated functions but are only available on a particular instance of a
type, rather than the type itself. We'll talk more about methods in Chapter XX.
We’re also passing one argument to `read_line()`: `&mut guess`.

[read_line]: ../std/io/struct.Stdin.html#method.read_line

Remember how we bound `guess` above? We said it was mutable. However,
`read_line` doesn’t take a `String` as an argument: it takes a `&mut String`.
The `&` is the feature of Rust called a ‘reference’, which allows you to have
multiple ways to access one piece of data in order to reduce copying.
References are a complex feature, as one of Rust’s major selling points is how
safe and easy it is to use references. We don’t need to know a lot of those
details to finish our program right now, though; Chapter XX will cover them in
more detail. For now, all we need to know is that like `let` bindings,
references are immutable by default. Hence, we need to write `&mut guess`,
rather than `&guess`.

Why does `read_line()` take a mutable reference to a string? Its job is
to take what the user types into standard input and place that into a
string. So it takes that string as an argument, and in order to add
the input, that string needs to be mutable.

But we’re not quite done with this line of code, though. While it’s
a single line of text, it’s only the first part of the single logical line of
code. This is the second part of the line:

```rust,ignore
.expect("Failed to read line");
```

When you call a method with the `.foo()` syntax, you may introduce a newline
and other whitespace. This helps you split up long lines. We _could_ have
written this code as:

```rust,ignore
io::stdin().read_line(&mut guess).expect("failed to read line");
```

But that gets hard to read. So we’ve split it up, two lines for two method
calls. We already talked about `read_line()`, but what about `expect()`? Well,
we already mentioned that `read_line()` puts what the user types into the `&mut
String` we pass it. But it also returns a value: in this case, an
[`io::Result`][ioresult]. Rust has a number of types named `Result` in its
standard library: a generic [`Result`][result], and then specific versions for
sub-libraries, like `io::Result`.

[ioresult]: ../std/io/type.Result.html
[result]: ../std/result/enum.Result.html

The purpose of these `Result` types is to encode error handling information.
Values of the `Result` type, like any type, have methods defined on them. In
this case, `io::Result` has an [`expect()` method][expect] that takes a value
it’s called on, and if it isn’t a successful one, `panic!`s with a
message you passed it. A `panic!` like this will cause our program to crash,
displaying the message.

[expect]: ../std/result/enum.Result.html#method.expect

If we don't call this method, our program will compile, but we’ll get a warning:

```bash
$ cargo build
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
src/main.rs:10:5: 10:39 warning: unused result which must be used,
#[warn(unused_must_use)] on by default
src/main.rs:10     io::stdin().read_line(&mut guess);
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

Rust warns us that we haven’t used the `Result` value. This warning comes from
a special annotation that `io::Result` has. Rust is trying to tell you that you
haven’t handled a possible error. The right way to suppress the error is to
actually write error handling. Luckily, if we want to crash if there’s a
problem, we can use `expect()`. If we can recover from the error somehow, we’d
do something else, but we’ll save that for a future project.

There’s only one line of this first example left, aside from the closing curly
brace:

```rust,ignore
    println!("You guessed: {}", guess);
}
```

This prints out the string we saved our input in. The `{}`s are a placeholder:
think of `{}` as little crab pincers, holding a value in place. The first `{}`
holds the first value after the format string, the second set holds the second
value, and so on. Printing out multiple values in one call to `println!()` would then look like this:

```rust
let x = 5;
let y = 10;

println!("x and y: {} and {}", x, y);
```

Which would print out "x and y: 5 and 10".

Anyway, back to our guessing game. We can run what we have with `cargo run`:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

All right! Our first part is done: we can get input from the keyboard and then
print it back out.

## Generating a secret number

Next, we need to generate a secret number. Rust does not yet include random
number functionality in its standard library. The Rust team does, however,
provide a [`rand` crate][randcrate]. A ‘crate’ is a package of Rust code.
We’ve been building a ‘binary crate’, which is an executable. `rand` is a
‘library crate’, which contains code that’s intended to be used with other
programs.

[randcrate]: https://crates.io/crates/rand

Using external crates is where Cargo really shines. Before we can write
the code using `rand`, we need to modify our `Cargo.toml`. Open it up, and
add this line at the bottom beneath the `[dependencies]` section header that
Cargo created for you:

```toml
[dependencies]

rand = "0.3.14"
```

The `[dependencies]` section of `Cargo.toml` is like the `[package]` section:
everything that follows the section heading is part of that section, until
another section starts. Cargo uses the dependencies section to know what
dependencies on external crates you have and what versions of those crates you
require. In this case, we’ve specified the `rand` crate with the semantic
version specifier `0.3.14`.

Cargo understands [Semantic Versioning][semver], which is a standard for
writing version numbers. A bare number like above is actually shorthand for
`^0.3.14`, which means "any version that has a public API compatible with
version 0.3.14". If we wanted to use only `0.3.14` exactly, we could say `rand
= "=0.3.14"` (note the equal sign within the version string). And if we wanted
to use whatever the latest version currently is, we could use `*`. [Cargo’s
documentation][cargodoc] contains more details and other ways to specify
dependencies.

[semver]: http://semver.org
[cargodoc]: http://doc.crates.io/specifying-dependencies.html

Now, without changing any of our code, let’s build our project:

```bash
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
```

You may see different versions (but they will be compatible, thanks to semver!)
and the lines may be in a different order.

Lots of new output! Now that we have an external dependency, Cargo fetches the
latest versions of everything from the *registry*, which is a copy of data from
[Crates.io][cratesio]. Crates.io is where people in the Rust ecosystem
post their open source Rust projects for others to use.

[cratesio]: https://crates.io

After updating the registry, Cargo checks our `[dependencies]` and downloads
any we don’t have yet. In this case, while we only said we wanted to depend on
`rand`, we’ve also grabbed a copy of `libc`. This is because `rand` depends on
`libc` to work. After downloading them, it compiles them and then compiles
our project.

If we run `cargo build` again, we’ll get different output:

```bash
$ cargo build
```

That’s right, no output! Cargo knows that our project has been built, that
all of its dependencies are built, and that no changes have been made. There’s
no reason to do all that stuff again. With nothing to do, it simply
exits. If we open up `src/main.rs`, make a trivial change, then save it again,
we’ll only see one line:

```bash
$ cargo build
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
```

What happens when next week version `v0.3.15` of the `rand` crate comes out,
with an important bugfix? While getting bugfixes is important, what if `0.3.15`
contains a regression that breaks our code?

The answer to this problem is the `Cargo.lock` file created the first time we
ran `cargo build` that is now in your project directory. When you build your
project for the first time, Cargo figures out all of the versions that fit your
criteria then writes them to the `Cargo.lock` file. When you build your project
in the future, Cargo will see that the `Cargo.lock` file exists and then use
that specific version rather than do all the work of figuring out versions
again. This lets you have a repeatable build automatically. In other words,
we’ll stay at `0.3.14` until we explicitly upgrade, and so will anyone who we
share our code with, thanks to the lock file.

What about when we _do_ want to use `v0.3.15`? Cargo has another command,
`update`, which says ‘ignore the `Cargo.lock` file and figure out all the
latest versions that fit what we’ve specified in `Cargo.toml`. If that works,
write those versions out to the lock file’. But by default, Cargo will only
look for versions larger than `0.3.0` and smaller than `0.4.0`. If we want to
move to `0.4.x`, we’d have to update what is in the `Cargo.toml` file. When we
do, the next time we `cargo build`, Cargo will update the index and re-evaluate
our `rand` requirements.

There’s a lot more to say about [Cargo][doccargo] and [its
ecosystem][doccratesio] that we will get into in Chapter XX, but for now,
that’s all we need to know. Cargo makes it really easy to re-use libraries, so
Rustaceans are able to write smaller projects which are assembled out of a
number of sub-packages.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

Let’s get on to actually _using_ `rand`. Here’s our next step:

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("failed to read line");

    println!("You guessed: {}", guess);
}
```

The first thing we’ve done is change the first line. It now says `extern crate
rand`. Because we declared `rand` in our `[dependencies]`, we can now put
`extern crate` in our code to let Rust know we’ll be making use of that
dependency. This also does the equivalent of a `use rand;` as well, so we can
call anything in the `rand` crate by prefixing it with `rand::`.

Next, we added another `use` line: `use rand::Rng`. We’re going to use a
method in a moment, and it requires that `Rng` be in scope to work. The basic
idea is this: methods are defined on something called ‘traits’, and for the
method to work, it needs the trait to be in scope. For more about the
details, read the traits section in Chapter XX.

There are two other lines we added, in the middle:

```rust,ignore
let secret_number = rand::thread_rng().gen_range(1, 101);

println!("The secret number is: {}", secret_number);
```

We use the `rand::thread_rng()` function to get a copy of the random number
generator, which is local to the particular thread of execution
we’re in. Because we put `use rand::Rng` above, the random number generator has
a `gen_range()` method available. This method takes two numbers as arguments
and generates a random number between them. It’s inclusive on the lower bound
but exclusive on the upper bound, so we need `1` and `101` to ask for a number
ranging from one to a hundred.

The second line prints out the secret number. This is useful while
we’re developing our program to let us easily test it out, but we’ll be
deleting it for the final version. It’s not much of a game if it prints out
the answer when you start it up!

Try running our new program a few times:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

You should get different random numbers, and they should all be between 1 and
100. Great job! Next up: comparing our guess to the secret number.

## Comparing guesses

Now that we’ve got user input, let’s compare our guess to the secret number.
Here’s part of our next step. It won't quite compile yet though:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("failed to read line");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

A few new bits here. The first is another `use`. We bring a type called
`std::cmp::Ordering` into scope. Then we add five new lines at the bottom that
use that type:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => println!("You win!"),
}
```

The `cmp()` method can be called on anything that can be compared, and it
takes a reference to the thing you want to compare it to. It returns the
`Ordering` type we `use`d earlier. We use a [`match`][match] statement to
determine exactly what kind of `Ordering` it is. `Ordering` is an
[`enum`][enum], short for ‘enumeration’, which looks like this:

```rust
enum Foo {
    Bar,
    Baz,
}
```

[match]: match.html
[enum]: enums.html

With this definition, anything of type `Foo` can be either a
`Foo::Bar` or a `Foo::Baz`. We use the `::` to indicate the
namespace for a particular `enum` variant.

The [`Ordering`][ordering] `enum` has three possible variants: `Less`, `Equal`,
and `Greater`. The `match` statement takes a value of a type and lets you
create an ‘arm’ for each possible value. An arm is made up of a pattern and the
code that we should execute if the pattern matches the value of the type. Since
we have three types of `Ordering`, we have three arms:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => println!("You win!"),
}
```

[ordering]: ../std/cmp/enum.Ordering.html

If it’s `Less`, we print `Too small!`, if it’s `Greater`, `Too big!`, and if
`Equal`, `You win!`. `match` is really useful and is used often in Rust.

I did mention that this won’t quite compile yet, though. Let’s try it:

```bash
$ cargo build
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
src/main.rs:23:21: 23:35 error: mismatched types [E0308]
src/main.rs:23     match guess.cmp(&secret_number) {
                                   ^~~~~~~~~~~~~~
src/main.rs:23:21: 23:35 help: run `rustc --explain E0308` to see a detailed explanation
src/main.rs:23:21: 23:35 note: expected type `&std::string::String`
src/main.rs:23:21: 23:35 note:    found type `&_`
error: aborting due to previous error
Could not compile `guessing_game`.
```

Whew! This is a big error. The core of it is that we have ‘mismatched types’.
Rust has a strong, static type system. However, it also has type inference.
When we wrote `let guess = String::new()`, Rust was able to infer that `guess`
should be a `String`, so it doesn’t make us write out the type. With our
`secret_number`, there are a number of types which can have a value between one
and a hundred: `i32`, a thirty-two-bit number, or `u32`, an unsigned
thirty-two-bit number, or `i64`, a sixty-four-bit number or others. So far,
that hasn’t mattered, and so Rust defaults to an `i32`. However, here, Rust
doesn’t know how to compare the `guess` and the `secret_number`. They need to
be the same type.

Ultimately, we want to convert the `String` we read as input
into a real number type so that we can compare it to the guess numerically. We
can do that with two more lines. Here’s our new program:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

The new two lines:

```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```

Wait a minute, I thought we already had a `guess`? We do, but Rust allows us
to ‘shadow’ the previous `guess` with a new one. This is often used in this
exact situation, where `guess` starts as a `String`, but we want to convert it
to a `u32`. Shadowing lets us re-use the `guess` name rather than forcing us
to come up with two unique names like `guess_str` and `guess` or something
else.

We bind `guess` to an expression that looks like something we wrote earlier:

```rust,ignore
guess.trim().parse()
```

Here, `guess` refers to the old `guess`, the one that was a `String` with our
input in it. The `trim()` method on `String`s will eliminate any white space at
the beginning and end of our string. This is important, as we had to press the
‘return’ key to satisfy `read_line()`. If we type `5` and hit return, `guess`
looks like this: `5\n`. The `\n` represents ‘newline’, the enter key. `trim()`
gets rid of this, leaving our string with only the `5`.

The [`parse()` method on strings][parse] parses a string into some kind of
number. Since it can parse a variety of numbers, we need to give Rust a hint as
to the exact type of number we want. Hence, `let guess: u32`. The colon (`:`)
after `guess` tells Rust we’re going to annotate its type. `u32` is an
unsigned, thirty-two bit integer. Rust has a number of built-in number
types, but we’ve chosen `u32`. It’s a good default choice for a small
positive number. You'll see the other number types in Chapter XX.

[parse]: ../std/primitive.str.html#method.parse

Just like `read_line()`, our call to `parse()` could cause an error. What if
our string contained `A👍%`? There’d be no way to convert that to a number. As
such, we’ll do the same thing we did with `read_line()`: use the `expect()`
method to crash if there’s an error.

Let’s try our program out!

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Nice! You can see I even added spaces before my guess, and it still figured
out that I guessed 76. Run the program a few times. Verify that guessing
the secret number works, as well as guessing a number too small.

Now we’ve got most of the game working, but we can only make one guess. Let’s
change that by adding loops!

## Looping

The `loop` keyword gives us an infinite loop. Let’s add that in:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => println!("You win!"),
        }
    }
}
```

And try it out. But wait, didn’t we just add an infinite loop? Yup. Remember
our discussion about `parse()`? If we give a non-number answer, we’ll `panic!`
and quit. Observe:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```

Ha! `quit` actually quits. As does any other non-number input. Well, this is
suboptimal to say the least. First, let’s actually quit when you win the game:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

By adding the `break` line after the `You win!`, we’ll exit the loop when we
win. Exiting the loop also means exiting the program, since the loop is the last
thing in `main()`. We have another tweak to make: when someone inputs a
non-number, we don’t want to quit, we want to ignore it. We can do that
like this:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

These are the lines that changed:

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

This is how you generally move from ‘crash on error’ to ‘actually handle the
error’: by switching from `expect()` to a `match` statement. A `Result` is the
return type of `parse()`. `Result` is an `enum` like `Ordering`, but in this
case, each variant has some data associated with it. `Ok` is a success, and
`Err` is a failure. Each contains more information: in this case, the
successfully parsed integer or an error type, respectively. When we `match` an
`Ok(num)`, that pattern sets the name `num` to the value inside the `Ok` (the
integer), and the code we run just returns that integer. In the `Err` case, we
don’t care what kind of error it is, so we just use the catch-all `_` instead
of a name. So for all errors, we run the code `continue`, which lets us move to
the next iteration of the loop, effectively ignoring the errors.

Now we should be good! Let’s try it:

```bash
$ cargo run
   Compiling guessing_game v0.1.0 (file:///home/you/projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Awesome! With one tiny last tweak, we can finish the guessing game. Can you
think of what it is? That’s right, we don’t want to print out the secret
number. It was good for testing, but it kind of ruins the game. Here’s our
final source:

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

## Complete!

This project showed you a lot: `let`, `match`, methods, associated
functions, using external crates, and more.

At this point, you have successfully built the Guessing Game! Congratulations!