# rust_hawktracer
Rust bindings for Amazon's Hawktracer profiler.  
This offers simple, minimal bindings to help you profile your programs.

## Warning
I have just tested it on Windows since that's what I have at home, use it at your own risk.  
You need an external tool in order to transform bindings from a binary format to something that can be interpreted by __chrome:://tracing__
This tool can be build for now from the main hawktracer repo (client/hawktracer-to-json).  
When the main hawktracer repo will offer binary releases, I would advise you to grab one from there, for now you either build it yourself, or use it from the rust_hawktracer releases.  

## How to use
In Cargo.toml:
```
[dependencies]
rust_hawktracer = {git = "https://github.com/AlexEne/rust_hawktracer.git", features=["profiling_enabled"]}
```

In your main.rs:

```
#[macro_use]
extern crate rust_hawktracer;
use rust_hawktracer::*;
use std::{thread, time};

fn main() {
    let instance = rust_hawktracer::start_hawktracer("trace.bin", 4096);
    println!("Hello, world!");
    {
        scoped_tracepoint!(_test);
        thread::sleep(time::Duration::from_millis(10));
        
        {
            for _ in 0..10 {
                scoped_tracepoint!(_second_tracepoint);
                thread::sleep(time::Duration::from_millis(10));
            }
        }
    }
    rust_hawktracer::stop_hawktracer(instance);
}
```

## Visualization

Download _hawktracer.json.exe_ and use it like this:

```
.\hawktracer-to-json.exe --source trace.bin --output trace.json
```

Open a chrome browser and go to this address: ```chrome://tracing/```

For the program above you should see the following trace:  

![alt text](https://github.com/AlexEne/rust_hawktracer/raw/master/images/trace_demo.PNG)


## Things to watch out for.
In rust macros I can't create new variable names right now, this means that if you want to avoid warnings, the tracepoint variable names have to start with a leading ```_```, as in ```scoped_tracepoint!(_second_tracepoint)```.
IF you figure out a way to do this, feel free to raise a PR / issue.
