---
layout:     post
title:      Executing Rust from C#
date:       2020-05-04
summary:    How to execute Rust code from a C# application.
categories: 
---

![C# and Rust logos](/images/cs-rs.png)

Right at the front of "The Rust Book" there is [a section about calling Rust code from other languages](https://doc.rust-lang.org/1.5.0/book/rust-inside-other-languages.html). The examples use Python, Ruby and Javascript and show how using Rust for expensive standalone processes can save time.

I've spent a lot of time working with C# in my career, so naturally I was curious how much faster the example Rust code would be than the C# equivalent.

The full repo is available [here](https://github.com/codingupastorm/rust-in-csharp) if you want to try it for yourself.

Here's the Rust code:

    pub extern fn process() {
        let handles: Vec<_> = (0..10).map(|_| {
            thread::spawn(|| {
                let mut x = 0;
                for _ in 0..5_000_000 {
                    x += 1
                }
                x
            })
        }).collect();

        for h in handles {
            println!("Rust thread finished with count={}",
            h.join().map_err(|_| "Could not join a thread!").unwrap());
        }
    }

This code is doing something very trivial: it's starting 10 threads, counting to 5,000,000 on each of them, and then announcing when it's complete.

Here is roughly the C# equivalent:

        private static void ProcessCSharp()
        {
            const int threadCount = 10;
            Task[] tasks = new Task[threadCount];

            for (int i = 0; i < threadCount; i++)
            {
                tasks[i] = Task.Factory.StartNew(() =>
                {
                    int count = 0;
                    for (int j = 0; j < 5_000_000; j++)
                    {
                        count += 1;
                    }
                    Console.WriteLine("C# thread finished with count={0}", count);
                });
            }

            Task.WaitAll(tasks);
        }

We'll see how these compare in terms of run-time soon.

## Building a DLL From a Rust Project

To be able to call Rust methods from C#, we need to put it into a format that C# can understand. In this case we're going to use DLL (Dynamic Linked Library) files. 

To generate a DLL from the Rust project, we have to firstly ensure that the external-facing methods are inside _lib.rs_ and given the visibility `pub extern`. They also need to be marked with the `#[no_mangle]` attribute, whch prevents Rust from internally messing with the API names during optimisation.

Lastly, we need to add these lines to Cargo.toml before the `[dependencies]` section:

    [lib]
    name="RustLibrary"
    crate-type = ["dylib"]

These lines tell Rust and Cargo that we want a DLL named _RustLibrary.dll_ when we build the project.

When all this is done, all we have to do is build our Rust project (with the Release flag because we want it to be as fast as possible):

    cargo build --release

And voila! Our DLL, _target/release/RustLibrary.dll_ has been created for us!

## Calling a DLL From A C# Project

Now that we have a DLL, we can interact with it by adding _RustLibrary.dll_ to a Visual Studio project, ensuring that it gets copied to the output folder on build, and importing the DLL inside our C# code. 

Here's how that looks:

        [DllImport("RustLibrary.dll", EntryPoint = "process")]
        private static extern void ProcessInRust();

This is where the magic happens - if you now call `ProcessInRust()` anywhere in your C# project, what happens behind the scenes is your .NET process calls into the Rust code we created earlier!

## Is it Faster? Yes.

Here's the extremely basic code I wrote to test the speed of the `ProcessInRust()` and `ProcessCSharp()` methods above.

    Stopwatch csharpStopwatch = new Stopwatch();
    Stopwatch rustStopwatch = new Stopwatch();

    csharpStopwatch.Start();
    ProcessCSharp();
    csharpStopwatch.Stop();

    rustStopwatch.Start();
    ProcessInRust();
    rustStopwatch.Stop();

    Console.WriteLine();
    Console.WriteLine("Execution time in C#: " + csharpStopwatch.Elapsed);
    Console.WriteLine("Execution time in Rust: " + rustStopwatch.Elapsed);

When I run this on my computer in the C# project:

    dotnet run -- --release

This is the output:

    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    C# thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000
    Rust thread finished with count=5000000

    Execution time in C#: 00:00:00.0387920
    Execution time in Rust: 00:00:00.0133602

The results varied but this is about the closest C# got to Rust in my runs. According to this, Rust is around 3 times faster at summing to 5,000,000 on 10 threads. That's not as big a difference as I was expecting, though I understand that calling into an external library itself is accounting for some of the Rust method's execution time. 

I'm excited to try out some more complicated scenarios and see what the difference is like.

